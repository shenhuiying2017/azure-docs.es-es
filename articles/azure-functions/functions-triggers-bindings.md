---
title: Trabajo con desencadenadores y enlaces de Azure Functions | Microsoft Docs
description: "Obtenga información sobre cómo usar desencadenadores y enlaces en Azure Functions para conectar la ejecución del código a los eventos en línea y servicios basados en la nube."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: donnam
ms.openlocfilehash: ab438f804c28d5528901c405311424e0344e00fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceptos básicos sobre los enlaces y desencadenadores de Azure Functions
Azure Functions le permite escribir código en respuesta a eventos en Azure y otros servicios mediante *desencadenadores* y *enlaces*. En este artículo, se ofrece una introducción conceptual a los desencadenadores y enlaces de todos los lenguajes de programación compatibles. Aquí se describen características comunes de todos los enlaces.

## <a name="overview"></a>Información general

Los desencadenadores y enlaces permiten definir de forma declarativa cómo se invoca una función y con qué datos funcionará. Los *desencadenadores* establecen el modo de invocar una función. Cada función debe tener exactamente un desencadenador. Los desencadenadores tienen datos asociados, que suelen ser la carga que desencadenó la función. 

Los *enlaces* de entrada y de salida permiten conectarse de manera declarativa a datos desde el código. De forma similar a los desencadenadores, se pueden especificar las cadenas de conexión y otras propiedades en la configuración de la función. Los enlaces son opcionales y cada función puede tener varios enlaces de entrada y de salida. 

Mediante los desencadenadores y enlaces, puede escribir código más genérico sin codificar de forma rígida los detalles de los servicios con los que interactúa. Los datos procedentes de los servicios se convierten simplemente en valores de entrada para el código de función. Si desea enviar datos a otro servicio (por ejemplo, para crear una fila nueva en Azure Table Storage), utilice el valor devuelto por el método. O bien, si necesita enviar varios valores, use un objeto auxiliar. Los desencadenadores y los enlaces cuentan con una propiedad denominada **name**, un identificador que se emplea en el código para acceder al enlace.

Puede configurar desencadenadores y enlaces en la pestaña **Integrar** del portal de Azure Functions. Entre bastidores, la interfaz de usuario modifica un archivo llamado *function.json* en el directorio de la función. Este archivo puede editarse cambiando a la opción **Editor avanzado**.

En la siguiente tabla, se muestran los desencadenadores y enlaces compatibles con Azure Functions. 

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

### <a name="example-queue-trigger-and-table-output-binding"></a>Ejemplo: desencadenador de colas y enlace de salida de tablas

Supongamos que quiere escribir una fila nueva en Azure Table Storage cada vez que aparezca un nuevo mensaje en Azure Queue Storage. Este escenario puede implementarse mediante un desencadenador de colas de Azure y un enlace de salida de Azure Table Storage. 

Un desencadenador de Azure Queue Storage precisa de la siguiente información en la pestaña **Integrar**:

* El nombre del valor de configuración de la aplicación que contiene la cadena de conexión de la cuenta de Azure Storage para Azure Queue Storage
* El nombre de la cola
* El identificador del código que leerá el contenido del mensaje de la cola, como, por ejemplo, `order`

Para escribir en Azure Table Storage, utilice un enlace de salida con estos datos:

* El nombre del valor de configuración de la aplicación que contiene la cadena de conexión de la cuenta de Azure Storage para Azure Table Storage
* El nombre de la tabla
* El identificador del código que creará los elementos de salida o el valor devuelto por la función

Los enlaces usan valores de cadenas de conexión almacenados en la configuración de la aplicación para exigir los procedimientos recomendados de que *function.json* no contengan secretos de servicio y en su lugar, simplemente contiene los nombres de la configuración de la aplicación.

A continuación, utilice los identificadores que facilitó para posibilitar la integración con Azure Storage en el código.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
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

Esta es la función *function.json* correspondiente al código anterior. Tenga en cuenta que se puede emplear la misma configuración, con independencia del lenguaje de la implementación de la función.

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
Para ver y editar el contenido de *function.json* en el portal de Azure, haga clic en la opción **Editor avanzado** en la pestaña **Integrar** de la función.

Para obtener más ejemplos de código e información sobre la integración con Azure Storage, consulte el artículo sobre [desencadenadores y enlaces de Azure Functions para Azure Storage](functions-bindings-storage.md).

### <a name="binding-direction"></a>Dirección de los enlaces

Todos los desencadenadores y enlaces tienen la propiedad `direction`:

- En el caso de los desencadenadores, esta propiedad siempre aparece como `in`
- Los enlaces de entrada y de salida usan `in` y `out`
- Algunos enlaces admiten la dirección especial `inout`. Si utiliza `inout`, solo estará disponible la opción **Editor avanzado** en la pestaña **Integrar**.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Uso del tipo de valor devuelto por la función para devolver un resultado único

En el ejemplo anterior, se muestra cómo utilizar el valor devuelto por la función para proporcionar la salida a un enlace, lo cual se consigue mediante el parámetro de nombre especial `$return`. (Esto solo se admite en los lenguajes que presenten un valor devuelto, como C#, JavaScript y F#). Si una función tiene varios enlaces de salida, emplee `$return` para únicamente uno de ellos. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

En los ejemplos siguientes, puede ver cómo se usan los tipos de valores devueltos con los enlaces de salida en C#, JavaScript y F#.

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

En .NET, use los tipos para definir el tipo de datos de los datos de entrada. Por ejemplo, use `string` para enlazar al texto de un desencadenador de cola, una matriz de bytes que se lee como binaria y un tipo personalizado para deserializar a un objeto POCO.

Para los idiomas que se escriben dinámicamente, como JavaScript, use la propiedad `dataType` en la definición de enlace. Por ejemplo, para leer el contenido de una solicitud HTTP en formato binario, use el tipo `binary`:

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

## <a name="trigger-metadata-properties"></a>Propiedades de metadatos de los desencadenadores

Además de la carga de datos que proporciona un desencadenador (como el mensaje de la cola que desencadenó una función), muchos desencadenadores facilitan valores de metadatos adicionales. Estos valores pueden usarse como parámetros de entrada en C# y F# o como propiedades en el objeto `context.bindings` de JavaScript. 

Por ejemplo, un desencadenador de Azure Storage Queue admite las siguientes propiedades:

* QueueTrigger (desencadena el contenido del mensaje si hay una cadena válida)
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Los detalles sobre las propiedades de metadatos de cada desencadenador se describen en el tema de referencia correspondiente. También podrá encontrar documentación en la pestaña **Integrar** del portal, en la sección **Documentación**, debajo del área de configuración de enlaces.  

Por ejemplo, debido a que los desencadenadores de blobs tienen algunos retrasos, puede utilizar un desencadenador de colas para ejecutar la función (consulte el artículo relativo al [desencadenador de Blob Storage](functions-bindings-storage-blob.md#storage-blob-trigger)). El mensaje de la cola contendría el nombre de archivo del blob que se desencadenará. Mediante la propiedad de metadatos `queueTrigger`, puede especificar este comportamiento en la configuración, en lugar del código.

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

Las propiedades de metadatos de los desencadenadores también se pueden usar en una *expresión de enlace* para otro enlace, tal y como se describe en la siguiente sección.

## <a name="binding-expressions-and-patterns"></a>Patrones y expresiones de enlace

Una de las características más eficaces de los desencadenadores y los enlaces son las *expresiones de enlace*. En el enlace, puede definir expresiones de patrón que, después, podrán emplearse en otros enlaces o en el código. Asimismo, los metadatos de los desencadenadores pueden utilizarse en expresiones de enlace, tal y como aparece en la muestra de la sección anterior.

Por ejemplo, imagine que desea cambiar el tamaño de unas imágenes en un contenedor de almacenamiento de blobs concreto, similar a la plantilla **Image Resizer** (Cambio de tamaño de imágenes) de la página **Nueva función**. Vaya a **Nueva función** -> lenguaje **C#** -> escenario **Samples** (Muestras) -> **ImageResizer-CSharp**. 

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


### <a name="random-guids"></a>GUID aleatorios
Azure Functions facilita una práctica sintaxis para generar GUID en los enlaces por medio de la expresión de enlace `{rand-guid}`. En el ejemplo siguiente, se usa dicha expresión para crear un nombre de blob único: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Hora actual

Puede usar la expresión de enlace `DateTime`, que se resuelve en `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>Enlace a propiedades de entrada personalizadas en una expresión de enlace

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

En C# y otros lenguajes .NET, puede usar un patrón de enlace imperativo, en contraposición a los enlaces declarativos de *function.json*. Los enlaces imperativos resultan útiles cuando los parámetros de enlace tienen que calcularse en tiempo de ejecución, en lugar de en el tiempo de diseño. Para obtener más información, vea [Enlace en tiempo de ejecución a través de enlaces imperativos](functions-reference-csharp.md#imperative-bindings) en la Referencia para desarrolladores de C#.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre un tipo de enlace concreto, consulte estos artículos:

- [HTTP y webhooks](functions-bindings-http-webhook.md)
- [Temporizador](functions-bindings-timer.md)
- [Queue Storage](functions-bindings-storage-queue.md)
- [Blob Storage](functions-bindings-storage-blob.md)
- [Table storage](functions-bindings-storage-table.md)
- [Event Hubs](functions-bindings-event-hubs.md)
- [Bus de servicio](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-documentdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Archivo externo](functions-bindings-external-file.md)
