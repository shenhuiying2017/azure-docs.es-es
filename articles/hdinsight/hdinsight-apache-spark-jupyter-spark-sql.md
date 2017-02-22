---
title: "Introducción al clúster de Apache Spark en Azure HDInsight | Microsoft Docs"
description: "Instrucciones paso a paso sobre cómo crear rápidamente un clúster Apache Spark en HDInsight y luego usar Spark SQL en cuadernos de Jupyter para ejecutar consultas interactivas."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a3bdeb6fea306babc9358134c37044843b9bdd1c
ms.openlocfilehash: d8d9c5111a19bb165c25d2796d6b6e933d75042a


---
# <a name="get-started-create-apache-spark-cluster-in-azure-hdinsight-and-run-interactive-queries-using-spark-sql"></a>Introducción: creación de un clúster de Apache Spark en Azure HDInsight y ejecución de consultas interactivas mediante Spark SQL

Aprenda a crear un clúster de [Apache Spark](hdinsight-apache-spark-overview.md) en HDInsight y después usar [Jupyter Notebook](https://jupyter.org) para ejecutar consultas interactivas de Spark SQL en el clúster Spark.

   ![Introducción al uso de Apache Spark en HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Tutorial de introducción al uso de Apache Spark en HDInsight. Pasos que se muestran: crear una cuenta de almacenamiento, crear un clúster, ejecutar instrucciones Spark SQL")

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte la página [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free).

* **Un cliente de Shell seguro**: sistemas Linux, Unix y OS X ofrecen un cliente SSH mediante el comando `ssh`. Para los clientes Windows, consulte [Uso de SSH con Hadoop en HDInsight basado en Linux desde Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md); para Linux, Unix u OS X, consulte [Uso SSH con HDInsight (Hadoop) desde Windows, Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!NOTE]
> Este artículo utiliza una plantilla de Azure Resource Manager para crear un clúster Spark que usa [Azure Storage Blobs como almacenamiento de clúster](hdinsight-hadoop-use-blob-storage.md). También puede crear un clúster Spark que usa el [Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md) como un almacenamiento adicional, además de Blobs de Almacenamiento de Azure como el almacenamiento predeterminado. Consulte [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-spark-cluster"></a>Creación de un clúster de Spark
En esta sección, creará un clúster de Spark en HDInsight usando una [plantilla de Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md). Para conocer otros métodos de creación de clústeres, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Escriba los siguientes valores:

    ![Creación de un clúster de Spark en HDInsight con una plantilla de Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Creación de un clúster de Spark en HDInsight con una plantilla de Azure Resource Manager")

   * **Suscripción**: seleccione su suscripción de Azure para este clúster.
   * **Grupo de recursos**: cree un grupo de recursos o seleccione uno existente. El grupo de recursos se usa para administrar los recursos de Azure para sus proyectos.
   * **Ubicación**: seleccione una ubicación para el grupo de recursos.  Esta ubicación también se utiliza para el almacenamiento de clústeres predeterminado y el clúster de HDInsight.
   * **ClusterName**: escriba un nombre para el clúster de Hadoop que va a crear.
   * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es admin.
   * **Nombre de usuario y contraseña de SSH**.

   Escriba estos valores.  Los necesitará más adelante en el tutorial.

3. Seleccione **I agree to the terms and conditions stated above** (Acepto los términos y condiciones indicados anteriormente) y **Anclar al panel** y haga clic en **Purchase** (Comprar). Puede ver un icono nuevo llamado Envío de implementación para la implementación de plantilla. Tarda aproximadamente 20 minutos en crear el clúster.

## <a name="run-spark-sql-queries-using-a-jupyter-notebook"></a>Ejecución de consultas SQL Spark mediante un cuaderno de Jupyter Notebook
En esta sección, utilice un cuaderno de Jupyter Notebook, para ejecutar consultas Spark SQL en un clúster Spark. Los clústeres de HDInsight Spark proporcionan dos kernels que se pueden utilizar con el cuaderno de Jupyter Notebook. Dichos componentes son:

* **PySpark** (para aplicaciones escritas con Python)
* **Spark** (para aplicaciones escritas con Scala)

En este artículo, se usa el kernel de PySpark. Para más información acerca de los dos kernels, consulte [Kernels disponibles para cuadernos de Jupyter con clústeres de Apache Spark en HDInsight Linux](hdinsight-apache-spark-jupyter-notebook-kernels.md). Algunas de las principales ventajas de usar el kernel de PySpark son:

* Los contextos de Spark y Hive se establecen automáticamente.
* Use instrucciones mágicas de celda, como `%%sql`, para ejecutar directamente consultas de SQL o Hive, sin fragmentos de código anteriores.
* Se visualiza automáticamente el resultado de las consultas de SQL o Hive.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Creación de un cuaderno de Jupyter Notebook con el kernel de PySpark

1. Abra el [Azure Portal](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **Grupos de recursos**.
3. Haga clic en el grupo de recursos creado en la última sección. Puede usar la función de búsqueda si hay demasiados grupos de recursos. Puede ver dos recursos en el grupo, el clúster de HDInsight y la cuenta de almacenamiento predeterminada.
4. Haga clic en el clúster para abrirlo.

2. En **Quick links** (Vínculos rápidos), haga clic en **Cluster dashboards** (Paneles de clúster) y después en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

   ![Paneles de clúster de HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-azure-portal-cluster-dashboards.png "Paneles de clúster de HDInsight")

   > [!NOTE]
   > También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Cree un nuevo notebook. Haga clic en **Nuevo** y, luego, en **PySpark**.

   ![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Crear un nuevo cuaderno de Jupyter")

   Se crea y se abre un nuevo Notebook con el nombre Untitled(Untitled.pynb).

4. Haga clic en el nombre del Notebook en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el cuaderno")
5. Pegue el código siguiente en una celda vacía y presione **MAYÚS + ENTRAR** para ejecutar el código. El código importa los tipos necesarios para este escenario:

        from pyspark.sql.types import *

    Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos Spark y Hive se crearán automáticamente al ejecutar la primera celda de código.

    ![Estado de un trabajo de cuaderno de Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Estado de un trabajo de cuaderno de Jupyter")

    Cada vez que se ejecuta un trabajo en Jupyter, el título de la ventana del explorador web mostrará el estado **(Busy)** (Ocupado) junto con el título del cuaderno. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha. Una vez completado el trabajo, cambiará a un círculo hueco.

6. Ejecute el código siguiente para registrar algunos datos de ejemplo en una tabla temporal denominada **hvac**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

    Los clústeres de Spark en HDInsight incluyen un archivo de datos de ejemplo, **hvac.csv**, en **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

7. Ejecute el código siguiente para consultar los datos:

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Al usar un kernel de PySpark, puede ejecutar directamente una consulta SQL en la tabla temporal **hvac** que acaba de crear con la instrucción mágica `%%sql`. Para obtener más información sobre la función mágica `%%sql` , así como otras función mágicas disponibles con el kernel de PySpark, vea [Kernels disponibles para cuadernos de Jupyter con clústeres Spark en HDInsight (Linux)](hdinsight-apache-spark-jupyter-notebook-kernels.md#choose-between-the-kernels).

   De forma predeterminada, se muestra la siguiente salida tabular.

     ![Salida de tabla del resultado de la consulta](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Salida de tabla del resultado de la consulta")

    También puede ver la salida en otras visualizaciones. Por ejemplo, un gráfico de área con la misma salida tendría el siguiente aspecto.

    ![Gráfico de área del resultado de la consulta](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Gráfico de área del resultado de la consulta")

9. Cuando haya terminado de ejecutar la aplicación, debe cerrar el Notebook para liberar recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. De esta manera se apagará y se cerrará el cuaderno.

## <a name="delete-the-cluster"></a>Eliminación del clúster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="see-also"></a>Otras referencias
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
* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

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



<!--HONumber=Feb17_HO1-->


