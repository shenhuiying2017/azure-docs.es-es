---
title: "Uso de servicios web de Administración de modelos de Azure Machine Learning| Microsoft Docs"
description: "Este documento describe los pasos y conceptos implicados en el uso de servicios web implementados con Administración de modelos de Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 64141afe421ace44fe71c04f8a2fba48144633c9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="consuming-web-services"></a>Uso de servicios web
Al implementar un modelo como un servicio web en tiempo real, puede enviarle datos y obtener predicciones de diversas plataformas y aplicaciones. El servicio web en tiempo real expone una API de REST para obtener predicciones. Puede enviar datos al servicio web en formato de una o varias filas para obtener una o varias predicciones al mismo tiempo.

Con el [servicio web Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy), una aplicación externa se comunica de forma sincrónica con un modelo predictivo mediante la realización de una llamada HTTP POST a la dirección URL del servicio. Para realizar una llamada al servicio web, la aplicación cliente debe especificar la clave de API que se crea al implementar una predicción y poner los datos de solicitud en el cuerpo de la solicitud POST.

Tenga en cuenta que las claves de API solo están disponibles en el modo de implementación de clúster. Los servicios web locales no tienen claves.

## <a name="service-deployment-options"></a>Opciones de implementación del servicio
Los servicios web Azure Machine Learning se pueden implementar en los clústeres basados en la nube tanto para escenarios de prueba como de producción, y en estaciones de trabajo locales mediante Docker Engine. La funcionalidad del modelo predictivo en ambos casos será la misma. La implementación basada en clústeres proporciona una solución escalable y eficiente basada en Azure Container Service, mientras que la implementación local se puede utilizar para la depuración. 

La API y la CLI de Azure Machine Learning proporcionan comandos prácticos para crear y administrar entornos de proceso para implementaciones de servicio mediante la opción ```az ml env```. 

## <a name="list-deployed-services-and-images"></a>Lista de imágenes y servicios implementados
Puede enumerar las imágenes de Docker y los servicios implementados actualmente mediante el comando de la CLI ```az ml service list realtime -o table```. Tenga en cuenta que este comando siempre funciona en el contexto del entorno de proceso actual y no mostrará los servicios implementados en un entorno que no esté establecido como el actual. Para establecer el entorno, use ```az ml env set```. 

## <a name="get-service-information"></a>Obtención de información del servicio
Tras implementar correctamente el servicio web, utilice el siguiente comando para obtener la dirección URL del servicio y otros detalles para llamar al punto de conexión de servicio. 

```
az ml service usage realtime -i <service name>
```

Este comando imprimirá la dirección URL del servicio, los encabezados de solicitud necesarios, la dirección URL de Swagger y datos de ejemplo para llamar al servicio si al realizar la implementación se proporcionó el esquema de la API del servicio.

Puede probar el servicio directamente desde la CLI sin crear una solicitud HTTP, escribiendo el comando de la CLI de ejemplo con los datos de entrada:

```
az ml service run realtime -i <service name> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Obtención de la clave de API del servicio
Para obtener la clave del servicio web, use el comando siguiente:

```
az ml service keys realtime -i <web service id>
```
Al crear la solicitud HTTP, utilice la clave del encabezado de autorización: "Authorization": "Bearer <key>"

## <a name="get-the-service-swagger-description"></a>Obtención de la descripción de Swagger del servicio
Si se proporcionó el esquema de la API del servicio, el punto de conexión de servicio expondría un documento de Swagger en ```http://<ip>/api/v1/service/<service name>/swagger.json```. El documento de Swagger puede utilizarse para generar automáticamente el cliente del servicio y explorar los datos de entrada esperados y otros detalles sobre el servicio.

## <a name="get-service-logs"></a>Obtención de registros del servicio
Para comprender el comportamiento del servicio y diagnosticar problemas, existen varias maneras de obtener los registros de servicio:
- Comando de la CLI ```az ml service logs realtime -i <service id>```. Este comando funciona tanto en el modo local como en el de clústeres.
- Si se ha habilitado el registro de servicio en la implementación, los registros de servicio también se enviarán a AppInsight. El comando de la CLI ```az ml service usage realtime -i <service id>``` muestra la dirección URL de AppInsight. Tenga en cuenta que los registros de AppInsight pueden retrasarse entre 2 y 5 minutos.
- Los registros de clúster pueden verse mediante la consola de Kubernetes que se conecta al configurar el entorno de clúster actual con ```az ml env set```.
- Los registros de Docker locales están disponibles mediante los registros de Docker Engine cuando el servicio se ejecuta localmente.

## <a name="call-the-service-using-c"></a>Llamada al servicio mediante C#
Use la dirección URL del servicio para enviar una solicitud desde una aplicación de la consola de C#. 

1. En Visual Studio, cree una nueva aplicación de consola: 
    * En el menú, haga clic en Archivo -> Nuevo -> Proyecto.
    * En Visual Studio C#, haga clic en Escritorio clásico de Windows y seleccione Aplicación de consola.
2. Escriba _MyFirstService_ como nombre del proyecto y, a continuación, haga clic en Aceptar.
3. En Referencias del proyecto, establezca las referencias en _System.Net_ y _System.Net.Http_.
4. Haga clic en Herramientas -> Administrador de paquetes NuGet -> Consola del Administrador de paquetes y, a continuación, instale el paquete Microsoft.AspNet.WebApi.Client.
5. Abra el archivo Program.cs y sustituya el código por el siguiente:
6. Actualice los parámetros _SERVICE_URL_ y _API_KEY_ con la información de su servicio web.
7. Ejecute el proyecto.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Llamada al servicio web mediante Python
Use Python para enviar una solicitud al servicio web en tiempo real. 

1. Copie el siguiente ejemplo de código en un archivo de Python nuevo.
2. Actualice los parámetros data, url y api_key. En el caso de los servicios web locales, quite el encabezado de autorización.
3. Ejecute el código. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, data, headers=headers)
resp.text
```
