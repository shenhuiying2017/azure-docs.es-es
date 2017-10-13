---
title: "Uso de puntos de conexión de Azure Machine Learning en Stream Analytics | Microsoft Docs"
description: "Funciones definidas por el usuario de Aprendizaje automático de Azure en Análisis de transmisiones"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 406b258f-b8c2-4e55-953c-b7f84e8e5354
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: ca656ce11f267340872f4b8566d0ee21791f29d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-integration-in-stream-analytics"></a>Integración de Aprendizaje automático en Análisis de transmisiones
Stream Analytics proporciona compatibilidad con las funciones definidas por el usuario que llamen a puntos de conexión de Azure Machine Learning. La compatibilidad con la API de REST para esta característica se detalla en la [biblioteca API de REST de Análisis de transmisiones](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artículo proporciona información adicional necesaria para una implementación correcta de esta capacidad en Análisis de transmisiones. También se ha publicado un tutorial, que está disponible [aquí](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Información general: terminología de Aprendizaje automático de Azure
Aprendizaje automático de Microsoft Azure proporciona una herramienta colaborativa de arrastrar y colocar, que le permite crear, probar e implementar soluciones de análisis predictivo en sus datos. Esta herramienta de denomina *Estudio de aprendizaje automático de Azure*. El estudio se usará para interactuar con los recursos de Machine Learning, así como para compilar, probar e iterar fácilmente en su diseño. A continuación, se proporcionan estos recursos y sus definiciones.

* **Área de trabajo**: el *área de trabajo* es un contenedor con todos los demás recursos de Aprendizaje automático en un solo lugar para la administración y el control.
* **Experimento**: los científicos de datos crean *experimentos* para utilizar conjuntos de datos y entrenar un modelo de Aprendizaje automático.
* **Punto de conexión**: los *puntos de conexión* son objetos de Aprendizaje automático de Azure que se usan para tomar características como entradas, aplicar un modelo de Aprendizaje automático especificado y devolver la salida con puntuación.
* **Servicio web de puntuación**: un *servicio web de puntuación* es una colección de puntos de conexión, como se mencionó anteriormente.

Cada punto de conexión tiene varias API para la ejecución de lotes y la ejecución sincrónica. Análisis de transmisiones usa la ejecución sincrónica. El servicio específico se denomina un [servicio de solicitud/respuesta](../machine-learning/studio/consume-web-services.md) en Estudio de aprendizaje automático de Azure.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Recursos de Machine Learning necesarios para trabajos de Stream Analytics
Para el procesamiento de trabajos de Stream Analytics, para la correcta ejecución se necesitan un punto de conexión de solicitud/respuesta, una [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)y una definición de Swagger. Análisis de transmisiones tiene un punto de conexión adicional que construye la dirección URL de Swagger, busca en la interfaz y devuelve una definición de función definida por el usuario predeterminada al usuario.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configuración de un Análisis de transmisiones y funciones definidas por el usuario de Aprendizaje automático mediante la API de REST
Mediante las API de REST, puede configurar el trabajo para llamar a funciones de Aprendizaje automático de Azure. Los pasos son los siguientes:

1. Creación de un trabajo de Análisis de transmisiones
2. Definición de una entrada
3. Definición de una salida
4. Creación de una función definida por el usuario
5. Creación de una transformación de Análisis de transmisiones que llame a la función definida por el usuario
6. Inicio del trabajo

## <a name="creating-a-udf-with-basic-properties"></a>Creación de una función definida por el usuario con las propiedades básicas
Por ejemplo, el siguiente código de ejemplo crea una función definida por el usuario escalar denominada *newudf* que enlaza a un punto de conexión de Aprendizaje automático de Azure. Tenga en cuenta que el *punto de conexión* (URI de servicio) se puede encontrar en la página de ayuda de API para el servicio seleccionado y la *apiKey* puede encontrarse en la página principal de servicios.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Ejemplo del cuerpo de solicitud:  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Llamada al punto de conexión RetrieveDefaultDefinition para la función definida por el usuario predeterminada
Una vez creado el esqueleto de la función definida por el usuario, es necesaria la definición completa de la función definida por el usuario. El punto de conexión RetreiveDefaultDefinition ayuda a obtener la definición predeterminada para una función escalar enlazada a un punto de conexión de Aprendizaje automático de Azure. La siguiente carga requiere obtener la definición de la función definida por el usuario predeterminada para una función escalar enlazada a un punto de conexión de Aprendizaje automático de Azure. No especifica el punto de conexión real, porque ya se ha proporcionado durante la solicitud PUT. Stream Analytics llamará al punto de conexión proporcionado en la solicitud si se proporciona explícitamente. De lo contrario, usará al que se hace referencia desde el principio. Aquí, la función definida por el usuario toma un parámetro de una sola cadena (una frase) y devuelve una única salida de tipo "string" que indica la etiqueta "sentiment" para esa frase.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Ejemplo del cuerpo de solicitud:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Un ejemplo de salida tendría el aspecto siguiente.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>Revisión de la función definida por el usuario con la respuesta
Ahora se debe revisar la función definida por el usuario con la respuesta anterior, tal como se muestra a continuación.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Cuerpo de la solicitud (salida de RetrieveDefaultDefinition):

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementación de una transformación de Análisis de transmisiones que llame a la función definida por el usuario
Ahora consulte la función definida por el usuario (aquí llamada scoreTweet) para cada evento de entrada y escriba una respuesta para ese evento en una salida.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
