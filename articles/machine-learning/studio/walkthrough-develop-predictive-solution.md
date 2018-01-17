---
title: "Una solución predictiva para el riesgo de crédito con Machine Learning | Microsoft Docs"
description: "Un tutorial detallado que muestra cómo crear una solución de análisis predictiva para la evaluación del riesgo de crédito en Azure Machine Learning Studio"
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
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: ea5274f6bfd2d6c68509f52d93b6875d3a43a5a0
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Tutorial: Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Azure Machine Learning

En este tutorial se explica con detalle el proceso de desarrollo de una solución de análisis predictivo en Machine Learning Studio. Desarrollaremos un modelo simple en Machine Learning Studio y lo implementaremos como un servicio web Azure Machine Learning, donde podrá realizar predicciones utilizando datos nuevos. 

En este tutorial, se presupone que usó Machine Learning Studio con anterioridad al menos una vez y que tiene ciertos conocimientos sobre los conceptos de aprendizaje automático. Pero no se asume de que sea un experto.

Si nunca ha utilizado **Azure Machine Learning Studio**, sería conveniente que realizara primero el tutorial [Creación del primer experimento de ciencia de datos en Azure Machine Learning Studio](create-experiment.md). Este tutorial lo guiará por primera vez por Machine Learning Studio. Aquí se muestran los conceptos básicos de cómo arrastrar y colocar módulos en el experimento, conectarlos, ejecutar el experimento y examinar los resultados. Otra herramienta que puede resultar útil para comenzar es un diagrama que muestra información general sobre las funcionalidades de Machine Learning Studio. Puede descargarlo e imprimirlo desde aquí: [Diagrama de información general de las funcionalidades de Azure Machine Learning Studio](studio-overview-diagram.md).
 
Si es su primera vez en el campo del aprendizaje automático en general, hay una serie de vídeos que puede resultarle útil. La serie se llama [Ciencia de datos para principiantes](data-science-for-beginners-the-5-questions-data-science-answers.md) y le puede brindar una excelente introducción al aprendizaje automático con un lenguaje y conceptos de uso diario.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>El problema

Suponga que necesita predecir el riesgo de crédito de un individuo en función de la información que se proporcionó en una solicitud de crédito.  

La evaluación de riesgos de un crédito es un problema complejo, pero podemos simplificarlo un poco para usarlo en este tutorial. La usaremos como ejemplo de cómo puede crear una solución de análisis predictivo con Microsoft Azure Machine Learning. Para hacerlo, usamos Azure Machine Learning Studio y un servicio web Machine Learning.  

## <a name="the-solution"></a>La solución

En este detallado tutorial, comenzamos con datos de riesgo de crédito y desarrollaremos y entrenaremos un modelo predictivo según esos datos. Luego, implementaremos el modelo como servicio web para que otros usuarios puedan usarlo para una evaluación de riesgos de crédito.

Para crear esta solución de evaluación de riesgos de crédito, seguiremos estos pasos:  

1. [Creación de un área de trabajo de Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carga de los datos existentes](walkthrough-2-upload-data.md)
3. [Creación de un experimento](walkthrough-3-create-new-experiment.md)
4. [Entrenamiento y evaluación de los modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implementación del servicio web](walkthrough-5-publish-web-service.md)
6. [Acceso al servicio web](walkthrough-6-access-web-service.md)

> [!TIP] 
> Puede encontrar una copia de trabajo del experimento que desarrollamos en este tutorial en la [Galería de Azure AI](https://gallery.cortanaintelligence.com). Vaya a **[Walkthrough - Credit risk prediction](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)** (Tutorial: predicción de riesgos de crédito) y haga clic en **Abrir en Studio** para descargar una copia del experimento al área de trabajo de Machine Learning Studio.
> 
> Este tutorial se basa en una versión simplificada del experimento de ejemplo, [Clasificación binaria: predicción de riesgos de crédito](http://go.microsoft.com/fwlink/?LinkID=525270), también disponible en la [galería](http://gallery.cortanaintelligence.com/).
