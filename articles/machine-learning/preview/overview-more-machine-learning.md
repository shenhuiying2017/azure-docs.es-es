---
title: ¿Qué es Azure Machine Learning? | Microsoft Docs
description: Información general sobre Experimentación de Azure Machine Learning y Administración de modelos, una solución de integrada y completa de ciencia de datos que permite a los científicos de datos profesionales desarrollar, experimentar e implementar aplicaciones de análisis avanzado a escala de nube.
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: 1c4826959bb92e2e515867261094b1154a3c805c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="machine-learning-solutions-from-microsoft"></a>Soluciones de aprendizaje automático de Microsoft

Además de [Azure Machine Learning](overview-what-is-azure-ml.md), Microsoft ofrece varias opciones para compilar, implementar y administrar los modelos de aprendizaje automático. 
* SQL Server Machine Learning Services
* Servidor de Microsoft Machine Learning
* Servicios de Azure Machine Learning
* Azure Machine Learning Studio
* Máquina virtual de ciencia de datos
* MLLib de Spark en HDInsight
* Servicio de entrenamiento de AI por lotes
* Microsoft Cognitive Toolkit
* Microsoft Cognitive Services


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services
[Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) permite ejecutar, entrenar e implementar modelos de aprendizaje automático mediante R o Python. Puede usar datos situados en entornos locales y en bases de datos SQL Server. 

Use Microsoft Machine Learning Services cuando necesite entrenar o implementar modelos en entornos locales o en Microsoft SQL Server. Los modelos generados con Machine Learning Services se pueden implementar mediante Administración de modelos de aprendizaje de Azure Machine Learning. 

## <a name="microsoft-machine-learning-server"></a>Servidor de Microsoft Machine Learning 
El [servidor de Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) es un servidor empresarial para hospedar y administrar cargas de trabajo paralelas y distribuidas de procesos de R y Python. El servidor de Microsoft Machine Learning se ejecuta en Linux, Windows, Hadoop y Apache Spark. También está disponible en [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Proporciona un motor de ejecución para las soluciones compiladas mediante [paquetes de Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) y amplía R y Python de código abierto con compatibilidad con los escenarios siguientes:

- Análisis de alto rendimiento
- Análisis estadístico
- Machine Learning
- Conjuntos de datos de grandes dimensiones

Se ofrece una funcionalidad de valor añadido a través de paquetes de propiedad que se instalan con el servidor. Para el desarrollo, puede usar IDE, como [Herramientas de R para Visual Studio](https://www.visualstudio.com/vs/rtvs/) y [Herramientas de Python para Visual Studio](https://www.visualstudio.com/vs/python/).

Use el servidor de Microsoft Machine Learning cuando necesite:

- Compilar e implementar modelos generados con R y Python en un servidor
- Distribuir el entrenamiento de R y Python a escala en un clúster de Hadoop o Spark

## <a name="data-science-virtual-machine"></a>Máquina virtual de ciencia de datos
La [máquina virtual de ciencia de datos (DSVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) es una imagen de VM personalizada en la nube de Azure de Microsoft diseñada específicamente para realizar la ciencia de datos. Tiene muchas ciencias de datos conocidas y otras herramientas preinstaladas y preconfiguradas para impulsar la creación de aplicaciones inteligentes para análisis avanzado. Está disponible en Windows Server y en Linux. La edición de Windows de la DSVM se ofrece en Server 2016 y Server 2012. La edición de Linux de la DSVM se ofrece en Ubuntu 16.04 LTS y en las distribuciones de Linux basadas en OpenLogic 7.2 CentOS. 

Use la máquina virtual de ciencia de datos cuando necesite ejecutar u hospedar trabajos en un único nodo. O bien si necesita escalar de forma remota el procesamiento en un único equipo. La máquina Virtual de ciencia de datos se admite como destino para Experimentación de Azure Machine Learning y Administración de modelos de Azure Machine Learning. 

## <a name="spark-mllib-in-hdinsight"></a>MLLib de Spark en HDInsight
[MLLib Spark en HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) permite crear modelos como parte de trabajos de Spark que se ejecutan en macrodatos. Spark le permite transformar y preparar los datos y, a continuación, escalar horizontalmente la creación de modelos en un único trabajo de forma sencilla. Los modelos creados a través de MLLib Spark se pueden implementar, administrar y supervisar mediante Administración de modelos de Azure Machine Learning. Las ejecuciones de entrenamiento se pueden enviar y administrar con Experimentación de Azure Machine Learning. Spark también puede utilizarse para escalar horizontalmente trabajos de preparación de datos creados en Machine Learning Workbench. 

Use Spark cuando necesite escalar horizontalmente el procesamiento de datos y crear modelos como parte de una canalización de datos. Puede crear trabajos de Spark en Scala, Java, Python o R. 

## <a name="batch-ai-training"></a>Entrenamiento de AI por lotes 
El [entrenamiento de AI por lotes de Azure](https://aka.ms/batchaitraining) ayuda a experimentar en paralelo con los modelos de AI con cualquier marco y, a continuación, los entrena a escala a través de GPU en clúster. Describa los requisitos del trabajo y la configuración para la ejecución y nos encargaremos del resto. 

El entrenamiento de AI por lotes le permite escalar horizontalmente trabajos de aprendizaje profundo a través de GPU en clúster, con marcos como:

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Administración de modelos de Machine Learning puede usarse para tomar los modelos de entrenamiento de AI para implementarlos, administrarlos y supervisarlos.  El entrenamiento de AI por lotes se integrará con Experimentación de Azure Machine Learning en el futuro. 

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) es un kit de herramientas de aprendizaje profundo unificado que describe redes neurales como pasos de cálculo en un grafo dirigido. En este grafo dirigido, los nodos de la hoja representan valores de entrada o parámetros de red, mientras que otros nodos representan operaciones de matriz basadas en las entradas. Cognitive Toolkit permite crear y combinar fácilmente tipos de modelos populares como DNN de prealimentación, redes convolucionales (CNNs) y redes recurrentes (RNN/LSTM). Implementa el aprendizaje de descenso de gradiente estocástico (SGD, retropropagación de errores) con diferenciación automática y ejecución en paralelo en varios servidores y GPU.

Use Cognitive Toolkit para crear un modelo mediante aprendizaje profundo.  Cognitive Toolkit puede usarse en cualquiera de los servicios anteriores.

## <a name="microsoft-cognitive-services"></a>Microsoft Cognitive Services
Microsoft Cognitive Services es un conjunto de 30 API que le permite crear aplicaciones que usan métodos naturales de comunicación. Estas API permiten a las aplicaciones ver, oír, hablar, comprender e interpretar nuestras necesidades con solo unas pocas líneas de código. Agregue fácilmente características inteligentes a las aplicaciones, como: 

- Detección de opiniones y emociones
- Reconocimiento de visión y voz
- Comprensión del lenguaje
- Conocimiento y búsqueda

Microsoft Cognitive Services se puede usar para desarrollar aplicaciones en distintos dispositivos y plataformas. Las API siguen mejorando y son fáciles de configurar. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Pasos siguientes

Consulte la introducción de [Azure Machine Learning](overview-what-is-azure-ml.md).