---
title: Referencia para desarrolladores de F# de Azure Functions | Microsoft Docs
description: "Cómo desarrollar Azure Functions usando F#."
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
editor: 
tags: 
keywords: "Azure Functions, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor, F#"
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: functions
ms.devlang: fsharp
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/09/2016
ms.author: syclebsc
ms.openlocfilehash: 314f528a1fcef2c7afb0eedba012023f3bc9502b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-f-developer-reference"></a>Referencia para desarrolladores de F# de Azure Functions
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

F# para Azure Functions es una solución para ejecutar fácilmente pequeños fragmentos de código, o "funciones", en la nube. Los datos fluyen en la función de F# a través de los argumentos de función. Los nombres de los argumentos se especifican en `function.json`, y hay nombres predefinidos para acceder a cosas como el registrador de funciones y los tokens de cancelación.

En este artículo se supone que ya ha leído [Referencia para desarrolladores de Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Cómo funciona .fsx
Un archivo `.fsx` es un script de F#. Se puede considerar como un proyecto de F# que se encuentra en un único archivo. El archivo contiene el código de programa (en este caso, Azure Function) y las directivas para la administración de dependencias.

Cuando se usa un `.fsx` para Azure Function, normalmente se incluyen automáticamente los ensamblados necesarios, esto le permite a usted centrarse en la función en lugar de en códigos "reutilizables".

## <a name="binding-to-arguments"></a>Enlace a argumentos
Cada enlace admite un conjunto de argumentos, como se detalla en [Referencias para desarrolladores de desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md). Por ejemplo, uno de los enlaces de argumento que un desencadenador de blob admite es un POCO, que se puede expresar utilizando un registro de F#. Por ejemplo:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

La F# de Azure Functions tendrá uno o más argumentos. Al hablar de los argumentos de Azure Functions, se hace referencia a argumentos de *entrada* y argumentos de *salida*. Un argumento de entrada es exactamente eso: una entrada a la F# de Azure Functions. Un argumento de *salida* es una serie de datos mutables o un argumento `byref<>` que sirve para volver a pasar datos de *salida* de la función.

En el ejemplo anterior, `blob` es un argumento de entrada, y `output` es un argumento de salida. Observe que hemos usado `byref<>` para `output` (no es necesario agregar la anotación `[<Out>]`). El uso de un tipo `byref<>` permite a la función cambiar el registro u objeto al que hace referencia el argumento.

Cuando se utiliza un registro de F# como un tipo de entrada, la definición de registro tiene que marcarse con `[<CLIMutable>]` para permitir que el marco de Azure Functions establezca los campos de forma adecuada antes de pasar el registro a la función. Internamente, `[<CLIMutable>]` genera métodos establecedores para las propiedades de registro. Por ejemplo:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

También puede utilizarse una clase F# tanto para argumentos de entrada como de salida. Para una clase, las propiedades normalmente necesitan captadores y establecedores. Por ejemplo:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registro
Para registrar la salida en los [registros de streaming](../app-service/web-sites-enable-diagnostic-log.md) en F#, la función debe adoptar un argumento de tipo `TraceWriter`. Por coherencia, se recomienda que se le dé el nombre `log`a este argumento. Por ejemplo:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
Se puede usar el flujo de trabajo `async`, pero el resultado tiene que devolver una `Task`. Esto puede hacerse con `Async.StartAsTask`, por ejemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token de cancelación
Si la función tiene que controlar el apagado correctamente, puede darle un argumento [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) . Esto se puede combinar con `async`, por ejemplo:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importación de espacios de nombres
Los espacios de nombres se pueden abrir en la manera habitual:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Los siguientes espacios de nombres se abren automáticamente:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referencia a ensamblados externos
De forma similar, las referencias del ensamblado de Framework se pueden agregar con la directiva `#r "AssemblyName"`.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
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
* `Microsoft.AspNEt.WebHooks.Common`.

Si necesita hacer referencia a un ensamblado privado, puede cargar el archivo de ensamblado en una carpeta `bin` relacionada con la función y hacer referencia a él mediante el nombre de archivo (por ejemplo, `#r "MyAssembly.dll"`). Para más información acerca de cómo cargar archivos en su carpeta de función, consulte la sección siguiente sobre administración de paquetes.

## <a name="editor-prelude"></a>Preludio del editor
Un editor que admita F# Compiler Services no será consciente de los espacios de nombres y ensamblados que Azure Functions incluye automáticamente. Por lo tanto, puede resultar útil incluir un preludio que ayude al editor a encontrar los ensamblados que esté utilizando y a abrir explícitamente los espacios de nombre. Por ejemplo:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Cuando Azure Functions ejecuta el código, procesa el origen con `COMPILED` definido, por lo que se omitirá el preludio del editor.

<a name="package"></a>

## <a name="package-management"></a>Administración de paquetes
Para utilizar paquetes de NuGet en una función de F#, agregue un archivo `project.json` a la carpeta de la función en el sistema de archivos de la aplicación de función. Este es un ejemplo de archivo `project.json` que agrega una referencia de paquetes de NuGet a `Microsoft.ProjectOxford.Face` versión 1.1.0:

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

Solo se admite .NET Framework 4.6, así que asegúrese de que su archivo `project.json` especifique `net46` como se muestra aquí.

Al cargar un archivo `project.json` , el sistema en tiempo de ejecución obtiene los paquetes y agrega automáticamente las referencias a sus ensamblados. No es necesario agregar directivas `#r "AssemblyName"` . Simplemente agregue las instrucciones `open` al archivo `.fsx`.

Puede que desee colocar automáticamente ensamblados de referencias en el preludio del editor, para mejorar la interacción de su editor con F# Compiler Services.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Cómo agregar un archivo `project.json` a Azure Functions
1. En primer lugar, asegúrese de que la aplicación de la función se está ejecutando, lo que puede hacer abriéndola en el Portal de Azure. Esto también proporciona acceso a los registros de streaming donde se mostrará la salida de la instalación del paquete.
2. Para cargar un archivo `project.json` , utilice uno de los métodos descritos en [Actualización de los archivos de aplicación de función](functions-reference.md#fileupdate). Si se usa [Implementación continua para Azure Functions](functions-continuous-deployment.md), se puede agregar un archivo `project.json` a la rama de ensayo para experimentar con él antes de agregarlo a la rama de implementación.
3. Una vez cargado el archivo `project.json` , verá un resultado similar al del ejemplo siguiente en el registro de streaming de la función:

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
Para obtener una variable de entorno o un valor de configuración de aplicación, use `System.Environment.GetEnvironmentVariable`, por ejemplo:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilización del código .fsx
Puede utilizar el código de otros archivos `.fsx` mediante una directiva `#load`. Por ejemplo:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Las rutas de acceso a la directiva `#load` son relativas a la ubicación del archivo `.fsx`.

* `#load "logger.fsx"` carga un archivo que se encuentra en la carpeta de la función.
* `#load "package\logger.fsx"` carga un archivo que se encuentra en la carpeta `package` dentro de la carpeta de la función.
* `#load "..\shared\mylogger.fsx"` carga un archivo ubicado en la carpeta `shared` al mismo nivel que la carpeta de la función, es decir, directamente en `wwwroot`.

La directiva `#load` solo funciona con archivos `.fsx` (script de F#) y no con archivos `.fs`.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Guía de F#](/dotnet/articles/fsharp/index)
* [Procedimientos recomendados de Azure Functions](functions-best-practices.md)
* [Azure Functions developer reference](functions-reference.md)
* [Enlaces y desencadenadores de las Funciones de azure](functions-triggers-bindings.md)
* [Prueba de Azure Functions](functions-test-a-function.md)
* [Escalado de Azure Functions](functions-scale.md)

