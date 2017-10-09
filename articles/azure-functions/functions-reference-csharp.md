---
title: Referencia para desarrolladores de scripts de C# de Azure Functions | Microsoft Docs
description: "Cómo desarrollar funciones de Azure con C#."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: f28cda01-15f3-4047-83f3-e89d5728301c
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/07/2017
ms.author: donnam
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: c9dfd3e3b9c155255959f76fd9b58b6935888db2
ms.contentlocale: es-es
ms.lasthandoff: 09/29/2017

---
# <a name="azure-functions-c-script-developer-reference"></a>Referencia para desarrolladores de scripts de C# de Azure Functions
> [!div class="op_single_selector"]
> * [Script de C#](functions-reference-csharp.md)
> * [Script de F#](functions-reference-fsharp.md)
> * [Node.js](functions-reference-node.md)
>
>

La experiencia de scripts de C# para Azure Functions se basa en el SDK de WebJobs de Azure. Los datos fluyen en la función de C# a través de los argumentos de método. Los nombres de los argumentos se especifican en `function.json`, y hay nombres predefinidos para acceder a cosas como el registrador de funciones y los tokens de cancelación.

En este artículo se supone que ya ha leído [Referencia para desarrolladores de Azure Functions](functions-reference.md).

Para obtener información sobre el uso de las bibliotecas de clases de C#, vea [Using .NET class libraries with Azure Functions](functions-dotnet-class-library.md) (Uso de bibliotecas de clases de .NET con Azure Functions).

## <a name="how-csx-works"></a>Funcionamiento de .csx
El formato `.csx` permite escribir menos "texto reutilizable" y centrarse en escribir solo una función de C#. Incluya las referencias a ensamblados y los espacios de nombres al principio del archivo como de costumbre. En lugar de encapsular todo en un espacio de nombres y una clase, defina solamente un método `Run`. Si necesita incluir todas las clases, por ejemplo para definir objetos CRL estándar (POCO), se puede incluir una clase dentro del mismo archivo.   

## <a name="binding-to-arguments"></a>Enlace a argumentos
Los distintos enlaces se vinculan a una función de C# mediante la propiedad `name` de la configuración de *function.json*. Cada enlace tiene sus propios tipos admitidos; por ejemplo, un desencadenador de blob puede admitir una cadena, un objeto POCO o un CloudBlockBlob. Los tipos admitidos se documentan en la referencia de cada enlace. Un objeto POCO debe tener un captador y establecedor definidos para cada propiedad.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="using-method-return-value-for-output-binding"></a>Uso del valor devuelto del método para el enlace de salida

Puede usar el valor devuelto de un método para un enlace de salida, mediante el nombre `$return` en *function.json*:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "$return",
    "queueName": "outqueue",
    "connection": "MyStorageConnectionString",
}
```

```csharp
public static string Run(string input, TraceWriter log)
{
    return input;
}
```

## <a name="writing-multiple-output-values"></a>Escribir varios valores de salida

Para escribir varios valores en un enlace de salida, use los tipos [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Estos tipos son colecciones de solo escritura que se escriben en el enlace de salida cuando se completa el método.

En este ejemplo se escriben varios mensajes en cola mediante `ICollector`:

```csharp
public static void Run(ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Hello");
    myQueueItem.Add("World!");
}
```

## <a name="logging"></a>Registro
Para registrar la salida en sus registros de streaming en C#, incluya un argumento de tipo `TraceWriter`. Es recomendable que lo denomine `log`. Evite el uso de `Console.Write` en Azure Functions. 

`TraceWriter` se define en el [SDK de WebJobs de Azure](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). El nivel de registro para `TraceWriter` se puede configurar en [host.json](functions-host-json.md).

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Async
Para convertir una función en asincrónica, use la palabra clave `async` y devuelva un objeto `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="cancellation-token"></a>Token de cancelación
Algunas operaciones requieren un cierre estable. Aunque siempre es mejor escribir código que pueda controlar los bloqueos, en los casos donde quiera controlar las solicitudes de cierre estable, defina un argumento con tipo [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx).  Se proporciona un `CancellationToken` para indicar que se desencadena un cierre del host.

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName,
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importación de espacios de nombres
Si necesita importar espacios de nombres, puede hacerlo de la manera habitual, con la cláusula `using` .

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Los siguientes espacios de nombres se importan automáticamente y, por tanto, son opcionales:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Referencia a ensamblados externos
Para los ensamblados de marco, agregue referencias mediante la directiva `#r "AssemblyName"` .

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

El entorno de hospedaje de las Funciones de Azure agrega automáticamente los siguientes ensamblados:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Se puede hacer referencia a los ensamblados siguientes con nombre simple (por ejemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Hacer referencia a ensamblados personalizados

Para hacer referencia a un ensamblado personalizado, puede usar un ensamblado *compartido* o un ensamblado *privado*:
- Los ensamblados compartidos se comparten en todas las funciones dentro de una aplicación de función. Para hacer referencia a un ensamblado personalizado, cargue el ensamblado en la aplicación de función, por ejemplo en una carpeta `bin` en la raíz de la aplicación de función. 
- Los ensamblados privados forman parte del contexto de una función determinada y admiten la instalación de prueba de versiones diferentes. Los ensamblados privados se deben cargar en una carpeta `bin` en el directorio de la función. Se hace referencia a ellos con el nombre de archivo, como `#r "MyAssembly.dll"`. 

Para más información acerca de cómo cargar archivos en su carpeta de función, consulte la sección siguiente sobre administración de paquetes.

### <a name="watched-directories"></a>Directorios inspeccionados

El directorio que contiene el archivo de script de función se inspecciona automáticamente para buscar cambios en los ensamblados. Para inspeccionar los cambios de los ensamblado en otros directorios, agréguelos a la lista `watchDirectories` en [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Uso de paquetes NuGet
Para usar paquetes NuGet en una función de C#, cargue un archivo *project.json* en la carpeta de la función del sistema de archivos de la aplicación de función. Este es un ejemplo de archivo *project.json* que agrega una referencia a la versión 1.1.0 de Microsoft.ProjectOxford.Face:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Solo se admite .NET Framework 4.6, así que asegúrese de que su archivo *project.json`net46` especifique*  como se muestra aquí.

Al cargar un archivo *project.json* , el sistema en tiempo de ejecución obtiene los paquetes y agrega automáticamente las referencias a sus ensamblados. No es necesario agregar directivas `#r "AssemblyName"` . Para usar los tipos definidos en los paquetes NuGet, agregue las instrucciones `using` necesarias al archivo *run.csx*. 

En el tiempo de ejecución de funciones, la restauración de NuGet funciona comparando `project.json` y `project.lock.json`. Si las marcas de fecha y hora de los archivos **no coinciden**, se ejecuta una restauración de NuGet y se descargan los paquetes actualizados. Pero si las marcas de fecha y hora de los archivos **coinciden**, NuGet no realiza una restauración. Por tanto, no debe implementarse `project.lock.json`, ya que hace que NuGet omita la restauración del paquete. Para evitar la implementación del archivo de bloqueo, agregue `project.lock.json` al archivo `.gitignore`.

Para usar una fuente NuGet personalizada, especifique la fuente en un archivo *Nuget.Config* en la raíz de la aplicación la función. Para más información, vea [Configuring NuGet behavior](/nuget/consume-packages/configuring-nuget-behavior) (Configuración del comportamiento de NuGet).

### <a name="using-a-projectjson-file"></a>Uso de un archivo project.json
1. Abra la función en Azure Portal. La pestaña de registros muestra el resultado de la instalación del paquete.
2. Para cargar un archivo project.json, use uno de los métodos descritos en [Actualización de los archivos de aplicación de función](functions-reference.md#fileupdate) en el tema de referencia para desarrolladores de Azure Functions.
3. Una vez cargado el archivo *project.json* , verá un resultado similar al del ejemplo siguiente en el registro de streaming de la función:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variables de entorno
Para obtener una variable de entorno o un valor de configuración de aplicación, use `System.Environment.GetEnvironmentVariable`, como se muestra en el ejemplo de código siguiente:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
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
```

## <a name="reusing-csx-code"></a>Reutilización del código .csx
Puede usar las clases y los métodos definidos en otros archivos *.csx* con el archivo *run.csx*. Para ello, utilice directivas `#load` en el archivo *run.csx*. En el siguiente ejemplo, una rutina de registro denominada `MyLogger` se comparte en *myLogger.csx* y se carga en *run.csx* mediante la directiva `#load`:

Archivo *run.csx*de ejemplo:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Archivo *mylogger.csx*de ejemplo:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext);
}
```

El uso de un *.csx* compartido es un patrón común para asignar rigurosamente los argumentos entre funciones mediante un objeto POCO. En el siguiente ejemplo simplificado, un desencadenador HTTP y un desencadenador de cola comparten un objeto POCO denominado `Order` para tipar fuertemente los datos del pedido:

Por ejemplo, *run.csx* para el desencadenador HTTP:

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Por ejemplo, *run.csx* para el desencadenador de cola:

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Por ejemplo, *order.csx*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

Puede usar una ruta de acceso relativa con la directiva `#load` :

* `#load "mylogger.csx"` carga un archivo que se encuentra en la carpeta de la función.
* `#load "loadedfiles\mylogger.csx"` carga un archivo ubicado en una carpeta dentro de la carpeta de la función.
* `#load "..\shared\mylogger.csx"` carga un archivo ubicado en una carpeta del mismo nivel que la carpeta de la función, es decir, directamente en *wwwroot*.

La directiva `#load` solo funciona con archivos *.csx* (script de C#), no con archivos *.cs*.

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime-via-imperative-bindings"></a>Enlace en tiempo de ejecución a través de enlaces imperativos

En C# y otros lenguajes .NET, puede usar un patrón de enlace [imperativo](https://en.wikipedia.org/wiki/Imperative_programming), en contraposición a los enlaces [*declarativos*](https://en.wikipedia.org/wiki/Declarative_programming) de *function.json*. Los enlaces imperativos resultan útiles cuando los parámetros de enlace tienen que calcularse en tiempo de ejecución, en lugar de en el tiempo de diseño. Con este patrón, se puede enlazar a enlaces compatibles de entrada y salida sobre la marcha en el código de función.

Defina un enlace imperativo como se indica a continuación:

- **No** incluya una entrada en *function.json* para los enlaces imperativos deseados.
- Pase un parámetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) o [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Utilice el siguiente patrón de C# para realizar el enlace de datos.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` es el atributo de .NET que define el enlace y `T` es el tipo de entrada o de salida compatible con ese tipo de enlace. `T` no puede ser también un tipo de parámetro `out` (como `out JObject`). Por ejemplo, el enlace de salida de la tabla de Mobile Apps admite [seis tipos de salida](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), pero solo se puede utilizar [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) para `T`.

El ejemplo de código siguiente crea un [enlace de salida al blob de almacenamiento](functions-bindings-storage-blob.md#using-a-blob-output-binding) con la ruta de acceso al blob definida en tiempo de ejecución y, a continuación, escribe una cadena en el blob.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define el enlace de entrada o salida del [blob de almacenamiento](functions-bindings-storage-blob.md), y [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) es un tipo de enlace de salida admitido.
De esta forma, el código obtiene la configuración de la aplicación predeterminada para la cadena de conexión de la cuenta de almacenamiento (que es `AzureWebJobsStorage`). Se puede especificar una configuración personalizada de la aplicación para utilizarla agregando el atributo [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) y pasando la matriz de atributos a `BindAsync<T>()`. Por ejemplo,

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

En la tabla siguiente, aparecen los atributos de .NET para cada tipo de enlace, así como los paquetes en los que se definen.

> [!div class="mx-codeBreakAll"]
| Enlace | Atributo | Agregar referencia |
|------|------|------|
| Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Cola de almacenamiento | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Blob de almacenamiento | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Tabla de almacenamiento | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Procedimientos recomendados de Azure Functions](functions-best-practices.md)
* [Azure Functions developer reference](functions-reference.md)
* [Referencia para desarrolladores de F# de Azure Functions](functions-reference-fsharp.md)
* [Referencia para desarrolladores de NodeJS de Funciones de Azure](functions-reference-node.md)
* [Enlaces y desencadenadores de las Funciones de azure](functions-triggers-bindings.md)

