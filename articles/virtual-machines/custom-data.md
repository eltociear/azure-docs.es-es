---
title: Datos personalizados y Azure Virtual Machines
description: Detalles sobre cómo usar datos personalizados y Cloud-Init en Azure Virtual Machines
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2020
ms.locfileid: "80089196"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Datos personalizados y Cloud-Init en Azure Virtual Machines

## <a name="what-is-custom-data"></a>¿Qué son los datos personalizados?

Los clientes suelen preguntar cómo pueden insertar un script u otros metadatos en una máquina virtual de Microsoft Azure en tiempo de aprovisionamiento.  En otras nubes, este concepto suele conocerse como datos de usuario.  En Microsoft Azure, tenemos una característica similar llamada datos personalizados. 

Los datos personalizados solo se ponen a disposición de la máquina virtual durante la primera configuración de arranque/inicial. Llamamos a esto "aprovisionamiento". El aprovisionamiento es el proceso donde los parámetros de creación de VM (por ejemplo, nombre de host, nombre de usuario, contraseña, certificados, datos personalizados, claves, etc.) se ponen a disposición de la máquina virtual y un agente de aprovisionamiento los procesa, como el [agente Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) y [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Pasar datos personalizados a la máquina virtual
Para usar datos personalizados, primero debe codificar como Base64 el contenido antes de pasarlo a la API, a menos que use una herramienta de la CLI que realice la conversión automáticamente, como la CLI de AZ. El tamaño no puede ser superior a 64 KB.

En la CLI, puede pasar sus datos personalizados como un archivo, convirtiéndose a base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

En Azure Resource Manager (ARM), hay una [función base64](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>Procesamiento de datos personalizados
Los agentes de aprovisionamiento instalados en las máquinas virtuales controlan la interacción con la plataforma y su inclusión en el sistema de archivos. 

### <a name="windows"></a>Windows
Los datos personalizados se incluyen en *%SYSTEMDRIVE%\AzureData\CustomData.bin* como archivo binario, pero no se procesan. Si desea procesar este archivo, deberá crear una imagen personalizada y escribir código para procesar CustomData.bin.

### <a name="linux"></a>Linux  
En los sistemas operativos Linux, los datos personales se pasan a la máquina virtual a través del archivo ovf-env.xml, que se copian al directorio */var/lib/waagent* durante el aprovisionamiento.  Las versiones más recientes del agente Linux de Microsoft Azure también copiarán los datos codificados en base64 en */var/lib/waagent/CustomData* también para mayor comodidad.

Azure admite actualmente dos agentes de aprovisionamiento:
* Agente Linux: de forma predeterminada, el agente no procesará datos personalizados, deberá crear una imagen personalizada con esta opción habilitada. La configuración pertinente, según la  [documentación](https://github.com/Azure/WALinuxAgent#configuration), es:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Al habilitarse los datos personalizados y ejecutarse un script, se retrasará la máquina virtual que notifica que está listo o que el aprovisionamiento se ha realizado correctamente hasta completarse el script. Si el script supera el tiempo permitido total de aprovisionamiento de máquinas virtuales de 40 minutos, se producirá un error en la creación de VM. Tenga en cuenta que si el script no se puede ejecutar o se producen errores durante la ejecución, no se considera un error de aprovisionamiento fatal. Tendrá que crear una ruta de acceso de notificación que le alerte del estado de finalización del script.

Para solucionar problemas de ejecución de datos personalizados, revise */var/log/waagent.log*

* cloud-init: de forma predeterminada, procesará los datos personalizados de forma predeterminada. cloud-init acepta [varios formatos](https://cloudinit.readthedocs.io/en/latest/topics/format.html) de datos personalizados, como la configuración cloud-init, scripts, etc. Similar al agente Linux, cuando cloud-init procesa los datos personalizados. Si se producen errores durante la ejecución del procesamiento de configuración o de scripts, no se considera un error de aprovisionamiento fatal. Tendrá que crear una ruta de acceso de notificación que le alerte del estado de finalización del script. Sin embargo, a diferencia del agente Linux, cloud-init no espera a que se completen las configuraciones de datos personalizadas del usuario antes de informar a la plataforma de que la máquina virtual está lista. Para obtener más información sobre cloud-init en Azure, revise la [documentación](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Para solucionar problemas de ejecución de datos personalizados, revise la [documentación](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init) de solución de problemas.


## <a name="faq"></a>Preguntas más frecuentes
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>¿Se pueden actualizar los datos personalizados una vez creada la máquina virtual?
En el caso de las máquinas virtuales, los datos personalizados en el modelo de máquina virtual no se pueden actualizar, pero, en el caso de VMSS, puede actualizar los datos personalizados de VMSS a través de la API de REST (esto no es aplicable a los clientes de la CLI de AZ o PS). Al actualizar los datos personalizados en el modelo de VMSS:
* Las instancias existentes en VMSS no obtendrán los datos personalizados actualizados, solo hasta que se restablezcan.
* Las instancias existentes en VMSS actualizadas no obtendrán los datos personalizados actualizados.
* Las nuevas instancias recibirán los nuevos datos personalizados.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>¿Se pueden incluir valores confidenciales en los datos personalizados?
Se recomienda **no** almacenar los datos confidenciales en los datos personalizados. Para obtener más información, consulte [Procedimientos recomendados de Azure Security y cifrado](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>¿Están disponibles los datos personalizados en IMDS?
No, esta característica no está disponible actualmente.
