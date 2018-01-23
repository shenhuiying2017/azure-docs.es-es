---
title: Enlaces HTTP y webhook en Azure Functions
description: "Descubra cómo utilizar desencadenadores y enlaces HTTP y webhook en Azure Functions."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor, HTTP, API, REST"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: mahender
ms.openlocfilehash: 080712e0a6c05348e7163f3c8e2055e6ff2806b2
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Enlaces HTTP y webhook en Azure Functions

En este artículo se explica cómo trabajar con enlaces HTTP en Azure Functions. Azure Functions admite enlaces de salida y desencadenadores HTTP.

Es posible personalizar un desencadenador HTTP para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook). Un desencadenador de webhook solo acepta una carga JSON y valida el JSON. Existen versiones especiales del desencadenador de webhook que permite facilitar el control de los webhooks de ciertos proveedores, como GitHub y Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="trigger"></a>Desencadenador

El desencadenador HTTP permite invocar una función con una solicitud HTTP. Puede usar un desencadenador HTTP para crear API sin servidor y responder a webhooks. 

De forma predeterminada, un desencadenador HTTP responde a la solicitud con un código de estado HTTP 200 OK y un cuerpo vacío. Para modificar la respuesta, configure un [enlace de salida HTTP](#http-output-binding).

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Desencadenador: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de script de C# que se enlaza a `HttpRequestMessage`:

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

Puede enlazar a un objeto personalizado en lugar de `HttpRequestMessage`. Este objeto se crea a partir del cuerpo de la solicitud, analizado como JSON. Del mismo modo, puede pasarse un tipo al enlace de la salida de respuesta HTTP y se devuelve como el cuerpo de la respuesta, con el código de estado 200.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

### <a name="trigger---f-example"></a>Desencadenador: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de desencadenador en un archivo *function.json* y una [función de F#](functions-reference-fsharp.md) que usa el enlace. La función busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de F#:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Necesita un archivo `project.json` que use NuGet para hacer referencia a los ensamblados `FSharp.Interop.Dynamic` y `Dynamitey`, como se muestra en el ejemplo siguiente:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
## <a name="trigger---webhook-example"></a>Desencadenador: ejemplo de webhook

Vea el ejemplo específico del lenguaje:

* [C#](#webhook---c-example)
* [Script de C# (.csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhook: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que envía una respuesta HTTP 200 a una solicitud JSON genérica.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhook: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de webhook en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. Esta función registra los comentarios de problemas de GitHub.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de script de C#:

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>Webhook: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de desencadenador de webhook en un archivo *function.json* y una [función de F#](functions-reference-fsharp.md) que usa el enlace. Esta función registra los comentarios de problemas de GitHub.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de F#:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>Webhook: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de webhook en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. Esta función registra los comentarios de problemas de GitHub.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
```

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Desencadenador: atributos

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http).

Puede establecer el nivel de autorización y los métodos HTTP permitidos en los parámetros del constructor de atributo. Además, existen propiedades para la plantilla de ruta y el tipo de webhook. Para más información sobre estos valores, consulte [Desencadenador: configuración](#trigger---configuration). A continuación, se muestra un atributo `HttpTrigger` en una signatura de método:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Para obtener un ejemplo completo, consulte [Desencadenador: ejemplo de C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `HttpTrigger`.


|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
| **type** | N/D| Requerida: se debe establecer en `httpTrigger`. |
| **dirección** | N/D| Requerida: se debe establecer en `in`. |
| **name** | N/D| Requerida: nombre de variable que se usa en el código de la función para la solicitud o el cuerpo de la solicitud. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina qué claves, si las hubiera, deben estar presentes en la solicitud para poder invocar a la función. El nivel de autorización puede ser uno de los siguientes: <ul><li><code>anonymous</code>: no se requiere ninguna clave de API.</li><li><code>function</code>: se requiere una clave de API específica de la función. Este es el valor predeterminado si no se proporciona ninguno.</li><li><code>admin</code>: se requiere la clave maestra.</li></ul> Para más información, consulte la sección sobre las [claves de autorización](#authorization-keys). |
| **methods** |**Métodos** | Una matriz de los métodos HTTP a los que responde la función. Si no se especifica, la función responde a todos los métodos HTTP. Consulte cómo [personalizar el punto de conexión HTTP](#trigger---customize-the-http-endpoint). |
| **route** | **Route** | Define la plantilla de ruta y controla las direcciones URL de solicitud a las que responde la función. El valor predeterminado es `<functionname>` si no se proporciona ninguno. Para más información, consulte cómo [personalizar el punto de conexión HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Configura el desencadenador HTTP para que actúe como un receptor de [webhook](https://en.wikipedia.org/wiki/Webhook) para el proveedor especificado. No establezca la propiedad `methods` si establece esta propiedad. El tipo de webhook puede ser uno de los valores siguientes:<ul><li><code>genericJson</code>: un punto de conexión de webhook de uso general sin lógica para un proveedor concreto. Este valor restringe las solicitudes a solo aquellas que usan HTTP POST y con el tipo de contenido `application/json`.</li><li><code>github</code>&mdash;La función responde a [webhooks de GitHub](https://developer.github.com/webhooks/). No use la propiedad _authLevel_ con webhooks de GitHub. Para más información, consulte la sección sobre webhooks de GitHub que aparece más adelante en este artículo.</li><li><code>slack</code>&mdash;La función responde a [webhooks de Slack](https://api.slack.com/outgoing-webhooks). No use la propiedad _authLevel_ con webhooks de Slack. Para más información, consulte la sección sobre webhooks de Slack que aparece más adelante en este artículo.</li></ul>|

## <a name="trigger---usage"></a>Desencadenador: uso

Para las funciones C# y F #, puede declarar que el tipo de entrada del desencadenador sea `HttpRequestMessage` o un tipo personalizado. Si elige `HttpRequestMessage`, obtiene acceso completo al objeto de solicitud. En un tipo personalizado, Functions intenta analizar el cuerpo de la solicitud JSON para establecer las propiedades del objeto. 

Para las funciones de JavaScript, el sistema en tiempo de ejecución de Funciones proporciona el cuerpo de la solicitud en lugar del objeto de solicitud. Para más información, consulte el [ejemplo de desencadenador de JavaScript](#trigger---javascript-example).

### <a name="github-webhooks"></a>Webhooks de GitHub

Para responder a webhooks de GitHub, primero cree la función con un desencadenador HTTP y establezca la propiedad **webHookType** en `github`. Luego copie su dirección URL y clave de API en la página **Agregar webhook** del repositorio GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Para obtener un ejemplo, vea [Creación de una función desencadenada por Webhook de GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Webhooks de Slack

El webhook de Slack genera un token en lugar de permitirle especificarlo, por lo que debe configurar una clave específica de función con el token desde Slack. Consulte [Claves de autorización](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Personalización del punto de conexión HTTP

De forma predeterminada, al crear una función para un desencadenador HTTP, o webhook, la función se puede dirigir con una ruta que tenga el siguiente formato:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Puede personalizar esta ruta mediante el parámetro opcional `route` del enlace de entrada del desencadenador HTTP. Por ejemplo, el siguiente archivo *function.json* define una propiedad `route` para un desencadenador HTTP:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Al usar esta configuración, ya se podrá dirigir la función con la ruta de abajo, en lugar de con la original.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Esto permite que el código de la función admita dos parámetros en la dirección: _category_ e _id_. Puede usar cualquier [restricción de ruta de API web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) con sus parámetros. El siguiente código de función de C# emplea los dos parámetros.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

A continuación, encontrará el código de función de Node.js que usa los mismos parámetros de ruta.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

De forma predeterminada, todas las rutas de la función tienen el prefijo *api*. También puede personalizar o quitar el prefijo con la propiedad `http.routePrefix` del archivo [host.json](functions-host-json.md). En el ejemplo siguiente, se quita el prefijo de ruta *api* utilizando una cadena vacía del prefijo del archivo *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Claves de autorización

Los desencadenadores HTTP permiten usar claves para una mayor seguridad. Un desencadenador HTTP estándar puede usarlas como claves de API, lo que exige que la clave esté en la solicitud. Los webhooks pueden usar claves para autorizar solicitudes de varias maneras, según lo que admita el proveedor.

Las claves se almacenan como parte de la aplicación de función en Azure y se cifran en reposo. Para ver las claves, crear unas nuevas o asignarles nuevos valores, navegue a una de las funciones en el portal y seleccione "Administrar". 

Existen dos tipos de claves:

- **Claves de host**: estas claves se comparten entre todas las funciones dentro de la aplicación de función. Cuando se usan como una clave de API, permiten el acceso a cualquier función dentro de la aplicación de función.
- **Claves de función**: estas claves se aplican solo a las funciones específicas en las que se definen. Cuando se usan como una clave de API, solo permiten el acceso a esa función.

Para cada clave se usa un nombre fácilmente referenciable y hay una clave predeterminada (denominada "predeterminada") en el nivel de función y host. Las claves de función tienen prioridad sobre las claves de host. Si se definen dos claves con el mismo nombre, siempre se usa la clave de función.

La **clave maestra** es una clave de host predeterminada denominada "_master" que se define para cada aplicación de función. No se puede revocar esta clave. Proporciona acceso administrativo a las API en tiempo de ejecución. El uso de `"authLevel": "admin"` en el JSON de enlace exige que esta clave esté presente en la solicitud; cualquier otra clave da lugar a un error de autorización.

> [!IMPORTANT]  
> Debido a los permisos elevados otorgados por la clave maestra, no debe compartir esta clave con terceros ni distribuirla en aplicaciones cliente nativas. Tenga cuidado al elegir el nivel de autorización de administrador.

### <a name="api-key-authorization"></a>Autorización de la clave de API

De forma predeterminada, un desencadenador HTTP requiere una clave de API en la solicitud HTTP. Por lo tanto, la solicitud HTTP normalmente tiene este aspecto:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

La clave se puede incluir en una variable de cadena de consulta denominada `code`, como se ha indicado anteriormente, o puede incluirse en un encabezado HTTP `x-functions-key`. El valor de la clave puede ser cualquier clave de función definida para la función o cualquier clave de host.

Puede permitir solicitudes anónimas, que no requieren claves. También puede exigir que se use la clave principal. Cambie el nivel de autorización predeterminado mediante la propiedad `authLevel` en el JSON de enlace. Para más información, consulte [Desencadenador: configuración](#trigger---configuration).

### <a name="keys-and-webhooks"></a>Claves y webhooks

La autorización de webhook se controla mediante el componente receptor de webhook, parte del desencadenador HTTP; el mecanismo varía según el tipo de webhook. Sin embargo, cada mecanismo se basa en una clave. De forma predeterminada, se usa la clave de función denominada "default". Para usar otra clave, configure el proveedor de webhook de modo que envíe el nombre de la clave con la solicitud de una de las siguientes maneras:

- **Cadena de consulta**: el proveedor pasa el nombre de la clave en el parámetro de la cadena de consulta `clientid`, como `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Encabezado de solicitud**: el proveedor pasa el nombre de clave en el encabezado `x-functions-clientid`.

## <a name="trigger---hostjson-properties"></a>Desencadenador: propiedades de host.json

El archivo [host.json](functions-host-json.md) contiene opciones de configuración que controlan el comportamiento de desencadenador HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Salida

Use el enlace de salida HTTP para responder al remitente de la solicitud HTTP. Este enlace requiere un desencadenador HTTP y le permite personalizar la respuesta asociada con la solicitud del desencadenador. Si no se proporciona un enlace de salida HTTP, un desencadenador HTTP devuelve HTTP 200 OK con un cuerpo vacío. 

## <a name="output---configuration"></a>Salida: configuración

En el caso de las bibliotecas de clases de C#, no hay propiedades de configuración de enlace específicas de la salida. Para enviar una respuesta HTTP, haga que el tipo de valor devuelto de la función sea `HttpResponseMessage` o `Task<HttpResponseMessage>`.

Para otros lenguajes, un enlace de salida HTTP en la matriz `bindings` de function.json, como se muestra en el ejemplo siguiente:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json*.

|Propiedad  |DESCRIPCIÓN  |
|---------|---------|
| **type** |Se debe establecer en `http`. |
| **dirección** | Se debe establecer en `out`. |
|**name** | Nombre de la variable usado en el código de la función para la respuesta. |

## <a name="output---usage"></a>Uso de salidas

Puede usar el parámetro de salida para responder al autor de la llamada HTTP o de webhook. También puede usar los patrones de respuesta estándar del lenguaje. Para respuestas de ejemplo, consulte el [ejemplo de desencadenador](#trigger---example) y el [ejemplo de webhook](#trigger---webhook-example).

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
