<properties
	pageTitle="Actualización a la versión 2 de la API de Text Analytics | Microsoft Azure"
	description="Text Analytics de Aprendizaje automático de Azure: actualización a la versión 2"
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

# Actualización a la versión 2 de la API de Text Analytics #

Esta guía le guiará por el proceso de actualizar el código tanto con la [primera versión de la API](../machine-learning/machine-learning-apps-text-analytics.md) como con la segunda.

Si no ha utilizado la API y desea obtener más información, puede **[hacerlo aquí](//go.microsoft.com/fwlink/?LinkID=759711)** o **[siga la Guía de inicio rápido](//go.microsoft.com/fwlink/?LinkID=760860)**. Para obtener referencia técnica, consulte la **[definición de la API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### Parte 1: obtener una clave nueva ###

En primer lugar, debe obtener una nueva clave de API en el **Portal de Azure**:

1. Navegue hasta el servicio de Text Analytics a través de la [galería de Cortana Intelligence](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). Aquí, también encontrará vínculos a la documentación y ejemplos de código.

1. Haga clic en **Regístrese**. Este vínculo le llevará al portal de administración de Azure, donde puede registrarse en el servicio.

1. Seleccione un plan. Puede seleccionar el **nivel Gratis de 5 000 transacciones/mes**. Como se trata de un plan gratuito, no se le cobrará por utilizar el servicio. No se olvide de iniciar sesión en su suscripción de Azure.

1. Después de registrarse en Text Analytics, se le proporcionará una **clave de API**. Copie esta clave, ya que la necesitará para los servicios de API.

### Parte 2: actualizar los encabezados ###

Actualice los valores de encabezado enviados tal y como se muestra a continuación. Tenga en cuenta que la clave de cuenta ya no está codificada.

**Versión 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Versión 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### Parte 3: actualizar la dirección URL base ###

**Versión 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Versión 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### Parte 4a: actualizar los formatos de opinión, frases clave e idiomas ###

#### Extremos ####

Los extremos GET han quedado en desuso, por lo que todas las entradas deben enviarse como solicitudes POST. Actualice los extremos a los que se muestran a continuación.

| |Extremo único de la versión 1|Extremo del lote de la versión 1|Extremo de la versión 2|
|---|---|---|---|
|Tipo de llamada|GET|POST|POST|
|Opinión|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Frases clave|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Idiomas|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### Formatos de entrada ####

Tenga en cuenta que, en este momento, solo se acepta el formato POST, por lo que debe volver a formatear cualquier entrada que utilizara anteriormente los extremos de documentos únicos según corresponda. Las entradas no distinguen mayúsculas de minúsculas.

**Versión 1 (lote)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versión 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### Salida de opinión ####

**Versión 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versión 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### Salida de frases clave ####

**Versión 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versión 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### Salida de idiomas ####


**Versión 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versión 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### Parte 4b: Actualizar los formatos de temas ###

#### Extremos ####

| |Extremo de la versión 1 | Extremo de la versión 2|
|---|---|---|
|Enviar para la detección de temas (POST)|```StartTopicDetection```|```topics```|
|Capturar los resultados de los temas (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### Formatos de entrada ####

**Versión 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versión 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### Resultados de envío ####

**Versión 1 (POST)**

Anteriormente, cuando el trabajo finalizaba, recibía el siguiente resultado JSON, donde jobId debería agregarse a una dirección URL para capturar la salida.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Versión 2 (POST)**

La respuesta ahora incluirá un valor de encabezado, tal y como se muestra a continuación, donde `operation-location` se utiliza como el extremo para sondear con el fin de obtener los resultados:

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### Operation results ####

**Versión 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versión 2 (GET)**

Al igual que antes, **sondee periódicamente la salida** (el período recomendado es cada minuto) hasta que se devuelven resultados.

Cuando haya finalizado la API de temas, se devolverá un estado con el valor `succeeded`. Los resultados se incluirán en el formato que se muestra a continuación:

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### Parte 5: realizar pruebas ###

Con esto debe poder continuar. Pruebe el código con una muestra pequeña para asegurarse de que puede procesar correctamente los datos.

<!---HONumber=AcomDC_0914_2016-->