---
title: Uso del portal para implementar máquinas virtuales de Azure Spot
description: Aprenda a usar Azure PowerShell para implementar máquinas virtuales de Spot con el fin de ahorrar costos.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 045cec080b9b1b8f2e4cb589b053c421897db5be
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547379"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Implementación de máquinas virtuales de Spot mediante Azure Portal

El uso de [máquinas virtuales de Spot](spot-vms.md) permite aprovechar las ventajas de nuestra capacidad no utilizada con un importante ahorro en los costos. Siempre que Azure necesite recuperar la capacidad, su infraestructura expulsará las máquinas virtuales de Spot. Por lo tanto, estas son excelentes para cargas de trabajo que soportan interrupciones, como los trabajos de procesamiento por lotes, los entornos de desarrollo y pruebas, las grandes cargas de trabajo de proceso, etc.

Los precios de las máquinas virtuales de Spot varían en función de la región y la SKU. Para más información, consulte precios de las máquinas virtuales para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) y [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para más información sobre la configuración del precio máximo, consulte [Máquinas virtuales de Spot - Precios](spot-vms.md#pricing).

Puede establecer el precio máximo por hora que esté dispuesto por la máquina virtual. El precio máximo de una máquina virtual de Spot se puede establecer en dólares estadounidenses (USD), con un máximo de 5 decimales. Por ejemplo, el valor `0.05701` correspondería a un precio máximo de 0,05701 USD por hora. Si establece el precio máximo en `-1`, la máquina virtual no se expulsará por precio. El precio de la máquina virtual será el actual de Spot o el de una máquina virtual estándar, el menor de los dos, siempre que haya capacidad y cuota disponibles.


## <a name="create-the-vm"></a>Creación de la máquina virtual

El proceso para crear una máquina virtual de Spot es el mismo que el que se detalla en el [inicio rápido](quick-create-portal.md). Al implementar una máquina virtual, puede elegir usar una instancia de Azure Spot.


En la pestaña **Básico**, en la sección **Detalles de instancia**, el valor predeterminado para el uso de una instancia de Azure Spot es **No**.

![Captura de pantalla para elegir No, no usar una instancia de Azure Spot](media/spot-portal/no.png)

Si selecciona **Sí**, la sección se expande y podrá elegir [tipo y directiva de expulsión](spot-vms.md#eviction-policy). 

![Captura de pantalla para elegir Sí, usar una instancia de Azure Spot](media/spot-portal/yes.png)


## <a name="next-steps"></a>Pasos siguientes

También puede crear máquinas virtuales de Spot con [PowerShell](spot-powershell.md).