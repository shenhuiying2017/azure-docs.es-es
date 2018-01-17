---
title: "¿Qué es Machine Learning en Azure? | Microsoft Docs"
description: "Explica los conceptos básicos del aprendizaje automático en la nube, describe para qué puede utilizarlo y define los términos de aprendizaje automático."
keywords: "qué es aprendizaje automático, terminología de aprendizaje automático, predictivo, qué es análisis predictivo, términos de aprendizaje automático"
services: machine-learning
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: eaee083e-eaa1-4408-838b-93e51423d159
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: cgronlun;tedway;olgali
ms.openlocfilehash: adbd1badd8053d3c2b53386b0311e120738099f9
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="introduction-to-machine-learning-in-the-azure-cloud"></a>Introducción a Machine Learning en la nube de Azure

## <a name="what-is-machine-learning"></a>¿Qué es el aprendizaje automático?
El aprendizaje automático es una técnica de ciencia de datos que permite a los equipos utilizar datos existentes para prever tendencias, resultados y comportamientos futuros. Mediante el aprendizaje automático, los equipos aprenden si necesidad de programarlos explícitamente.

Las previsiones o predicciones del aprendizaje automático pueden hacer que las aplicaciones y los dispositivos sean más inteligentes. Cuando compra en línea, el aprendizaje automático ayuda a recomendar otros productos según lo que haya adquirido. Al pasar su tarjeta de crédito, el aprendizaje automático compara la transacción con una base de datos de transacciones y ayuda a detectar fraudes. Cuando la aspiradora robot aspira una sala, el aprendizaje automático le ayuda a decidir si se ha terminado el trabajo.

Si desea una pequeña introducción, vea la serie de vídeos [Ciencia de datos para principiantes](data-science-for-beginners-the-5-questions-data-science-answers.md). Sin utilizar tecnicismos ni matemáticas, la serie de vídeos Ciencia de datos para principiantes hace una introducción al aprendizaje automático y le guía a lo largo de un sencillo modelo de predicción.

## <a name="what-is-machine-learning-in-the-microsoft-azure-cloud"></a>¿Qué es Machine Learning en la nube de Microsoft Azure?
Azure Machine Learning es un servicio de análisis predictivo en la nube que permite crear e implementar rápidamente modelos predictivos como soluciones de análisis.

Puede trabajar desde una biblioteca de algoritmos lista para usar, utilizarlos para crear modelos en un equipo conectado a Internet e implementar rápidamente la solución de predicción. Comience con los ejemplos y las soluciones listos para usar de la [Galería de Azure AI](https://gallery.cortanaintelligence.com/).

![¿Qué es el aprendizaje automático? Flujo de trabajo básico para poner en funcionamiento el análisis predictivo en Azure Machine Learning.](./media/what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

Azure Machine Learning no solo proporciona herramientas para análisis predictivos de modelos, sino que también proporciona un servicio totalmente administrado que se puede usar para implementar los modelos predictivos como servicios web listos para consumir.

## <a name="what-is-predictive-analytics"></a>¿Qué es el análisis predictivo?
El análisis predictivo emplea fórmulas matemáticas llamadas algoritmos, que analizan datos históricos o actuales para identificar patrones o tendencias para la previsión de futuros eventos.

## <a name="tools-to-build-complete-machine-learning-solutions-in-the-cloud"></a>Herramientas para crear completas soluciones de aprendizaje automático en la nube
Azure Machine Learning tiene todo lo que necesita para crear completas soluciones de análisis predictivo en la nube, desde una biblioteca de algoritmos grande a un estudio para la creación de modelos o una manera fácil de implementar su modelo como un servicio web. Cree, pruebe, ponga en funcionamiento y administre rápidamente modelos de predicción.

### <a name="machine-learning-studio-create-predictive-models"></a>Machine Learning Studio: creación de modelos predictivos
En [Machine Learning Studio](what-is-ml-studio.md), puede crear rápidamente modelos predictivos arrastrando, quitando y conectando módulos. Puede experimentar con diferentes combinaciones y [probarlas gratuitamente](https://studio.azureml.net/?selectAccess=true&o=2).

* En la [Galería de Azure AI](gallery-how-to-use-contribute-publish.md), puede intentar las soluciones de análisis creadas por otros usuarios o aportar las suyas propias. Publique preguntas o comentarios sobre experimentos en la comunidad o comparta vínculos a experimentos a través de redes sociales, como LinkedIn y Twitter.

  ![Pruebe experimentos predictivos o contribuya con los suyos propios en la Galería de Azure AI](./media/what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)
* Utilice una gran biblioteca de [algoritmos y módulos de Machine Learning](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) en Machine Learning Studio para impulsar sus modelos de predictivos. Elija entre experimentos de ejemplo, paquetes R y Python y los mejores algoritmos de su clase en servicios de Microsoft como Xbox y Bing. Amplíe los módulos de Studio con sus propios scripts de [R](extend-your-experiment-with-r.md) y [Python](execute-python-scripts.md) personalizados.

  ![Descripción de análisis predictivo: ejemplo de un experimento de análisis predictivo en Azure Machine Learning Studio](./media/what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

### <a name="operationalize-predictive-analytics-solutions-by-publishing-your-own"></a>Publicar soluciones de análisis predictivo para ponerlas en funcionamiento
Los tutoriales siguientes muestran cómo poner en funcionamiento los modelos de análisis predictivo:

 * [Implementación de servicios web](publish-a-machine-learning-web-service.md)
 * [Repetición del entrenamiento de modelos mediante API](retrain-models-programmatically.md)
 * [Administración de extremos de servicios web](create-endpoint.md)
 * [Escalado de un servicio web](scaling-webservice.md)
 * [Consumo de servicios web](consume-web-services.md)

## <a name="key-machine-learning-terms-and-concepts"></a>Términos clave del aprendizaje automático
La terminología del aprendizaje automático puede resultar confusa. Aquí se muestran definiciones de términos clave que le ayudarán. Use los comentarios que aparecen a continuación para indicarnos cualquier otro término del que le gustaría ver una definición.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Exploración de datos, análisis descriptivo y análisis predictivos

**Exploración de datos** es el proceso de recopilación de información sobre un conjunto de datos grande y a menudo no estructurado para encontrar características para análisis más detallados.

**Minería de datos** hace referencia a la exploración de datos automatizada.

**Análisis descriptivo** es el proceso de análisis de un conjunto de datos con el fin de resumir lo que sucedió. La inmensa mayoría de análisis de negocios, como los informes de ventas, las métricas de web y el análisis de redes sociales, son descriptivos.

**Análisis predictivos** es el proceso de creación de modelos de datos históricos o actuales con el fin de pronosticar resultados futuros.

### <a name="supervised-and-unsupervised-learning"></a>Aprendizaje supervisado y no supervisado
 **aprendizaje supervisado** se ha entrenado con datos etiquetados; es decir, con datos formados por ejemplos de las respuestas deseadas. Por ejemplo, se podría preparar un modelo que identifique el uso de tarjetas de crédito fraudulentas desde un conjunto de datos en el cual se etiqueten los puntos de datos que indiquen cargos fraudulentos y cargos válidos conocidos. Se supervisa la mayoría del aprendizaje automático.

 **aprendizaje no supervisado** se utiliza en los datos sin etiquetas y el objetivo es buscar relaciones en los datos. Por ejemplo, puede querer buscar agrupaciones de datos demográficos del cliente con hábitos de compra similares.

### <a name="model-training-and-evaluation"></a>Entrenamiento y evaluación de modelos
Un modelo de aprendizaje automático es una abstracción de la pregunta que intenta responder o el resultado que desea predecir. Modelos se entrenan y se evalúan a partir de datos existentes.

#### <a name="training-data"></a>Datos de aprendizaje.
Cuando entrena un modelo a partir de datos, utiliza un conjunto de datos conocido y realiza los ajustes necesarios en el modelo en función de las características de datos para obtener la respuesta más precisa. En Azure Machine Learning, se crea un modelo a partir de un módulo de algoritmo que procesa los datos de entrenamiento y módulos funcionales, como un módulo de puntuación.

En el aprendizaje supervisado, si está entrenando un modelo de detección de fraudes, usará un conjunto de transacciones que se etiquetan como fraudulentas o válidas. Podrá dividir el conjunto de datos de forma aleatoria y usar una parte para entrenar el modelo y otra parte para probar o evaluar el modelo.

#### <a name="evaluation-data"></a>Datos de evaluación
Una vez entrenado un modelo, se evalúa con los datos restantes de la prueba. Puede usar los datos de los que ya conoce los resultados, por lo que puede saber si su modelo predice con precisión.

## <a name="other-common-machine-learning-terms"></a>Otros términos comunes relativos al aprendizaje automático
* **Algoritmo**: un conjunto independiente de reglas usado para resolver problemas a través de procesamiento de datos, cálculos matemáticos o razonamiento automatizado.
* **Detección de anomalías**: un modelo que marca valores o eventos no habituales y le ayuda a detectar problemas. Por ejemplo, para la detección de fraudes en tarjetas de crédito se buscan compras no usuales.
* **Datos categóricos**: datos que se organizan por categorías y que se pueden dividir en grupos. Por ejemplo, un conjunto de datos categóricos de coches podría especificar el año, la marca, el modelo y el precio.
* **Clasificación**: un modelo para organizar los puntos de datos en categorías según un conjunto de datos para el que ya se conocen las agrupaciones de categorías.
* **Ingeniería de características**: el proceso de extracción o de selección de características relacionados con un conjunto de datos con el fin de mejorar el conjunto de datos y de mejorar los resultados. Por ejemplo, los datos de vuelo podrían mejorarse por días de la semana y días festivos. Consulte [Selección de características e ingeniería en Azure Machine Learning](../team-data-science-process/create-features.md).
* **módulo**: una parte funcional en un modelo de Machine Learning Studio, como el módulo Especificar datos que permite especificar y editar conjuntos de datos pequeños. Un algoritmo también es un tipo de módulo de Machine Learning Studio.
* **modelo**: un modelo de aprendizaje supervisado es el producto de un experimento de aprendizaje automático que consta de un conjunto de datos de entrenamiento, un módulo de algoritmo y módulos funcionales, como un módulo de puntuación de modelos.
* **Datos numéricos**: datos que significan medidas (datos continuos) o cuentas (datos discretos). También se denomina *datos cuantitativos*.
* **Partición**: el método por el que se dividen los datos en los ejemplos. Consulte [Partición y ejemplo](https://msdn.microsoft.com/library/azure/dn905960.aspx) para obtener más información.
* **Predicción**: una predicción es una previsión de un valor o valores en un modelo de aprendizaje automático. También puede ver el término "puntuación prevista". Sin embargo, los resultados previstos no son el resultado final de un modelo. Una evaluación del modelo sigue la puntuación.
* **Regresión**: un modelo para predecir un valor en función de variables independientes; por ejemplo, predecir el precio de un coche en función de su año y marca.
* **puntuación**: un valor predictivo que se genera a partir de una clasificación entrenada o un modelo de regresión, mediante el [módulo Puntuar modelo](https://msdn.microsoft.com/library/azure/dn905995.aspx) en Machine Learning Studio. Los modelos de clasificación también devuelven un resultado de la probabilidad del valor de predicción. Cuando se hayan generado las puntuaciones de un modelo, puede evaluar la exactitud del modelo con el [módulo Evaluar modelo](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **Ejemplo**: una parte de un conjunto de datos diseñado para ser representante del conjunto. Los ejemplos pueden elegirse aleatoriamente o según características específicas del conjunto de datos.

## <a name="next-steps"></a>pasos siguientes
Puede aprender los datos básicos del análisis predictivo y de Machine Learning mediante un [tutorial detallado](create-experiment.md) y el [uso de ejemplos](sample-experiments.md).  

<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/
