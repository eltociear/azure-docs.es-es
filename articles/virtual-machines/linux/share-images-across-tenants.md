---
title: Compartir imágenes de la galería entre inquilinos de Azure
description: Obtenga información sobre cómo compartir imágenes de máquina virtual entre inquilinos de Azure mediante Galerías de imágenes compartidas.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034983"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Uso compartido de imágenes de la máquina virtual de la galería entre inquilinos de Azure

Las galerías de imágenes compartidas le permiten compartir las imágenes mediante RBAC. Puede utilizar RBAC para compartir imágenes dentro de su inquilino e incluso con personas de fuera de él. Para más información sobre esta opción de uso compartido simple, consulte [Compartir la galería](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> No se puede usar el portal para implementar una VM desde una imagen en otro inquilino de Azure. Para crear una VM desde una imagen que se comparte entre los inquilinos, debe usar la CLI de Azure o [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Creación de una máquina virtual mediante la CLI de Azure

Inicie sesión en la entidad de servicio del inquilino 1 mediante el id. de la aplicación, la clave de aplicación y el identificador del inquilino 1. Puede usar `az account show --query "tenantId"` para obtener el identificador del inquilino, si es necesario.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Inicie sesión en la entidad de servicio del inquilino 2 mediante el id. de la aplicación, la clave de aplicación y el identificador del inquilino 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Cree la máquina virtual. Reemplace la información del ejemplo por la suya.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Pasos siguientes

Si experimenta algún problema, puede [solucionar problemas de galerías de imágenes compartidas](troubleshooting-shared-images.md).