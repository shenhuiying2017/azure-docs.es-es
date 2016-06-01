<properties
	pageTitle="Enlaces y desencadenadores de Funciones de Azure | Microsoft Azure"
	description="Descripción sobre cómo utilizar desencadenadores y enlaces en Funciones de Azure."
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
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Referencias para desarrolladores de desencadenadores y enlaces de Funciones de Azure

Este artículo explica cómo configurar y codificar desencadenadores y enlaces de Funciones de Azure. La mayoría de estos enlaces se administra fácilmente mediante la interfaz de usuario **Integrar** del Portal de Azure, pero este no ofrece toda la funcionalidad y las opciones de cada enlace.

En este artículo se supone que ya ha leído la [referencia para desarrolladores de Funciones de Azure](functions-reference.md) y los artículos de referencia para desarrolladores de [C#](functions-reference-csharp.md) o [NodeJS](functions-reference-node.md).

## Desencadenadores y enlaces de HTTP y WebHook

Puede usar un desencadenador de HTTP o WebHook para llamar a una función en respuesta a una solicitud HTTP. La solicitud debe incluir una clave de API, que actualmente solo está disponible en la interfaz de usuario del Portal de Azure.

La dirección URL de la función es una combinación de la dirección URL de la aplicación de la función y el nombre de la misma:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

El archivo *function.json* proporciona propiedades que pertenecen a la solicitud y respuesta HTTP.

Propiedades de la solicitud HTTP:

- `name` : nombre de la variable que se usa en el código de la función para el objeto de solicitud (o en el cuerpo de la solicitud en caso de funciones de Node.js).
- `type` : debe establecerse en *httpTrigger*.
- `direction` : debe establecerse en *in*. 
- `webHookType` : para los desencadenadores de WebHook, los valores válidos son *github*, *slack* y *genericJson*. Para un desencadenador de HTTP que no sea un WebHook, establezca esta propiedad en una cadena vacía. Para más información sobre Webhooks, consulte la sección siguiente, [Desencadenadores de WebHook](#webhook-triggers).
- `authLevel` : no es aplicable a los desencadenadores de WebHook. Se establece en "function" para que solicite una clave de API, en "anonymous" para que ignore este requisito, o en "admin" para que solicite una clave maestra de API. Consulte las [claves de API](#apikeys) a continuación para más información.

Propiedades de la respuesta HTTP:

- `name` : nombre de la variable utilizada en el código de función para el objeto de respuesta.
- `type` : debe establecerse en *http*.
- `direction` : debe establecerse en *out*. 
 
Ejemplo *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
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

### Desencadenadores de WebHook

Un desencadenador de WebHook es como un desencadenador de HTTP que tiene las siguientes características diseñadas para WebHooks:

* Para los proveedores específicos de WebHook (actualmente se admiten GitHub y Slack), el sistema en tiempo de ejecución de Funciones permite validar la firma del proveedor.
* Para las funciones de Node.js, el sistema en tiempo de ejecución de Funciones proporciona el cuerpo de la solicitud en lugar del objeto de solicitud. No hay ningún control especial para las funciones de C#, ya que puede controlar lo que se proporciona especificando el tipo de parámetro. Si especifica `HttpRequestMessage`, obtendrá el objeto de solicitud. Si especifica un tipo de objeto POCO, el sistema en tiempo de ejecución de Funciones intenta analizar un objeto JSON en el cuerpo de la solicitud para rellenar las propiedades del objeto.
* Para desencadenar una función de WebHook la solicitud HTTP debe incluir una clave de API. En caso de desencadenadores de HTTP que no sean de WebHook, este requisito es opcional.

Para más información acerca de cómo configurar un WebHook de GitHub, consulte [GitHub Developer - Creating WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) (Desarrolladores de GitHub: Creación de WebHooks).

### Claves de API

De forma predeterminada, debe incluir una clave de API con una solicitud HTTP para desencadenar una función de HTTP o WebHook. La clave se puede incluir en una variable de cadena de consulta denominada `code`, o puede estar incluida en un encabezado HTTP `x-functions-key`. Para las funciones que no son de WebHook, puede indicar que no se requiere una clave de API estableciendo la propiedad `authLevel` en "anonymous" en el archivo *function.json*.

Puede encontrar los valores de clave de API en la carpeta *D:\\home\\data\\Functions\\secrets* del sistema de archivos de la aplicación de la función. La clave maestra y la de función se establecen en el archivo *host.json* como se muestra en este ejemplo.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

La clave de función del archivo *host.json* puede usarse para desencadenar una función pero no podrá desencadenar una función deshabilitada. La clave maestra puede usarse para desencadenar cualquier función incluso si está deshabilitada. Puede configurar una función para que solicite la clave maestra estableciendo la propiedad `authLevel` en "admin".

Si la carpeta *secrets* contiene un archivo JSON con el mismo nombre que una función, la propiedad `key` de ese archivo también se puede utilizar para desencadenar la función y esta clave solo funcionará con la función a la que hace referencia. Por ejemplo, la clave de API para una función denominada `HttpTrigger` se especifica en *HttpTrigger.json* en la carpeta *secrets*. Este es un ejemplo:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Cuando configure un desencadenador de WebHook, no comparta la clave maestra con el proveedor de WebHook. Use una clave que solo funcione con la función que procesa el WebHook. La clave maestra se puede usar para desencadenar cualquier función, incluso las que están deshabilitadas.

### Código de ejemplo de C# para una función de desencadenador de HTTP 

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

### Código de ejemplo de Node.js para una función de desencadenador de HTTP 

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

### Código de ejemplo de C# para una función de WebHook de GitHub 

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

### Código de ejemplo de Node.js para una función de WebHook de GitHub 

Este código de ejemplo registra los comentarios de problemas de GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Desencadenador de temporizador

El archivo *function.json* proporciona una expresión de programación y un modificador que indica si la función se debe desencadenar inmediatamente.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

El desencadenador de temporizador controla el escalado horizontal de varias instancias de forma automática: solo se ejecutará una única instancia de una función de temporizador determinada en todas las instancias.

### Formato de expresión de programación

La expresión de programación puede ser una [expresión CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que incluye 6 campos: {segundo} {minuto} {hora} {día} {mes} {día de la semana}. Muchos de los documentos de la expresión CRON que puede encontrar en Internet omiten el campo {segundo}, por lo que si copia uno de ellos tendrá que ajustar el campo adicional.

La expresión de programación también puede estar en el formato *hh:mm:ss* para especificar el retraso cada vez que se desencadena la función.

Estos son algunos ejemplos de expresiones de programación.

Para desencadenar una vez cada cinco minutos:

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Para desencadenar inmediatamente y, luego, cada dos horas a partir de entonces:

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

Para desencadenar cada 15 segundos:

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

### Ejemplo de código de C# de desencadenador de temporizador

Este ejemplo de código de C# escribe un único registro cada vez que se desencadena la función.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Desencadenadores y enlaces (colas, blobs, tablas) de Almacenamiento de Azure

Esta sección contiene las siguientes subsecciones:

* [Desencadenador de cola de Almacenamiento de Azure](#storagequeuetrigger)
* [Enlace de salida de la cola de Almacenamiento de Azure](#storagequeueoutput)
* [Desencadenador de blobs de Almacenamiento de Azure](#storageblobtrigger)
* [Enlaces de entrada y salida de blobs de Almacenamiento de Azure](#storageblobbindings)
* [Enlaces de entrada y salida de tablas de Almacenamiento de Azure](#storagetablesbindings)

### <a id="storagequeuetrigger"></a> Desencadenador de cola de Almacenamiento de Azure

El archivo *function.json* de un desencadenador de la cola de almacenamiento especifica las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de la función de la cola o el mensaje de la cola. 
- `queueName`: nombre de la cola que se sondea. Para conocer las reglas de nomenclatura de colas, consulte [Asignar nombres a colas y metadatos](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection` : nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el desencadenador funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type` : debe establecerse en *queueTrigger*.
- `direction` : debe establecerse en *in*. 

#### Ejemplo de *Function.json* para un desencadenador de la cola de almacenamiento

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### Tipos admitidos de desencadenador de cola

El mensaje de la cola se puede deserializar en cualquiera de los siguientes tipos:

* Object (de JSON)
* String
* Byte array 
* `CloudQueueMessage` (C#) 

#### Metadatos de desencadenador de cola

Puede obtener metadatos de la cola en la función mediante estos nombres de variable:

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (otra manera de recuperar el texto del mensaje en cola como una cadena)

Este ejemplo de código de C# recupera y registra metadatos de la cola:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### Control de mensajes dudosos en la cola

Los mensajes cuyo contenido produce un error de una función se denominan *mensajes dudosos*. Cuando se produce un error en la función, el mensaje de la cola no se elimina y se recoge de nuevo, provocando que el ciclo se repita. El SDK puede interrumpir automáticamente el ciclo después de un número limitado de iteraciones, o puede hacerlo usted manualmente.

El SDK llamará a una función hasta 5 veces para procesar un mensaje de la cola. Si se produce un error en el quinta intento, el mensaje se mueve a una cola de mensajes dudosos.

La cola de mensajes dudosos se denomina *{originalqueuename}*-poison. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación indicando que se necesita atención manual.

Si desea controlar los mensajes dudosos manualmente, puede obtener el número de veces que se ha recogido un mensaje para su procesamiento mediante la comprobación de `dequeueCount`.

### <a id="storagequeueoutput"></a> Enlace de salida de la cola de Almacenamiento de Azure

El archivo *function.json* del enlace de la salida de un cola de almacenamiento especifica las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de la función de la cola o el mensaje de la cola. 
- `queueName` : nombre de la cola. Para conocer las reglas de nomenclatura de colas, consulte [Asignar nombres a colas y metadatos](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection` : nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el desencadenador funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type`: debe establecerse en *queue*.
- `direction`: debe establecerse en *out*. 

#### Ejemplo de *Function.json* para una salida de la cola de almacenamiento

Este ejemplo usa un desencadenador de cola y escribe un mensaje en la cola.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Tipos admitidos de enlace de salida de la cola

El enlace `queue` puede serializar los siguientes tipos para un mensaje de cola:

* Object (`out T` en C#, crea un mensaje con un objeto nulo si el parámetro es nulo cuando termina la función)
* String (`out string` en C#, crea un mensaje de cola si el valor del parámetro es no nulo cuando termina la función)
* Byte array (`out byte[]` en C#, funciona como una cadena) 
* `out CloudQueueMessage` (C#, funciona como una cadena) 

En C# también puede enlazar a `ICollector<T>` o `IAsyncCollector<T>`, donde `T` es uno de los tipos admitidos.

#### Ejemplos de código de enlace de salida de cola

Este ejemplo de código de C# escribe un mensaje de cola de salida única para cada mensaje de la cola de entrada.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Este ejemplo de código de C# escribe varios mensajes mediante `ICollector<T>` (use `IAsyncCollector<T>` en una función asincrónica):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

### <a id="storageblobtrigger"></a> Desencadenador de blobs de Almacenamiento de Azure

El archivo *function.json* de un desencadenador de blobs de almacenamiento especifica las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de la función para el blob. 
- `path` : ruta de acceso que especifica el contenedor que se supervisa y, opcionalmente, un patrón de nombre de blob.
- `connection` : nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el desencadenador funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type` : debe establecerse en *blobTrigger*.
- `direction` : debe establecerse en *in*.

#### Ejemplo de *Function.json* para un desencadenador de blobs de almacenamiento

Este ejemplo se desencadena en cualquier blob que se agregue al contenedor de ejemplos y elementos de trabajo.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### Tipos admitidos de desencadenador de blobs

El blob se puede deserializar en cualquiera de las siguientes funciones de Node o C#:

* Object (de JSON)
* String

En las funciones de C# también puede enlazar a cualquiera de los siguientes tipos:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Otros tipos deserializados por [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

#### Ejemplo de código de C# de desencadenador de blobs

Este ejemplo de código de C# registra el contenido de cada blob que se agrega al contenedor supervisado.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### Patrones de nombre para desencadenadores de blobs

Puede especificar un patrón de nombre de blob en la propiedad `path`. Por ejemplo:

```json
"path": "input/original-{name}",
```

Esta ruta de acceso encontraría un blob denominado *original-Blob1.txt* en el contenedor *input* y el valor de la variable `name` en el código de la función sería `Blob1`.

Otro ejemplo:

```json
"path": "input/{blobname}.{blobextension}",
```

Esta ruta de acceso también encontraría un blob denominado *original-Blob1.txt* y el valor de las variables `blobname` y `blobextension` en el código de la función sería *original-Blob1* y *txt*.

Puede restringir los tipos de blobs que desencadenan la función especificando un patrón con un valor fijo para la extensión de archivo. Si establece la propiedad `path` en *de samples/{nombre}.png*, solo los blobs *.png* del contenedor *samples* desencadenarán la función.

Si necesita especificar un patrón de nombre para nombres de blob que contienen llaves en el nombre, duplique las llaves. Por ejemplo, si desea encontrar blobs en el contenedor *images* que tengan nombres como este:

		{20140101}-soundfile.mp3

úselo para la propiedad `path`:

		images/{{20140101}}-{name}

En el ejemplo, el valor de la variable `name` sería *soundfile.mp3*.

#### Recepciones de blobs

El sistema en tiempo de ejecución de Funciones de Azure garantiza que a ninguna función de desencadenador de blobs se le llamará más de una vez para el mismo blob nuevo o actualizado. Para hacerlo, mantiene *recepciones de blobs* para determinar si se ha procesado alguna versión de blob determinada.

Las recepciones de blobs se almacenan en un contenedor llamado *azure-webjobs-hosts* en la cuenta de almacenamiento de Azure que especifica la cadena de conexión AzureWebJobsStorage. Una recepción de blobs tiene la información siguiente:

* La función que se llamó para el blob ("*{nombre de aplicación de función}*.Functions.*{nombre de función}*", por ejemplo: "functionsf74b96f7.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre del blob
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar el reprocesamiento de un blob, puede eliminar manualmente la recepción de blob de ese blob desde el contenedor *azure-webjobs-hosts*.

#### Control de blobs dudosos

Cuando una función de desencadenador de blobs genera un error, el SDK la llamará de nuevo por si se hubiese producido por un error transitorio. Si el error se produce debido al contenido del blob, la función presentará un error cada vez que intente procesar el blob. De manera predeterminada, el SDK llama una función hasta cinco veces para un blob determinado. Si el quinto intento falla, el SDK agrega un mensaje a una cola llamada *webjobs-blobtrigger-poison*.

El mensaje de cola para los blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *{nombre de aplicación de función}*.Functions.*{nombre de función}*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

#### Sondeo de blobs en contenedores grandes

Si el contenedor de blobs que el desencadenador está supervisando contiene más de 10 000 blobs, el sistema en tiempo de ejecución de Funciones examinará los archivos de registro para detectar los blobs nuevos o modificados. Este proceso no se produce en tiempo real; podrían tardarse varios minutos o más en desencadenar una función después de crear el blob. Además, los [registros de almacenamiento hacen lo posible por capturar todos los eventos](https://msdn.microsoft.com/library/azure/hh343262.aspx), pero no hay ninguna garantía de que esto suceda. En algunos casos, podrían faltar registros. Si los límites de velocidad y confiabilidad de los desencadenadores de blobs para contenedores grandes no son aceptables para su aplicación, el método recomendado consiste en crear un mensaje en cola al crear el blob y usar un desencadenador de cola en lugar de un desencadenador de blobs para procesar el blob.
 
### <a id="storageblobbindings"></a> Enlaces de entrada y salida de blobs de Almacenamiento de Azure

El archivo *function.json* del enlace de entrada o salida de un blob de almacenamiento especifica las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de la función para el blob. 
- `path` : ruta de acceso que especifica el contenedor del que se lee o en el que se escribe el blob y, opcionalmente, un patrón de nombre de blob.
- `connection` : nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el enlace funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type`: debe establecerse en *blob*.
- `direction` : se establece en *in* u *out*. 

#### Ejemplo de *Function.json* para una enlace de entrada y salida de la cola de almacenamiento

En este ejemplo se usa un desencadenador de cola para copiar un blob:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Tipos compatibles de entrada y salida de blobs

El enlace `blob` puede serializar o deserializar los siguientes tipos en las funciones de Node.js o C#:

* Object (`out T` en C# para blobs de salida: crea un blob como objeto nulo si el valor del parámetro es nulo cuando finaliza la función)
* String (`out string` en C# para blobs de salida: crea un blob solo si el parámetro de cadena es no nulo cuando se devuelve la función)

En las funciones de C#, también puede enlazar a los siguientes tipos:

* `TextReader` (solo de entrada)
* `TextWriter` (solo de salida)
* `Stream`
* `CloudBlobStream` (solo de salida)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### Ejemplo de código de C# de salida de blobs

Este ejemplo de código de C# copia un blob cuyo nombre se recibe en un mensaje en cola.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a id="storagetablesbindings"></a> Enlaces de entrada y salida de tablas de Almacenamiento de Azure

El archivo *function.json* de las tablas de almacenamiento especifica las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de la función para el enlace de la tabla. 
- `tableName` : nombre de la tabla.
- `partitionKey` y `rowKey`: se utilizan conjuntamente para leer una entidad individual en una función de C# o de Node, o para escribir una entidad individual en una función de Node.
- `take` : número máximo de filas que se leen en la entrada de la tabla en una función de Node.
- `filter`: expresión de filtro de OData en la entrada de la tabla en una función de Node.
- `connection` : nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el enlace funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type`: debe establecerse en *table*.
- `direction` : se establece en *in* u *out*. 

#### Tipos compatibles de entrada y salida de tablas de almacenamiento

El enlace `table` puede serializar o deserializar objetos en las funciones de Node.js o C#: Los objetos tendrán las propiedades RowKey y PartitionKey.

En las funciones de C#, también puede enlazar a los siguientes tipos:

* `T` donde `T` implementa `ITableEntity`
* `IQueryable<T>` (solo de entrada)
* `ICollector<T>` (solo de salida)
* `IAsyncCollector<T>` (solo de salida)

#### Escenarios de enlace de tablas de almacenamiento

El enlace de tablas admite los siguientes escenarios:

* Lectura de una sola fila de una función de C# o Node.

	Establezca `partitionKey` y `rowKey`. Las propiedades `filter` y `take` no se utilizan en este escenario.

* Lectura de varias filas en una función de C#.

	El sistema en tiempo de ejecución de Funciones proporciona un objeto `IQueryable<T>` enlazado a la tabla. El tipo `T` debe derivar de `TableEntity` o implementar `ITableEntity`. Las propiedades `partitionKey`, `rowKey`, `filter` y `take` no se utilizan en este escenario; puede utilizar el objeto `IQueryable` para realizar todo el filtrado requerido.

* Lectura de varias filas en una función de Node.

	Establezca las propiedades `filter` y `take`. No establezca `partitionKey` o `rowKey`.

* Escriba una o varias filas en una función de C#.

	El sistema en tiempo de ejecución de Funciones proporciona un `ICollector<T>` o `IAsyncCollector<T>` enlazado a la tabla, donde `T` especifica el esquema de las entidades que desea agregar. Normalmente, el tipo `T` deriva de `TableEntity` o implementa `ITableEntity`, pero no tiene por qué ser así. Las propiedades `partitionKey`, `rowKey`, `filter` y `take` no se utilizan en este escenario.

#### Ejemplo de tablas de almacenamiento: lectura de una entidad de tabla individual en C# o Node

Este ejemplo de *function.json* usa un desencadenador de cola para leer una única fila de la tabla, con un valor de clave de partición codificado de forma rígida y la clave de fila proporcionada en el mensaje en cola.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de código de C# funciona con el archivo *function.json* anterior para leer una entidad de tabla individual. El mensaje en cola tiene el valor de la clave de fila y la entidad de tabla se lee en un tipo que se define en el archivo *run.csx*. El tipo incluye las propiedades `PartitionKey` y `RowKey`, y no se deriva de `TableEntity`.

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

El siguiente ejemplo de código de Node también funciona con el archivo *function.json* anterior para leer una entidad de tabla individual.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Ejemplo de tablas de almacenamiento: lectura de varias entidades de tabla en C# 

El siguiente ejemplo de *function.json* y de código de C# lee las entidades de una clave de partición que se especifica en el mensaje en cola.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

El código de C# agrega una referencia al SDK de Almacenamiento de Azure, con el fin de que el tipo de entidad pueda derivar de `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### Ejemplo de tablas de almacenamiento: creación de entidades de tabla en C# 

El siguiente ejemplo de *function.json* y *run.csx* muestra cómo escribir entidades de tabla en C#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### Ejemplo de tablas de almacenamiento: creación de una entidad de tabla en Node

El siguiente ejemplo de *function.json* y *run.csx* muestra cómo escribir una entidad de tabla en Node.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Desencadenadores y enlaces del Bus de servicio de Azure

Esta sección contiene las siguientes subsecciones:

* [Bus de servicio de Azure: comportamiento de PeekLock](#sbpeeklock)
* [Bus de servicio de Azure: tratamiento de mensajes dudosos](#sbpoison)
* [Bus de servicio de Azure: subprocesamiento único](#sbsinglethread)
* [Desencadenador de tema o de cola de Bus de servicio de Azure](#sbtrigger)
* [Enlace de salida de tema o de cola de Bus de Almacenamiento de Azure](#sboutput)

### <a id="sbpeeklock"></a> Bus de servicio de Azure: comportamiento de PeekLock

El sistema en tiempo de ejecución de Funciones recibe un mensaje en el modo `PeekLock` y llama a `Complete` en el mensaje si la función finaliza correctamente, o bien llama a `Abandon` si se produce un error en la función. Si la ejecución de la función dura más que el tiempo de espera de `PeekLock`, el bloqueo se renovará automáticamente.

### <a id="sbpoison"></a> Bus de servicio de Azure: tratamiento de mensajes dudosos

El Bus de servicio realiza su propio tratamiento de mensajes dudosos, que no se puede controlar ni configurar en el código ni en la configuración de Funciones de Azure.

### <a id="sbsinglethread"></a> Bus de servicio de Azure: subprocesamiento único

De forma predeterminada, el sistema en tiempo de ejecución de Funciones procesa simultáneamente varios mensajes en cola. Para indicar al sistema en tiempo de ejecución que procese los mensajes de la cola o del tema de uno en uno, establezca `serviceBus.maxConcurrrentCalls` en 1 en el archivo *host.json*. Para obtener información sobre el archivo *host.json*, consulte [Estructura de carpetas](functions-reference.md#folder-structure) en el artículo Referencia para desarrolladores de Funciones de Azure y [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) en el wiki del repositorio WebJobs.Script.

### <a id="sbtrigger"></a> Desencadenador de tema o de cola de Bus de servicio de Azure

El archivo *function.json* de un desencadenador del Bus de servicio especifica las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de la función de la cola o tema, o bien de la cola o del mensaje de la cola. 
- `queueName` : solo para el desencadenador de cola, el nombre de la cola que se sondea.
- `topicName` : solo para el desencadenador de tema, el nombre del tema que se sondea.
- `subscriptionName` : solo para el desencadenador de tema, el nombre de la suscripción.
- `connection` : nombre de una configuración de aplicación que contiene una cadena de conexión del Bus de servicio. La cadena de conexión debe ser para un espacio de nombres del Bus de servicio, no estar limitada a una cola o un tema concretos. Si la cadena de conexión no haya derechos de administración, establezca la propiedad `accessRights`. Si `connection` se queda vacía, el desencadenador o el enlace funcionarán con la cadena de conexión del Bus de servicio predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsServiceBus.
- `accessRights` : especifica los derechos de acceso disponibles para la cadena de conexión. El valor predeterminado es `manage`. Establézcalo en `listen` si utilizando una cadena de conexión que no proporciona permisos de administración. De lo contrario, el sistema en tiempo de ejecución de Funciones puede intentar realizar operaciones que requieran derechos de administración y no conseguirlo.
- `type` : debe establecerse en *serviceBusTrigger*.
- `direction` : debe establecerse en *in*. 

El mensaje de la cola del Bus de servicio se puede deserializar en cualquiera de los siguientes tipos:

* Object (de JSON)
* string
* byte array 
* `BrokeredMessage` (C#) 

#### Ejemplo de *Function.json* del uso de un desencadenador de cola del Bus de servicio

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Ejemplo de código de C# que procesa un mensaje de cola del Bus de servicio

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Ejemplo de código de Node.js que procesa un mensaje de cola del Bus de servicio

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

### <a id="sboutput"></a> Salida de tema o de cola del Bus de servicio de Azure

El archivo *function.json* del enlace de la salida de un Bus de servicio especifica las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de la función de la cola o el mensaje de la cola. 
- `queueName` : solo para el desencadenador de cola, el nombre de la cola que se sondea.
- `topicName` : solo para el desencadenador de tema, el nombre del tema que se sondea.
- `subscriptionName` : solo para el desencadenador de tema, el nombre de la suscripción.
- `connection` : igual que para el desencadenador del Bus de servicio.
- `accessRights` : especifica los derechos de acceso disponibles para la cadena de conexión. El valor predeterminado es `manage`. Establézcalo en `send` si utilizando una cadena de conexión que no proporciona permisos de administración. De lo contrario, el sistema en tiempo de ejecución de Funciones puede intentar realizar operaciones que requieran derechos de administración, como la creación de colas, y no conseguirlo.
- `type` : debe establecerse en *serviceBus*.
- `direction`: debe establecerse en *out*. 

Funciones de Azure puede crear un mensaje de cola del Bus de servicio desde cualquiera de los siguientes tipos:

* Object (siempre crea un mensaje JSON, crea el mensaje con un objeto null si el valor es null cuando finaliza la función)
* string (crea un mensaje si el valor no es null cuando termina la función)
* byte array (funciona como una cadena) 
* `BrokeredMessage` (C#, funciona como una cadena)

Para crear varios mensajes en una función de C#, puede utilizar `ICollector<T>` o `IAsyncCollector<T>`. Se crea un mensaje cuando se llama al método `Add`.

#### Ejemplo de *function.json* del uso de un desencadenador del temporizador para escribir mensajes de cola del Bus de servicio

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Ejemplos de código de C# que crean mensajes en cola del Bus de servicio

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Ejemplo de código de Node.js que crea un mensajes en cola del Bus de servicio

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Enlaces de Azure DocumentDB

Esta sección contiene las siguientes subsecciones:

* [Enlace de entrada de Azure DocumentDB](#docdbinput)
* [Enlace de salida de Azure DocumentDB:](#docdboutput)

### <a id="docdbinput"></a> Enlace de entrada de Azure DocumentDB

Los enlaces de entrada pueden cargar un documento desde una colección de DocumentDB y pasarlo directamente al enlace. Se puede determinar el identificador de documento según el desencadenador que invocó la función. En una función de C#, los cambios realizados en el registro se enviarán automáticamente a la colección una vez que la función se complete correctamente.

El archivo function.json proporciona las siguientes propiedades para su uso con el enlace de entrada de DocumentDB:

- `name`: nombre de la variable utilizado en el código de función para el documento.
- `type`: se debe establecer en "documentdb".
- `databaseName`: la base de datos que contiene el documento.
- `collectionName`: la colección que contiene el documento.
- `id`: el identificador del documento que se recupera. Esta propiedad admite enlaces similares a "{queueTrigger}", que usarán el valor de cadena del mensaje en cola como identificador del documento.
- `connection`: esta cadena debe ser una configuración de la aplicación establecida en el punto de conexión de su cuenta de DocumentDB. Si elige la cuenta en la pestaña Integrar, se creará una nueva configuración de la aplicación con un nombre que tiene el formato siguiente: yourAccount\_DOCUMENTDB. Si necesita crear manualmente la configuración de la aplicación, la cadena de conexión real deberá tener el formato siguiente: AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;.
- `direction: debe establecerse en *"in"*.

Function.json de ejemplo:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Ejemplo de código de entrada de Azure DocumentDB para un desencadenador de cola de C#
 
Con el archivo function.json de ejemplo anterior, el enlace de entrada de DocumentDB recuperará el documento con el identificador que coincida con la cadena del mensaje en la cola y lo pasará al parámetro 'document'. Si no se encuentra el documento, el parámetro 'document' será nulo. El documento se actualiza entonces con el nuevo valor de texto cuando se completa la función.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Ejemplo de código de entrada de Azure DocumentDB para un desencadenador de cola de Node.js
 
Con el archivo function.json de ejemplo anterior, el enlace de entrada de DocumentDB recuperará el documento con el identificador que coincida con la cadena del mensaje en la cola y lo pasará a la propiedad del enlace `documentIn`. En las funciones de Node.js, los documentos actualizados no se envían de nuevo a la colección. Sin embargo, puede pasar el enlace de entrada directamente a un enlace de salida de DocumentDB denominado `documentOut` para admitir las actualizaciones. Este ejemplo de código actualiza la propiedad de texto del documento de entrada y lo establece como documento de salida.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

### <a id="docdboutput"></a> Enlaces de salida de Azure DocumentDB

Las funciones pueden escribir documentos JSON en una base de datos de Azure DocumentDB mediante el enlace de salida de **Documento de Azure DocumentDB**. Para más información sobre Azure DocumentDB, consulte [Introducción a DocumentDB: una base de datos de JSON NoSQL](../documentdb/documentdb-introduction.md) y [Tutorial de NoSQL: Crear una aplicación de consola de C# DocumentDB](../documentdb/documentdb-get-started.md).

El archivo function.json proporciona las siguientes propiedades para su uso con el enlace de salida de DocumentDB:

- `name`: nombre de la variable utilizado en el código de función para el documento nuevo.
- `type`: se debe establecer en *"documentdb"*.
- `databaseName`: la base de datos que contiene la colección en la que se creará el nuevo documento.
- `collectionName`: la colección en la que se creará el nuevo documento.
- `createIfNotExists`: es un valor booleano que indica si la colección se creará si no existe. El valor predeterminado es *false*. Esto se debe a que se crean nuevas colecciones con rendimiento reservado, lo cual tiene implicaciones de precios. Para más información, visite la [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: esta cadena debe ser una **configuración de la aplicación** establecida en el punto de conexión de su cuenta de DocumentDB. Si elige la cuenta en la pestaña **Integrar**, se creará una nueva configuración de la aplicación con un nombre que tiene el formato siguiente: `yourAccount_DOCUMENTDB`. Si necesita crear manualmente la configuración de la aplicación, la cadena de conexión real deberá tener el formato siguiente: `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: debe establecerse en *"out"*. 
 
Function.json de ejemplo:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Ejemplo de código de salida de Azure DocumentDB para un desencadenador de cola de Node.js

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

El documento de salida:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Ejemplo de código de salida de Azure DocumentDB para un desencadenador de cola de C#


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Nombre del archivo de configuración de ejemplo de código de salida de Azure DocumentDB

Si desea establecer el nombre del documento en la función, establezca el valor `id`. Por ejemplo, si se descartó el contenido JSON de un empleado en una cola similar a la siguiente:

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

Podría utilizar el siguiente código de C# en una función de desencadenador de cola:
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

Salida de ejemplo:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Enlaces de Aplicaciones móviles de Azure

Las Aplicaciones móviles del Servicio de aplicaciones de Azure le permiten exponer los datos de punto de conexión de la tabla a clientes móviles. Estos mismos datos tabulares se pueden usar con los enlaces de entrada y salida de Funciones de Azure. Dado que admiten el esquema dinámico, las aplicaciones móviles del back-end de Node.js son ideales para exponer los datos tabulares que se van a usar con las funciones. El esquema dinámico está habilitado de forma predeterminada y debe deshabilitarse en una aplicación móvil de producción. Para más información acerca de los puntos de conexión de tabla en un back-end de Node.js, consulte [Información general: Operaciones de tabla](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations). En Aplicaciones móviles, el back-end de Node.js admite la exploración y la edición de tablas en el portal. Para más información, consulte [Trabajo con tablas fáciles en el Portal de Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing) en el tema Uso del SDK de Node.js de Aplicaciones móviles de Azure. Si utiliza una aplicación móvil de back-end de .NET con Funciones de Azure, debe actualizar manualmente el modelo de datos, tal como lo requiera la función. Para más información acerca de los punto de conexión de la tabla en una aplicación móvil del back-end .NET, consulte [Cómo definir un controlador de tabla](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) en el tema Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure.

Esta sección contiene las siguientes subsecciones:

* [Claves de API de tablas de Aplicaciones móviles de Azure](#mobiletablesapikey)
* [Enlace de entrada para tablas de Aplicaciones móviles de Azure](#mobiletablesinput)
* [Enlace de salida para tablas de Aplicaciones móviles de Azure](#mobiletablesoutput)

### <a id="mobiletablesapikey"></a> Use una clave de API para proteger el acceso a los puntos de conexión de las tablas de Aplicaciones móviles.

En Funciones de Azure, los enlaces de tablas móviles permiten especificar una clave de API, que es un secreto compartido que se puede utilizar para evitar el acceso no deseado desde aplicaciones que no sean sus funciones. Las Aplicaciones móviles no incluyen compatibilidad integrada para la autenticación de la clave de API. Sin embargo, puede implementar una clave de API en su aplicación móvil de back-end de Node.js siguiendo los ejemplos descritos en [Azure App Service Mobile Apps backend implementing an API key](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) (Back-end de Aplicaciones móviles del Servicio de aplicaciones de Azure que implementa una clave de API). De forma similar, puede implementar una clave de API en una [aplicación móvil de back-end de .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key).

>[AZURE.IMPORTANT] Esta clave de API no se debe distribuir con los clientes de la aplicación móvil, se debe distribuir de forma segura solo a los clientes del servicio, como Funciones de Azure.

### <a id="mobiletablesinput"></a>Enlace de entrada de Aplicaciones móviles de Azure

Los enlaces de entrada pueden cargar un registro desde un punto de conexión de tabla móvil y pasarlo directamente al enlace. Se puede determinar el identificador de registro según el desencadenador que invocó la función. En una función de C#, los cambios realizados en el registro se enviarán automáticamente a la tabla una vez que la función se complete correctamente.

El archivo function.json admite las siguientes propiedades para usarlas con los enlaces de entrada de Aplicaciones móviles:

- `name`: nombre de variable utilizado en el código de función para el nuevo registro.
- `type`: el tipo de enlace debe establecerse en *mobileTable*.
- `tableName`: tabla donde se creará el nuevo registro.
- `id`: identificador del registro que se recupera. Esta propiedad admite enlaces similares a `{queueTrigger}`, que usarán el valor de cadena del mensaje en cola como identificador del registro.
- `apiKey`: cadena que constituye el parámetro de la aplicación que especifica la clave de API opcional para la aplicación móvil. Esta es necesaria si la aplicación móvil usa una clave de API para restringir el acceso de cliente.
- `connection`: cadena que constituye la configuración de la aplicación que especifica el identificador URI de la aplicación móvil.
- `direction`: dirección del enlace, que se debe establecer en *in*.

Function.json de ejemplo:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Ejemplo de código de Aplicaciones móviles de Azure para un desencadenador de cola de C#

En función del archivo function.json del ejemplo anterior, el enlace de entrada recupera el registro de un punto de conexión de tabla de Aplicaciones móviles con el identificador que coincide con la cadena del mensaje en la cola y lo pasa al parámetro *record*. Si no se encuentra el registro, el parámetro será nulo. El registro se actualiza con el nuevo valor de *Text* cuando se sale la función.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Ejemplo de código de Aplicaciones móviles de Azure para un desencadenador de cola de Node.js

En función del archivo function.json del ejemplo anterior, el enlace de entrada recupera el registro de un punto de conexión de tabla de Aplicaciones móviles con el identificador que coincide con la cadena del mensaje en la cola y lo pasa al parámetro *record*. En las funciones de Node.js, los registros actualizados no se envían de nuevo a la tabla. Este ejemplo de código escribe el registro recuperado en el registro.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


### <a id="mobiletablesoutput"></a>Enlace de salida de Aplicaciones móviles de Azure

La función puede escribir un registro en un punto de conexión de tabla de Aplicaciones móviles mediante un enlace de salida.

El archivo function.json admite las siguientes propiedades para su uso con enlaces de salida de una tabla móvil:

- `name`: nombre de variable utilizado en el código de función para el nuevo registro.
- `type`: el tipo de enlace que se debe establecer en *mobileTable*.
- `tableName`: la tabla donde se crea el nuevo registro.
- `apiKey`: cadena que constituye el parámetro de la aplicación que especifica la clave de API opcional para la aplicación móvil. Esta es necesaria si la aplicación móvil usa una clave de API para restringir el acceso de cliente.
- `connection`: cadena que constituye la configuración de la aplicación que especifica el identificador URI de la aplicación móvil.
- `direction`: dirección de enlace, que se debe establecer en *out*.

Function.json de ejemplo:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Ejemplo de código de Aplicaciones móviles de Azure para un desencadenador de cola de C#

Este ejemplo de código de C# inserta un registro nuevo en un punto de conexión de tabla de Aplicaciones móviles con una propiedad *Text* en la tabla especificada en el enlace anterior.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Ejemplo de código de Aplicaciones móviles de Azure para un desencadenador de cola de Node.js

Este ejemplo de código de Node.js inserta un registro nuevo en un punto de conexión de tabla de Aplicaciones móviles con una propiedad *Text* en la tabla especificada en el enlace anterior.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Enlace de salida del centro de notificaciones de Azure

Las funciones pueden enviar notificaciones push mediante un Centro de notificaciones de Azure configurado mediante unas líneas de código tan solo. Sin embargo, el centro de notificaciones debe configurarse para los servicios de notificaciones de plataforma (PNS) que desea utilizar. Para más información sobre cómo configurar un centro de notificaciones de Azure y desarrollar unas aplicaciones cliente que se registren para notificaciones, consulte [Introducción a Centros de notificaciones para aplicaciones de la Tienda Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started.md) y haga clic en la plataforma de cliente de destino de la parte superior.

El archivo function.json proporciona las siguientes propiedades para su uso con el enlace de salida de un centro de notificaciones:

- `name`: nombre de variable utilizado en el código de función para el mensaje del centro de notificaciones.
- `type`: se debe establecer en *"notificationHub"*.
- `tagExpression`: las expresiones de etiqueta le permiten especificar las notificaciones que se entregarán a un conjunto de dispositivos que se han registrado para recibir las notificaciones que coincidan con estas expresiones. Para más información, consulte [Expresiones de etiqueta y enrutamiento](../notification-hubs/notification-hubs-routing-tag-expressions.md).
- `hubName`: nombre del recurso del centro de notificaciones en el Portal de Azure.
- `connection`: esta cadena de conexión debe ser una cadena de conexión de la **configuración de la aplicación** establecida en el valor *DefaultFullSharedAccessSignature* para el centro de notificaciones.
- `direction`: debe establecerse en *"out"*. 
 
Function.json de ejemplo:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

### Configuración de la cadena de conexión del centro de notificaciones de Azure

Para usar un enlace de salida del centro de notificaciones debe configurar la cadena de conexión para el mismo. Esto se puede realizar en la pestaña *Integrar* simplemente seleccionando el centro de notificaciones o creando uno nuevo.

También puede agregar manualmente una cadena de conexión para un centro existente mediante la adición de una cadena de conexión para *DefaultFullSharedAccessSignature* al centro de notificaciones. Esta cadena de conexión le proporciona sus permisos de acceso de función para enviar mensajes de notificación. Al valor de la cadena de conexión *DefaultFullSharedAccessSignature* se puede acceder desde el botón de **claves** de la hoja principal del recurso del centro de notificaciones en el Portal de Azure. Para agregar manualmente una cadena de conexión para su centro, siga estos pasos:

1. En la hoja **Contenedor de funciones** del Portal de Azure, haga clic en **Function App Settings > Go to App Service settings** (Configuración del contenedor de funciones > Ir a la configuración del Servicio de aplicaciones).

2. Vuelva a la hoja **Configuración** y haga clic en **Configuración de la aplicación**.

3. Desplácese hacia abajo hasta la sección **Cadenas de conexión** y agregue una entrada con nombre para el valor *DefaultFullSharedAccessSignature* para el centro de notificaciones. Cambie el tipo a **Personalizado**.
4. Haga referencia al nombre de la cadena de conexión en los enlaces de salida. De forma parecida al valor **MyHubConnectionString** utilizado en el ejemplo anterior.

### Ejemplo de código de centro de notificaciones de Azure para un desencadenador de temporizador de Node.js 

En este ejemplo se envía una notificación a un [registro de plantillas](../notification-hubs/notification-hubs-templates.md) que contiene `location` y `message`.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

### Ejemplo de código de centro de notificaciones de Azure para un desencadenador de cola de C#

En este ejemplo se envía una notificación a un [registro de plantilla](../notification-hubs/notification-hubs-templates.md) que contiene `message`.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

En este ejemplo se envía una notificación a un [registro de plantilla](../notification-hubs/notification-hubs-templates.md) que contiene `message` mediante una cadena JSON válida.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

### Ejemplo de código de C# de desencadenador de cola del Centro de notificaciones de Azure que utiliza un tipo de notificación

En este ejemplo se muestra cómo utilizar el tipo `Notification` definido en la [biblioteca de centros de notificaciones de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Para utilizar este tipo y la biblioteca, debe cargar un archivo *project.json* para la aplicación de la función. El archivo project.json es un archivo de texto JSON que tendrá un aspecto similar al siguiente:

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

Para más información acerca de cómo cargar el archivo project.json, consulte el artículo sobre la [carga de un archivo project.json](http://stackoverflow.com/questions/36411536/how-can-i-use-nuget-packages-in-my-azure-functions).

Ejemplo de código:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)
* [Referencia para desarrolladores de C# de Funciones de Azure](functions-reference-csharp.md)
* [Referencia para desarrolladores de NodeJS de Funciones de Azure](functions-reference-node.md)

<!---HONumber=AcomDC_0518_2016-->