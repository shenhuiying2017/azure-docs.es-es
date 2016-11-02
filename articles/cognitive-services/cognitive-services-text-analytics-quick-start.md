<properties
	pageTitle="Guía de inicio rápido: API de análisis de texto de aprendizaje automático | Microsoft Azure"
	description="Análisis de texto de Aprendizaje automático de Azure - Guía de inicio rápido"
	services="cognitive-services"
	documentationCenter=""
	authors="onewth"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="onewth"/>

# Introducción a Text Analytics API para detectar opiniones, frases clave, temas e idioma

<a name="HOLTop"></a>

En este documento se explica cómo incorporar su servicio o aplicación para usar las [API de análisis de texto](//go.microsoft.com/fwlink/?LinkID=759711). Puede usar estas API para detectar opiniones, frases clave, temas e idioma del texto. [Haga clic aquí para ver una demostración interactiva de la experiencia.](//go.microsoft.com/fwlink/?LinkID=759712)

Consulte las [definiciones de API](//go.microsoft.com/fwlink/?LinkID=759346) para obtener la documentación técnica de las API.

Esta guía se refiere a la versión 2 de las API. Para obtener detalles sobre la versión 1 de las API, [consulte este documento](../machine-learning/machine-learning-apps-text-analytics.md).

Al final de este tutorial, podrá detectar lo siguiente mediante programación:

- **Opiniones**. ¿El texto es positivo o negativo?

- **Frases clave**. ¿Qué se analiza en un artículo?

- **Temas**. ¿Qué se analiza en varios artículos?

- **Idiomas**. ¿En qué idioma está escrito el texto?

Tenga en cuenta que esta API cobra una transacción por documento enviado. A modo de ejemplo, si solicita una opinión para 1000 documentos en una sola llamada, se deducirán 1000 transacciones.



<a name="Overview"></a>
## Información general ##

Este documento es una guía paso a paso. Nuestro objetivo es guiarle a través de los pasos necesarios para entrenar un modelo, así como indicarle los recursos que le permitirán ponerlo en producción. La duración de este ejercicio es de unos 30 minutos.

Para realizar estas tareas, necesitará un editor y llamar a los puntos de conexión RESTful en el lenguaje de su preferencia.

Comencemos.

## Tarea 1: Registrarse en las API de análisis de texto ####

En esta tarea, se registrará en el servicio de análisis de texto.

1. Vaya a **Cognitive Services** en el [Portal de Azure](//go.microsoft.com/fwlink/?LinkId=761108) y asegúrese de que **Análisis de texto** esté seleccionado como el "Tipo de API".

1. Seleccione un plan. Puede seleccionar el **nivel Gratis de 5 000 transacciones/mes**. Como se trata de un plan gratuito, no se le cobrará por utilizar el servicio. No se olvide de iniciar sesión en su suscripción de Azure.

1. Complete los otros campos y cree su cuenta.

1. Una vez que se registre en el análisis de texto, encuentre su **Clave de API**. Copie la clave principal, puesto que la necesitará cuando utilice los servicios de API.


## Tarea 2: Detectar opiniones, frases clave e idiomas ####

Es fácil detectar opiniones, frases clave e idiomas en el texto. Obtendrá de manera programática los mismos resultados que devuelve la [experiencia de demostración](//go.microsoft.com/fwlink/?LinkID=759712).

>[AZURE.TIP] En el caso del análisis de opiniones, se recomienda dividir el texto en oraciones. Generalmente, esto lleva a una mayor precisión en las predicciones de opiniones.

Tenga en cuenta que los idiomas admitidos son los siguientes:

| Característica | Códigos de idioma admitidos |
|:-----|:----|
| Opinión | `en` (Inglés), `es` (Español), `fr` (Francés), `pt` (Portugués) |
| Frases clave | `en` (Inglés), `es` (Español), `de` (Alemán), `ja` (Japonés) |


1. Deberá definir los encabezados en lo siguiente. Tenga en cuenta que JSON actualmente es el único formato de entrada aceptado para las API. XML no se admite.

		Ocp-Apim-Subscription-Key: <your API key>
		Content-Type: application/json
		Accept: application/json

1. A continuación, aplique el formato JSON a las filas de entrada. El formato es el mismo para las opiniones, las frases clave y el idioma. Tenga en cuenta que cada id. debe ser único y será el id. que el sistema devuelva. El tamaño máximo de un documento que se puede enviar es de 10 KB y el tamaño máximo total de las entradas enviadas es de 1 MB. No se pueden enviar más de 1 000 documentos en una llamada. Existe una limitación de velocidad de 100 llamadas por minuto, por lo que se recomienda que envíe grandes cantidades de documentos en una sola llamada. Lenguaje es un parámetro opcional que se debe especificar si se analiza texto en un idioma que no es el inglés. A continuación, se muestra un ejemplo de la entrada, donde se ha incluido el parámetro opcional `language` para analizar opiniones o extraer frases clave:

		{
			"documents": [
				{
					"language": "en",
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"language": "en",
					"id": "100",
					"text": "Final document"
				}
			]
		}

1. Realice una llamada **POST** al sistema con la entrada para opiniones, frases clave e idioma. Las direcciones URL tendrán el siguiente aspecto:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. Esta llamada devolverá una respuesta con formato JSON con los id. y las propiedades detectadas. El siguiente es un ejemplo de la salida para las opiniones (se excluyen los detalles de error). En el caso de las opiniones, se devolverá una puntuación entre 0 y 1 para cada documento:

        // Sentiment response
		{
		  	"documents": [
				{
					"id": "1",
					"score": "0.934"
		        },
                ...
                {
					"id": "100",
					"score": "0.002"
		        },
			]
		}

        // Key phrases response
        {
		  	"documents": [
				{
					"id": "1",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
                ...
                {
					"id": "100",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
			]
		}

        // Languages response
        {
		  	"documents": [
				{
					"id": "1",
					"detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
		        },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
		        }
			]
		}


## Tarea 3: Detectar temas en el cuerpo del texto ####

Se trata de una API recién publicada que devuelve los temas detectados más importantes para obtener los registros de texto enviados. Se identifica un tema con una frase clave, que puede ser una o más palabras relacionadas. La API se ha diseñado para funcionar bien con texto escrito humano y corto, como revisiones y comentarios del usuario.

Esta API requiere **un mínimo de 100 registros de texto** que enviar, pero se ha diseñado para detectar temas en cientos o miles de registros. Los registros que no estén en inglés o que tengan menos de 3 palabras se descartarán y, por lo tanto, no se les asignarán temas. En el caso de la detección de temas, el tamaño máximo de un documento que se puede enviar es de 30 KB y el tamaño máximo total de las entradas enviadas es de 30 MB. La detección de temas tiene una velocidad limitada a 5 envíos cada 5 minutos.

Hay dos parámetros de entrada **opcionales** que pueden ayudar a mejorar la calidad de los resultados:

- **Palabras no significativas.** Estas palabras y sus formas cercanas (por ejemplo, plurales) se excluirán de toda la canalización de detección de temas. Use este parámetro para las palabras comunes (por ejemplo, "problema", "error" y "usuario" pueden ser opciones adecuadas para las quejas que los clientes puedan tener sobre el software). Cada cadena debe tener solo una palabra.
- **Frases no significativas**: estas frases se excluirán de la lista de temas devueltos. Use este parámetro para excluir temas genéricos que no desea ver en los resultados. Por ejemplo, "Microsoft" y "Azure" podrían ser opciones adecuadas de temas a excluir. Las cadenas pueden contener varias palabras.

Siga estos pasos para detectar temas en el texto.

1. Aplique el formato JSON a la entrada. Esta vez, puede definir las palabras y frases no significativas.

		{
			"documents": [
				{
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"id": "100",
					"text": "Final document"
				}
			],
			"stopWords": [
				"issue", "error", "user"
			],
			"stopPhrases": [
				"Microsoft", "Azure"
			]
		}

1. Use los mismos encabezados definidos en la Tarea 2 para realizar una llamada **POST** al punto de conexión de temas:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. Esto devolverá un valor `operation-location` como el encabezado de la respuesta, donde el valor es la dirección URL para consultar los temas resultantes:

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. Consulte periódicamente el valor `operation-location` devuelto con una solicitud **GET**. Se recomienda una vez por minuto.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. El punto de conexión devolverá una respuesta que incluya `{"status": "notstarted"}` antes del procesamiento, `{"status": "running"}` durante el procesamiento y `{"status": "succeeded"}` con la salida una vez que se complete. Luego, puede usar la salida que tendrá el siguiente formato (observe que los detalles como formato de error y fechas se excluyen de este ejemplo):

		{
			"status": "succeeded",
			"operationProcessingResult": {
			  	"topics": [
                    {
					    "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
					    "score": "5"
					    "keyPhrase": "first topic name"
                    },
                    ...
                    {
					    "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
					    "score": "3"
					    "keyPhrase": "final topic name"
                    }
                ],
			  	"topicAssignments": [
                    {
					    "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
					    "documentId": "1",
					    "distance": "0.354"
                    },
                    ...
                    {
					    "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
					    "documentId": "55",
					    "distance": "0.758"
                    },            
                ]
			}
		}

Tenga en cuenta que la respuesta correcta para los temas del punto de conexión `operations` tendrá el siguiente esquema:

	{
    		"topics" : [{
        		"id" : "string",
        		"score" : "number",
        		"keyPhrase" : "string"
    		}],
    		"topicAssignments" : [{
        		"documentId" : "string",
        		"topicId" : "string",
        		"distance" : "number"
    		}],
    		"errors" : [{
        		"id" : "string",
        		"message" : "string"
    		}]
    	}

Las explicaciones de cada parte de la respuesta son las siguientes:

**topics**

| Clave | Description |
|:-----|:----|
| id | Un identificador único para cada tema. |
| de la aplicación | Recuento de documentos asignados al tema. |
| keyPhrase | Una palabra o frase de resumen para el tema. |

**topicAssignments**

| Clave | Description |
|:-----|:----|
| documentId | Identificador del documento. Equivale al identificador incluido en la entrada. |
| topicId | El identificador de tema que se ha asignado al documento. |
| distancia | Puntuación de afiliación de documento a tema entre 0 y 1. Cuando menor sea la puntuación de la distancia mayor será la afiliación del tema. |

**errors**

| Clave | Description |
|:-----|:----|
| id | Identificador único del documento de entrada al que se refiere el error. |
| message | Mensaje de error. |

## Pasos siguientes ##

¡Enhorabuena! Completó el tutorial sobre cómo usar el análisis de textos en sus datos. Es posible que ahora quiera obtener información sobre una herramienta como [Power BI](//powerbi.microsoft.com) para visualizar los datos, así como automatizar la información para brindarle una vista de los datos de texto en tiempo real.

Para ver cómo pueden usarse las funcionalidades de análisis de textos, como una opinión, como parte de un bot, vea el ejemplo [Emotional Bot](http://docs.botframework.com/es-ES/bot-intelligence/language/#example-emotional-bot) en el sitio de Bot Framework.

<!---HONumber=AcomDC_0914_2016-->