---
title: "Compilación de aplicaciones de aprendizaje automático de Apache Spark en Azure HDInsight | Microsoft Docs"
description: "En este artículo se muestran instrucciones paso a paso sobre cómo compilar aplicaciones de aprendizaje automático de Apache Spark en clústeres Spark de HDInsight utilizando Jupyter Notebook."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: 74dcd368d8696df26c5ad294c5657161fbe7f408
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Compilación de aplicaciones de aprendizaje automático de Apache Spark en Azure HDInsight

Obtenga información sobre cómo compilar una aplicación de aprendizaje automático con un clúster Apache Spark en HDInsight. En este artículo se muestra cómo usar el cuaderno de Jupyter Notebook disponible con el clúster para compilar y probar esta aplicación. La aplicación usa los datos de ejemplo de HVAC.csv, que está disponible en todos los clústeres de manera predeterminada.

[MLlib](https://spark.apache.org/docs/1.1.0/mllib-guide.html): es una biblioteca de aprendizaje automático escalable de Spark que consta de algoritmos y utilidades de aprendizaje comunes, como la clasificación, la regresión, la agrupación en clústeres, el filtrado colaborativo, la reducción de dimensionalidad, y las primitivas de optimización subyacentes.

**Requisitos previos:**

Debe tener lo siguiente:

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Comprensión del conjunto de datos

Los datos siguientes muestran la temperatura objetivo y la temperatura real de algunos edificios con sistemas HVAC instalados. La columna **System** representa el identificador del sistema y la columna **SystemAge**, el número de años que lleva el sistema HVAC instalado en el edificio. Los datos de este tutorial se usan para predecir si un edificio será más cálido o frío en función de la temperatura objetivo, dados un identificador del sistema y la antigüedad del sistema.

![Instantánea de datos usados para el ejemplo de aprendizaje automático de Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Instantánea de datos usados para el ejemplo de aprendizaje automático de Spark")

El archivo de datos, **HVAC.csv**, se encuentra en **\HdiSamples\HdiSamples\SensorSampleData\hvac** en todos los clústeres de HDInsight.

## <a name="app"></a>Escritura de una aplicación de aprendizaje automático de Spark mediante Spark MLlib
En esta aplicación se usa una [canalización ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) de Spark para realizar una clasificación de documentos. Las canalizaciones ML proporcionan un conjunto uniforme de API de alto nivel creadas a partir de tramas de datos que ayudan a los usuarios a crear y ajustar las canalizaciones prácticas de aprendizaje automático. En la canalización, se divide el documento en palabras, se convierten las palabras en un vector numérico de característica y finalmente se genera un modelo de predicción que use los vectores de característica y las etiquetas. Realice los siguientes pasos para crear la aplicación:

1. Cree un cuaderno de Jupyter Notebook con el kernel de PySpark. Para las instrucciones, consulte [Creación de un cuaderno de Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Importe los tipos necesarios para este escenario. Pegue el siguiente fragmento de código en una celda vacía y presione **MAYÚS + ENTRAR**. 

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```
3. Cargue los datos (hvac.csv), analícelos y úselos para entrenar el modelo. 

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0        

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    En el fragmento de código, se define una función que compara la temperatura real con la temperatura de destino. Si la temperatura real es mayor, el edificio está cálido, lo que viene indicado por el valor **1.0**. De lo contrario, el edificio está frío, lo que se indica con el valor **0.0**. 

4. Configure la canalización de aprendizaje automático de Spark, que consta de tres fases: tokenizer, hashingTF e lr. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Para más información sobre qué es una canalización y cómo funciona, vea <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Canalización de aprendizaje automático de Spark</a>.

5. Ajuste la canalización al documento de formación.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Compruebe el documento de aprendizaje para controlar el progreso con la aplicación.
   
    ```PySpark
    training.show()
    ```
   
    Esto dará un resultado similar al siguiente:

    ```
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    La comparación de la salida con el archivo CSV sin procesar. Por ejemplo, la primera fila del archivo CSV tiene estos datos:

    ![Instantánea de datos de salida para el ejemplo de aprendizaje automático de Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Instantánea de datos de salida para el ejemplo de aprendizaje automático de Spark")

    Observe que la temperatura real es menor que la temperatura objetivo, lo que indica que el edificio está frío. Por lo tanto, en la salida de aprendizaje, el valor de **label** en la primera fila es **0.0**, lo que significa que la temperatura del edificio no es cálida.

7. Prepare un conjunto de datos con el que ejecutar el modelo entrenado. Para ello, deberá pasar un identificador del sistema y la antigüedad del sistema (representados como **SystemInfo** en la salida de entrenamiento), y el modelo predecirá si el edificio con ese identificador y esa antigüedad del sistema es más cálido (indicado por 1.0) o frío (indicado por 0.0).
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. Por último, realice predicciones basadas en los datos de prueba. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Debería ver una salida similar a la siguiente:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   En la primera fila de la predicción, puede ver que, en un sistema HVAC con el identificador 20 y una antigüedad de 25 años, el edificio tendrá una temperatura cálida (**prediction=1.0**). El primer valor de DenseVector (0.49999) corresponde a la predicción 0.0 y el segundo, (0.5001), corresponde a la predicción 1.0. En la salida, aunque el segundo valor solo es levemente superior, el modelo muestra **prediction=1.0**.
10. Cierre el cuaderno para liberar los recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. De esta manera se apagará y se cerrará el cuaderno.

## <a name="anaconda"></a>Uso de la biblioteca scikit-learn de Anaconda para el aprendizaje automático de Spark
Los clústeres Apache Spark en HDInsight incluyen bibliotecas de Anaconda, Entre ellas, está la biblioteca **scikit-learn** para aprendizaje automático. La biblioteca también contiene diversos conjuntos de datos que puede usar para crear aplicaciones de ejemplo directamente a partir de un cuaderno de Jupyter. Para ver ejemplos sobre el uso de la biblioteca scikit-learn, consulte [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Consulte también
* [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Use Spark in HDInsight for building real-time streaming applications](apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
