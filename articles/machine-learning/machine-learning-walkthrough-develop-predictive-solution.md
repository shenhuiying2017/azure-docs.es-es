<properties 
	pageTitle="Desarrollo de una solución predictiva con Aprendizaje automático | Azure" 
	description="Tutorial sobre la creación de un experimento de análisis predictivo en Estudio de aprendizaje automático de Microsoft Azure" 
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
	ms.topic="article" 
	ms.date="08/06/2014" 
	ms.author="garye"/>

# Desarrollo de una solución predictiva con Aprendizaje automático de Azure.

Suponga que necesita predecir el riesgo de crédito de un individuo en función de la información que se proporciona en una solicitud de crédito.

Es cierto que se trata de un problema difícil. Pero vamos a simplificar un poco los parámetros de la cuestión y usarlos como ejemplo de cómo podría utilizar el Aprendizaje automático de Microsoft Azure con el Estudio de aprendizaje automático y el servicio API de Aprendizaje automático para crear dicha solución de análisis predictivo.

En este tutorial, seguiremos el proceso de desarrollo de un modelo de análisis predictivo en el Estudio de aprendizaje automático y luego lo publicaremos en el servicio API de Aprendizaje automático. Comenzaremos con los datos de riesgo de crédito que se encuentran disponibles públicamente, desarrollaremos y entrenaremos un modelo predictivo en función de esos datos y luego publicaremos el modelo como un servicio web que otros pueden usar.

Seguiremos estos pasos:

1.  [Creación de un área de trabajo de Aprendizaje automático][Creación de un área de trabajo de Aprendizaje automático]
2.  [Carga de los datos existentes][Carga de los datos existentes]
3.  [Creación de un nuevo experimento][Creación de un nuevo experimento]
4.  [Entrenamiento y evaluación de los modelos][Entrenamiento y evaluación de los modelos]
5.  [Publicación del servicio web][Publicación del servicio web]
6.  [Acceso al servicio web][Acceso al servicio web]

Este tutorial se basa en una versión simplificada del
[experimento de ejemplo de predicción de riesgo de crédito][experimento de ejemplo de predicción de riesgo de crédito] que se incluye en el Estudio de aprendizaje automático.

  [Creación de un área de trabajo de Aprendizaje automático]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Carga de los datos existentes]: ../machine-learning-walkthrough-2-upload-data/
  [Creación de un nuevo experimento]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Entrenamiento y evaluación de los modelos]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Publicación del servicio web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Acceso al servicio web]: ../machine-learning-walkthrough-6-access-web-service/
  [experimento de ejemplo de predicción de riesgo de crédito]: ../machine-learning-sample-credit-risk-prediction/

<!--HONumber=46--> 

<!--HONumber=46--> 
 