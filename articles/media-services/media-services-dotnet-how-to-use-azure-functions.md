---
title: Desarrollo de Azure Functions con Media Services
description: "En este tema se muestra cómo empezar a desarrollar Azure Functions con Media Services mediante Azure Portal."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b077504e7a289fd4976aa22bab0ad5784ffb491b
ms.lasthandoff: 03/15/2017


---
#<a name="develop-azure-functions-with-media-services"></a>Desarrollo de Azure Functions con Media Services
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

En este tema se trata cómo empezar a desarrollar Azure Functions con Media Services mediante Azure Portal. 

También puede implementar instancias de Azure Functions de Media Services existentes desde [aquí](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) presionando el botón **Deploy to Azure** (Implementar en Azure). Este repositorio incluye ejemplos de Azure Functions que usan Azure Media Services para mostrar flujos de trabajo relacionados con la ingesta directa de contenido desde Blob Storage, la codificación y la escritura de contenido de nuevo en Blob Storage. También incluye ejemplos de cómo supervisar las notificaciones de trabajo por medio de webhooks y colas de Azure.

Puede desarrollar sus funciones a partir de los ejemplos de [este](https://github.com/Azure-Samples/media-services-dotnet-Functions-integration) repositorio. En este tema se muestra cómo empezar a crear instancias de Azure Functions que usan Media Services. 

## <a name="prerequisites"></a>Requisitos previos

Para poder crear la primera función, es necesario tener una cuenta de Azure activa. Si aún no tiene una cuenta de Azure, [tiene a su disposición la creación de una cuenta gratis](https://azure.microsoft.com/free/).

Si va a crear instancias de Azure Functions que llevan a cabo acciones en su cuenta de Azure Media Services (AMS) o escuchan eventos enviados por Media Services, debe crear una cuenta de AMS, tal como se describe [aquí](media-services-portal-create-account.md).

## <a name="create-a-function-app"></a>Creación de una aplicación de función

Cree una aplicación de función como se describe [aquí](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app).

## <a name="create-a-function"></a>Creación de una función

Una vez implementada su aplicación de función, puede encontrarla entre Azure Functions de **App Services**. 

1. Seleccione la aplicación de función y haga clic en **Nueva función**.
3. Elija el lenguaje **C#** y el escenario **Webhook y API**.
3. Seleccione **GenericWebHook-CSharp** (se ejecutará siempre que se reciba una solicitud de webhook) o **HttpTrigger-CSharp** (se ejecutará siempre que se reciba una solicitud HTTP) y asigne un nombre a la función.
4. Haga clic en **Crear**. 

## <a name="get-function-url"></a>Obtención de dirección URL de la función

Para desencadenar la ejecución de las funciones desde una herramienta de pruebas HTTP o desde otra ventana del explorador, necesitará el valor de la dirección URL de la función. 

![Configuración](./media/media-services-azure-functions/media-services-azure-functions002.png)

## <a name="files"></a>Archivos

La función de Azure está asociada a archivos de código y otros archivos que se describen en esta sección. De forma predeterminada, una función está asociada con los archivos **function.json** y **run.csx**. Debe agregar un archivo **project.json**. El resto de esta sección muestra las definiciones de estos archivos.

![files](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

El archivo function.json define los enlaces de función y otras opciones de configuración. Este archivo se usa en tiempo de ejecución para determinar los eventos que se supervisarán y cómo pasar datos y devolverlos al ejecutarse una función. 

Este es un ejemplo de archivo **function.json**.

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

El archivo project.json contiene dependencias. Este es un ejemplo de archivo **function.json** que incluye bibliotecas de AMS.

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

Este es el código C# para la función. Para ver un ejemplo de una función de webhook, consulte [este](media-services-dotnet-check-job-progress-with-webhooks.md) tema. 

Una vez que haya terminado de definir la función, haga clic en **Ejecutar**.
    
    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;
    

    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");
    
    static CloudMediaContext _context = null;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;
    
        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");
    
        // some valication code ...

        _context = new CloudMediaContext(new MediaServicesCredentials(
        _mediaServicesAccountName,
        _mediaServicesAccountKey));

        // some AMS operations ...
      
        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }



## <a name="configure-function-app-settings"></a>Configuración de la aplicación de función

Cuando desarrolle funciones de Media Services, es útil agregar parámetros que se usarán en todas las funciones a la sección **Configuración de la aplicación**. 

Por ejemplo:

![Configuración](./media/media-services-azure-functions/media-services-azure-functions001.png)


## <a name="next-step"></a>Paso siguiente

En este punto, está listo para iniciar el desarrollo de una aplicación de Servicios multimedia. Para más información, consulte [Uso de Azure WebHooks para supervisar las notificaciones de trabajo de Media Services con .NET](media-services-dotnet-check-job-progress-with-webhooks.md).   

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


