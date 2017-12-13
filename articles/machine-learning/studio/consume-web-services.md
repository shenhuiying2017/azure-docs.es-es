---
title: "Cómo consumir un servicio web Azure Machine Learning | Microsoft Docs"
description: "Una vez implementado un servicio de aprendizaje automático, se puede consumir el servicio web RESTFul facilitado como servicio de solicitud-respuesta en tiempo real o como servicio de ejecución por lotes."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.author: garye
ms.openlocfilehash: ed41ec58dbdfd41931e8bab3a3cbe33caafe9a74
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Cómo consumir un servicio web Azure Machine Learning

Una vez que implemente un modelo predictivo de Azure Machine Learning como servicio web, puede usar una API de REST para enviarle datos y obtener predicciones. Puede enviar los datos en tiempo real o por lotes.

Puede encontrar más información sobre cómo crear e implementar un servicio web Machine Learning con Machine Learning Studio aquí:

* Para obtener un tutorial sobre cómo crear un experimento en Machine Learning Studio, consulte [Creación del primer experimento](create-experiment.md).
* Para obtener detalles sobre cómo implementar un servicio web, vea [Implementación de un servicio web Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Para obtener más información sobre Machine Learning, visite el [Centro de documentación de Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Información general
Con el servicio web Azure Machine Learning, una aplicación externa se comunica con un modelo de puntuación de flujo de trabajo de Machine Learning en tiempo real. Una llamada al servicio web Machine Learning devuelve resultados de predicción a una aplicación externa. Para llamar a un servicio web Machine Learning, se pasa una clave de API que se crea cuando se implementa una predicción. El servicio web Machine Learning se basa en REST, una opción popular de arquitectura para proyectos de programación web.

Azure Machine Learning tiene dos tipos de servicios:

* Servicio de solicitud y respuesta (RRS): servicio de latencia baja altamente escalable que proporciona una interfaz con los modelos sin estado creados e implementados desde Machine Learning Studio.
* Servicio de ejecución por lotes (BES): servicio asincrónico que puntúa un lote de registros de datos.

Para más información sobre los servicios web Machine Learning, consulte [Implementación de un servicio web Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obtener una clave de autorización de Azure Machine Learning
Al implementar el experimento, se generan claves de API para el servicio web. Puede recuperar las claves de varias ubicaciones.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>En el portal Servicios web Microsoft Azure Machine Learning
Inicie sesión en el portal [Servicios web Microsoft Azure Machine Learning](https://services.azureml.net).

Para recuperar la clave de API para un nuevo servicio web Machine Learning:

1. En el portal de servicios web Azure Machine Learning, haga clic en la opción **Servicios web** del menú superior.
2. Haga clic en el servicio web del que quiere recuperar la clave.
3. En el menú superior, haga clic en **Consume**(Consumo).
4. Copie y guarde la **clave principal**.

Para recuperar la clave de API para un servicio web Machine Learning clásico:

1. En el portal Servicios web Azure Machine Learning, haga clic en la opción **Classic Web Services** (Servicios web clásicos) del menú superior.
2. Haga clic en el servicio web que está usando.
3. Haga clic en el punto de conexión del que quiere recuperar la clave.
4. En el menú superior, haga clic en **Consume**(Consumo).
5. Copie y guarde la **clave principal**.

### <a name="classic-web-service"></a>Servicio web clásico
 También puede recuperar una clave para un servicio web clásico de Machine Learning Studio.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. En Machine Learning Studio, haga clic en **SERVICIOS WEB** a la izquierda.
2. Haga clic en un servicio web. La **clave de API** está en la pestaña **PANEL**.

## <a id="connect"></a>Conexión a un servicio web Machine Learning
Puede conectarse a un servicio web Machine Learning mediante cualquier lenguaje de programación que admita la respuesta y la solicitud HTTP. Puede ver ejemplos en C#, Python y R desde una página de ayuda de servicio web Machine Learning.

**Ayuda de la API de Machine Learning** Se crea una página de ayuda de API de Machine Learning al implementar un servicio web. Vea [Tutorial de Azure Machine Learning: Implementación de un servicio web](walkthrough-5-publish-web-service.md).
La ayuda de la API de Machine Learning contiene información sobre un servicio web de predicción.

1. Haga clic en el servicio web que está usando.
2. Haga clic en el punto de conexión para el que desea ver la página de ayuda de la API.
3. En el menú superior, haga clic en **Consume**(Consumo).
4. Haga clic en **API help page** (Página de ayuda de la API) en los puntos de conexión Solicitud-respuesta o Ejecución de lotes.

**Para ver la ayuda de la API de Machine Learning para un servicio web nuevo**

En el [portal Servicios web de Azure Machine Learning](https://services.azureml.net/):

1. Haga clic en la opción de menú **SERVICIOS WEB** del menú superior.
2. Haga clic en el servicio web del que quiere recuperar la clave.

Haga clic en **Use Web Service** (Usar servicio web) para obtener los URI de los servicios de solicitud-respuesta y ejecución por lotes, además del código de ejemplo en C#, R y Python.

Haga clic en **Swagger API** para obtener la documentación basada en Swagger de las API que se invocan desde los URI proporcionados.

### <a name="c-sample"></a>Ejemplo de C#
Para conectarse a un servicio web Machine Learning, use un elemento **HttpClient** que pase ScoreData. ScoreData contiene un FeatureVector, un vector de n dimensiones de características numéricos que representa el ScoreData. Se autentica en el servicio de Machine Learning con una clave API.

Para conectarse a un servicio web Machine Learning, se debe instalar el paquete NuGet **Microsoft.AspNet.WebApi.Client**.

**Instalar Microsoft.AspNet.WebApi.Client NuGet en Visual Studio**

1. Publique el conjunto de datos de descarga de UCI: Servicio web de conjunto de datos de clase de contenido para adultos 2.
2. Haga clic en **Herramientas** > **Administrador de paquetes NuGet** > **Consola del administrador de paquetes**.
3. Elija **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Machine Learning.
2. Asigne una clave de API con la clave de un servicio web. Consulte el apartado anterior **Obtener una clave de autorización de Azure Machine Learning** .
3. Asigne la URI de servicio a la URI de solicitud.

**Este es el aspecto que tendrá una solicitud completa.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.Net application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the requert ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Ejemplo de Python
Para conectarse a un servicio web de Machine Learning, use la biblioteca **urllib2** para Python 2.X y la biblioteca **urllib.request** para Python 3.X. Se aprobará ScoreData, que contiene un valor de FeatureVector, un vector de n dimensiones de características numéricos que representa ScoreData. Se autentica en el servicio de Machine Learning con una clave API.

**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Machine Learning.
2. Asigne una clave de API con la clave de un servicio web. Vea la sección **Obtener una clave de autorización de Azure Machine Learning** casi al principio de este artículo.
3. Asigne la URI de servicio a la URI de solicitud.

**Este es el aspecto que tendrá una solicitud completa.**
```python
import urllib2 # urllib.request for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(uri, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Ejemplo de R

Para conectarse a un servicio web de Machine Learning, use las bibliotecas **RCurl** y **rjson** para crear la solicitud y procesar la respuesta JSON devuelta. Se aprobará ScoreData, que contiene un valor de FeatureVector, un vector de n dimensiones de características numéricos que representa ScoreData. Se autentica en el servicio de Machine Learning con una clave API.

**Este es el aspecto que tendrá una solicitud completa.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>Ejemplo de JavaScript

Para conectarse a un servicio web de Machine Learning, use el paquete de NPM **request** de su proyecto. También se usará el objeto `JSON` para dar formato a la entrada y analizar el resultado. Realice la instalación mediante `npm install request --save`, o agregue `"request": "*"` a su archivo package.json en `dependencies` y ejecute `npm install`.

**Este es el aspecto que tendrá una solicitud completa.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```