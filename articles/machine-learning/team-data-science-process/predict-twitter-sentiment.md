---
title: "Predicción de las opiniones de Twitter con inserciones de palabras mediante el proceso de ciencia de datos en equipo en Azure | Documentos de Microsoft"
description: Pasos necesarios para ejecutar los proyectos de ciencia de datos.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev;
ms.openlocfilehash: df1124ddb436f0cfeec8f4ed9728fa15278b9325
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Predicción de las opiniones de Twitter con inserciones de palabras mediante el proceso de ciencia de datos en equipo

Este artículo muestra cómo colaborar de forma eficaz mediante el uso del algoritmo de inserción de palabras _Word2Vec_ y el algoritmo _Sentiment-Specific Word Embedding (SSWE)_ para predecir las opiniones de Twitter con [Azure Machine Learning](../preview/index.yml). Para más información sobre predicción de la polaridad de opiniones de Twitter, consulte el [repositorio MachineLearningSamples TwitterSentimentPrediction](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) en GitHub. La normalización de la estructura y la documentación de los proyectos con un ciclo de vida de ciencia de datos establecido es fundamental para facilitar una colaboración eficaz entre los equipos en los proyectos de ciencia de datos. El [Proceso de ciencia de datos en equipo (TDSP)](overview.md) proporciona este tipo de [ciclo de vida](lifecycle.md) estructurado. 

La creación de proyectos de ciencia de datos con la _plantilla TDSP_ proporciona una plataforma estandarizada para los proyectos de Azure Machine Learning. Previamente el equipo TDSP presentó un [repositorio de GitHub para las plantillas y la estructura del proyecto de TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Ahora los proyectos de Machine Learning que crean instancias con [plantillas TDSP para Azure Machine Learning](https://github.com/amlsamples/tdsp) están habilitados. Para obtener instrucciones, consulte cómo usar la [Estructuración de proyectos con la plantilla de Proceso de ciencia de datos en equipo](../preview/how-to-use-tdsp-in-azure-ml.md) en Azure Machine Learning. 


## <a name="twitter-sentiment-polarity-sample"></a>Ejemplo de polaridad de opiniones de Twitter

En este artículo se utiliza un ejemplo para mostrar cómo crear una instancia y ejecutar un proyecto de Machine Learning. El ejemplo usa las plantillas y la estructura de TDSP en Azure Machine Learning Workbench. El ejemplo completo se proporciona en [este tutorial](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). La tarea de modelado predice la polaridad de opinión (positiva o negativa) utilizando el texto de los tweets. Este artículo resume las tareas de modelado de datos que se describen en el tutorial. En el tutorial se describen las tareas siguientes:

- Exploración de datos, aprendizaje e implementación de un modelo de aprendizaje automático que permita resolver el problema de la predicción que se describe en la introducción del caso de uso. [Los datos de opinión de Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) se utilizan para estas tareas.
- Ejecución del proyecto mediante la plantilla TDSP de Azure Machine Learning para este proyecto. Para la ejecución de proyectos y los informes se usa el ciclo de vida de TDSP.
- Operacionalización de la solución directamente desde Azure Machine Learning en Azure Container Service.

El proyecto resalta las características siguientes de Azure Machine Learning:

- Creación de instancias y uso de la estructura TDSP.
- Ejecución de código en Azure Machine Learning Workbench.
- Operacionalizacion sencilla en Container Service con Docker y Kubernetes.

## <a name="team-data-science-process"></a>Proceso de ciencia de los datos en equipos
Para ejecutar este ejemplo, use la estructura de proyecto TDSP y las plantillas de documentación en Azure Machine Learning Workbench. El ejemplo implementa el [ciclo de vida TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), tal y como se muestra en la ilustración siguiente:

![Ciclo de vida del TDSP](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

Se crea el proyecto de TDSP en Azure Machine Learning Workbench basado en [estas instrucciones](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), tal y como se muestra en la ilustración siguiente:

![Creación del TDSP en Azure Machine Learning Workbench](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Adquisición y preparación de los datos](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
El primer paso en este ejemplo es descargar el conjunto de datos sentiment140 y dividir los datos en dos conjuntos de datos: de prueba y de aprendizaje. El conjunto de datos sentiment140 incluye el contenido real del tweet (con los iconos gestuales eliminados). El conjunto de datos también contiene la polaridad de cada tweet (negativo = 0, positivo = 4) con los tweets neutros eliminados. Una vez se dividen los datos, los datos de aprendizaje tienen 1,3 millones de filas y los datos de prueba tienen 320.000 filas.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Desarrollo del modelo](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

El siguiente paso en el ejemplo es desarrollar un modelo para los datos. La tarea de modelado se divide en tres partes:

- Ingeniería de características: generación de características para el modelo mediante el uso de distintos algoritmos de inserción de palabras. 
- Creación del modelo: entrenamiento de modelos diferentes para predecir la opinión del texto de entrada. Ejemplos de estos modelos incluyen la _regresión logística_ y la _potenciación del gradiente_.
- Evaluación del modelo: evaluación de los modelos entrenados sobre los datos de prueba.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Diseño de características](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Los algoritmos Word2Vec y SSWE se usan para generar inserciones de palabras. 


#### <a name="word2vec-algorithm"></a>Algoritmo Word2Vec

Se utiliza el algoritmo Word2Vec en el modelo Skip-Gram. Este modelo se explica en el documento escrito por Tomas Mikolov y otros "[Distributed Representations of Words and Phrases and their Compositionality. Advances in neural information processing systems](https://arxiv.org/abs/1310.4546)" (Representaciones distribuidas de palabras y frases y su composicionalidad. Avances en los sistemas de procesamiento de información neuronal). 2013.

El modelo Skip-Gram es una red neuronal superficial. La entrada es la palabra de destino que se codifica como un vector "uno a n", lo que se usa para predecir palabras cercanas. Si **V** es el tamaño del vocabulario, el tamaño de la capa de salida es __C*V__ donde C es el tamaño de la ventana de contexto. La siguiente ilustración muestra una arquitectura que se basa en el modelo Skip-Gram:

![Modelo Skip-Gram](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Los mecanismos detallados del algoritmo Word2Vec y el modelo Skip-Gram quedan fuera del ámbito de este ejemplo. Para más información, consulte las siguientes referencias:

- [Código 02_A_Word2Vec.py con ejemplos de TensorFlow que se de referencia](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Representaciones vectoriales de palabras](https://www.tensorflow.org/tutorials/word2vec)
- [How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf) (¿Cómo funciona exactamente word2vec?)
- [Notes on Noise Contrastive Estimation and Negative Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf) (Notas sobre la estimación comparativa de ruido y el muestreo negativo)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Algoritmo Sentiment-Specific Word Embedding
El algoritmo SSWE intenta superar una debilidad del algoritmo Word2Vec donde palabras con contextos similar y polaridad opuesta pueden tener vectores de palabra similares. Las similitudes pueden hacer que el algoritmo Word2Vec no se comporte con precisión para tareas como el análisis de opiniones. El algoritmo SSWE intenta controlar esta debilidad incorporando la polaridad de la frase y el contexto de la palabra en su función de pérdida.

El ejemplo utiliza una variante del algoritmo SSWE como sigue:

- Tanto el _ngram_ original y el _ngram_ dañado se usan como entradas.
- Una función de pérdida de bisagra de estilo de categoría se usa tanto para la pérdida sintáctica como para la pérdida semántica.
- La función de pérdida definitiva es la combinación ponderada de la pérdida sintáctica y la pérdida semántica.
- Para simplificar, se utiliza solo la entropía cruzada semántica como función de pérdida.

El ejemplo muestra que, incluso con la función de pérdida más simple, el rendimiento de la inserción SSWE es mejor que el de la inserción Word2Vec. La siguiente ilustración muestra el modelo convolucional que se usa para generar la inserción de palabras específicas de opiniones:

![Modelo de red neuronal inspirado en SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

Una vez acabado el proceso de aprendizaje, se generan dos archivos de inserción en el formato de valores separados por tabulaciones (TSV) para la fase de modelado.

Para más información acerca de los algoritmos SSWE, consulte el documento escrito por Duyu Tang y otros. "[Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification](http://www.aclweb.org/anthology/P14-1146)" (Aprendizaje de inserción de palabras específicas de opinión para la clasificación de opiniones de Twitter). Association for Computational Linguistics (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[ Creación de modelos](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Después de generar los vectores de palabra mediante el algoritmo SSWE o Word2Vec, los modelos de clasificación están entrenados para predecir la polaridad de opiniones real. Dos tipos de características: Word2Vec y SSWE, se aplican a dos modelos: el modelo de potenciación del gradiente y el de regresión logística. Como resultado, se entrenan cuatro modelos diferentes.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Evaluación de modelos](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Una vez que los modelos están entrenados se utilizan para probar los datos de texto de Twitter y evaluar el rendimiento de cada modelo. El ejemplo evalúa los cuatro modelos siguientes:

- Potenciación del gradiente sobre inserción SSWE.
- Regresión logística sobre inserción SSWE.
- Potenciación del gradiente sobre inserción Word2Vec.
- Regresión logística sobre inserción Word2Vec.

La comparación de los resultados de rendimiento de los cuatro modelos se muestra en la siguiente ilustración:

![Comparación de los modelos de característica operativa del receptor (ROC)](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

El modelo de potenciación del gradiente con la característica SSWE ofrece el mejor rendimiento al comparar los modelos utilizando la métrica de área bajo la curva (AUC).


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Implementación](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

El paso final es la implementación del modelo de predicción de opiniones entrenado en un servicio web en un clúster en Azure Container Service. El ejemplo usa el modelo de potenciación del gradiente con el algoritmo de inserción SSWE como modelo entrenado. El entorno de operacionalización aprovisiona Docker y Kubernetes en el clúster para administrar la implementación del servicio web, como se muestra en la siguiente ilustración: 

![Panel de Kubernetes](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Para más información sobre el proceso de operacionalización, consulte [Implementación de un modelo de Azure Machine Learning como un servicio web](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Conclusión

En este artículo, ha aprendido a entrenar un modelo de inserción de palabras usando los algoritmos Word2Vec y Sentiment-Specific Word Embedding. Las inserciones extraídas se usaron como características para entrenar varios modelos para predecir las puntuaciones de opiniones para datos de texto de Twitter. La característica SSWE que se usa con el modelo de potenciación del gradiente proporcionó el mejor rendimiento. El modelo se implementó a continuación como un servicio web en tiempo real en Container Service mediante el uso de Azure Machine Learning Workbench.


## <a name="references"></a>Referencias

* [Proceso de ciencia de datos en equipo (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Uso del proceso de ciencia de datos en equipo (TDSP) en Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Plantillas de proyecto TDSP para Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Conjunto de datos de ingresos de EE. UU. obtenido del repositorio de aprendizaje automático de UCI](https://archive.ics.uci.edu/ml/datasets/adult)
* [Reconocimiento de entidades biomédicas mediante la plantilla del proceso de ciencia de los datos en equipos (TDSP)](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov, Tomas y otros "Distributed representations of words and phrases and their compositionality. Advances in neural information processing systems" (Representaciones distribuidas de palabras y frases y su composicionalidad. Avances en los sistemas de procesamiento de información neuronal). 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu y otros "Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification" (Aprendizaje de inserción de palabras específicas de opinión para la clasificación de opiniones de Twitter). ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
