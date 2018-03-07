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
ms.date: 02/07/2018
ms.author: glenga
ms.openlocfilehash: f43132beb0abae3d4bdf0f538de1b437e6099822
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
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

## <a name="register-binding-extensions"></a>Registro de extensiones de enlace

En la versión 2.x del entorno de ejecución de Azure Functions, debe registrar explícitamente las [extensiones de enlace](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) que utilice en la aplicación de función. 

Las extensiones se entregan como paquetes NuGet, donde el nombre del paquete normalmente comienza con [microsoft.azure.webjobs.extensions](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions).  La forma de instalar y registrar las extensiones de enlace depende de cómo desarrolle sus funciones: 

+ [Localmente en C# con Visual Studio o VS Code](#precompiled-functions-c)
+ [Localmente con Azure Functions Core Tools](#local-development-azure-functions-core-tools)
+ [En Azure Portal](#azure-portal-development) 

En la versión 2.x hay un conjunto de enlaces que no se proporcionan como extensiones. No es necesario registrar extensiones para los siguientes desencadenadores y enlaces: HTTP, el temporizador y Azure Storage. 

Para obtener información sobre cómo establecer que una aplicación de función use la versión 2.x del entorno de ejecución de Functions, consulte [Cómo seleccionar un destino para versiones en tiempo de ejecución de Azure Functions](set-runtime-version.md). La versión 2.x del entorno de ejecución de Functions está actualmente en fase preliminar. 

Las versiones del paquete que se muestran en esta sección se proporcionan únicamente como ejemplos. Compruebe el [sitio de NuGet.org](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions) para determinar qué versión de una extensión determinada requieren otras dependencias de la aplicación de función.    

###  <a name="local-c-development-using-visual-studio-or-vs-code"></a>Desarrollo local con C# mediante Visual Studio o VS Code 

Cuando utilice Visual Studio o Visual Studio Code para desarrollar localmente funciones en C#, basta con agregar el paquete NuGet de la extensión. 

+ **Visual Studio**: use las herramientas del Administrador de paquetes NuGet. El siguiente comando [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) instala la extensión de Azure Cosmos DB desde la Consola del Administrador de paquetes:

    ```
    Install-Package Microsoft.Azure.WebJobs.Extensions.CosmosDB -Version 3.0.0-beta6 
    ```
+ **Visual Studio Code**: puede instalar paquetes desde el símbolo del sistema mediante el comando [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) de la CLI. para .NET, como se indica a continuación:

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
    ```

### <a name="local-development-azure-functions-core-tools"></a>Desarrollo local con Azure Functions Core Tools

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

### <a name="azure-portal-development"></a>Desarrollo con Azure Portal

Al crear una función o agregar un enlace a una función existente, se le preguntará cuando haya que registrar la extensión para el desencadenador o el enlace que se van a agregar.   

Después de que aparezca una advertencia para la extensión específica que se va a instalar, haga clic en **Instalar** para registrar la extensión. Solo necesita instalar cada extensión una vez para una aplicación de función determinada. 

>[!Note] 
>El proceso de instalación en el portal puede tardar hasta 10 minutos en un plan de consumo.

## <a name="example-trigger-and-binding"></a>Enlace y desencadenador de ejemplo

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

En una biblioteca de clases, los atributos proporcionan la misma información &mdash; de desencadenador y enlace (nombres de tabla y cola, cuentas de almacenamiento, parámetros de función de entrada y salida &mdash;), en lugar de un archivo function.json. Este es un ejemplo:

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

## <a name="using-the-function-return-value"></a>Uso del valor devuelto de la función

En los lenguajes que tienen un valor devuelto, puede enlazar un enlace de salida al valor devuelto:

* En una biblioteca de clases C#, aplique el atributo de enlace de salida para el valor devuelto del método.
* En otros lenguajes, establezca la propiedad `name` de *function.json* en `$return`.

Si tiene que escribir más de un elemento, use un [objeto recopilador](functions-reference-csharp.md#writing-multiple-output-values) en lugar del valor devuelto. Si hay varios enlaces de salida, use el valor devuelto para solo uno de ellos.

Vea el ejemplo específico del lenguaje:

* [C#](#c-example)
* [Script de C# (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Ejemplo de C#

Este es el código de C# que utiliza el valor devuelto de un enlace de salida, seguido de un ejemplo asincrónico:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Ejemplo de script de C#

Este es el enlace de salida del archivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Este es el código de script de C#, seguido de un ejemplo asincrónico:

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>Ejemplo de F#

Este es el enlace de salida del archivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Este es el código de F#:

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>Ejemplo de JavaScript

Este es el enlace de salida del archivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

En JavaScript, el valor devuelto va en el segundo parámetro para `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
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

## <a name="binding-expressions-and-patterns"></a>Patrones y expresiones de enlace

Una de las características más eficaces de los desencadenadores y los enlaces son las *expresiones de enlace*. En el archivo *function.json*, y en el código y en los parámetros de función, puede usar expresiones que se resuelvan como valores procedentes de diversos orígenes.

La mayoría de las expresiones se identifican encerrándolas entre llaves. Por ejemplo, en una función de desencadenador de cola, `{queueTrigger}` se resuelve como el texto del mensaje de cola. Si la propiedad `path` de un enlace de blob de salida es `container/{queueTrigger}` y la función se desencadena mediante un mensaje de cola `HelloWorld`, se crea un blob denominado `HelloWorld`.

Tipos de expresiones de enlace

* [Configuración de aplicación](#binding-expressions---app-settings)
* [Nombre de archivo de desencadenador](#binding-expressions---trigger-file-name)
* [Metadatos de desencadenador](#binding-expressions---trigger-metadata)
* [Cargas JSON](#binding-expressions---json-payloads)
* [GUID nuevos](#binding-expressions---create-guids)
* [Fecha y hora actuales](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Expresiones de enlace: configuración de aplicación

Como procedimiento recomendado, los secretos y las cadenas de conexión deberían administrarse mediante los ajustes de la aplicación, en lugar de archivos de configuración. De este modo, se limita el acceso a estos secretos y resulta seguro almacenar archivos como *function.json* en repositorios de control de código fuente públicos.

Los ajustes de la aplicación también son útiles cuando se desea cambiar la configuración en función del entorno. Por ejemplo, en un entorno de prueba, es posible que quiera supervisar una cola o un contenedor de almacenamiento de blobs diferente.

Las expresiones de enlace de configuración de aplicación se identifican de forma diferente de otras expresiones de enlace: se encierran entre símbolos de porcentaje en lugar de entre llaves. Por ejemplo, si la ruta de acceso del enlace de salida de blob es `%Environment%/newblob.txt` y el valor de configuración de aplicación `Environment` es `Development`, se creará un blob en el contenedor `Development`.

Cuando una función se ejecuta localmente, los valores de configuración de aplicación proceden del archivo *local.settings.json*.

Tenga en cuenta que la propiedad `connection` de los desencadenadores y enlaces es un caso especial, ya que resuelve automáticamente los valores como configuración de aplicación, sin símbolos de porcentaje. 

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

### <a name="binding-expressions---trigger-file-name"></a>Expresiones de enlace: nombre de archivo de desencadenador

El patrón `path` de un desencadenador de blob puede ser uno que permite hacer referencia al nombre del blob de desencadenamiento en otros enlaces y el código de función. El patrón también puede incluir criterios de filtrado que especifiquen qué blobs pueden desencadenar una invocación de función.

Por ejemplo, en el siguiente enlace de desencadenador de blob, el patrón `path` es `sample-images/{filename}`, lo que crea una expresión de enlace denominada `filename`:

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
    ...
```

Luego, la expresión `filename` puede utilizarse en un enlace de salida para especificar el nombre del blob que se va a crear:

```json
    ...
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

El código de función tiene acceso a este mismo valor usando `filename` como nombre de parámetro:

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

La misma capacidad para usar patrones y expresiones de enlace se aplica a los atributos de las bibliotecas de clases. En el ejemplo siguiente, los parámetros del constructor de atributo son los mismos valores `path` que los ejemplos de *function.json* anteriores: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

También puede crear expresiones para partes del nombre de archivo como la extensión. Para obtener más información sobre cómo usar expresiones y patrones en la cadena de ruta de acceso de blob, consulte la [referencia de enlace de blob de almacenamiento](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Expresiones de enlace: metadatos de desencadenador

Además de la carga de datos que proporciona un desencadenador (como el contenido del mensaje de la cola que desencadenó una función), muchos desencadenadores facilitan valores de metadatos adicionales. Estos valores pueden usarse como parámetros de entrada en C# y F# o como propiedades en el objeto `context.bindings` de JavaScript. 

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

### <a name="binding-expressions---json-payloads"></a>Expresiones de enlace: cargas JSON

Cuando una carga de desencadenador es JSON, puede hacer referencia a sus propiedades en la configuración de otros enlaces de la misma función y del código de función.

El siguiente ejemplo muestra el archivo *function.json* de una función de webhook que recibe el nombre de un blob en JSON: `{"BlobName":"HelloWorld.txt"}`. Un enlace de entrada de blob lee el blob y el enlace de salida HTTP devuelve el contenido del blob en la respuesta HTTP. Tenga en cuenta que el enlace de entrada de blob obtiene el nombre del blob haciendo referencia directamente a la propiedad `BlobName` (`"path": "strings/{BlobName}"`).

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
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
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

Para que funcione en C# y F#, necesita una clase que defina los campos que se deserializarán, como en el ejemplo siguiente:

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

En JavaScript, la deserialización de JSON se lleva a cabo de manera automática.

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

#### <a name="dot-notation"></a>Notación de puntos

Si algunas de las propiedades de la carga útil JSON son objetos con propiedades, puede hacer referencia a ellos directamente mediante la notación de puntos. Por ejemplo, suponga que su carga útil JSON tiene el siguiente aspecto:

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

Puede hacer referencia directamente a `FileName` como `BlobName.FileName`. Con este formato JSON, así sería el aspecto de la propiedad `path` del ejemplo anterior:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

En C#, necesitaría dos clases:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

### <a name="binding-expressions---create-guids"></a>Expresiones de enlace: creación de GUID

La expresión de enlace `{rand-guid}` crea un identificador único global. La siguiente ruta de acceso de blob en un archivo `function.json` crea un blob con un nombre como *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Expresiones de enlace: hora actual

La expresión de enlace `DateTime` se resuelve como `DateTime.UtcNow`. La siguiente ruta de acceso de blob en un archivo `function.json` crea un blob con un nombre como *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>Enlace en tiempo de ejecución

En C# y otros lenguajes .NET, puede usar un patrón de enlace imperativo, en contraposición a los enlaces declarativos de *function.json* y los atributos. Los enlaces imperativos resultan útiles cuando los parámetros de enlace tienen que calcularse en tiempo de ejecución, en lugar de en el tiempo de diseño. Para obtener más información, consulte la [referencia para desarrolladores de C#](functions-dotnet-class-library.md#binding-at-runtime) o la [referencia para desarrolladores de scripts de C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="functionjson-file-schema"></a>Esquema del archivo function.json

El esquema del archivo*function.json* está disponible en [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Control de errores de enlace

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Para obtener vínculos a todos los temas de error relevantes para los diversos servicios compatibles con Functions, vea la sección [Códigos de error de enlace](functions-bindings-error-pages.md#binding-error-codes) del tema introductorio [Control de errores en Azure Functions](functions-bindings-error-pages.md).  

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
