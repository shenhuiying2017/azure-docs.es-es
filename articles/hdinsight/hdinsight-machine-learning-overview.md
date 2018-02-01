---
title: "Información general del aprendizaje automático: Azure HDInsight | Microsoft Docs"
description: "Describe las opciones del aprendizaje automático en HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: nitinme
ms.openlocfilehash: ff99a7a60573cad5e6dd30d4ca48903423e9f87f
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="machine-learning-on-hdinsight"></a>Aprendizaje automático en HDInsight

HDInsight permite el aprendizaje automático con macrodatos, lo que proporciona la capacidad de obtener información valiosa de grandes cantidades de datos estructurados y no estructurados (petabytes o incluso exabytes), y en rápido movimiento. Hay varias opciones de aprendizaje automático en HDInsight: SparkML y MLlib, R, Hive y Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML y MLlib

[HDInsight Spark](spark/apache-spark-overview.md) es una oferta de [Spark](http://spark.apache.org/) hospedada por Azure. Consiste en una plataforma de procesamiento paralelo de datos de código abierto y unificado que admite el procesamiento en memoria para agilizar el análisis de macrodatos. El motor de procesamiento Spark se ha creado para ofrecer velocidad, facilidad de uso y análisis sofisticados. Las capacidades de cálculo distribuido en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático. Hay dos bibliotecas escalables de aprendizaje automático que ofrecen las funcionalidades del modelado algorítmico a este entorno distribuido: MLlib y SparkML. MLlib contiene la API original que se basa en RDD. SparkML es un paquete más reciente que proporciona una API de nivel más alto que se basa en DataFrames para construir canalizaciones ML. SparkML aún no admite todas las características de MLlib, pero está reemplazándola como biblioteca de aprendizaje automático estándar de Spark.

La biblioteca Microsoft Machine Learning para Apache Spark es [MMLSpark](https://github.com/Azure/mmlspark). Esta biblioteca se ha diseñado para que los científicos de datos sean más productivos en Spark, aumenten la velocidad de experimentación y aprovechen las técnicas de aprendizaje automático de vanguardia, así como el aprendizaje profundo, en grandes conjuntos de datos. MMLSpark proporciona una capa sobre las API de bajo nivel de SparkML cuando se crean modelos de aprendizaje automático escalables, como las cadenas de indexación, la conversión de datos en un diseño esperado por los algoritmos de aprendizaje automático y el ensamblado de vectores de características. La biblioteca MMLSpark simplifica estas y otras tareas comunes para la creación de modelos en PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) es actualmente el lenguaje de programación estadístico más popular del mundo. Es una herramienta de visualización de datos de código abierto con una comunidad de más de 2,5 millones de usuarios y que sigue aumentando. Con su próspera cuenta de usuarios y la contribución de más de 8 000 paquetes, R es una opción factible para muchas empresas que requieren el aprendizaje automático. Puede crear un clúster de HDInsight con R Server preparado para usarse con grandes conjuntos de datos y modelos. Esta funcionalidad proporciona a los científicos de datos y a los estadísticos una interfaz conocida de R que se puede escalar a petición mediante HDInsight, sin la sobrecarga de mantenimiento y configuración del clúster.

![Aprendizaje para la predicción con R server](./media/hdinsight-machine-learning-overview/r-training.png)

El nodo perimetral de un clúster proporciona un lugar conveniente para conectarse al clúster y ejecutar los scripts de R.  También tiene la opción de ejecutarlos en estos nodos del clúster utilizando los contextos de proceso de Spark o Hadoop MapReduce de ScaleR.

Con R Server en HDInsight con Spark, puede simultanear el aprendizaje en todos los nodos de un clúster mediante el uso de un contexto de proceso de Spark. Puede ejecutar scripts de R directamente en el nodo perimetral, utilizando todos los núcleos disponibles en paralelo, según sea necesario. Como alternativa, puede ejecutar el código desde el nodo perimetral para iniciar el procesamiento que se distribuye entre todos los nodos del clúster. R Server en HDInsight con Spark también permite simultanear las funciones de los paquetes de R de código abierto, si se desea.

## <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning y Hive

Azure Machine Learning no solo proporciona herramientas para análisis predictivo de modelos, sino también un servicio totalmente administrado que se puede usar para implementar los modelos predictivos como servicios web listos para consumir. Azure Machine Learning proporciona una completa solución de análisis predictivo en la nube que se puede usar para crear modelos predictivos, probarlos, ponerlos en funcionamiento y administrarlos. Seleccione entre una biblioteca de algoritmos de gran tamaño, utilice un estudio basado en web para la compilación de modelos e implemente fácilmente el modelo como un servicio web.

![Análisis avanzado accesible en Hadoop con Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Cree características para los datos de un clúster de Hadoop en HDInsight mediante [consultas de Hive](../machine-learning/team-data-science-process/create-features-hive.md). *La ingeniería de características* intenta aumentar la eficacia predictiva de los algoritmos de aprendizaje creando características a partir de los datos sin procesar que facilitan el proceso de aprendizaje. Puede ejecutar consultas de HiveQL desde Azure ML y obtener acceso a los datos procesados en Hive y almacenados en blobs, mediante el uso del [módulo de importación de datos](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

El [aprendizaje profundo](https://www.microsoft.com/en-us/research/group/dltc/) es una rama del aprendizaje automático que utiliza redes neurales profundas, inspiradas por los procesos biológicos del cerebro humano. Muchos investigadores ven el aprendizaje profundo como un enfoque prometedor para mejorar la inteligencia artificial. Algunos ejemplos de aprendizaje profundo son los intérpretes automáticos de idiomas, los sistemas de reconocimiento de imágenes y el razonamiento automático.

Con el fin de realizar avances en su trabajo de aprendizaje profundo, Microsoft ha desarrollado el kit de herramientas [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) de código abierto, gratuito y fácil de usar. Actualmente, el kit de herramientas lo usa una amplia variedad de productos de Microsoft, compañías de todo el mundo con necesidad de implementar el aprendizaje profundo a escala y estudiantes interesados en los algoritmos y las técnicas más recientes. 

## <a name="see-also"></a>Otras referencias

### <a name="scenarios"></a>Escenarios

* [Creación de aplicaciones de Machine Learning con Apache Spark en HDInsight de Azure](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generación de recomendaciones de películas con Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive y Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive y Azure Machine Learning de un extremo a otro](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Aprendizaje automático con Spark en HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Recursos de aprendizaje profundo

* [Deep learning toolkit with Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/) (Kit de herramientas de aprendizaje profundo con Spark)
* [Embarrassingly parallel image classification, using Cognitive Toolkit and TensorFlow on Azure HDInsight Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/) (Clasificación de imágenes paralelas, con Cognitive Toolkit y TensorFlow en Spark)
