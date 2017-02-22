---
title: "Una solución predictiva para el riesgo de crédito con Machine Learning | Microsoft Docs"
description: "Un tutorial detallado que muestra cómo crear una solución de análisis predictiva para la evaluación del riesgo de crédito en Estudio de aprendizaje automático de Azure"
keywords: "riesgo de crédito, solución de análisis predictivo, evaluación de riesgos"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Tutorial: Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure

En este tutorial, explicaremos con detalle el proceso de desarrollo de una solución en Machine Learning Studio. Vamos a desarrollar un modelo de análisis predictivo en Machine Learning Studio y lo implementaremos como un servicio web Azure Machine Learning, donde podrá realizar predicciones utilizando datos nuevos. 

> [!TIP]
> En este tutorial, se presupone que ha utilizado Machine Learning Studio con anterioridad al menos una vez y que tiene ciertos conocimientos sobre los conceptos de aprendizaje automático, aunque no se espera que sea un experto.
> 
>Si nunca ha utilizado **Azure Machine Learning Studio**, sería conveniente que realizara primero el tutorial [Creación del primer experimento de ciencia de datos en Azure Machine Learning Studio](machine-learning-create-experiment.md). Dicho tutorial le ayudará a utilizar Machine Learning Studio por primera vez, ya que se explican conceptos básicos acerca de cómo arrastrar y colocar módulos en el experimento, conectarlos, ejecutar el experimento y examinar los resultados.
>
>Si está familiarizado con el aprendizaje automático, la serie de vídeos [Ciencia de datos para principiantes](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) podría ser un buen lugar para comenzar. Esta serie de vídeos es una excelente introducción al aprendizaje automático mediante el uso de conceptos y lenguaje cotidianos.
> 

## <a name="the-problem"></a>El problema

Suponga que necesita predecir el riesgo de crédito de un individuo en función de la información que se proporciona en una solicitud de crédito.  

No hay duda de que evaluar el riesgo crediticio es un problema complejo, pero vamos a simplificar un poco los parámetros del asunto. Después, usaremos este caso para ilustrar un ejemplo de cómo puede utilizar Microsoft Azure Machine Learning con Machine Learning Studio y el servicio web Machine Learning para crear una solución de análisis predictivo.  

## <a name="the-solution"></a>La solución

En este detallado tutorial, comenzaremos con datos de riesgo crediticio que están disponibles públicamente, desarrollaremos y entrenaremos un modelo predictivo en función de esos datos y luego implementaremos el modelo como un servicio web que otros pueden usar para evaluar el riesgo crediticio.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para crear una solución de evaluación del riesgo de crédito, seguiremos estos pasos:  

1. [Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3. [Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

Este tutorial se basa en una versión simplificada del experimento de ejemplo [Binary Classification: Credit risk prediction](http://go.microsoft.com/fwlink/?LinkID=525270) (Clasificación binaria: predicción de riesgo de crédito) de la [Galería de Cortana Intelligence](http://gallery.cortanaintelligence.com/).


> [!TIP]
> Para descargar e imprimir un diagrama con información general de las funcionalidades de Estudio de aprendizaje automático, consulte [Diagrama de información general de las funcionalidades de Estudio de aprendizaje automático de Azure](machine-learning-studio-overview-diagram.md).
> 
> 



<!--HONumber=Dec16_HO3-->


