---
title: Desencadenadores y enlaces en Azure Functions
description: "Obtenga información sobre cómo usar desencadenadores y enlaces en Azure Functions para conectar la ejecución del código a los eventos en línea y servicios basados en la nube."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: a122271b5fdffd9db33a7dca5908e15f002041d7
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceptos básicos sobre los enlaces y desencadenadores de Azure Functions

En este artículo se ofrece una introducción conceptual a los desencadenadores y enlaces en Azure Functions. Aquí se describen las características comunes a todos los enlaces y los lenguajes admitidos.

## <a name="overview"></a>Información general

Los *desencadenadores* establecen el modo de invocar una función. Cada función debe tener exactamente un desencadenador. Los desencadenadores tienen datos asociados, que suelen ser la carga que desencadenó la función.

Los *enlaces* de entrada y de salida permiten conectarse de manera declarativa a datos desde el código. Los enlaces son opcionales y cada función puede tener varios enlaces de entrada y de salida. 

Los desencadenadores y los enlaces permiten evitar la codificación de los detalles de los servicios con los que se trabaja. La función recibe los datos (por ejemplo, el contenido de un mensaje de cola) en parámetros de función. El usuario envía datos (por ejemplo, para crear un mensaje de la cola) mediante el valor devuelto de la función, un parámetro `out` o un [objeto Collector](functions-reference-csharp.md#writing-multiple-output-values).

Al desarrollar funciones mediante Azure Portal, los desencadenadores y los enlaces se configuran en un archivo *function.json*. El portal proporciona una interfaz de usuario para esta configuración, pero puede editar el archivo directamente; para ello, cambie a **Editor avanzado**.

Al desarrollar funciones con Visual Studio para crear una biblioteca de clases, configure los desencadenadores y los enlaces mediante la decoración de los parámetros y los métodos con atributos.

## <a name="supported-bindings"></a>Enlaces admitidos

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para información sobre qué enlaces están en versión preliminar o aprobados para su uso en producción, consulte los [lenguajes admitidos](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Ejemplo: desencadenador de colas y enlace de salida de tablas

Supongamos que quiere escribir una fila nueva en Azure Table Storage cada vez que aparezca un nuevo mensaje en Azure Queue Storage. Este escenario puede implementarse mediante un desencadenador de Azure Queue Storage y un enlace de salida de Azure Table Storage. 

Este es un archivo *function.json* para este escenario. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

El primer elemento de la matriz `bindings` es el desencadenador de Queue Storage. Las propiedades `type` y `direction` identifican el desencadenador. La propiedad `name` identifica el parámetro de función que va a recibir el contenido del mensaje de cola. El nombre de la cola que supervisar está en `queueName` y la cadena de conexión, en la configuración de la aplicación identificada por `connection`.

El segundo elemento de la matriz `bindings` es el enlace de salida de Azure Table Storage. Las propiedades `type` y `direction` identifican el enlace. La propiedad `name` especifica cómo la función proporcionará la nueva fila de tabla, en este caso, con el valor devuelto de función. El nombre de la tabla está en `tableName` y la cadena de conexión, en la configuración de la aplicación identificada por `connection`.

Para ver y editar el contenido de *function.json* en el portal de Azure, haga clic en la opción **Editor avanzado** en la pestaña **Integrar** de la función.

> [!NOTE]
> El valor de `connection` es el nombre de una configuración de aplicación que contiene la cadena de conexión, no la propia cadena de conexión. Los enlaces utilizan las cadenas de conexión almacenadas en la configuración de la aplicación para que *function.json* no contenga secretos de los servicios (procedimiento recomendado).

Este es el código de script de C# que funciona con este enlace y este desencadenador. Tenga en cuenta que el nombre del parámetro que proporciona el contenido del mensaje de cola es `order`; este nombre es necesario porque el valor de propiedad `name`de *function.json* es `order`. 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

El mismo archivo function.json puede utilizarse con una función de JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

En una biblioteca de clases, los atributos proporcionan la misma información &mdash; de desencadenador y enlace (nombres de tabla y cola, cuentas de almacenamiento, parámetros de función de entrada y salida &mdash;):

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>Dirección de los enlaces

Todos los desencadenadores y enlaces tienen una propiedad `direction` en el archivo *function.json*:

- En el caso de los desencadenadores, esta propiedad siempre aparece como `in`
- Los enlaces de entrada y de salida usan `in` y `out`
- Algunos enlaces admiten la dirección especial `inout`. Si utiliza `inout`, solo estará disponible la opción **Editor avanzado** en la pestaña **Integrar**.

Cuando se usan [atributos en una biblioteca de clases](functions-dotnet-class-library.md) para configurar los desencadenadores y los enlaces, la dirección se proporciona en un constructor de atributos o se deduce del tipo de parámetro.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Uso del tipo de valor devuelto por la función para devolver un resultado único

En el ejemplo anterior, se muestra cómo utilizar el valor devuelto por la función para proporcionar la salida a un enlace, lo cual se especifica en *function.json* con el valor especial `$return` de la propiedad `name`. (Esto solo se admite en los lenguajes que presenten un valor devuelto, como un script de C#, JavaScript y F#). Si una función tiene varios enlaces de salida, emplee `$return` para únicamente uno de ellos. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

En los ejemplos siguientes, puede ver cómo se usan los tipos de valores devueltos con los enlaces de salida en un script de C#, JavaScript y F#.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>Enlace de la propiedad DataType

En .NET, use el tipo de parámetro para definir el tipo de datos de entrada. Por ejemplo, use `string` para enlazar al texto de un desencadenador de cola, una matriz de bytes que se lee como binaria y un tipo personalizado para deserializar a un objeto POCO.

Para los lenguajes que se escriben dinámicamente, como JavaScript, use la propiedad `dataType` del archivo *function.json*. Por ejemplo, para leer el contenido de una solicitud HTTP en formato binario, establezca `dataType` en `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Otras opciones para `dataType` son `stream` y `string`.

## <a name="resolving-app-settings"></a>Resolver la configuración de la aplicación

Como procedimiento recomendado, los secretos y las cadenas de conexión deberían administrarse mediante los ajustes de la aplicación, en lugar de archivos de configuración. De este modo, se limita el acceso a estos secretos y resulta seguro almacenar *function.json* en un repositorio de control de código fuente público.

Los ajustes de la aplicación también son útiles cuando se desea cambiar la configuración en función del entorno. Por ejemplo, en un entorno de prueba, es posible que quiera supervisar una cola o un contenedor de almacenamiento de blobs diferente.

Los ajustes de la aplicación se resuelven cuando un valor aparece entre símbolos de porcentaje; por ejemplo `%MyAppSetting%`. Tenga en cuenta que la propiedad `connection` de los desencadenadores y los enlaces es un caso especial y resuelve automáticamente los valores como ajustes de la aplicación. 

En el ejemplo siguiente, aparece un desencadenador de Azure Queue Storage que se sirve del valor de configuración de la aplicación `%input-queue-name%` para definir la cola que se desencadenará.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Puede usar el mismo enfoque con las bibliotecas de clases:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-metadata-properties"></a>Propiedades de metadatos de los desencadenadores

Además de la carga de datos que proporciona un desencadenador (como el mensaje de la cola que desencadenó una función), muchos desencadenadores facilitan valores de metadatos adicionales. Estos valores pueden usarse como parámetros de entrada en C# y F# o como propiedades en el objeto `context.bindings` de JavaScript. 

Por ejemplo, un desencadenador de Azure Queue Storage admite las siguientes propiedades:

* QueueTrigger (desencadena el contenido del mensaje si hay una cadena válida)
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

A estos valores de metadatos se accede en las propiedades del archivo *function.json*. Por ejemplo, supongamos que usa un desencadenador de cola y el mensaje de la cola contiene el nombre de un blob que desea leer. En el archivo *function.json*, puede usar la propiedad de metadatos `queueTrigger` en la propiedad de blob `path`, como se muestra en el ejemplo siguiente:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Los detalles sobre las propiedades de metadatos de cada desencadenador se describen en el artículo de referencia correspondiente. Para un ejemplo, consulte la sección acerca de los [metadatos de los desencadenadores de cola](functions-bindings-storage-queue.md#trigger---message-metadata). También podrá encontrar documentación en la pestaña **Integrar** del portal, en la sección **Documentación**, debajo del área de configuración de enlaces.  

## <a name="binding-expressions-and-patterns"></a>Patrones y expresiones de enlace

Una de las características más eficaces de los desencadenadores y los enlaces son las *expresiones de enlace*. En la configuración del enlace puede definir expresiones de patrón que, después, podrán emplearse en otros enlaces o en el código. Asimismo, los metadatos de los desencadenadores pueden utilizarse en expresiones de enlace, tal y como aparece en la sección anterior.

Por ejemplo, imagine que desea cambiar el tamaño de unas imágenes de un contenedor de almacenamiento de blobs concreto, similar a la plantilla **Image Resizer** (Cambio de tamaño de imágenes) de la página **Nueva función** de Azure Portal (consulte el escenario **Ejemplos**). 

Esta es la definición de *function.json*:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Observe que el parámetro `filename` se usa tanto en la definición del desencadenador de blobs como en el enlace de salida de blobs. Este parámetro también puede incluirse en el código de la función.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

La misma capacidad para usar patrones y expresiones de enlace se aplica a los atributos de las bibliotecas de clases. Por ejemplo, aquí se muestra una función de cambio de tamaño de imágenes en una biblioteca de clases:

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

### <a name="create-guids"></a>Creación de los identificadores únicos globales

La expresión de enlace `{rand-guid}` crea un identificador único global. En el ejemplo siguiente se usa un identificador único global para crear un nombre de blob único: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Hora actual

La expresión de enlace `DateTime` se resuelve como `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>Enlace a las propiedades personalizadas de entrada

Las expresiones de enlace también pueden hacer referencia a propiedades definidas en la propia carga de los desencadenadores. Por ejemplo, es posible que quiera establecer un enlace de forma dinámica a un archivo de almacenamiento de blobs a partir de un nombre de archivo proporcionado en un webhook.

Por ejemplo, la función *function.json* siguiente recurre a una propiedad denominada `BlobName` en la carga del desencadenador:

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Para realizar esto en C# y F#, debe definir un POCO que establezca los campos que se deserializarán en la carga del desencadenador.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

En JavaScript, la deserialización de JSON se lleva a cabo de manera automática y el usuario puede usar directamente las propiedades.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

## <a name="configuring-binding-data-at-runtime"></a>Configuración de datos de enlace en tiempo de ejecución

En C# y otros lenguajes .NET, puede usar un patrón de enlace imperativo, en contraposición a los enlaces declarativos de *function.json* y los atributos. Los enlaces imperativos resultan útiles cuando los parámetros de enlace tienen que calcularse en tiempo de ejecución, en lugar de en el tiempo de diseño. Para obtener más información, vea [Enlace en tiempo de ejecución a través de enlaces imperativos](functions-reference-csharp.md#imperative-bindings) en la Referencia para desarrolladores de C#.

## <a name="functionjson-file-schema"></a>Esquema del archivo function.json

El esquema del archivo*function.json* está disponible en [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="next-steps"></a>pasos siguientes

Para obtener más información sobre un tipo de enlace concreto, consulte estos artículos:

- [HTTP y webhooks](functions-bindings-http-webhook.md)
- [Temporizador](functions-bindings-timer.md)
- [Queue Storage](functions-bindings-storage-queue.md)
- [Blob Storage](functions-bindings-storage-blob.md)
- [Table storage](functions-bindings-storage-table.md)
- [Event Hubs](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Archivo externo](functions-bindings-external-file.md)
