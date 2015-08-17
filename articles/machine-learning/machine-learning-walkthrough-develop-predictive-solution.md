<properties
	pageTitle="Una solución predictiva para el riesgo de crédito con Aprendizaje automático | Microsoft Azure"
	description="Un tutorial detallado que muestra cómo crear una solución de análisis predictiva para la evaluación del riesgo de crédito en Estudio de aprendizaje automático de Azure"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/10/2015"
	ms.author="garye"/>


# Tutorial: Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure

Suponga que necesita predecir el riesgo de crédito de un individuo en función de la información que se proporciona en una solicitud de crédito.

Es cierto que se trata de un problema difícil. Pero vamos a simplificar un poco los parámetros del asunto. Después lo usaremos como ejemplo de cómo podría utilizar el Aprendizaje automático de Microsoft Azure con el Estudio de aprendizaje automático y el servicio web de Aprendizaje automático para crear dicha solución de análisis predictivo.

En este tutorial detallado, seguiremos el proceso de desarrollar un modelo de análisis predictivo en Estudio de aprendizaje automático y, a continuación, publicarlo como un servicio web de Aprendizaje automático de Azure. Comenzaremos con los datos de riesgo de crédito que se encuentran disponibles públicamente, desarrollaremos y entrenaremos un modelo predictivo en función de esos datos y luego publicaremos el modelo como un servicio web que otros pueden usar para la evaluación del crédito de riesgo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Abra Estudio de aprendizaje automático: [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Para obtener más información acerca de cómo empezar a utilizar Estudio de aprendizaje automático, consulte [Inicio de Estudio de aprendizaje automático de Microsoft Azure](https://studio.azureml.net/).

Para crear una solución de evaluación del riesgo de crédito, seguiremos estos pasos:

1.	[Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Publicación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

Este tutorial se basa en una versión simplificada del [experimento de ejemplo de predicción de riesgo de crédito](../machine-learning-sample-credit-risk-prediction.md) que se incluye en el Estudio de aprendizaje automático.
 

<!---HONumber=August15_HO6-->