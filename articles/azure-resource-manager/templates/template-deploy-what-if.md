---
title: Despliegue de plantillas hipotéticas (Vista previa)
description: Determine los cambios que se producirán en los recursos antes de implementar una plantilla de Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jgao
ms.openlocfilehash: b8e94d0b4f364e2873dfc21792a67f11c33483bf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010196"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Operación hipotética de la implementación de plantilla de Resource Manager (vista previa)

Antes de implementar una plantilla de Azure Resource Manager, es posible que desee obtener una vista previa de los cambios que se producirán. Azure Resource Manager proporciona la operación hipotética que le permite ver cómo cambiarán los recursos si implementa la plantilla. La operación hipotética no realiza ningún cambio en los recursos existentes. En su lugar, predice los cambios si se implementa la plantilla especificada.

> [!NOTE]
> La operación hipotética se encuentra actualmente en versión preliminar. Como versión preliminar, los resultados a veces pueden mostrar que un recurso cambiará cuando realmente no se produzca ningún cambio. Trabajamos para reducir estos problemas, pero necesitamos su ayuda. Informe de estos problemas en [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Puede usar la operación what-if con los comandos de PowerShell o las operaciones de la API REST.

## <a name="install-powershell-module"></a>Instalación del módulo de PowerShell

Para usar hipótesis en PowerShell, instale una versión preliminar del módulo Az.Resources de la galería de PowerShell.

### <a name="install-preview-version"></a>Instalación de la versión preliminar

Para instalar el módulo de versión preliminar, use lo siguiente:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Desinstalación de la versión alfa

Si previamente ha instalado una versión alfa del módulo de hipótesis, desinstale ese módulo. La versión alfa solo estaba disponible para los usuarios que se suscribieron para obtener una versión preliminar anticipada. Si no instaló dicha versión preliminar, puede omitir esta sección.

1. Ejecute PowerShell como administrador.
1. Compruebe las versiones instaladas del módulo Az.Resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Si tiene una versión instalada con un número de versión con el formato **2.x.x-alpha**, desinstale esa versión.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Anule el registro del repositorio de hipótesis que usó para instalar la versión preliminar.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Está listo para usar hipótesis.

## <a name="see-results"></a>Ver los resultados

En PowerShell, la salida incluye resultados codificados por colores que le ayudarán a apreciar los distintos tipos de cambios.

![Operación hipotética de implementación de plantilla al administrador de recursos fullresourcepayload y tipos de cambio](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

La salida de texto es:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Comandos what-if

Puede usar Azure PowerShell o la API REST de Azure para la operación what-if.

### <a name="azure-powershell"></a>Azure PowerShell

Para obtener una vista previa de los cambios antes de implementar una plantilla, agregue el parámetro de modificador `-Whatif` al comando de implementación.

* `New-AzResourceGroupDeployment -Whatif` para implementaciones de grupos de recursos
* `New-AzSubscriptionDeployment -Whatif` y `New-AzDeployment -Whatif` para implementaciones de nivel de suscripción

O bien, puede usar el parámetro de modificador `-Confirm` para obtener una vista previa de los cambios y recibir un aviso para continuar con la implementación.

* `New-AzResourceGroupDeployment -Confirm` para implementaciones de grupos de recursos
* `New-AzSubscriptionDeployment -Confirm` y `New-AzDeployment -Confirm` para implementaciones de nivel de suscripción

Los comandos anteriores devuelven un resumen de texto que puede inspeccionar manualmente. Para obtener un objeto en el que pueda inspeccionar los cambios mediante programación, use:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` para implementaciones de grupos de recursos
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` o `$results = Get-AzDeploymentWhatIfResult` para implementaciones de nivel de suscripción

### <a name="azure-rest-api"></a>API REST de Azure

Para la API REST, use:

* [Implementaciones: What If](/rest/api/resources/deployments/whatif) para implementaciones de grupos de recursos
* [Implementaciones: What If en el ámbito de suscripción](/rest/api/resources/deployments/whatifatsubscriptionscope) para implementaciones de nivel de suscripción

## <a name="change-types"></a>Tipos de cambio

La operación hipotética muestra seis tipos diferentes de cambios:

- **Crear**: El recurso no existe actualmente, pero se define en la plantilla. Se creará el recurso.

- **Eliminar**: Este tipo de cambio solo se aplica cuando se usa [Modo completo](deployment-modes.md) para la implementación. El recurso existe, pero no se define en la plantilla. Con el modo completo, se eliminará el recurso. Solo los recursos que [admiten la eliminación de modo completo](complete-mode-deletion.md) se incluyen en este tipo de cambio.

- **Ignorar**: El recurso existe, pero no se define en la plantilla. No se implementará ni modificará el recurso.

- **NoChange**: El recurso existe y se define en la plantilla. El recurso se volverá a implementar, pero las propiedades del recurso no cambiarán. Este tipo de cambio se devuelve cuando [ResultFormat](#result-format) se establece en `FullResourcePayloads`, que es el valor predeterminado.

- **Modificar**: El recurso existe y se define en la plantilla. El recurso se volverá a implementar y las propiedades del recurso cambiarán. Este tipo de cambio se devuelve cuando [ResultFormat](#result-format) se establece en `FullResourcePayloads`, que es el valor predeterminado.

- **Implementación**: El recurso existe y se define en la plantilla. El recurso será reimplementado. Es posible que las propiedades del recurso cambien o no. La operación devuelve este tipo de cambio cuando no tiene información suficiente para determinar si cualquier propiedad cambiará. Solo verá esta condición cuando [ResultFormat](#result-format) se establece en `ResourceIdOnly`.

## <a name="result-format"></a>Formato de resultado

Puede controlar el nivel de detalle que se devuelve sobre los cambios de predicción. En los comandos de implementación (`New-Az*Deployment`), use el parámetro **-WhatIfResultFormat**. En los comandos de objeto de programación (`Get-Az*DeploymentWhatIf`), use el parámetro **ResultFormat**.

Establezca el parámetro de formato en **FullResourcePayloads** para obtener una lista de recursos que vayan a cambiar y detalles sobre las propiedades que cambiarán. Establezca el parámetro de formato en **ResourceIdOnly** para obtener una lista de los recursos que cambiarán. El valor predeterminado es **FullResourcePayloads**.  

Los resultados siguientes muestran los dos formatos de salida diferentes:

- Cargas de recursos completas

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Solo identificador de recurso

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Ejecutar la operación hipotética

### <a name="set-up-environment"></a>Configuración de entorno

Para ver cómo funciona, vamos a ejecutar algunas pruebas. En primer lugar, implemente una [plantilla que cree una red virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Usará esta red virtual para probar cómo se registran los cambios con what-if.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Modificación de prueba

Una vez finalizada la implementación, está listo para probar la operación hipotética. Esta vez, implemente una [plantilla que cambie la red virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Falta una de las etiquetas originales, se ha quitado una subred y el prefijo de dirección ha cambiado.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

La salida de what-if es similar a esta:

![Administrador de recursos de implementación de plantilla: salida de la operación hipotética](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

La salida de texto es:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Observe en la parte superior de la salida que los colores están definidos para indicar el tipo de cambios.

En la parte inferior de la salida, se muestra que se eliminó el propietario de la etiqueta. El prefijo de dirección cambió de 10.0.0.0/16 a 10.0.0.0/15. Se eliminó la subred denominada subnet001. Recuerde que estos cambios no se implementaron realmente. Obtendrá una vista previa de los cambios que se producirán si implementa la plantilla.

Algunas de las propiedades que se enumeran como eliminadas no cambiarán realmente. Las propiedades se pueden notificar incorrectamente como eliminadas cuando no están en la plantilla, pero se establecen de forma automática durante la implementación como valores predeterminados. Este resultado se considera "ruido" en la respuesta what-if. El recurso implementado final tendrá los valores establecidos para las propiedades. A medida que la operación what-if evolucione, estas propiedades se excluirán del resultado.

## <a name="programmatically-evaluate-what-if-results"></a>Evaluación de los resultados de what-if mediante programación

Ahora, vamos a establecer el comando en una variable para evaluar mediante programación los resultados de what-if.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Puede ver un resumen de cada cambio.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Confirmar eliminación

La operación hipotética admite el uso del [modo de implementación](deployment-modes.md). Cuando se establece en el modo completo, se eliminan los recursos que no están en la plantilla. En el ejemplo siguiente se implementa una plantilla [que no tiene recursos definidos](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) en el modo completo.

Para obtener una vista previa de los cambios antes de implementar una plantilla, use el parámetro de modificador `-Confirm` con el comando de implementación. Si los cambios son los esperados, confirme que desea que se complete la implementación.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Dado que no hay recursos definidos en la plantilla y el modo de implementación está establecido en completo, se eliminará la red virtual.

![Implementación de plantilla del Administrador de recursos modo de implementación de salida de operación hipotética completa](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

La salida de texto es:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Verá los cambios esperados y puede confirmar que desea que se ejecute la implementación.

## <a name="next-steps"></a>Pasos siguientes

- Si observa resultados incorrectos de la versión preliminar de what-if, informe de los problemas en [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Para implementar plantillas con Azure PowerShell, consulte [Implementar recursos con plantillas de Resource Manager y Azure PowerShell](deploy-powershell.md).
- Para implementar plantillas con REST, consulte [Implementación de recursos con plantillas de Resource Manager y Administrador de recursos API de REST](deploy-rest.md).
