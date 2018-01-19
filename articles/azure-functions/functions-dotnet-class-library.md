---
title: Referencia para desarrolladores de C# de Azure Functions
description: "Cómo desarrollar funciones de Azure con C#."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 3de1e9b042a7a356c3c88e604e1e26c256d85657
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-functions-c-developer-reference"></a>Referencia para desarrolladores de C# de Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Este artículo es una introducción al desarrollo de Azure Functions mediante el uso de C# en las bibliotecas de clases .NET.

Azure Functions es compatible con C# y con los lenguajes de programación de scripts de C#. Si busca orientación sobre cómo [usar C# en Azure Portal](functions-create-function-app-portal.md), vea la [referencia para desarrolladores de scripts de C#](functions-reference-csharp.md).

En este artículo se supone que ya ha leído los siguientes artículos:

* [Guía para desarrolladores de Azure Functions](functions-reference.md)
* [Herramientas de Visual Studio 2017 de Azure Functions](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Proyecto de biblioteca de clases de Functions

En Visual Studio, la plantilla de proyecto de **Azure Functions** crea un proyecto de biblioteca de clases de C# que contiene los archivos siguientes:

* [host.json](functions-host-json.md): almacena los valores de configuración que afectan a todas las funciones del proyecto cuando se ejecuta localmente o en Azure.
* [local.settings.json](functions-run-local.md#local-settings-file): almacena la configuración de la aplicación y las cadenas de conexión que se utilizan cuando se ejecuta localmente.

### <a name="functionname-and-trigger-attributes"></a>Atributos FunctionName y desencadenador

En una biblioteca de clases, una función es un método estático con un atributo `FunctionName` y un atributo desencadenador, tal como se muestra en el ejemplo siguiente:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

El atributo `FunctionName` marca el método como punto de entrada de una función. El nombre debe ser único dentro de un proyecto.

El atributo desencadenador especifica el tipo de desencadenador y enlaza los datos de entrada a un parámetro del método. La función de ejemplo se desencadena mediante un mensaje de cola, y este último se pasa al método en el `myQueueItem` parámetro.

### <a name="additional-binding-attributes"></a>Atributos de enlace adicionales

Se pueden usar atributos de enlace de entrada y salida adicionales. En el ejemplo siguiente se modifica el ejemplo anterior mediante la adición de un enlace de la cola de salida. La función escribe el mensaje de la cola de entrada en un nuevo mensaje de cola de una cola distinta.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

### <a name="conversion-to-functionjson"></a>Conversión a function.json

El proceso de compilación crea un archivo *function.json* en una carpeta de la función en la carpeta de compilación. Este archivo no está pensado para que se pueda modificar directamente. No se puede cambiar la configuración del enlace ni deshabilitar la función mediante la edición de este archivo. 

El propósito de este archivo es proporcionar información al controlador de escala que se usará para [escalar decisiones en el plan de consumo](functions-scale.md#how-the-consumption-plan-works). Por esta razón, el archivo solo tiene información del desencadenador, no sobre los enlaces de entrada o salida.

El archivo *function.json* generado incluye una propiedad `configurationSource` que indica el tiempo de ejecución para utilizar atributos de .NET para los enlaces, en lugar de la configuración de *function.json*. Este es un ejemplo:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

El archivo *function.json* se genera mediante el paquete NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). El código fuente está disponible en el repositorio de GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="supported-types-for-bindings"></a>Tipos compatibles para los enlaces

Cada enlace tiene sus propios tipos compatibles; por ejemplo, un atributo desencadenador de blobs puede aplicarse a un parámetro de cadena, un parámetro POCO, un parámetro `CloudBlockBlob` o cualquiera de los demás tipos compatibles. En el [artículo de referencia sobre los enlaces de blobs](functions-bindings-storage-blob.md#trigger---usage) se enumeran todos los tipos de parámetros compatibles. Para obtener más información, vea el artículo sobre [desencadenadores y enlaces](functions-triggers-bindings.md) y los [documentos de referencia sobre enlaces para cada tipo de enlace](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Enlace al valor devuelto del método

Puede usar el valor devuelto de un método para un enlace de salida, como se muestra en el ejemplo siguiente:

```csharp
public static class ReturnValueOutputBinding
{
    [FunctionName("CopyQueueMessageUsingReturnValue")]
    [return: Queue("myqueue-items-destination")]
    public static string Run(
        [QueueTrigger("myqueue-items-source-2")] string myQueueItem,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        return myQueueItem;
    }
}
```

## <a name="writing-multiple-output-values"></a>Escribir varios valores de salida

Para escribir varios valores en un enlace de salida, use los tipos [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Estos tipos son colecciones de solo escritura que se escriben en el enlace de salida cuando se completa el método.

En este ejemplo se escriben varios mensajes en cola en la misma cola mediante `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 1: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Registro

Para registrar la salida en sus registros de streaming en C#, incluya un argumento de tipo `TraceWriter`. Es recomendable que lo denomine `log`. Evite el uso de `Console.Write` en Azure Functions. 

`TraceWriter` se define en el [SDK de Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). El nivel de registro para `TraceWriter` se puede configurar en [host.json](functions-host-json.md).

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> Para obtener información sobre un marco de trabajo de registro más reciente que puede usar en lugar de `TraceWriter`, consulte [Escribir registros en funciones de C#](functions-monitoring.md#write-logs-in-c-functions) en el artículo **Supervisión de Azure Functions**.

## <a name="async"></a>Async

Para convertir una función en asincrónica, use la palabra clave `async` y devuelva un objeto `Task`.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="cancellation-tokens"></a>Tokens de cancelación

Algunas operaciones requieren un cierre estable. Aunque siempre es mejor escribir código que pueda controlar los bloqueos, en los casos donde quiera controlar las solicitudes de cierre, defina un argumento con tipo [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx).  Se proporciona un `CancellationToken` para indicar que se desencadena un cierre del host.

```csharp
public static class CancellationTokenExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="environment-variables"></a>Variables de entorno

Para obtener una variable de entorno o un valor de configuración de aplicación, use `System.Environment.GetEnvironmentVariable`, como se muestra en el ejemplo de código siguiente:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
    {
        log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
        log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

## <a name="binding-at-runtime"></a>Enlace en tiempo de ejecución

En C# y otros lenguajes .NET, puede usar un patrón de enlace [imperativo](https://en.wikipedia.org/wiki/Imperative_programming), en contraposición a los enlaces [*declarativos*](https://en.wikipedia.org/wiki/Declarative_programming) de los atributos. Los enlaces imperativos resultan útiles cuando los parámetros de enlace tienen que calcularse en tiempo de ejecución, en lugar de en el tiempo de diseño. Con este patrón, se pueden establecer enlaces compatibles de entrada y salida sobre la marcha en el código de función.

Defina un enlace imperativo como se indica a continuación:

- **No** incluya un atributo en la signatura de función para los enlaces imperativos deseados.
- Pase un parámetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) o [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Utilice el siguiente patrón de C# para realizar el enlace de datos.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` es el atributo de .NET que define el enlace y `T` es un tipo de entrada o de salida compatible con ese tipo de enlace. `T` no puede ser un tipo de parámetro `out` (como `out JObject`). Por ejemplo, el enlace de salida de la tabla de Mobile Apps admite [seis tipos de salida](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), pero solo se puede utilizar [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) con el enlace imperativo.

### <a name="single-attribute-example"></a>Ejemplo de un único atributo

El ejemplo de código siguiente crea un [enlace de salida al blob de almacenamiento](functions-bindings-storage-blob.md#output) con la ruta de acceso al blob definida en tiempo de ejecución y, a continuación, escribe una cadena en el blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define el enlace de entrada o salida del [blob de almacenamiento](functions-bindings-storage-blob.md), y [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) es un tipo de enlace de salida admitido.

### <a name="multiple-attribute-example"></a>Ejemplo de varios atributos

En el ejemplo anterior se obtiene el valor de la aplicación para la cadena de conexión en la cuenta de almacenamiento principal de la aplicación de función (que es `AzureWebJobsStorage`). Se puede especificar una configuración personalizada de la aplicación para utilizarla para la cuenta de almacenamiento agregando el atributo [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) y pasando la matriz de atributos a `BindAsync<T>()`. Use un parámetro `Binder`, no `IBinder`.  Por ejemplo: 

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Desencadenadores y enlaces 

En la tabla siguiente se enumeran los atributos desencadenadores y de enlace que están disponibles en un proyecto de biblioteca de clases de Azure Functions. Todos los atributos están en el espacio de nombres `Microsoft.Azure.WebJobs`.

| Desencadenador | Entrada | Salida|
|------   | ------    | ------  |
| [BlobTrigger](functions-bindings-storage-blob.md#trigger---attributes)| [Blob](functions-bindings-storage-blob.md#input---attributes)| [Blob](functions-bindings-storage-blob.md#output---attributes)|
| [CosmosDBTrigger](functions-bindings-cosmosdb.md#trigger---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#input---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#output---attributes) |
| [EventHubTrigger](functions-bindings-event-hubs.md#trigger---attributes)|| [EventHub](functions-bindings-event-hubs.md#output---attributes) |
| [HTTPTrigger](functions-bindings-http-webhook.md#trigger---attributes)|||
| [QueueTrigger](functions-bindings-storage-queue.md#trigger---attributes)|| [Cola](functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](functions-bindings-service-bus.md#trigger---attributes)|| [Service Bus](functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](functions-bindings-external-file.md)| [ApiHubFile](functions-bindings-external-file.md)|
| |[MobileTable](functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](functions-bindings-mobile-apps.md#output---attributes) | 
| |[Table](functions-bindings-storage-table.md#input---attributes)| [Table](functions-bindings-storage-table.md#output---attributes)  | 
| ||[NotificationHub](functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](functions-bindings-twilio.md#attributes)| 

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre los desencadenadores y los enlaces](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Más información sobre procedimientos recomendados para Azure Functions](functions-best-practices.md)
