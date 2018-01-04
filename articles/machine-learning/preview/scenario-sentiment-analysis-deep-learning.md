---
title: "Análisis de sentimiento mediante aprendizaje profundo con Azure Machine Learning | Microsoft Docs"
description: "Realización del análisis de sentimiento mediante aprendizaje profundo de Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: miprasad
ms.openlocfilehash: 255c39ea21378aae23fc61da6dc882138fb66ab1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Análisis de sentimiento mediante aprendizaje profundo con Azure Machine Learning

El análisis de sentimiento es una tarea bien conocida perteneciente al procesamiento de lenguaje natural. Dado un conjunto de textos, el objetivo es determinar el sentimiento que produce ese texto. El objetivo de esta solución es utilizar CNTK como back-end para Keras (una biblioteca de niveles de modelo, que proporciona bloques de creación de alto nivel para desarrollar modelos de aprendizaje profundo) e implementar el análisis de sentimiento en críticas de películas.

La solución se encuentra en https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería

Siga este vínculo al repositorio de GitHub público:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Información general del caso de uso

La explosión en la cantidad de datos y la proliferación de dispositivos móviles ha generado muchas oportunidades para que los clientes expresen sus impresiones y actitudes sobre todas las cosas en cualquier momento. Esta opinión o "sentimiento" se genera a menudo a través de canales sociales en forma de críticas, chats, recursos compartidos, me gusta, tweets, etc. Estos sentimientos pueden ser muy útiles para las empresas que desean mejorar los productos y servicios, tomar decisiones mejor fundamentadas y mejorar la promoción de sus marcas.

Para obtener valor del análisis de sentimiento, las empresas deben tener la capacidad de extraer grandes cantidades de datos no estructurados de redes sociales para obtener una información que se pueda procesar. En este ejemplo, se desarrollarán modelos de aprendizaje profundo para realizar análisis de sentimiento de críticas de películas con AMLWorkbench

## <a name="prerequisites"></a>requisitos previos

* Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).

* Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](./quickstart-installation.md) para instalar el programa y crear un área de trabajo.

* Para la operacionalización, es mejor si dispone del motor de Docker instalado y en ejecución local. Si no es así, puede utilizar la opción de un clúster. Sin embargo, la ejecución de un servicio Azure Container Service (ACS) puede resultar costosa.

* En esta solución se da por supuesto que está ejecutando Azure Machine Learning Workbench en Windows 10 con el motor de Docker instalado localmente. Si usa macOS, las instrucciones son en gran medida las mismas.

## <a name="data-description"></a>Descripción de los datos

El conjunto de datos que se usa para este ejemplo es un pequeño conjunto de datos de creación manual que se encuentra en la [carpeta de datos](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

La primera columna contiene las críticas de películas y la segunda columna contiene los sentimientos (0: negativo, 1: positivo). El conjunto de datos se usa simplemente para fines de demostración pero, normalmente, si desea obtener unas puntuaciones de sentimiento más sólidas, necesitará un conjunto de datos más grande. Por ejemplo, el [problema de clasificación de sentimientos de críticas de películas de IMDB](https://keras.io/datasets/#datasets ) de Keras consta de un conjunto de datos de críticas de 25.000 películas de IMDB, etiquetadas por sentimiento (positivo o negativo). La intención de este laboratorio es mostrar cómo realizar un análisis de sentimiento mediante aprendizaje profundo con Azure Machine Learning Workbench.

## <a name="scenario-structure"></a>Estructura del escenario

La estructura de carpetas se organiza de la siguiente forma:

1. Todo el código relacionado con el análisis de sentimiento mediante Azure Machine Learning Workbench se encuentra en esta carpeta raíz
2. data: contiene el conjunto de datos utilizado en la solución
3. docs: contiene todos los laboratorios prácticos

El orden de los laboratorios prácticos para llevar a cabo la solución es el siguiente:

| Orden| Nombre de archivo | Archivos relacionados |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 4 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | 'Operaionalization' |

## <a name="conclusion"></a>Conclusión

En conclusión, esta solución le muestra cómo usar el aprendizaje profundo para realizar el análisis de sentimiento con Azure Machine Learning Workbench. También se pueden usar modelos de HDF5.
