---
title: Enlaces de webhook y HTTP de Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores y enlaces HTTP y webhook en funciones de Azure."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: mahender
ms.openlocfilehash: 3c3247592cbe2bc382d220264b0c646ee566b8a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Enlaces HTTP y webhook en funciones de Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y trabajar con desencadenadores y enlaces HTTP en Azure Functions.
Con ellos, puede usar Azure Functions para crear API sin servidor y responder a webhooks.

Azure Functions proporciona los siguientes enlaces:
- Un [desencadenador HTTP](#httptrigger) permite invocar una función con una solicitud HTTP. Puede personalizarlo para responder a [webhooks](#hooktrigger).
- Un [enlace de salida HTTP](#output) le permite responder a la solicitud.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>Desencadenador HTTP
El desencadenador HTTP ejecuta la función en respuesta a una solicitud HTTP. Puede personalizarlo para que responda a una dirección URL o un conjunto de métodos HTTP determinados. Un desencadenador HTTP también se puede configurar para responder a webhooks. 

Si usa el portal de Functions, también puede comenzar de inmediato a usar una plantilla predefinida. Seleccione **Nueva función** y elija "API y Webhooks" en el menú desplegable **Escenario**. Seleccione una de las plantillas y haga clic en **Crear**.

De forma predeterminada, un desencadenador HTTP responde a la solicitud con un código de estado HTTP 200 OK y un cuerpo vacío. Para modificar la respuesta, configure un [enlace de salida HTTP](#output).

### <a name="configuring-an-http-trigger"></a>Configuración de un desencadenador HTTP
Un objeto JSON define un desencadenador HTTP en la matriz `bindings` de function.json, como se muestra en el ejemplo siguiente:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
El enlace admite las siguientes propiedades:

|Propiedad  |Descripción  |
|---------|---------|
| **name** | Requerida: nombre de variable que se usa en el código de la función para la solicitud o el cuerpo de la solicitud. Consulte [Trabajar con un desencadenador HTTP desde código](#httptriggerusage). |
| **type** | Requerida: se debe establecer en `httpTrigger`. |
| **dirección** | Requerida: se debe establecer en `in`. |
| **authLevel** | Determina qué claves, si las hubiera, deben estar presentes en la solicitud para poder invocar a la función. El valor puede ser uno de los siguientes: <ul><li><code>anonymous</code>: no se requiere ninguna clave de API.</li><li><code>function</code>: se requiere una clave de API específica de la función. Este es el valor predeterminado si no se proporciona ninguno.</li><li><code>admin</code>: se requiere la clave maestra.</li></ul> Para más información, vea [Trabajar con claves](#keys). |
| **methods** | Una matriz de los métodos HTTP a los que responde la función. Si no se especifica, la función responde a todos los métodos HTTP. Consulte [Personalización del punto de conexión HTTP](#url). |
| **route** | Define la plantilla de ruta y controla las direcciones URL de solicitud a las que responde la función. El valor predeterminado es `<functionname>` si no se proporciona ninguno. Para más información, vea [Personalización del punto de conexión HTTP](#url). |
| **webHookType** | Configura el desencadenador HTTP para que actúe como un receptor de webhook para el proveedor especificado. No use la propiedad _methods_ cuando emplee este valor. El valor puede ser uno de los siguientes:<ul><li><code>genericJson</code>: un punto de conexión de webhook de uso general sin lógica para un proveedor concreto.</li><li><code>github</code>: la función responde a webhooks de GitHub. No use la propiedad _authLevel_ cuando emplee este valor.</li><li><code>slack</code>: la función responde a webhooks de Slack. No use la propiedad _authLevel_ cuando emplee este valor.</li></ul> Para más información, vea [Respuesta a webhooks](#hooktrigger). |

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Trabajar con un desencadenador HTTP desde código
En las funciones de C# y F#, puede declarar que el tipo de entrada del desencadenador sea `HttpRequestMessage` o un tipo .NET personalizado. Si elige `HttpRequestMessage`, obtiene acceso completo al objeto de solicitud. En un tipo .NET personalizado, Functions intenta analizar el cuerpo de la solicitud JSON para establecer las propiedades del objeto. 

Para las funciones de Node.js, el sistema en tiempo de ejecución de Funciones proporciona el cuerpo de la solicitud en lugar del objeto de solicitud. Para más información, vea [Ejemplos de desencadenador HTTP](#httptriggersample).


<a name="output"></a>
## <a name="http-response-output-binding"></a>Enlace de salida de respuesta HTTP
Use el enlace de salida HTTP para responder al remitente de la solicitud HTTP. Este enlace requiere un desencadenador HTTP y le permite personalizar la respuesta asociada con la solicitud del desencadenador. Si no se proporciona un enlace de salida HTTP, un desencadenador HTTP devuelve HTTP 200 OK con un cuerpo vacío. 

### <a name="configuring-an-http-output-binding"></a>Configuración de un enlace de salida HTTP
Un objeto JSON define un enlace de salida HTTP en la matriz `bindings` de function.json, como se muestra en el ejemplo siguiente:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
El enlace admite las siguientes propiedades requeridas:

|Propiedad  |Descripción  |
|---------|---------|
|**name** | Nombre de la variable usado en el código de la función para la respuesta. Consulte [Trabajar con un enlace de salida HTTP desde código](#outputusage). |
| **type** |Se debe establecer en `http`. |
| **dirección** | Se debe establecer en `out`. |

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Trabajar con un enlace de salida HTTP desde código
Puede usar el parámetro de salida para responder al autor de la llamada de http o webhook. También puede usar los patrones de respuesta estándar del lenguaje. Para obtener respuestas de ejemplo, vea [Ejemplos de desencadenador HTTP](#httptriggersample) y [Ejemplos de desencadenador de webhook](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Respuesta a webhooks
Un desencadenador HTTP con la propiedad _webHookType_ se configura para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook). La configuración básica usa el valor "genericJson". Este valor restringe las solicitudes a solo aquellas que usan HTTP POST y con el tipo de contenido `application/json`.

El desencadenador se puede adaptar aún más a un proveedor de webhook específico, como [GitHub](https://developer.github.com/webhooks/) o [Slack](https://api.slack.com/outgoing-webhooks). Si se especifica un proveedor, el runtime Functions puede encargarse de la lógica de validación del proveedor.  

### <a name="configuring-github-as-a-webhook-provider"></a>Configuración de GitHub como proveedor de webhook
Para responder a webhooks de GitHub, primero cree la función con un desencadenador HTTP y establezca la propiedad **webHookType** en `github`. Luego copie su [dirección URL](#url) y [clave de API](#keys) en la página **Agregar Webhook** del repositorio de GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Para obtener un ejemplo, vea [Creación de una función desencadenada por Webhook de GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="configuring-slack-as-a-webhook-provider"></a>Configuración de Slack como un proveedor de webhooks
El webhook de Slack genera un token en lugar de permitirle especificarlo, por lo que debe configurar una clave específica de función con el token desde Slack. Consulte [Trabajar con claves](#keys).

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>Personalización del punto de conexión HTTP
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

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

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

A continuación, encontrará un código de función de Node.js que usa los mismos parámetros de ruta.

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

De forma predeterminada, todas las rutas de la función tienen el prefijo *api*. También puede personalizar o quitar el prefijo con la propiedad `http.routePrefix` del archivo *host.json*. En el ejemplo siguiente, se quita el prefijo de ruta *api* utilizando una cadena vacía del prefijo del archivo *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

Para obtener información detallada sobre cómo actualizar el archivo *host.json* para su función, consulte el artículo sobre [Actualización de los archivos de aplicación de función](functions-reference.md#fileupdate). 

Para obtener información sobre otras propiedades que puede configurar en el archivo *host.json*, consulte la [referencia de host.json](functions-host-json.md).


<a name="keys"></a>
## <a name="working-with-keys"></a>Trabajar con claves
Los desencadenadores HTTP permiten usar claves para una mayor seguridad. Un desencadenador HTTP estándar puede usarlas como claves de API, lo que exige que la clave esté en la solicitud. Los webhooks pueden usar claves para autorizar solicitudes de varias maneras, según lo que admita el proveedor.

Las claves se almacenan como parte de la aplicación de función en Azure y se cifran en reposo. Para ver las claves, crear unas nuevas o asignarles nuevos valores, desplácese a una de las funciones en el portal y seleccione "Administrar". 

Existen dos tipos de claves:
- **Claves de host**: estas claves se comparten entre todas las funciones dentro de la aplicación de función. Cuando se usan como una clave de API, permiten el acceso a cualquier función dentro de la aplicación de función.
- **Claves de función**: estas claves se aplican solo a las funciones específicas en las que se definen. Cuando se usan como una clave de API, solo permiten el acceso a esa función.

Para cada clave se usa un nombre fácilmente referenciable y hay una clave predeterminada (denominada "predeterminada") en el nivel de función y host. La **clave maestra** es una clave de host predeterminada denominada "_master" que se define para cada aplicación de función. No se puede revocar esta clave. Proporciona acceso administrativo a las API en tiempo de ejecución. El uso de `"authLevel": "admin"` en el JSON de enlace exige que esta clave esté presente en la solicitud; cualquier otra clave da lugar a un error de autorización.

> [!IMPORTANT]  
> Debido a los permisos elevados otorgados por la clave maestra, no debe compartir esta clave con terceros ni distribuirla en aplicaciones cliente nativas. Tenga cuidado al elegir el nivel de autorización de administrador.

### <a name="api-key-authorization"></a>Autorización de la clave de API
De forma predeterminada, un desencadenador HTTP requiere una clave de API en la solicitud HTTP. Por lo tanto, la solicitud HTTP normalmente tiene este aspecto:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

La clave se puede incluir en una variable de cadena de consulta denominada `code`, como se ha indicado anteriormente, o puede incluirse en un encabezado HTTP `x-functions-key`. El valor de la clave puede ser cualquier clave de función definida para la función o cualquier clave de host.

Puede permitir solicitudes anónimas, que no requieren claves. También puede exigir que se use la clave principal. Cambie el nivel de autorización predeterminado mediante la propiedad `authLevel` en el JSON de enlace. Para más información, vea [Desencadenador HTTP](#httptrigger).

### <a name="keys-and-webhooks"></a>Claves y webhooks
La autorización de webhook se controla mediante el componente receptor de webhook, parte del desencadenador HTTP; el mecanismo varía según el tipo de webhook. Sin embargo, cada mecanismo se basa en una clave. De forma predeterminada, se usa la clave de función denominada "default". Para usar otra clave, configure el proveedor de webhook de modo que envíe el nombre de la clave con la solicitud de una de las siguientes maneras:

- **Cadena de consulta**: el proveedor pasa el nombre de la clave en el parámetro de la cadena de consulta `clientid`, como `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Encabezado de solicitud**: el proveedor pasa el nombre de clave en el encabezado `x-functions-clientid`.

> [!NOTE]
> Las claves de función tienen prioridad sobre las claves de host. Si se definen dos claves con el mismo nombre, siempre se usa la clave de función.


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>Ejemplos de desencadenador HTTP
Supongamos que el siguiente desencadenador HTTP se encuentra en la matriz `bindings` de function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Consulte el ejemplo específico del idioma que busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.js](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>Ejemplo de desencadenador HTTP en C# #
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

También puede enlazar a un objeto .NET en lugar de a **HttpRequestMessage**. Este objeto se crea a partir del cuerpo de la solicitud, analizado como JSON. Del mismo modo, puede pasarse un tipo al enlace de la salida de respuesta HTTP y se devuelve como el cuerpo de la respuesta, con el código de estado 200.

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

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>Ejemplo de desencadenador HTTP en F# #
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

Necesita un archivo `project.json` que use NuGet para hacer referencia a los ensamblados `FSharp.Interop.Dynamic` y `Dynamitey`, como el siguiente:

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

Este usa NuGet para obtener las dependencias y hace referencia a ellas en el script.

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Ejemplo de desencadenador HTTP en Node.js
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
     
<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Ejemplos de webhook
Supongamos que tiene el siguiente desencadenador webhook en la matriz `bindings` de function.json:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Consulte el ejemplo específico del idioma que registra comentarios de problemas de GitHub.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.js](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>Ejemplo de webhook en C# #
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

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>Ejemplo de webhook en F# #
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

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Ejemplo de webhook en Node.js
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

