---
title: Escalado del proceso del grupo de SQL de Synapse (Azure Portal)
description: El proceso del grupo de SQL de Synapse (almacenamiento de datos) se puede escalar desde Azure Portal.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f92152658b9db83740ffc2de2dc6956003849e06
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350832"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Inicio rápido: Escalado del proceso del grupo de SQL de Synapse con Azure Portal

El proceso del grupo de SQL de Synapse (almacenamiento de datos) se puede escalar desde Azure Portal. [Escale horizontalmente un proceso](sql-data-warehouse-manage-compute-overview.md) para aumentar el rendimiento, o bien revierta la escalabilidad del proceso para ahorrar costos. 

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de empezar

Puede escalar un grupo de SQL que ya tenga, o bien usar el artículo [Inicio rápido: Creación y conexión (Azure Portal)](create-data-warehouse-portal.md) para crear un grupo de SQL llamado **mySampleDataWarehouse**. En esta guía de inicio rápido se escala **mySampleDataWarehouse**.

>[!IMPORTANT] 
>El grupo de SQL debe estar en línea para que se pueda escalar. 

## <a name="scale-compute"></a>Escalado de proceso

Los recursos de proceso del grupo de SQL se pueden escalar mediante el aumento o la reducción de las unidades de almacenamiento de datos. En el inicio rápido Creación y conexión: portal (create-data-warehouse-portal.md), creó **mySampleDataWarehouse** y lo inició con 400 DWU. En los siguientes pasos se ajustan las DWU para **mySampleDataWarehouse**.

Para cambiar las unidades de almacenamiento de datos:

1. Haga clic en **Azure Synapse Analytics (formerly SQL DW)** a la izquierda de la página de Azure Portal.
2. Seleccione **mySampleDataWarehouse** en la página de **Azure Synapse Analytics (formerly SQL DW)** . Se abre el grupo de SQL.
3. Haga clic en **Escalar**.

    ![Haga clic en Escala](./media/quickstart-scale-compute-portal/click-scale.png)

2. En el panel Escalar, mueva el control deslizante hacia la izquierda o la derecha para cambiar el valor de DWU. A continuación, seleccione Escalar.

    ![Mueva el control deslizante](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca del grupo de SQL, diríjase al tutorial [Carga de datos en un grupo de SQL](load-data-from-azure-blob-storage-using-polybase.md). 
