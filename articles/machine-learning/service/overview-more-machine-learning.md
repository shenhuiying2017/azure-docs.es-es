---
title: 'Otros productos de aprendizaje automático de Microsoft: Azure Machine Learning | Microsoft Docs'
description: Además de Azure Machine Learning, Microsoft ofrece varias opciones para compilar, implementar y administrar los modelos de aprendizaje automático.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: bbb8deeab8368d9c0e6d29c8d7e1e2e0a8805d60
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Otros servicios y productos de aprendizaje automático de Microsoft

Además de [Azure Machine Learning](overview-what-is-azure-ml.md), Microsoft ofrece varias opciones para compilar, implementar y administrar los modelos de aprendizaje automático. 
* SQL Server Machine Learning Services
* Servidor de Microsoft Machine Learning
* Máquina virtual de ciencia de datos de Azure
* MLLib de Spark en HDInsight
* Servicio de entrenamiento de AI por lotes
* Microsoft Cognitive Toolkit (CNTK)
* Azure Cognitive Services


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services
[SQL Server Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) permite ejecutar, entrenar e implementar modelos de aprendizaje automático mediante R o Python. Puede usar datos situados en entornos locales y en bases de datos SQL Server. 

Use Microsoft Machine Learning Services cuando necesite entrenar o implementar modelos en entornos locales o en Microsoft SQL Server. Los modelos generados con Machine Learning Services se pueden implementar mediante Administración de modelos de aprendizaje de Azure Machine Learning. 

## <a name="microsoft-machine-learning-server"></a>Servidor de Microsoft Machine Learning 
El [servidor de Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) es un servidor empresarial para hospedar y administrar cargas de trabajo paralelas y distribuidas de procesos de R y Python. El servidor de Microsoft Machine Learning se ejecuta en Linux, Windows, Hadoop y Apache Spark. También está disponible en [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Proporciona un motor de ejecución para las soluciones compiladas mediante [paquetes de Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) y amplía R y Python de código abierto con compatibilidad con los escenarios siguientes:

- Análisis de alto rendimiento
- Análisis estadístico
- Machine Learning
- Conjuntos de datos de grandes dimensiones

Se ofrece una funcionalidad adicional a través de paquetes de propiedad que se instalan con el servidor. Para el desarrollo, puede usar IDE, como [Herramientas de R para Visual Studio](https://www.visualstudio.com/vs/rtvs/) y [Herramientas de Python para Visual Studio](https://www.visualstudio.com/vs/python/).

Use el servidor de Microsoft Machine Learning cuando necesite:

- Compilar e implementar modelos generados con R y Python en un servidor
- Distribuir el entrenamiento de R y Python a escala en un clúster de Hadoop o Spark

## <a name="azure-data-science-virtual-machine"></a>Máquina virtual de ciencia de datos de Azure
La [máquina virtual de ciencia de datos](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) es un entorno de máquina virtual personalizado en la nube de Microsoft Azure diseñado específicamente para hacer ciencia de datos. Tiene muchas ciencias de datos conocidas y otras herramientas preinstaladas y preconfiguradas para impulsar la creación de aplicaciones inteligentes para análisis avanzado. La máquina virtual de ciencia de datos está disponible en las versiones 2016 y 2012 de Windows Server, en una VM Linux con Ubuntu 16.04 LTS y en distribuciones basadas en OpenLogic CentOS 7.4. 

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

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft Cognitive Toolkit (CNTK)
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) es un kit de herramientas de aprendizaje profundo unificado que describe redes neurales como pasos de cálculo en un grafo dirigido. En este grafo dirigido, los nodos de la hoja representan valores de entrada o parámetros de red, mientras que otros nodos representan operaciones de matriz basadas en las entradas. Cognitive Toolkit permite crear y combinar fácilmente tipos de modelos populares como DNN de prealimentación, redes convolucionales (CNNs) y redes recurrentes (RNN/LSTM). Implementa el aprendizaje de descenso de gradiente estocástico (SGD, retropropagación de errores) con diferenciación automática y ejecución en paralelo en varios servidores y GPU.

Use Cognitive Toolkit para crear un modelo mediante aprendizaje profundo.  Cognitive Toolkit puede usarse en cualquiera de los servicios anteriores.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) es un conjunto de 30 API que le permiten compilar aplicaciones que usan métodos naturales de comunicación. Estas API permiten a las aplicaciones ver, escuchar, hablar, comprender e interpretar las necesidades del usuario con solo unas pocas líneas de código. Agregue fácilmente características inteligentes a las aplicaciones, como: 

- Detección de opiniones y emociones
- Reconocimiento de visión y voz
- Language Understanding (LUIS)
- Conocimiento y búsqueda

Cognitive Services se puede usar para desarrollar aplicaciones en distintos dispositivos y plataformas. Las API siguen mejorando y son fáciles de configurar. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Pasos siguientes

Consulte la introducción de [Azure Machine Learning](overview-what-is-azure-ml.md).
