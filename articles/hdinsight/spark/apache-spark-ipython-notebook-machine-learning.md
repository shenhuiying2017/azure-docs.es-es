---
title: 'Tutorial: Compilación de aplicaciones de aprendizaje automático de Spark en HDInsight | Microsoft Docs'
description: Instrucciones detalladas acerca de cómo compilar una aplicación de aprendizaje automático de Apache Spark en clústeres Spark de HDInsight mediante Jupyter Notebook.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 70876196eb6b37065a663afa56ed496a0e9755db
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33770912"
---
# <a name="tutorial-build-a-spark-machine-learning-application-in-hdinsight"></a>Tutorial: Compilación de aplicaciones de aprendizaje automático de Spark en HDInsight 

En este tutorial, aprenderá a usar Jupyter Notebook para crear una aplicación de aprendizaje de automático de Azure Spark para Apache HDInsight. 

[MLlib](https://spark.apache.org/docs/1.1.0/mllib-guide.html): es una biblioteca de aprendizaje automático escalable de Spark que consta de algoritmos y utilidades de aprendizaje comunes, como la clasificación, la regresión, la agrupación en clústeres, el filtrado colaborativo, la reducción de dimensionalidad, y las primitivas de optimización subyacentes.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Desarrollo de una aplicación de aprendizaje automático de Spark

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos:

Debe tener lo siguiente:

* Complete [Creación de un clúster de Apache Spark en Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="understand-the-data-set"></a>Información acerca del conjunto de datos

La aplicación usa los datos de ejemplo de HVAC.csv, que está disponible en todos los clústeres de manera predeterminada. El archivo se encuentra en **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Los datos muestran la temperatura objetivo y la temperatura real de algunos edificios que tienen sistemas de calefacción, ventilación y aire acondicionado instalados. La columna **System** representa el identificador del sistema y la columna **SystemAge**, el número de años que lleva el sistema HVAC instalado en el edificio. Mediante los datos se puede predecir si un edificio será más cálido o frío en función de la temperatura objetivo, dados un identificador del sistema y la antigüedad del sistema.

![Instantánea de datos usados para el ejemplo de aprendizaje automático de Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Instantánea de datos usados para el ejemplo de aprendizaje automático de Spark")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Desarrollo de una aplicación de aprendizaje automático de Spark mediante Spark MLlib

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

    Para más información acerca de las canalizaciones y cómo funcionan, consulte <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Canalización de aprendizaje automático de Spark</a>.

5. Ajuste la canalización al documento de formación.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Compruebe el documento de aprendizaje para controlar el progreso con la aplicación.
   
    ```PySpark
    training.show()
    ```
   
    La salida es parecida a esta:

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

    La salida es parecida a esta:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   En la primera fila de la predicción, puede ver que, en un sistema HVAC con el identificador 20 y una antigüedad de 25 años, el edificio tiene una temperatura cálida (**prediction=1.0**). El primer valor de DenseVector (0.49999) corresponde a la predicción 0.0 y el segundo, (0.5001), corresponde a la predicción 1.0. En la salida, aunque el segundo valor solo es levemente superior, el modelo muestra **prediction=1.0**.
10. Cierre el cuaderno para liberar los recursos. Para ello, en el menú **File** (Archivo) del cuaderno y seleccione **Close and Halt** (Cerrar y detener). Con esta acción se cerrará el cuaderno.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Uso de la biblioteca scikit-learn de Anaconda para el aprendizaje automático de Spark
Los clústeres Apache Spark de HDInsight incluyen bibliotecas de Anaconda. También incluyen la biblioteca **scikit-learn** para el aprendizaje automático. La biblioteca también contiene diversos conjuntos de datos que puede usar para crear aplicaciones de ejemplo directamente a partir de un cuaderno de Jupyter. Para obtener ejemplos sobre el uso de la biblioteca scikit-learn, consulte [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

* Desarrollo de una aplicación de aprendizaje automático de Spark

Para aprender a usar IntelliJ IDEA para trabajos de Spark, pase al siguiente tutorial. 

> [!div class="nextstepaction"]
> [Creación de una aplicación de Scala Maven mediante IntelliJ](./apache-spark-create-standalone-application.md)

