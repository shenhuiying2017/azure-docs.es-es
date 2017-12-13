---
title: "Ejecución de consultas interactivas en un clúster de Azure HDInsight Spark | Microsoft Docs"
description: "Tutorial de inicio rápido para HDInsight Spark sobre cómo crear un clúster de Apache Spark en HDInsight."
keywords: "inicio rápido para spark,spark interactivo,consulta interactiva,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 78ab44a7afa6523e1e9e4082b3f45b1a28affe77
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>Ejecución de consultas interactivas en clústeres de Spark en HDInsight

Aprenda a usar Jupyter Notebook para ejecutar consultas SQL de Spark interactivas en un clúster de Spark. 

[Jupyter Notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) es una aplicación basada en explorador que extiende a la web la experiencia interactiva basada en la consola. Spark en HDInsight también incluye [Zeppelin Notebook](apache-spark-zeppelin-notebook.md). En este tutorial, se usa Jupyter Notebook.

Los cuadernos de Jupyter Notebook en clústeres de HDInsight también admiten tres kernels: **PySpark**, **PySpark3** y **Spark**. En este tutorial, se usa el kernel **PySpark**. Para más información sobre los kernels y las ventanas del uso de **PySpark**, consulte [Kernels para Jupyter Notebook en clústeres Spark en Azure HDInsight](apache-spark-jupyter-notebook-kernels.md). Para usar Zeppelin Notebook, consulte [Uso de cuadernos de Zeppelin con un clúster Apache Spark en Azure HDInsight](./apache-spark-zeppelin-notebook.md).

En este tutorial, se consultan los datos en un archivo csv. Primero debe cargar esos datos en Spark como trama de datos. A continuación, puede ejecutar consultas en la trama de datos mediante Jupyter Notebook. 

## <a name="prerequisites"></a>Requisitos previos

* **Clúster Spark de Azure HDInsight**. Para las instrucciones, consulte [Creación de un clúster de Apache Spark en Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Cuaderno de Jupyter Notebook con PySpark**. Para las instrucciones, consulte [Creación de un cuaderno de Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

## <a name="create-a-dataframe-from-a-csv-file"></a>Creación de una trama de datos a partir de un archivo csv

Con SQLContext, las aplicaciones pueden crear tramas de datos a partir de un RDD existente, una tabla de Hive u orígenes de datos. 

**Creación de una trama de datos a partir de un archivo csv**

1. Cree un cuaderno de Jupyter Notebook con PySpark si no tiene uno. Para las instrucciones, consulte [Creación de un cuaderno de Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Pegue el código siguiente en una celda vacía del cuaderno y presione **MAYÚS + ENTRAR** para ejecutarlo. El código importa los tipos necesarios para este escenario:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    Al crear un cuaderno con el kernel de PySpark, los contextos de Spark y Hive se crean automáticamente al ejecutar la primera celda de código. No es necesario crear contextos explícitamente.

    Al ejecutar una consulta interactiva en Jupyter, la ventana del explorador web o la leyenda de la pestaña muestran el estado **(Busy)** [(Ocupado)] junto con el título del cuaderno. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha. Cuando finaliza el trabajo, cambia a un círculo vacío.

    ![Estado de consulta Spark SQL interactiva](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Estado de consulta Spark SQL interactiva")

3. Ejecute el siguiente código para crear una trama de datos y una tabla temporal (**hvac**) mediante la ejecución del siguiente código, que no extrae todas las columnas disponibles en el archivo CSV. 

    ```PySpark
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
    ```
    La captura de pantalla siguiente muestra una instantánea del archivo HVAC.csv. El archivo csv incluye todos los clústeres de HDInsigt Spark. Los datos capturan las variaciones de temperatura de un edificio.

    ![Instantánea de los datos de consulta SQL interactiva de Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Instantánea de los datos de consultas SQL interactivas de Spark")

## <a name="run-queries-on-the-dataframe"></a>Ejecución de consultas en la trama de datos

Una vez creada la tabla, puede ejecutar una consulta interactiva en los datos.

**Ejecución de una consulta**

1. Ejecute el siguiente código en una celda vacía del cuaderno:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Como con el cuaderno se usa el kernel de PySpark, ahora puede ejecutar directamente una consulta SQL interactiva en la tabla temporal **hvac** que creó con la instrucción mágica `%%sql`. Para más información sobre la instrucción mágica `%%sql`, así como otras instrucciones mágicas disponibles con el kernel de PySpark, consulte [Kernels disponibles en Jupyter Notebook con clústeres de Spark en HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   De forma predeterminada, se muestra la siguiente salida tabular.

     ![Tabla de salida de resultados de consultas Spark interactivas](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabla de salida de resultados de consultas Spark interactivas")

3. También puede ver la salida en otras visualizaciones. Para ver un gráfico de áreas para la misma salida, seleccione **Área** y establezca otros valores tal como se muestra.

    ![Gráfico de área de resultados de consultas Spark interactivas](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Gráfico de área de resultados de consultas Spark interactivas")

10. En el menú **Archivo** del bloc de notas, haga clic en **Guardar y punto de control**. 

11. Si ahora va a empezar con el [siguiente tutorial](apache-spark-use-bi-tools.md), deje el bloc de notas abierto. Si no es así, cierre el bloc de notas para liberar los recursos de clúster: desde el menú **Archivo** del bloc de notas, haga clic en **Cerrar y detener**.

## <a name="next-step"></a>Paso siguiente

En este artículo aprendió a ejecutar consultas interactivas en Spark con un cuaderno de Jupyter Notebook. Continúe con el siguiente artículo para ver cómo se pueden extraer los datos registrados en Spark en una herramienta de análisis de BI, como Power BI y Tableau. 

> [!div class="nextstepaction"]
>[Spark BI mediante herramientas de visualización de datos con Azure HDInsight](apache-spark-use-bi-tools.md)




