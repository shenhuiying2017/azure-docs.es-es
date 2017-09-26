---
title: "Ejecución de consultas interactivas en un clúster de Azure HDInsight Spark | Microsoft Docs"
description: "Tutorial de inicio rápido para HDInsight Spark sobre cómo crear un clúster de Apache Spark en HDInsight."
keywords: "inicio rápido para spark,spark interactivo,consulta interactiva,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: ada1c3d1482c68834dbbf5eabbd045a7e0c01f9f
ms.contentlocale: es-es
ms.lasthandoff: 07/25/2017

---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>Ejecución de consultas interactivas en un clúster Spark de HDInsight

En este artículo, utilice un notebook de Jupyter para ejecutar consultas SQL de Spark interactivas en un clúster Spark. Notebook de Jupyter es una aplicación basada en explorador que extiende a la web la experiencia interactiva basada en la consola. Para más información, consulte [Notebook de Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html).

En este tutorial, use el kernel **PySpark** en el notebook de Jupyter para ejecutar una consulta SQL de Spark interactiva. Los notebooks de Jupyter en clústeres de HDInsight también admiten dos kernels: **PySpark3** y **Spark**. Para más información sobre los kernels y las ventanas del uso de **PySpark**, consulte [Kernels para Jupyter Notebook en clústeres Spark en Azure HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Requisitos previos

* **Clúster Spark de Azure HDInsight**. Para obtener instrucciones, consulte [Creación de un clúster de Apache Spark en Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>Creación de un notebook de Jupyter para ejecutar consultas interactivas

Para ejecutar consultas, utilizamos los datos de ejemplo que están disponibles de forma predeterminada en el almacenamiento asociado al clúster. Sin embargo, primero debe cargar esos datos en Spark como una trama de datos. Una vez que tenga la trama de datos, puede ejecutar consultas con ella usando el notebook de Jupyter. En esta sección, puede aprender a:

* Registrar un conjunto de datos de ejemplo como una trama de datos de Spark.
* Ejecutar consultas en la trama de datos.

1. Abra el [Azure Portal](https://portal.azure.com/). Si ha elegido anclar el clúster al panel, haga clic en el icono de clúster desde el panel para abrir la hoja del clúster.

    Si no ancló el clúster al panel, en el panel izquierdo, haga clic en **Clústeres de HDInsight** y luego haga clic en el clúster que ha creado.

3. En **Quick links** (Vínculos rápidos), haga clic en **Cluster dashboards** (Paneles de clúster) y después en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

   ![Abrir un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "Abrir un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas")

   > [!NOTE]
   > También puede acceder al cuaderno de Jupyter en el clúster si abre la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Cree un cuaderno. Haga clic en **Nuevo** y, luego, en **PySpark**.

   ![Crear un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Crear un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas")

   Se crea y se abre un nuevo Notebook con el nombre Untitled(Untitled.pynb).

4. Haga clic en el nombre del Notebook en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el cuaderno de Jupyter para ejecutar consultas Spark interactivas](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "Proporcionar un nombre para el cuaderno de Jupyter ejecutar consultas Spark interactivas")

5. Pegue el código siguiente en una celda vacía y presione **MAYÚS + ENTRAR** para ejecutar el código. El código importa los tipos necesarios para este escenario:

        from pyspark.sql import *
        from pyspark.sql.types import *

    Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos de Spark y Hive se crean automáticamente al ejecutar la primera celda de código.

    ![Estado de consulta Spark SQL interactiva](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Estado de consulta Spark SQL interactiva")

    Cada vez que se ejecuta una consulta interactiva en Jupyter, el título de la ventana del explorador web muestra el estado **(Busy)** (Ocupado) junto con el título del cuaderno. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha. Cuando finaliza el trabajo, cambia a un círculo vacío.

6. Antes de cargar los datos en un clúster de Spark, echemos un vistazo a una instantánea del mismo. Los datos de ejemplo utilizados en este tutorial están disponibles como un archivo CSV en todos los clústeres Spark de HDInsight en **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Los datos capturan las variaciones de temperatura de un edificio. Aquí están las primeras filas de los datos.

    ![Instantánea de los datos de consulta SQL interactiva de Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Instantánea de los datos de consultas SQL interactivas de Spark")

6. Ejecute el código siguiente para crear una trama de datos y una tabla temporal (**hvac**). En este tutorial, no creamos todas las columnas en la tabla temporal en comparación con las columnas de los datos CSV sin procesar. 

        # Create an RDD from sample data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
        
        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. Una vez creada la tabla, ejecute la consulta interactiva en los datos con el código siguiente.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Como va a usar un kernel de PySpark, ahora puede ejecutar directamente una consulta SQL interactiva en la tabla temporal **hvac** que creó con la instrucción mágica `%%sql`. Para más información sobre la instrucción mágica `%%sql`, así como otras instrucciones mágicas disponibles con el kernel de PySpark, consulte [Kernels disponibles en Jupyter Notebook con clústeres de Spark en HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   De forma predeterminada, se muestra la siguiente salida tabular.

     ![Tabla de salida de resultados de consultas Spark interactivas](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabla de salida de resultados de consultas Spark interactivas")

    También puede ver la salida en otras visualizaciones. Por ejemplo, un gráfico de área con la misma salida tendría el siguiente aspecto.

    ![Gráfico de área de resultados de consultas Spark interactivas](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Gráfico de área de resultados de consultas Spark interactivas")

9. Cuando haya terminado de ejecutar la aplicación, cierre el Notebook para liberar los recursos del clúster. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**.

## <a name="next-step"></a>Paso siguiente

En este artículo aprendió a ejecutar consultas interactivas en Spark con un notebook de Jupyter. Continúe con el siguiente artículo para ver cómo se pueden extraer los datos registrados en Spark en una herramienta de análisis de BI, como Power BI y Tableau. 

> [!div class="nextstepaction"]
>[Spark BI mediante herramientas de visualización de datos con Azure HDInsight](hdinsight-apache-spark-use-bi-tools.md)





