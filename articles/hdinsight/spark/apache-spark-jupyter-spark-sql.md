---
title: 'Inicio rápido: Creación de un clúster de Apache Spark mediante una plantilla: Azure HDInsight'
description: Esta guía de inicio rápido muestra cómo usar la plantilla de Resource Manager para crear un clúster de Apache Spark en Azure HDInsight y ejecutar una consulta SQL de Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 6d590659a4ed73fa27193961721d949b555c3444
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064534"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Inicio rápido: Creación de clústeres de Apache Spark en Azure HDInsight con plantillas de Resource Manager

En este inicio rápido, se usa una plantilla de Azure Resource Manager para crear un clúster de [Apache Spark](./apache-spark-overview.md) en Azure HDInsight. Luego se crea un cuaderno de Jupyter Notebook y se usa para ejecutar consultas de Spark SQL en tablas de Apache Hive. Azure HDInsight es un servicio administrado, de espectro completo y de análisis de código abierto para empresas. El marco de Apache Spark para HDInsight permite un análisis de datos y un procesamiento de clústeres rápidos mediante el procesamiento en memoria. Jupyter Notebook permite interactuar con los datos, combinar código con texto Markdown y realizar visualizaciones simples.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-an-apache-spark-cluster"></a>Creación de un clúster de Apache Spark

### <a name="review-the-template"></a>Revisión de la plantilla

La plantilla usada en este inicio rápido forma parte de las [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json" range="1-143":::

En la plantilla se definen dos recursos de Azure:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): permite crear una cuenta de Azure Storage.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): permite crear un clúster de HDInsight.

### <a name="deploy-the-template"></a>Implementación de la plantilla

1. Seleccione el botón **Implementar en Azure** siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager.

    [![Implementación en Azure](./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Escriba o seleccione los siguientes valores:

    |Propiedad |Descripción |
    |---|---|
    |Subscription|En la lista desplegable, seleccione la suscripción de Azure que se usa para el clúster.|
    |Resource group|En la lista desplegable, seleccione el grupo de recursos existente o seleccione **Crear**.|
    |Location|El valor se rellenará automáticamente con la ubicación usada para el grupo de recursos.|
    |Cluster Name|Escriba un nombre único global. Use solo letras minúsculas y números para esta plantilla.|
    |Cluster Login User Name (Nombre de usuario de inicio de sesión del clúster)|Proporcione el nombre de usuario; el valor predeterminado es **admin**.|
    |Cluster Login Password (Contraseña de inicio de sesión del clúster)|Indique una contraseña. La contraseña debe tener un mínimo de 10 caracteres y contener al menos un dígito, una letra mayúscula y una letra minúscula, y un carácter no alfanumérico (excepto los caracteres ' " y `). |
    |Nombre de usuario de SSH|Proporcione el nombre de usuario; el valor predeterminado es **sshuser**.|
    |Contraseña de SSH|Proporcione la contraseña.|

    ![Creación de un clúster de Spark en HDInsight mediante una plantilla de Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Creación de un clúster de Spark en HDInsight mediante una plantilla de Azure Resource Manager")

1. Revise los **TÉRMINOS Y CONDICIONES**. A continuación, seleccione **Acepto los términos y condiciones indicados anteriormente** y, después, seleccione **Comprar**. Recibirá una notificación de que la implementación está en curso. Se tarda aproximadamente 20 minutos en crear un clúster.

Si surge algún problema al crear clústeres de HDInsight, podría deberse a que no tiene los permisos adecuados para hacerlo. Para más información, consulte [Requisitos de control de acceso](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Revisión de los recursos implementados

Una vez que se cree el clúster, recibirá una notificación de **Implementación correcta** con el vínculo **Ir al recurso**. La página Grupo de recursos mostrará el nuevo clúster de HDInsight y el almacenamiento predeterminado asociado con el clúster. Cada clúster depende de una cuenta de [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) o de una [cuenta de Azure Data Lake Storage](../hdinsight-hadoop-use-data-lake-store.md). Se conoce como cuenta de almacenamiento predeterminada. El clúster de HDInsight y su cuenta de almacenamiento predeterminada deben estar en la misma región de Azure. Al eliminar los clústeres no se elimina la cuenta de almacenamiento.

## <a name="create-a-jupyter-notebook"></a>Creación de un cuaderno de Jupyter

[Jupyter Notebook](https://jupyter.org/) es un entorno de cuaderno interactivo que admite varios lenguajes de programación. El cuaderno le permite interactuar con los datos, combinar código con el texto de Markdown y realizar visualizaciones simples.

1. Abra [Azure Portal](https://portal.azure.com).

2. Seleccione **Clústeres de HDInsight** y, a continuación, seleccione el clúster que creó.

    ![Apertura de un clúster de HDInsight en Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. En el portal, en la sección **Paneles de clúster**, seleccione **Jupyter Notebook**. Cuando se le solicite, escriba las credenciales de inicio de sesión del clúster.

   ![Apertura de un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Apertura de un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas")

4. Seleccione **Nuevo** > **PySpark** para crear un cuaderno.

   ![Creación de un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Creación de un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas")

   Se crea y se abre un nuevo cuaderno con el nombre Untitled(Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Ejecución de instrucciones de Apache Spark SQL

SQL (Lenguaje de consulta estructurado) es el lenguaje más común y ampliamente usado para la consulta y la transformación de datos. Spark SQL funciona como una extensión de Apache Spark para procesar datos estructurados, mediante la conocida sintaxis de SQL.

1. Compruebe que el kernel esté preparado. El kernel está preparado cuando aparece un círculo vacío junto al nombre del kernel en el cuaderno. Un círculo sólido indica que el kernel está ocupado.

    ![Estado del kernel](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Estado del kernel")

    Al iniciar el cuaderno por primera vez, el kernel realiza tareas en segundo plano. Espere a que el kernel esté preparado.

1. Pegue el código siguiente en una celda vacía y presione **MAYÚS + ENTRAR** para ejecutar el código. El comando muestra las tablas Hive del clúster:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Cuando se usa un cuaderno de Jupyter Notebook con un clúster de HDInsight, se obtiene una sesión de `spark` preestablecida que puede usar para ejecutar consultas de Hive mediante Spark SQL. `%%sql` indica a Jupyter Notebook que use la sesión `spark` preestablecida para ejecutar la consulta de Hive. La consulta recupera las 10 primeras filas de una tabla de Hive (**hivesampletable**) que se incluye de forma predeterminada en todos los clústeres de HDInsight. La primera vez que se envíe la consulta, Jupyter creará una aplicación de Spark para el cuaderno. La operación tarda unos 30 segundos en completarse. Cuando la aplicación de Spark está preparada, la consulta se ejecuta aproximadamente en un segundo y genera los resultados. El resultado tendrá una apariencia similar a la siguiente:

    ![Consulta de Apache Hive en HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Consulta de Hive en HDInsight")

    Cada vez que se ejecuta una consulta en Jupyter, el título de la ventana del explorador web muestra el estado **(Busy)** (Ocupado) junto con el título del cuaderno. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha.

1. Ejecute otra consulta para ver los datos en `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Debe actualizar la pantalla para mostrar la salida de la consulta.

    ![Salida de una consulta de Hive en HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Salida de una consulta de Hive en HDInsight")

1. En el menú **File** (Archivo) del cuaderno, seleccione **Close and Halt** (Cerrar y detener). Al cerrar el cuaderno se liberan los recursos del clúster, lo que incluye la aplicación de Spark.

## <a name="clean-up-resources"></a>Limpieza de recursos

Después de completar el inicio rápido, puede ser conveniente eliminar el clúster. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no se esté usando. Los clústeres de HDInsight se cobran aunque no se estén usando. Como en muchas ocasiones los cargos por el clúster son mucho más elevados que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no se usen.

En Azure Portal, vaya al clúster y seleccione **Eliminar**.

![Azure Portal: eliminación de un clúster de HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Eliminación de un clúster de HDInsight")

También puede seleccionar el nombre del grupo de recursos para abrir la página del grupo de recursos y, a continuación, seleccionar **Eliminar grupo de recursos**. Al eliminar el grupo de recursos, se eliminan tanto el clúster de HDInsight como la cuenta de almacenamiento predeterminada.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a crear un clúster de Apache Spark en HDInsight y a ejecutar una consulta básica de Spark SQL. Pase al siguiente tutorial, donde aprenderá a usar un clúster de HDInsight para ejecutar consultas interactivas en datos de ejemplo.

> [!div class="nextstepaction"]
> [Ejecución de consultas interactivas en Apache Spark](./apache-spark-load-data-run-query.md)
