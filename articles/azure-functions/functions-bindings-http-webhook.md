<properties
	pageTitle="Enlaces HTTP y webhook en funciones de Azure | Microsoft Azure"
	description="Descubra cómo utilizar desencadenadores y enlaces HTTP y webhook en funciones de Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Enlaces HTTP y webhook en funciones de Azure

Este artículo explica cómo configurar y codificar desencadenadores y enlaces HTTP y webhook en funciones de Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## function.json para enlaces HTTP y webhook

El archivo *function.json* proporciona propiedades que pertenecen tanto a la solicitud y como a la respuesta.

Propiedades de la solicitud HTTP:

- `name`: nombre de la variable que se usa en el código de la función para el objeto de solicitud (o en el cuerpo de la solicitud en caso de funciones de Node.js).
- `type`: debe establecerse en *httpTrigger*.
- `direction`: debe establecerse en *in*. 
- `webHookType`: para los desencadenadores de WebHook, los valores válidos son *github*, *slack* y *genericJson*. Para un desencadenador de HTTP que no sea un WebHook, establezca esta propiedad en una cadena vacía. Para obtener más información sobre Webhooks, consulte la sección siguiente, [Desencadenadores de WebHook](#webhook-triggers).
- `authLevel`: no se aplica a los desencadenadores de WebHook. Se establece en "function" para que solicite una clave de API, en "anonymous" para que ignore este requisito, o en "admin" para que solicite una clave maestra de API. Consulte las siguientes [claves de API](#apikeys) para obtener más información.

Propiedades de la respuesta HTTP:

- `name`: nombre de la variable utilizada en el código de función para el objeto de respuesta.
- `type`: debe establecerse en *http*.
- `direction`: debe establecerse en *out*. 
 
Ejemplo de *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## Desencadenadores de WebHook

Un desencadenador de WebHook es como un desencadenador de HTTP que tiene las siguientes características diseñadas para WebHooks:

* Para los proveedores específicos de WebHook (actualmente se admiten GitHub y Slack), el sistema en tiempo de ejecución de Funciones permite validar la firma del proveedor.
* Para las funciones de Node.js, el sistema en tiempo de ejecución de Funciones proporciona el cuerpo de la solicitud en lugar del objeto de solicitud. No hay ningún control especial para las funciones de C#, ya que puede controlar lo que se proporciona especificando el tipo de parámetro. Si especifica `HttpRequestMessage`, obtendrá el objeto de solicitud. Si especifica un tipo de objeto POCO, el sistema en tiempo de ejecución de Funciones intenta analizar un objeto JSON en el cuerpo de la solicitud para rellenar las propiedades del objeto.
* Para desencadenar una función de WebHook la solicitud HTTP debe incluir una clave de API. En caso de desencadenadores de HTTP que no sean de WebHook, este requisito es opcional.

Para obtener más información acerca de cómo configurar un WebHook de GitHub, consulte [GitHub Developer - Creating WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) (Desarrolladores de GitHub: Creación de WebHooks).

## Dirección URL para desencadenar la función

Para desencadenar una función, se envía una solicitud HTTP a una URL que es una combinación de la dirección URL de aplicación de función y el nombre de función:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## Claves de API

De forma predeterminada, debe incluir una clave de API con una solicitud HTTP para desencadenar una función de HTTP o WebHook. La clave se puede incluir en una variable de cadena de consulta denominada `code` o puede estar incluida en un encabezado HTTP `x-functions-key`. Para las funciones que no son de WebHook, puede indicar que no se requiere una clave de API estableciendo la propiedad `authLevel` en "anonymous" en el archivo *function.json*.

Puede encontrar los valores de clave de API en la carpeta *D:\\home\\data\\Functions\\secrets* del sistema de archivos de la aplicación de la función. La clave maestra y la de función se establecen en el archivo *host.json* como se muestra en este ejemplo.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

La clave de función del archivo *host.json* puede usarse para desencadenar una función, pero no podrá desencadenar una función deshabilitada. La clave maestra puede usarse para desencadenar cualquier función incluso si está deshabilitada. Puede configurar una función para que solicite la clave maestra estableciendo la propiedad `authLevel` en "admin".

Si la carpeta *secrets* contiene un archivo JSON con el mismo nombre que una función, la propiedad `key` de ese archivo también se puede utilizar para desencadenar la función y esta clave solo funcionará con la función a la que hace referencia. Por ejemplo, la clave de API para una función denominada `HttpTrigger` se especifica en *HttpTrigger.json*, en la carpeta *secrets*. Este es un ejemplo:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Cuando configure un desencadenador de WebHook, no comparta la clave maestra con el proveedor de WebHook. Use una clave que solo funcione con la función que procesa el WebHook. La clave maestra se puede usar para desencadenar cualquier función, incluso las que están deshabilitadas.

## Código de ejemplo de C# para una función de desencadenador de HTTP 

El código de ejemplo busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

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

## Código de ejemplo de Node.js para una función de desencadenador de HTTP 

Este código de ejemplo busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

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

## Código de ejemplo de C# para una función de WebHook de GitHub 

Este código de ejemplo registra los comentarios de problemas de GitHub.

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

## Código de ejemplo de Node.js para una función de WebHook de GitHub 

Este código de ejemplo registra los comentarios de problemas de GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Pasos siguientes

[AZURE.INCLUDE [pasos siguientes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0622_2016-->