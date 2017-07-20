---
title: Utilizar bibliotecas de clases de .NET con Azure Functions | Microsoft Docs
description: "Obtenga información acerca de cómo crear bibliotecas de clases de .NET para su uso con Azure Functions"
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/09/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: dbcec586fe5ee06da38c37cf1ead2469386cc5c3
ms.contentlocale: es-es
ms.lasthandoff: 06/10/2017


---
# <a name="using-net-class-libraries-with-azure-functions"></a>Utilizar bibliotecas de clases de .NET con Azure Functions

Además de los archivos de script, Azure Functions admite la publicación de una biblioteca de clases como la implementación para una o más funciones. Se recomienda utilizar [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/).

## <a name="prerequisites"></a>Requisitos previos 

Este artículo tiene los siguientes requisitos previos:

- [Versión preliminar de Visual Studio 2017 15.3](https://www.visualstudio.com/vs/preview/). Instalación de las cargas de trabajo **ASP.NET y desarrollo web** y **Desarrollo de Azure**.
- [Herramientas de Azure Functions para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=AndrewBHall-MSFT.AzureFunctionToolsforVisualStudio2017)

## <a name="functions-class-library-project"></a>Proyecto de biblioteca de clases de Functions

En Visual Studio, cree un nuevo proyecto de Azure Functions. La nueva plantilla de proyecto crea los archivos *host.json* y *local.settings.json*. Puede [personalizar la configuración en tiempo de ejecución de Azure Functions en host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json). 

El archivo *local.settings.json* almacena la configuración de la aplicación, las cadenas de conexión y la configuración de Azure Functions Core Tools. Para más información acerca de su estructura, consulte [Codificar y probar Azure Functions localmente](functions-run-local.md#local-settings).

### <a name="functionname-attribute"></a>Atributo FunctionName

El atributo [ `FunctionNameAttribute` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) marca un método como punto de entrada de una función. Se debe utilizar exactamente con un desencadenador y 0 o más enlaces de entrada y salida.

### <a name="conversion-to-functionjson"></a>Conversión a function.json

Cuando se compila un proyecto de Azure Functions, se genera un archivo `function.json` en el directorio, que coincide con el nombre de función definido por `[FunctionName]`. Especifica los desencadenadores y los enlaces y puntos en el archivo de ensamblado del proyecto.

Esta conversión la realiza el paquete de NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). El código fuente está disponible en el repositorio de GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="triggers-and-bindings"></a>Desencadenadores y enlaces

En la tabla siguiente se enumeran los desencadenadores y los enlaces que están disponibles en un proyecto de biblioteca de clases de Azure Functions. Todos los atributos están en el espacio de nombres `Microsoft.Azure.WebJobs`.

| Enlace | Atributo | Paquete de NuGet |
|------   | ------    | ------        |
| [Desencadenador de almacenamiento de blobs, entrada, salida](#blob-storage) | [BlobAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [Almacenamiento de blobs] |
| [Enlaces de entrada y salida de Cosmos DB](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [Desencadenador y salida de Event Hubs](#event-hub) | [EventHubTriggerAttribute], [EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [Entrada y salida de archivos externos](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [Desencadenador HTTP y webhook](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [Entrada y salida de Mobile Apps](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [Salida de Notification Hubs](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [Desencadenador y salida de Queue Storage](#queue) | [QueueAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Salida de SendGrid](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [Desencadenador y salida de Service Bus](#service-bus) | [ServiceBusAttribute], [ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [Entrada y salida de Table Storage](#table) | [TableAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Desencadenador de temporizador](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Salida de Twilio](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-and-output-bindings"></a>Desencadenador y enlaces de entrada y salida de Blob Storage

Azure Functions admite desencadenador y enlaces de entrada y salida para Azure Blob Storage. Para más información sobre las expresiones de enlace y los metadatos, consulte [Enlaces de Blob Storage de Azure Functions](functions-bindings-storage-blob.md).

Un desencadenador de blob se define con el atributo `[BlobTrigger]`. Puede utilizar el atributo `[StorageAccount]` para definir la cuenta de almacenamiento que se usa en una función completa o clase.

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

La entrada y salida de blob se definen utilizando el atributo `[Blob]` junto con un parámetro `FileAccess` que indica lectura o escritura. En el ejemplo siguiente se usa un desencadenador de blob y un enlace de salida.

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

<a name="cosmos-db"></a>

### <a name="cosmos-db-input-and-output-bindings"></a>Enlaces de entrada y salida de Cosmos DB

Azure Functions admite enlaces de entrada y salida para Cosmos DB. Para más información sobre las características del enlace de Cosmos DB, consulte [Enlaces de Cosmos DB de Azure Functions](functions-bindings-documentdb.md).

Para enlazar a un documento de Cosmos DB, use el atributo `[DocumentDB]` en el paquete de NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. El siguiente ejemplo tiene un desencadenador de cola y un enlace de salida de la API de DocumentDB:

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", ConnectionStringSetting = "DocDBConnection")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>Desencadenador y salida de Event Hubs

Azure Functions admite enlaces de desencadenador y salida para Event Hubs. Para más información, consulte [Enlaces de Event Hub de Azure Functions](functions-bindings-event-hubs.md).

Los tipos `[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` y `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]` se definen en el paquete de NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

En el ejemplo siguiente se usa un desencadenador de Event Hub:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

El siguiente ejemplo tiene una salida de Event Hub, utilizando el valor devuelto por el método como salida:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>Entrada y salida de archivo externo

Azure Functions admite desencadenador y enlaces de entrada y salida para archivos externos, como Google Drive, Dropbox y OneDrive. Para más información, consulte [Enlaces de archivos externos de Azure Function](functions-bindings-external-file.md). Los atributos `[ExternalFileTrigger]` y `[ExternalFile]` se definen en el paquete de NuGet [Microsoft.Azure.WebJobs.Extensions.ApiHub].

En el ejemplo de C# siguiente se muestran enlaces de archivo externo de entrada y salida. El código copia el archivo de entrada en el archivo de salida.

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP y webhooks

Use el atributo `HttpTrigger` para definir un desencadenador HTTP o un webhook. Este atributo se define en el paquete de NuGet [Microsoft.Azure.WebJobs.Extensions.Http]. Puede personalizar el nivel de autorización, el tipo de webhook, la ruta y los métodos. En el ejemplo siguiente se define un desencadenador HTTP con autenticación anónima y tipo de webhook _genericJson_.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>Entrada y salida de Mobile Apps

Azure Functions admite enlaces de entrada y salida para Mobile Apps. Para más información, consulte [Enlaces de Mobile Apps de Azure Functions](functions-bindings-mobile-apps.md). El atributo `[MobileTable]` se define en el paquete de NuGet [Microsoft.Azure.WebJobs.Extensions.MobileApps].

En el siguiente ejemplo se muestra un enlace de salida de Mobile Apps:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>Salida de Notification Hubs

Azure Functions admite un enlace de salida para Notification Hubs. Para más información, consulte [Enlace de salida de Notification Hub de Azure Functions](functions-bindings-notification-hubs.md). El atributo `[NotificationHub]` se define en el paquete de NuGet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs].

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>Desencadenador y salida de Queue Storage

Azure Functions admite desencadenador y enlaces de salida para Azure Queues. Para más información, consulte [Enlaces de Queue Storage de Azure Functions](functions-bindings-storage-queue.md).

En el ejemplo siguiente se muestra cómo usar el tipo de valor devuelto por la función con un enlace de salida de cola, usando el atributo `[Queue]`. Para definir un desencadenador de cola, utilice el atributo `[QueueTrigger]`.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }

    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```

<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>Salida de SendGrid

Azure Functions admite un enlace de salida de SendGrid para el envío de correo electrónico mediante programación. Para más información, consulte [Enlaces de SendGrid de Azure Functions](functions-bindings-sendgrid.md).

El atributo `[SendGrid]` se define en el paquete de NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid].

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>Desencadenador y salida de Service Bus

Azure Functions admite enlaces de desencadenador y salida para colas y temas de Service Bus. Para más información sobre la configuración de enlaces, consulte [Enlaces de Service Bus de Azure Functions](functions-bindings-service-bus.md).

Los atributos `[ServiceBusTrigger]` y `[ServiceBus]` se definen en el paquete de NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

A continuación se muestra un ejemplo de un desencadenador de cola de Service Bus:

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

El siguiente es un ejemplo de un enlace de salida de Service Bus, utilizando el tipo de valor devuelto por el método como salida:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>Entrada y salida de Table Storage

Azure Functions admite enlaces de entrada y salida para Table Storage de Azure. Para más información, consulte [Enlaces de Table Storage de Azure Functions](functions-bindings-storage-table.md).

El siguiente ejemplo es una clase con dos funciones, mostrando los enlaces de entrada y salida de Table Storage. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>Desencadenador de temporizador

Azure Functions incluye un enlace a un desencadenador de temporizador que le permite ejecutar su código de función según una programación definida. Para más información sobre las características del enlace, consulte [Programar la ejecución de código con Azure Functions](functions-bindings-timer.md).

En el plan de Consumo, puede definir programaciones con una [expresión CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression). Si utiliza un plan de App Service, también puede usar una cadena TimeSpan. 

En el ejemplo siguiente se define un desencadenador de temporizador que se ejecuta cada 5 minutos:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Salida de Twilio

Azure Functions admite enlaces de salida de Twilio para permitir a sus funciones enviar mensajes de texto SMS. Para más información, consulte [Envío de mensajes SMS desde Azure Functions mediante el enlace de salida de Twilio](functions-bindings-twilio.md). 

El atributo `[TwilioSms]` se define en el paquete [Microsoft.Azure.WebJobs.Extensions.Twilio].

En el ejemplo de C# siguiente se utiliza un desencadenador de cola y un enlace de salida de Twilio:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el uso de Azure Functions en C# de scripting, consulte [Referencia para desarrolladores de C\# de script de Azure Functions](functions-reference-csharp.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs
