---
title: Referencia para desarrolladores de Azure Functions | Microsoft Docs
description: "Cómo desarrollar funciones de Azure con C#."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: f28cda01-15f3-4047-83f3-e89d5728301c
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/13/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: da9da90e7ccd5d324b8f87a3585555ea5d5ed475
ms.openlocfilehash: d587ff744fea5393a34d5a576e6af32cac5d2b44


---
# <a name="azure-functions-c-developer-reference"></a>Referencia para desarrolladores de C# de Funciones de Azure
> [!div class="op_single_selector"]
> * [Script de C#](functions-reference-csharp.md)
> * [Script de F#](functions-reference-fsharp.md)
> * [Node.js](functions-reference-node.md)
> 
> 

La experiencia en C# para las Funciones de Azure se basa en el SDK de WebJobs de Azure. Los datos fluyen en la función de C# a través de los argumentos de método. Los nombres de los argumentos se especifican en `function.json`, y hay nombres predefinidos para acceder a cosas como el registrador de funciones y los tokens de cancelación.

En este artículo se supone que ya ha leído [Referencia para desarrolladores de Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Funcionamiento de .csx
El formato `.csx` permite escribir menos "texto reutilizable" y centrarse en escribir solo una función de C#. En Azure Functions, incluya solo las referencias a ensamblados y espacios de nombres que necesite, como de costumbre, y en lugar de ajustar todo en un espacio de nombres y una clase, simplemente defina el método `Run` . Si necesita incluir todas las clases, por ejemplo para definir objetos CRL estándar (POCO), se puede incluir una clase dentro del mismo archivo.   

## <a name="binding-to-arguments"></a>Enlace a argumentos
Los distintos enlaces se vinculan a una función de C# mediante la propiedad `name` de la configuración de *function.json*. Cada enlace tiene sus propios tipos admitidos que se documentan por enlace. Por ejemplo, un desencadenador de blob puede admitir una cadena, un objeto POCO o algunos otros tipos. Puede utilizar el tipo que mejor se adapte a sus necesidades. Un objeto POCO debe tener un captador y establecedor definidos para cada propiedad. 

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

## <a name="logging"></a>Registro
Para registrar la salida en sus registros de streaming en C#, puede incluir un argumento con tipo `TraceWriter` . Es recomendable que lo denomine `log`. Le recomendamos que evite `Console.Write` en Azure Functions.

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
En algunos casos, puede que tenga operaciones que son sensibles al cierre. Aunque siempre es mejor escribir código para controlar los bloqueos, en los casos donde desee controlar las solicitudes de cierre estable, defina un argumento con tipo [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx).  Se proporcionará un `CancellationToken` si se desencadena un cierre del host. 

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
* `Microsoft.Azure.WebJobs.Host`.

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

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Además, en los siguientes ensamblados se hace un uso especial de las mayúsculas y minúsculas y se puede hacer referencia a ellos con SimpleName (por ejemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Si necesita hacer referencia a un ensamblado privado, puede cargar el archivo de ensamblado en una carpeta `bin` relacionada con la función y hacer referencia a él mediante el nombre de archivo (por ejemplo, `#r "MyAssembly.dll"`). Para más información acerca de cómo cargar archivos en su carpeta de función, consulte la sección siguiente sobre administración de paquetes.

## <a name="package-management"></a>Administración de paquetes
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

Solo se admite .NET Framework 4.6, así que asegúrese de que su archivo *project.json`net46` especifique * como se muestra aquí.

Al cargar un archivo *project.json* , el sistema en tiempo de ejecución obtiene los paquetes y agrega automáticamente las referencias a sus ensamblados. No es necesario agregar directivas `#r "AssemblyName"` . Basta con agregar las instrucciones `using` necesarias al archivo *run.csx* para que use los tipos definidos en los paquetes NuGet.

### <a name="how-to-upload-a-projectjson-file"></a>Cómo cargar un archivo project.json
1. En primer lugar, asegúrese de que la aplicación de la función se está ejecutando, lo que puede hacer abriéndola en el Portal de Azure. 
   
    Esto también proporciona acceso a los registros de streaming donde se mostrará la salida de la instalación del paquete. 
2. Para cargar un archivo project.json, use uno de los métodos descritos en la sección **Actualización de los archivos de la aplicación de función** del [tema Referencia para desarrolladores de Azure Functions](functions-reference.md#fileupdate). 
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

El uso de un *.csx* compartido es un patrón común para asignar rigurosamente los argumentos entre funciones mediante un objeto POCO. En el siguiente ejemplo simplificado, un desencadenador HTTP y un desencadenador de cola comparten un objeto POCO denominado `Order` para asignar rigurosamente los datos del pedido:

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

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Procedimientos recomendados de Azure Functions](functions-best-practices.md)
* [Azure Functions developer reference](functions-reference.md)
* [Referencia para desarrolladores de F# de Azure Functions](functions-reference-fsharp.md)
* [Referencia para desarrolladores de NodeJS de Funciones de Azure](functions-reference-node.md)
* [Enlaces y desencadenadores de las Funciones de azure](functions-triggers-bindings.md)




<!--HONumber=Nov16_HO3-->


