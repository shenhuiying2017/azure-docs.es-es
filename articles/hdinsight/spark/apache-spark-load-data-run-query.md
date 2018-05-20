---
title: 'Tutorial: Carga de datos y ejecución de consultas en un clúster de Apache Spark en Azure HDInsight | Microsoft Docs'
description: Aprenda a cargar datos y a ejecutar consultas interactivas en clústeres de Spark en Azure HDInsight.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.author: jgao
ms.date: 05/07/2018
ms.openlocfilehash: 63a876dc148129cd2a3eb93ed7ab6baf06a07c62
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Tutorial: Carga de datos y ejecución de consultas en un clúster de Apache Spark en Azure HDInsight

En este tutorial, aprenderá a crear una trama de datos desde un archivo csv y a ejecutar consultas de Spark SQL interactivas en un clúster de Apache Spark en Azure HDInsight. En Spark, una trama de datos es una colección distribuida de datos que se organizan en columnas con nombre. Una trama de datos es conceptualmente equivalente a una tabla de una base de datos relacional o a una trama de datos de R/Python.
 
En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Crear una trama de datos a partir de un archivo csv
> * Ejecutar consultas en la trama de datos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* Complete [Creación de un clúster de Apache Spark en Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Creación de una trama de datos a partir de un archivo csv

Las aplicaciones pueden crear tramas de datos a partir de un RDD (Resilient Distributed Dataset) existente, de una tabla de Hive o de orígenes de datos que usan el objeto SQLContext. La captura de pantalla siguiente muestra una instantánea del archivo HVAC.csv que se usa en este tutorial. El archivo csv incluye todos los clústeres de HDInsight Spark. Los datos capturan las variaciones de temperatura de varios edificios.
    
![Instantánea de los datos de consulta SQL interactiva de Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Instantánea de los datos de consultas SQL interactivas de Spark")


1. Abra la instancia de Jupyter Notebook que creó en la sección de requisitos previos.
2. Pegue el código siguiente en una celda vacía del cuaderno y presione **MAYÚS + ENTRAR** para ejecutarlo. El código importa los tipos necesarios para este escenario:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Al ejecutar una consulta interactiva en Jupyter, la ventana del explorador web o la leyenda de la pestaña muestran el estado **(Busy)** [(Ocupado)] junto con el título del cuaderno. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha. Cuando finaliza el trabajo, cambia a un círculo vacío.

    ![Estado de consulta Spark SQL interactiva](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Estado de consulta Spark SQL interactiva")

3. Ejecute el código siguiente para crear una trama de datos y una tabla temporal (**hvac**). El código no extrae todas las columnas disponibles en el archivo CSV. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for the data
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

    > [!NOTE]
    > Si se usa el kernel de PySpark para crear un cuaderno, los contextos de SQL se crean automáticamente al ejecutar la primera celda de código. No es necesario crear contextos explícitamente.


## <a name="run-queries-on-the-dataframe"></a>Ejecución de consultas en la trama de datos

Una vez creada la tabla, puede ejecutar una consulta interactiva en los datos.

1. Ejecute el siguiente código en una celda vacía del cuaderno:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Como el kernel de PySpark se usa en el cuaderno, se puede ejecutar directamente una consulta SQL interactiva en la tabla temporal **hvac**.

   Se muestra la siguiente salida tabular.

     ![Tabla de salida de resultados de consultas Spark interactivas](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabla de salida de resultados de consultas Spark interactivas")

3. También puede ver la salida en otras visualizaciones. Para ver un gráfico de áreas para la misma salida, seleccione **Área** y establezca otros valores tal como se muestra.

    ![Gráfico de área de resultados de consultas Spark interactivas](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Gráfico de área de resultados de consultas Spark interactivas")

10. En el menú **File** (Archivo) del cuaderno, haga clic en **Save and Checkpoint** (Guardar y punto de control). 

11. Si ahora va a empezar con el [siguiente tutorial](apache-spark-use-bi-tools.md), deje el bloc de notas abierto. Si no va a hacerlo, cierre el cuaderno para liberar los recursos de clúster: en el menú **File** (Archivo) del cuaderno, haga clic en **Close and Halt** (Cerrar y detener).

## <a name="clean-up-resources"></a>Limpieza de recursos

Con HDInsight, los datos se almacenan en Azure Storage o Azure Data Lake Store, por lo que cualquier clúster se puede eliminar de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Si tiene previsto pasar inmediatamente al siguiente tutorial, es aconsejable que no elimine el clúster.

Abra el clúster en Azure Portal y seleccione **Eliminar**.

![Eliminar clúster de HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Delete HDInsight cluster")

También puede seleccionar el nombre del grupo de recursos para abrir la página del grupo de recursos y, después, hacer clic en **Eliminar grupo de recursos**. Al eliminar el grupo de recursos, se eliminan tanto el clúster de HDInsight Spark como la cuenta de almacenamiento predeterminada.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a:

* Crear una trama de datos de Spark.
* Ejecutar Spark SQL en la trama de datos.

En el siguiente artículo podrá ver cómo extraer en una herramienta de análisis de BI, como Power BI, los datos que registró en Spark. 
> [!div class="nextstepaction"]
> [Análisis de datos mediante herramientas de inteligencia empresarial](apache-spark-use-bi-tools.md)

