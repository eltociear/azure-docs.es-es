---
title: 'Tamaños de las máquinas virtuales de Azure: GPU | Microsoft Docs'
description: Enumera los tamaños diferentes optimizados para GPU disponibles para las máquinas virtuales en Azure. Se proporciona información sobre el número de unidades vCPU, discos de datos y NIC, así como sobre el rendimiento de almacenamiento y el ancho de banda de red para los tamaños de esta serie.
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913589"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamaños de máquinas virtuales optimizadas para GPU

Los tamaños de máquina virtual optimizada para GPU son máquinas virtuales especializadas con GPU de NVIDIA. Estos tamaños están diseñados para cargas de trabajo de proceso intensivo, uso intensivo de gráficos y visualización. En este artículo se proporciona información sobre el número y el tipo de GPU, vCPU, discos de datos y NIC. El ancho de banda de red y el rendimiento del almacenamiento también se incluyen para cada tamaño de esta agrupación.

- Los tamaños de las series [NC](nc-series.md), [NCv2](ncv2-series.md) y [NCv3](ncv3-series.md) están optimizados para aplicaciones y algoritmos de proceso intensivo y que usan mucho la red. Algunos ejemplos son las simulaciones y las aplicaciones basadas en CUDA y en OpenCL, la inteligencia artificial y el aprendizaje profundo. La serie NCv3 se centra en las cargas de trabajo de informática de alto rendimiento e incorpora la GPU NVIDIA Tesla V100. La serie NC utiliza el procesador Intel Xeon E5-2690 v3 a 2,60 GHz v3 (Haswell), y las series NCv2 y NCv3 utilizan el procesador Intel Xeon E5-2690 v4 (Broadwell).

- Los tamaños de las series [ND](nd-series.md) y [NDv2](ndv2-series.md) se centran en escenarios de entrenamiento e inferencia para el aprendizaje profundo. Utilizan la GPU NVIDIA Tesla P40 y el procesador Intel Xeon E5-2690 v4 (Broadwell). La serie NDv2 usa el procesador Intel Xeon Platinum 8168 (Skylake).

- Los tamaños de las series [NV](nv-series.md) y [NVv3](nvv3-series.md) están optimizados y diseñados para escenarios de visualización remota, streaming, juegos, codificación y VDI mediante marcos como OpenGL y DirectX. Estas máquinas virtuales están respaldadas por la GPU NVIDIA Tesla M60.

- Los tamaños de las máquinas virtuales de la serie [NVv4](nvv4-series.md) están optimizados y diseñados para VDI y la visualización remota. Con las GPU con particiones, NVv4 ofrece el tamaño adecuado para las cargas de trabajo que requieren recursos de GPU más pequeños. Estas máquinas virtuales usan la GPU AMD Radeon Instinct MI25. Las máquinas virtuales NVv4 actualmente solo admiten el sistema operativo invitado Windows.

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos y controladores compatibles

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure, deben instalarse controladores de GPU de NVIDIA.

La [extensión de controlador de GPU de NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) instala los controladores CUDA de NVIDIA o GRID adecuados en una máquina virtual de la serie N. Instale o administre la extensión mediante Azure Portal o con herramientas como las plantillas de Azure PowerShell o Azure Resource Manager. Consulte la [documentación de la extensión de controlador de GPU de NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para los sistemas operativos compatibles y los pasos de implementación. Para una información general sobre las extensiones de máquina virtual, consulte [Características y extensiones de las máquinas virtuales de Azure](/azure/virtual-machines/extensions/overview).

Si decide instalar manualmente los controladores de GPU de NVIDIA, consulte el artículo sobre la [instalación de controladores de GPU de la serie N para Windows](/azure/virtual-machines/windows/n-series-driver-setup) o el artículo sobre la [instalación de controladores de GPU de la serie N para Linux](/azure/virtual-machines/linux/n-series-driver-setup) para obtener información sobre los sistemas operativos compatibles, los controladores, la instalación y los pasos de verificación.

## <a name="deployment-considerations"></a>Consideraciones de la implementación

- Para ver la disponibilidad de máquinas virtuales de la serie N, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/).

- Las máquinas virtuales de la serie N solo se pueden implementar en el modelo de implementación de Resource Manager.

- Las máquinas virtuales de serie N difieren en el tipo de Azure Storage que admiten en sus discos. Las máquinas virtuales NC y NV solo admiten discos de máquina virtual respaldados por Disk Storage (HDD) estándar. Las máquinas virtuales NCv2, NCv3, ND, NDv2 y NVv2 solo admiten discos de máquina virtual respaldados por Disk Storage (SSD) Premium.

- Si desea implementar más de un pequeño número de máquinas virtuales de la serie N, considere la posibilidad de usar una suscripción de pago por uso u otras opciones de compra. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), solo puede usar un número limitado de núcleos de proceso de Azure.

- Es posible que necesite aumentar la cuota de núcleos (por región) de la suscripción de Azure y la cuota independiente para los núcleos NC, NCv2, NCv3, ND, NDv2, NV o NVv2. Para solicitar un aumento de cuota, [abra una solicitud de soporte técnico al cliente en línea](../azure-portal/supportability/how-to-create-azure-support-request.md) sin cargo alguno. Los límites predeterminados pueden variar según la categoría de suscripción.

## <a name="other-sizes"></a>Otros tamaños

- [Uso general](sizes-general.md)
- [Proceso optimizado](sizes-compute.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [Generaciones anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.
