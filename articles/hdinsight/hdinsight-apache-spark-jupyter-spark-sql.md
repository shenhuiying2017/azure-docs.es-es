---
title: "Introducción: Apache Spark y consultas Spark SQL interactivas (Azure HDInsight) | Microsoft Docs"
description: "Tutorial de inicio rápido para HDInsight Spark sobre cómo crear un clúster de Apache Spark en HDInsight y ejecutar consultas interactivas mediante cuadernos de Jupyter."
keywords: "inicio rápido para spark,spark interactivo,consulta interactiva,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 0c59792423bfe2848ab5773746db466890228ddc
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---
# <a name="get-started-create-an-apache-spark-cluster-in-hdinsight-and-run-interactive-spark-sql-queries"></a>Introducción: creación de un clúster de Apache Spark en HDInsight y ejecución de consultas Spark SQL interactivas

Aprenda a crear un clúster de [Apache Spark](hdinsight-apache-spark-overview.md) en HDInsight y a ejecutar consultas Spark SQL interactivas mediante cuadernos de [Jupyter](https://jupyter.org).

   ![Diagrama de inicio rápido donde se describen los pasos para crear un clúster de Apache Spark en Azure HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Inicio rápido para Spark mediante Apache Spark en HDInsight. Pasos que ilustran cómo crear un clúster y ejecutar una consulta Spark interactiva")

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte la página [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Creación de un clúster de HDInsight Spark

En esta sección, creará un clúster de HDInsight Spark mediante una [plantilla de Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Para conocer otros métodos de creación de clústeres, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Escriba los siguientes valores:

    ![Creación de un clúster de HDInsight Spark mediante una plantilla de Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Creación de un clúster de HDInsight Spark mediante una plantilla de Azure Resource Manager")

    * **Suscripción**: seleccione su suscripción de Azure para este clúster.
    * **Grupo de recursos**: cree un grupo de recursos o seleccione uno existente. El grupo de recursos se usa para administrar los recursos de Azure para sus proyectos.
    * **Ubicación**: seleccione una ubicación para el grupo de recursos.  Esta ubicación también se utiliza para el almacenamiento de clústeres predeterminado y el clúster de HDInsight.
    * **ClusterName**: escriba un nombre para el clúster de Hadoop que va a crear.
    * **Versión de Spark**: seleccione la versión de Spark que quiere instalar en el clúster.
    * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es admin.
    * **Nombre de usuario y contraseña de SSH**.

   Anote estos valores.  Los necesitará más adelante en el tutorial.

3. Seleccione **I agree to the terms and conditions stated above** (Acepto los términos y condiciones indicados anteriormente) y **Anclar al panel** y haga clic en **Purchase** (Comprar). Puede ver un icono nuevo llamado Envío de implementación para la implementación de plantilla. La creación del clúster tarda aproximadamente 20 minutos.

> [!NOTE]
> En este artículo se crea un clúster de Spark que usa [blobs de Azure Storage como almacenamiento](hdinsight-hadoop-use-blob-storage.md). También puede crear un clúster de Spark que use [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) como almacenamiento adicional, junto con blobs de Azure Storage como almacenamiento predeterminado. Consulte [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-an-interactive-spark-sql-query"></a>Ejecución de una consulta Spark SQL interactiva

En esta sección, usará Jupyter Notebook para ejecutar consultas Spark SQL interactivas en el clúster de Spark creado anteriormente. Los clústeres de Spark en HDInsight proporcionan tres kernels que se pueden usar con Jupyter Notebook. Dichos componentes son:

* **PySpark** (para aplicaciones escritas con Python)
* **PySpark3** (para aplicaciones escritas en Python3)
* **Spark** (para aplicaciones escritas con Scala)

En este artículo, usará el kernel de **PySpark** en el cuaderno desde donde ejecutará la consulta SQL Spark interactiva. Para más información sobre los kernels, consulte [Uso de kernels de Jupyter Notebook con clústeres de Apache Spark en HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md). Algunas de las principales ventajas de usar el kernel de PySpark son:

* Los contextos de Spark y Hive se establecen automáticamente.
* Use instrucciones mágicas de celda, como `%%sql`, para ejecutar consultas SQL o Hive interactivas, sin fragmentos de código anteriores.
* La salida de las consultas interactivas se visualiza automáticamente.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Creación de un cuaderno de Jupyter Notebook con el kernel de PySpark

1. Abra el [Azure Portal](https://portal.azure.com/).

2. Si ha elegido anclar el clúster al panel, haga clic en el icono de clúster desde el panel para abrir la hoja del clúster.

    Si no ancló el clúster al panel, en el panel izquierdo, haga clic en **Clústeres de HDInsight** y luego haga clic en el clúster que ha creado.

3. En **Quick links** (Vínculos rápidos), haga clic en **Cluster dashboards** (Paneles de clúster) y después en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

   ![Abrir un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Abrir un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas")

   > [!NOTE]
   > También puede acceder al cuaderno de Jupyter en el clúster si abre la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Cree un cuaderno. Haga clic en **Nuevo** y, luego, en **PySpark**.

   ![Crear un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Crear un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas")

   Se crea y se abre un nuevo Notebook con el nombre Untitled(Untitled.pynb).

4. Haga clic en el nombre del Notebook en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el cuaderno de Jupyter para ejecutar consultas Spark interactivas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Proporcionar un nombre para el cuaderno de Jupyter ejecutar consultas Spark interactivas")

5. Pegue el código siguiente en una celda vacía y presione **MAYÚS + ENTRAR** para ejecutar el código. El código importa los tipos necesarios para este escenario:

        from pyspark.sql.types import *

    Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos de Spark y Hive se crean automáticamente al ejecutar la primera celda de código.

    ![Estado de consulta Spark SQL interactiva](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-interactive-spark-query-status.png "Estado de consulta Spark SQL interactiva")

    Cada vez que se ejecuta una consulta interactiva en Jupyter, el título de la ventana del explorador web muestra el estado **(Busy)** (Ocupado) junto con el título del cuaderno. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha. Cuando finaliza el trabajo, cambia a un círculo vacío.

6. Registre un conjunto de datos de ejemplo como una tabla temporal (**hvac**) mediante la ejecución del siguiente código.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data frame as a table to run queries against
        hvacdf.registerTempTable("hvac")

    Los clústeres de Spark en HDInsight incluyen un archivo de datos de ejemplo, **hvac.csv**, en **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

7. Para ejecutar la consulta interactiva en los datos, use el código siguiente.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Como va a usar un kernel de PySpark, ahora puede ejecutar directamente una consulta SQL interactiva en la tabla temporal **hvac** que creó con la instrucción mágica `%%sql`. Para más información sobre la instrucción mágica `%%sql`, así como otras instrucciones mágicas disponibles con el kernel de PySpark, consulte [Kernels disponibles en Jupyter Notebook con clústeres de Spark en HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   De forma predeterminada, se muestra la siguiente salida tabular.

     ![Tabla de salida de resultados de consultas Spark interactivas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result.png "Tabla de salida de resultados de consultas Spark interactivas")

    También puede ver la salida en otras visualizaciones. Por ejemplo, un gráfico de área con la misma salida tendría el siguiente aspecto.

    ![Gráfico de área de resultados de consultas Spark interactivas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result-area-chart.png "Gráfico de área de resultados de consultas Spark interactivas")

9. Cuando haya terminado de ejecutar la aplicación, cierre el Notebook para liberar los recursos del clúster. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**.

## <a name="delete-the-cluster"></a>Eliminación del clúster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot-access-control"></a>Solución de problemas de control de acceso

Si experimenta un problema con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="see-also"></a>Consulte también
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Análisis de datos de telemetría de Application Insights con Spark en HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

