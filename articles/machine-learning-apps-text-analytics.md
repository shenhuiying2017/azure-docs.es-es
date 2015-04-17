<properties
	pageTitle="Aplicación de aprendizaje automático: Servicio de análisis de texto para analizar la opinión | Azure "
	description="La API de análisis de texto es un conjunto de análisis de texto que se encuentra integrado en el aprendizaje automático de Azure. La API puede utilizarse para analizar el texto no estructurado para tareas como el análisis de opiniones y la extracción de frases clave."
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/> 

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/27/2015"
	ms.author="luisca"/>


# Servicio de análisis de texto de aprendizaje automático#
##Información general
API de análisis de texto es un conjunto de [servicios web]( https://datamarket.azure.com/dataset/amla/text-analytics) de análisis de texto integrados con el aprendizaje automático de Azure. La API puede utilizarse para analizar el texto no estructurado para tareas como el análisis de opiniones y la extracción de frases clave. No se necesitan datos de formación para utilizar esta API, simplemente pase los datos del texto. Por el momento solo se admite el idioma inglés. Esta API usa técnicas de procesamiento de lenguaje natural avanzadas.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 
 
## Análisis de opiniones##
La API devuelve una puntuación numérica comprendida entre 0 y 1. Las puntuaciones cercanas a 1 indican una opinión positiva, y las puntuaciones cercanas a 0 indican una opinión negativa. La puntuación de las opiniones se genera mediante técnicas de clasificación. Las funciones de entrada en el clasificador incluyen n-gramas, funciones generadas a partir de etiquetas de categorías gramaticales e incrustaciones de palabras.
 
## Extracción de la frase clave##
La API devuelve una lista de cadenas que representan los puntos clave de la conversación en el texto de entrada. Empleamos las técnicas del kit de herramientas de procesamiento de lenguaje natural sofisticadas de Microsoft Office.

## Definición de la API##

###GetSentiment###

**URL**	

https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Solicitud de ejemplo**

En la siguiente llamada GET, se precisa para la opinión de la frase *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Encabezados:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey);  

Puede obtener la clave de la cuenta [aquí]( https://datamarket.azure.com/account/keys). 

**Respuesta de ejemplo**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL**

https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Solicitud de ejemplo**

En la siguiente llamada GET, se precisa la opinión de las frases clave en el texto *Era un hotel maravilloso para alojarse, con una decoración única y personal amable*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Encabezados:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey)

Puede obtener la clave de la cuenta [aquí]( https://datamarket.azure.com/account/keys). 


**Respuesta de ejemplo**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 

<!--HONumber=49-->