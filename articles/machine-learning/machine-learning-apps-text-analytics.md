<properties
	pageTitle="API de aprendizaje automático: análisis de texto | Microsoft Azure"
	description="API de análisis de texto proporcionadas por Aprendizaje automático de Azure. Se puede usar para analizar texto no estructurado para análisis de opiniones, extracción de frases clave y detección de idiomas."
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="onewth"/>


# API de aprendizaje automático: análisis de texto de opinión, extracción de frases clave y detección de idiomas

## Información general

La API de análisis de texto es un conjunto de [servicios web](https://datamarket.azure.com/dataset/amla/text-analytics) de análisis de texto creado con Aprendizaje automático de Azure. La API puede usarse para analizar el texto no estructurado para tareas como el análisis de opiniones, la extracción de frases clave y la detección de idiomas. No se necesitan datos de formación para usar esta API, simplemente pase los datos del texto. Esta API usa técnicas de procesamiento de lenguaje natural avanzadas para proporcionar las mejores predicciones.

Puede ver el análisis de texto en acción en nuestro [sitio de demostración](https://text-analytics-demo.azurewebsites.net/), donde también encontrará [ejemplos](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) sobre cómo implementar el análisis de texto en C# y Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## Análisis de opiniones

La API devuelve una puntuación numérica comprendida entre 0 y 1. Las puntuaciones cercanas a 1 indican una opinión positiva, y las puntuaciones cercanas a 0 indican una opinión negativa. La puntuación de las opiniones se genera mediante técnicas de clasificación. Las funciones de entrada en el clasificador incluyen n-gramas, funciones generadas a partir de etiquetas de categorías gramaticales e incrustaciones de palabras. Actualmente, el inglés es el único idioma que se admite.
 
## Extracción de la frase clave

La API devuelve una lista de cadenas que representan los puntos clave de la conversación en el texto de entrada. Empleamos las técnicas del kit de herramientas de procesamiento de lenguaje natural sofisticadas de Microsoft Office. Actualmente, el inglés es el único idioma que se admite.

## Detección de idiomas

La API devuelve el idioma detectado y una puntuación numérica comprendida entre 0 y 1. Las puntuaciones cercanas a 1 indican una certeza del 100 % de que el idioma identificado es verdadero. Se admiten un total de 120 idiomas.

---

## Definición de la API

### Encabezados

Asegúrese de incluir los encabezados correctos en la solicitud, que debería ser como sigue:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Puede encontrar la clave de su cuenta en [Microsoft Azure Marketplace ](https://datamarket.azure.com/account/keys).

---

## API de respuesta única

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Solicitud de ejemplo**

En la siguiente llamada, vamos a solicitar el análisis de opinión para la frase "Hello World":

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Esto devolverá una respuesta como sigue:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Solicitud de ejemplo**

En la siguiente llamada, vamos a solicitar las frases clave encontradas en el texto "It was a wonderful hotel to stay at, with unique decor and friendly staff":

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Esto devolverá una respuesta como sigue:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Solicitud de ejemplo**

En la siguiente llamada GET, se precisa la opinión de las frases clave en el texto *Hello World*.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

Esto devolverá una respuesta como sigue:

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**Parámetros opcionales**

`NumberOfLanguagesToDetect` es un parámetro opcional. El valor predeterminado es 1.

---

## API de lote

El servicio de análisis de texto permite realizar extracciones de opiniones y frases clave en el modo por lotes. Tenga en cuenta que cada uno de los registros puntuó recuentos como una transacción. A modo de ejemplo, si solicita una opinión para 1000 registros en una sola llamada, se deducirán 1000 transacciones.

Tenga en cuenta que los identificadores especificados en el sistema son los identificadores que devuelve el sistema. El servicio web no comprueba que estos identificadores sean únicos. Es responsabilidad del autor de la llamada hacerlo.


### GetSentimentBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Solicitud de ejemplo**

En la siguiente llamada POST, vamos a solicitar las opiniones sobre las frases "Hello World", "Hello Foo World" y "Hello My World" en el cuerpo de la solicitud:

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Cuerpo de la solicitud:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	    {"Id":"2","Text":"hello foo world"},
    	    {"Id":"3","Text":"hello my world"},
	]}

En la respuesta siguiente, obtendrá la lista de puntuaciones asociadas a sus identificadores de texto:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Solicitud de ejemplo**

En este ejemplo, vamos a solicitar la lista de opiniones para las frases clave de los siguientes textos:

* "It was a wonderful hotel to stay at, with unique decor and friendly staff"
* "It was an amazing build conference, with very interesting talks"
* "The traffic was terrible, I spent three hours going to the airport"

Esta solicitud se realiza como una llamada POST al punto de conexión :

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Cuerpo de la solicitud:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

En la respuesta siguiente, obtendrá la lista de las frases clave asociadas a sus identificadores de texto:

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

En la siguiente llamada POST, vamos a solicitar la detección de idioma para las dos entradas de texto:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Cuerpo de la solicitud:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

Esto devuelve la respuesta siguiente, donde se detecta inglés en la primera entrada y francés en la segunda:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

<!---HONumber=AcomDC_1125_2015-->