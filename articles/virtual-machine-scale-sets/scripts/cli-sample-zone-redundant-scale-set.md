---
title: 'Ejemplos de la CLI de Azure: Conjunto de escalado con redundancia de zona'
description: Este script crea un conjunto de escalado de máquinas virtuales de Azure que ejecutan Ubuntu en varias zonas de disponibilidad.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 80b173daf2101180e5d78de625b914bd8396b7af
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010365"
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-azure-cli"></a>Creación de un conjunto de escalado de máquinas virtuales con redundancia de zona con la CLI de Azure
Este script crea un conjunto de escalado de máquinas virtuales que ejecutan Ubuntu en varias zonas de disponibilidad. Después de ejecutar el script, puede acceder a la máquina virtual a través de RDP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Ejecute el siguiente comando para quitar el grupo de recursos, el conjunto de escalado y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos para crear un grupo de recursos, un conjunto de escalado de máquinas virtuales y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/ad/group) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az vmss create](/cli/azure/vmss) | Crea el conjunto de escalado de máquinas virtuales y lo conecta a la red virtual, la subred y el grupo de seguridad de red. También se crea un equilibrador de carga para distribuir el tráfico a varias instancias de máquina virtual. Este comando también especifica la imagen de máquina virtual que se usará y las credenciales administrativas.  |
| [az group delete](/cli/azure/ad/group) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Se pueden encontrar otros ejemplos de scripts de la CLI de Azure para conjuntos de escalado de máquinas virtuales en la [documentación de conjuntos de escalado de máquinas virtuales de Azure](../cli-samples.md).
