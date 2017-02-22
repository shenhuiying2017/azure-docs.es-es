---
title: (en desuso) Ejemplos de servicios web Machine Learning creados con R - Azure | Microsoft Docs
description: "(en desuso) Encuentre un útil conjunto de ejemplos de servicios creados con código R y Machine Learning y publicados en Azure Marketplace."
keywords: "csharp, código r, ejemplos de servicios web"
services: machine-learning
documentationcenter: 
author: jaymathe
manager: jhubbard
editor: cgronlun
ms.assetid: 97d66cb7-6a84-4ae9-8095-0b5f5ba82d7f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: jaymathe
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: 303fb71faf73d4a1b6d17b4ca66b83b15157332b


---
# <a name="deprecated-web-services-examples-using-r-code-on-azure-machine-learning-and-published-to-microsoft-azure-marketplace"></a>(en desuso) Ejemplos de servicios web con R en Azure Machine Learning y publicados en Microsoft Azure Marketplace

> [!NOTE]
> Microsoft DataMarket está en proceso de retirada y estas API están en desuso. 
> 
> Puede encontrar muchos experimentos y API de ejemplo útiles en la [Galería de Cortana Intelligence](http://gallery.cortanaintelligence.com). Para más información sobre la Galería, consulte [Uso compartido y descubrimiento de soluciones en la Galería de Cortana Intelligence](machine-learning-gallery-how-to-use-contribute-publish.md).

En este artículo se crearon servicios web de ejemplo mediante Aprendizaje automático de Azure y se publicaron en Azure Marketplace. Cada ejemplo de servicio web tiene un documento exhaustivo asociado, donde se integran conjuntos de datos de ejemplo para probar los servicios y se explica cómo el usuario puede crear un servicio similar. 

Con Estudio de aprendizaje automático de Azure, los usuarios pueden escribir código de R y, con tan solo hacer algunos clics, publicarlo como un servicio web para que lo consuman aplicaciones y dispositivos en todo el mundo. 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Desde la producción de calculadoras sencillas que proporcionan funcionalidad estadística hasta la creación de un previsor de análisis de opiniones para la minería de texto personalizado, los usuarios noveles y profesionales de R pueden beneficiarse de la facilidad con que los usuarios de Aprendizaje automático de Azure pueden aplicar el código R. Si bien estos servicios web podrían haberlos consumido los usuarios, posiblemente a través de una aplicación móvil o un sitio web, su propósito es mostrar el modo en que Aprendizaje automático puede aplicar scripts de R para fines analíticos y usarlos para crear servicios web basados en código R.

Cada ejemplo incluye un ejemplo de C# para el consumo de servicios web.

![Diagrama de código de R en Aprendizaje automático de Azure: soluciones de R para uso propietario o publicado en Azure Marketplace.][1]

Considere los siguientes escenarios.

## <a name="scenario-1-generic-model"></a>Escenario 1: modelo genérico
Un usuario trabaja con un modelo genérico que se puede aplicar a los datos de un nuevo usuario, como una previsión básica de datos de serie temporal o un método de R personalizado e integrado con análisis avanzado. Este usuario publica el modelo como un servicio web para que otros usuarios puedan utilizar sus datos.

* [Clasificador binario](machine-learning-r-csharp-binary-classifier.md)
* [Modelo de clúster](machine-learning-r-csharp-cluster-model.md)
* [Regresión lineal multivariada](machine-learning-r-csharp-multivariate-linear-regression.md)
* [Previsión: suavizado exponencial](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [Previsión con ETS + STL](machine-learning-r-csharp-retail-demand-forecasting.md)
* [Previsión - Media móvil integrada autorregresiva (ARIMA)](machine-learning-r-csharp-arima.md)
* [Análisis de supervivencia](machine-learning-r-csharp-survival-analysis.md)

## <a name="scenario-2-trained-model--specific-data"></a>Escenario 2: modelo entrenado - datos específicos
Un usuario tiene datos que proporcionan útiles predicciones a través del código R, como un ejemplo grande de cuestionarios de personalidad agrupados mediante un algoritmo k-means para predecir el tipo de personalidad del usuario o los datos de encuestas de salud que pueden utilizarse para predecir el riesgo de que un individuo padezca cáncer de pulmón mediante un paquete de análisis de supervivencia basado en R. El usuario publica los datos a través de un servicio web que predice el resultado del nuevo usuario.

## <a name="scenario-3-trained-model--generic-data"></a>Escenario 3: modelo entrenado - datos genéricos
Un usuario tiene datos genéricos (por ejemplo, un conjunto de texto) que permiten crear y aplicar de manera general un servicio web en diferentes tipos de escenarios y casos de uso.

* [Análisis de opiniones basado en léxico](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

## <a name="scenario-4-advanced-calculator"></a>Escenario 4: calculadora avanzada
Un usuario proporciona cálculos avanzados o simulaciones, que no requieren ningún modelo entrenado o ajuste de un modelo a los datos del usuario.

* [Diferencia en la prueba de proporciones](machine-learning-r-csharp-difference-in-two-proportions.md)
* [Conjunto de distribución normal](machine-learning-r-csharp-normal-distribution.md)
* [Conjunto de distribución binomial](machine-learning-r-csharp-binomial-distribution.md)

## <a name="faq"></a>P+F
Para ver las preguntas más frecuentes sobre el uso del servicio web o la publicación en Marketplace, haga clic [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png






<!--HONumber=Jan17_HO2-->


