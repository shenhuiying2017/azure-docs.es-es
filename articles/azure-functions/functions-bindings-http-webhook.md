---
title: Enlaces de webhook y HTTP de Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores y enlaces HTTP y webhook en funciones de Azure."
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 06958522139d621f86afd8bf25128ee64cf822b3
ms.lasthandoff: 03/29/2017


---
# <a name="azure-functions-http-and-webhook-bindings"></a>Enlaces HTTP y webhook en funciones de Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y trabajar con desencadenadores y enlaces HTTP en Azure Functions.
Con ellos, puede usar Azure Functions para crear API sin servidor y responder a webhooks.

Azure Functions proporciona los siguientes enlaces:
- Un [desencadenador HTTP](#httptrigger) permite invocar una función con una solicitud HTTP. Puede personalizarlo para responder a [webhooks](#hooktrigger).
- Un [enlace de salida HTTP](#output) le permite responder a la solicitud.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!TIP]
>
> Se recomienda leer este documento de procedimientos recomendados sobre [HTTPClient](https://github.com/mspnp/performance-optimization/blob/master/ImproperInstantiation/docs/ImproperInstantiation.md).
>

<a name="httptrigger"></a>

## <a name="http-trigger"></a>Desencadenador HTTP
El desencadenador HTTP ejecutará la función en respuesta a una solicitud HTTP. Puede personalizarlo para que responda a una dirección URL o un conjunto de métodos HTTP determinados. Un desencadenador HTTP también se puede configurar para responder a webhooks. 

Si usa el portal de Functions, también puede comenzar de inmediato a usar una plantilla predefinida. Seleccione **Nueva función** y elija "API y Webhooks" en el menú desplegable **Escenario**. Seleccione una de las plantillas y haga clic en **Crear**.

De forma predeterminada, un desencadenador HTTP responderá a la solicitud con un código de estado HTTP 200 OK y un cuerpo vacío. Para modificar la respuesta, configure un [enlace de salida HTTP](#output).

### <a name="configuring-an-http-trigger"></a>Configuración de un desencadenador HTTP
Un desencadenador HTTP se define mediante la inclusión de un objeto JSON similar al siguiente en la matriz `bindings` de function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "GET" ],
    "route": "values/{id}"
},
```
El enlace admite las siguientes propiedades:

* **name** (Requerido): el nombre de variable que se usa en el código de función para la solicitud o el cuerpo de la solicitud. Consulte [Trabajar con un desencadenador HTTP desde código](#httptriggerusage).
* **type** (Requerido): debe establecerse en "httpTrigger".
* **dirección** (Requerido): debe establecerse en "in".
* _authLevel_: determina qué claves, si las hay, deben estar presentes en la solicitud para poder invocar la función. Consulte [Trabajar con claves](#keys) a continuación. El valor puede ser uno de los siguientes:
    * _anonymous_: no se requiere ninguna clave de API.
    * _function_: se requiere una clave de API específica de la función. Este es el valor predeterminado si no se proporciona ninguno.
    * _admin_: se requiere la clave maestra.
* **methods**: se trata de una matriz de los métodos HTTP a los que responderá la función. Si no se especifica, la función responderá a todos los métodos HTTP. Consulte [Personalización del punto de conexión HTTP](#url).
* **route**: define la plantilla de ruta y controla las direcciones URL de solicitud a las que responderá la función. El valor predeterminado es `<functionname>` si no se proporciona ninguno. Consulte [Personalización del punto de conexión HTTP](#url).
* **webHookType**: configura el desencadenador HTTP para que actúe como un receptor de webhook para el proveedor especificado. Si se elige esta opción, no debe establecerse la propiedad _methods_. Consulte [Respuesta a webhooks](#hooktrigger). El valor puede ser uno de los siguientes:
    * _genericJson_: un punto de conexión de webhook de uso general sin lógica para un proveedor concreto.
    * _github_: la función responderá a webhooks de GitHub. Si se elige esta opción, no debe establecerse la propiedad _authLevel_.
    * _slack_: la función responderá a webhooks de Slack. Si se elige esta opción, no debe establecerse la propiedad _authLevel_.

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Trabajar con un desencadenador HTTP desde código
Para las funciones C# y F #, puede declarar que el tipo de entrada del desencadenador sea `HttpRequestMessage` o un tipo personalizado. Si elige `HttpRequestMessage`, obtendrá acceso completo al objeto de solicitud. Para un tipo personalizado (como, POCO), Functions intentará analizar el cuerpo de la solicitud como JSON para rellenar las propiedades del objeto.

Para las funciones de Node.js, el sistema en tiempo de ejecución de Funciones proporciona el cuerpo de la solicitud en lugar del objeto de solicitud.

Consulte [Ejemplos de desencadenador HTTP](#httptriggersample) para ver ejemplos de uso.


<a name="output"></a>
## <a name="http-response-output-binding"></a>Enlace de salida de respuesta HTTP
Use el enlace de salida HTTP para responder al remitente de la solicitud HTTP. Este enlace requiere un desencadenador HTTP y le permite personalizar la respuesta asociada con la solicitud del desencadenador. Si no se proporciona el enlace de salida HTTP, un desencadenador HTTP devolverá HTTP 200 OK con un cuerpo vacío. 

### <a name="configuring-an-http-output-binding"></a>Configuración de un enlace de salida HTTP
El enlace de salida HTTP se define mediante la inclusión de un objeto JSON similar al siguiente en la matriz `bindings` de function.json:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
El enlace contiene las siguientes propiedades:

* **name** (Requerido): el nombre de variable que se usa en el código de función para la respuesta. Consulte [Trabajar con un enlace de salida HTTP desde código](#outputusage).
* **type** (Requerido): se debe establecer en "http".
* **dirección** (Requerido): debe establecerse en "out".

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Trabajar con un enlace de salida HTTP desde código
Puede usar el parámetro de salida (p. ej., "res") para responder a autor de la llamada de http o webhook. Como alternativa, puede usar el patrón `Request.CreateResponse()` (C#) o `context.res` estándar (Node.JS) para devolver la respuesta. Para ver ejemplos de cómo usar el último método, consulte [Ejemplos de desencadenador HTTP](#httptriggersample) y [Ejemplos de desencadenador de webhook](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Respuesta a webhooks
Un desencadenador HTTP con la propiedad _webHookType_ se configurará para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook). La configuración básica usa el valor "genericJson". Este valor restringe las solicitudes a solo aquellas que usan HTTP POST y con el tipo de contenido `application/json`.

El desencadenador se puede adaptar adicionalmente a un proveedor de webhook específico (p. ej., [GitHub](https://developer.github.com/webhooks/) y [Slack](https://api.slack.com/outgoing-webhooks)). Si se especifica un proveedor, el tiempo de ejecución de Functions puede encargarse de la lógica de validación del proveedor.  

### <a name="configuring-github-as-a-webhook-provider"></a>Configuración de GitHub como proveedor de webhook
Para responder a un webhook de GitHub, primero cree una función con un desencadenador HTTP y establezca la propiedad _webHookType_ en "github". A continuación, copie su [dirección URL](#url) y [la clave de API](#keys) en la página **Agregar Webhook** del repositorio de GitHub. Consulte la documentación [Creación de Webhooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) de GitHub para más información.

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

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

Esto permite que el código de función admita dos parámetros en la dirección: "category" e "id". Puede usar cualquier [restricción de ruta de API web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) con sus parámetros. El siguiente código de función de C# emplea los dos parámetros.

```csharp
    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
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

Para obtener información sobre otras propiedades que puede configurar en el archivo *host.json*, consulte la [referencia de host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).


<a name="keys"></a>
## <a name="working-with-keys"></a>Trabajar con claves
HttpTriggers puede hacer uso de claves para aumentar la seguridad. Un objeto HttpTrigger estándar puede usarlas como claves de API, que requieren que la clave exista en la solicitud. Los webhooks pueden usar claves para autorizar solicitudes de varias maneras, según lo que admita el proveedor.

Las claves se almacenan como parte de la aplicación de función en Azure y se cifran en reposo. Para ver las claves, crear unas nuevas o asignarles nuevos valores, desplácese a una de las funciones en el portal y seleccione "Administrar". 

Existen dos tipos de claves:
- **Claves de host**: estas claves se comparten entre todas las funciones dentro de la aplicación de función. Cuando se usan como una clave de API, permiten el acceso a cualquier función dentro de la aplicación de función.
- **Claves de función**: estas claves se aplican solo a las funciones específicas en las que se definen. Cuando se usan como una clave de API, solo permiten el acceso a esa función.

Para cada clave se usa un nombre fácilmente referenciable y hay una clave predeterminada (denominada "predeterminada") en el nivel de función y host. La **clave maestra** es una clave de host predeterminada denominada "_master" que se define para cada aplicación de función y no se puede revocar. Proporciona acceso administrativo a las API en tiempo de ejecución. El uso de `"authLevel": "admin"` en el enlace JSON requerirá que esta clave esté presente en la solicitud; cualquier otra clave producirá un error de autorización.

> [!NOTE]
> Debido a los permisos elevados otorgados por la clave maestra, no debe compartir esta clave con terceros ni distribuirla en aplicaciones cliente nativas. Tenga cuidado al elegir el nivel de autorización del administrador.
> 
> 

### <a name="api-key-authorization"></a>Autorización de la clave de API
De forma predeterminada, un objeto HttpTrigger requiere una clave de API en la solicitud HTTP. Por lo tanto, su solicitud HTTP normalmente se verá así:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

La clave se puede incluir en una variable de cadena de consulta denominada `code`, como se ha indicado anteriormente, o puede incluirse en un encabezado HTTP `x-functions-key`. El valor de la clave puede ser cualquier clave de función definida para la función o cualquier clave de host.

Puede optar por permitir solicitudes sin claves o especificar que se use la clave maestra cambiando la propiedad `authLevel` en el JSON de enlace (consulte [Desencadenador HTTP](#httptrigger)).

### <a name="keys-and-webhooks"></a>Claves y webhooks
La autorización de webhook se controla mediante el componente receptor de webhook, parte de HttpTrigger, y el mecanismo varía según el tipo de webhook. Sin embargo, cada mecanismo se basa en una clave. De forma predeterminada, se usará la clave de función denominada "predeterminada". Si quiere usar una clave diferente, debe configurar el proveedor de webhook para enviar el nombre de clave con la solicitud de una de las siguientes maneras:

- **Cadena de consulta**: el proveedor pasa el nombre de la clave en el parámetro de la cadena de consulta `clientid` (por ejemplo, `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`).
- **Encabezado de solicitud**: el proveedor pasa el nombre de clave en el encabezado `x-functions-clientid`.

> [!NOTE]
> Las claves de función tienen prioridad sobre las claves de host. Si se definen dos claves con el mismo nombre, se usará la clave de función.
> 
> 


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

Necesita un archivo `project.json` que use NuGet para hacer referencia a los ensamblados `FSharp.Interop.Dynamic` y `Dynamitey`, de este modo:

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

Se usará NuGet para capturar las dependencias y se hará referencia a ellas en el script.

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


