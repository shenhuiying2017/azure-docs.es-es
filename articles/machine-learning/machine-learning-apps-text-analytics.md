<properties
	pageTitle="Aplicación Aprendizaje de máquina: Análisis de opinión | Microsoft Azure"
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
	ms.date="09/11/2015"
	ms.author="luisca"/>


# Aplicación Aprendizaje automático: Text Analytics Service para el análisis de las opiniones#
##Información general
La API de análisis de texto es un conjunto de [servicios web](https://datamarket.azure.com/dataset/amla/text-analytics) de análisis de texto creado con Aprendizaje automático de Azure. La API puede utilizarse para analizar el texto no estructurado para tareas como el análisis de opiniones y la extracción de frases clave. No se necesitan datos de formación para utilizar esta API, simplemente pase los datos del texto. Por el momento solo se admite el idioma inglés. Esta API usa técnicas de procesamiento de lenguaje natural avanzadas.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
## Análisis de opiniones##
La API devuelve una puntuación numérica comprendida entre 0 y 1. Las puntuaciones cercanas a 1 indican una opinión positiva, y las puntuaciones cercanas a 0 indican una opinión negativa. La puntuación de las opiniones se genera mediante técnicas de clasificación. Las funciones de entrada en el clasificador incluyen n-gramas, funciones generadas a partir de etiquetas de categorías gramaticales e incrustaciones de palabras.
 
## Extracción de la frase clave##
La API devuelve una lista de cadenas que representan los puntos clave de la conversación en el texto de entrada. Empleamos las técnicas del kit de herramientas de procesamiento de lenguaje natural sofisticadas de Microsoft Office.

## Definición de la API##

###GetSentiment###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Solicitud de ejemplo**

En la siguiente llamada GET, vamos a solicitar la opinión sobre la frase *Hola a todos*.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Encabezados:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

La clave de la cuenta se puede obtener [aquí](https://datamarket.azure.com/account/keys).

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

En la siguiente llamada GET, vamos a solicitar la opinión sobre las frases clave del texto *Era un hotel maravilloso donde quedarse, con una decoración única y un personal amable*.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Encabezados:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

La clave de la cuenta se puede obtener [aquí](https://datamarket.azure.com/account/keys).


**Respuesta de ejemplo**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 
---

##API de lote
El servicio de análisis de texto permite realizar extracciones de opiniones y frases clave en el modo por lotes. Para GetSentimentBatch y GetKeyPhrasesBatch, cada uno de los registros puntuados cuenta como una transacción. Por lo tanto, por ejemplo, si obtiene una opinión sobre 1000 registros en una sola llamada, se deducirán 1000 transacciones.

###GetSentimentBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Solicitud de ejemplo**

En la siguiente llamada POST, vamos a solicitar las opiniones sobre las siguientes frases: Hola a todos, Hola tontos, Hola a mis colegas en el cuerpo de la solicitud

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Cuerpo:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	{"Id":"2","Text":"hello foo world"},
    	{"Id":"3","Text":"hello my world"},
	]}


Encabezados:

	Authorization: Basic <creds>
	Accept: application/json

	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  


La clave de la cuenta se puede obtener [aquí](https://datamarket.azure.com/account/keys).

**Respuesta de ejemplo**

En la respuesta siguiente, obtendrá la lista de puntuaciones asociadas a sus identificadores de texto:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", "SentimentBatch":
		[{"Score":0.9549767,"Id":"1"},
		 {"Score":0.7767222,"Id":"2"},
		 {"Score":0.8988889,"Id":"3"}
		],  
		"Errors":[
		   {"Id": "4", Message:"Record cannot be null/empty"}
		]
	}


---

###GetKeyPhrasesBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Solicitud de ejemplo**

En la siguiente llamada POST, vamos a solicitar la lista de opiniones para las frases clave de los siguientes textos:

*Era un hotel maravilloso donde quedarse, con una decoración única y un personal amable*
 
*Fue una conferencia muy bien desarrollada, con charlas muy interesantes*

*El tráfico fue terrible, tardé tres horas en ir al aeropuerto*



	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Cuerpo:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

Encabezados:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

La clave de la cuenta se puede obtener [aquí](https://datamarket.azure.com/account/keys).


**Respuesta de ejemplo**

En la respuesta siguiente, obtendrá la lista de las frases clave asociadas a sus identificadores de texto:

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[
		   {"Id": "4", Message:"Record cannot be null/empty"}
		]
	}

---

**Notas relacionadas con el procesamiento por lotes**

Los identificadores especificados en el sistema son los identificadores que devuelve el sistema. El servicio web no comprueba que los identificadores sean únicos. Es responsabilidad del autor de la llamada hacerlo.
 

<!---HONumber=Nov15_HO1-->