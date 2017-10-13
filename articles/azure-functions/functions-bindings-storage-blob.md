---
title: Enlaces de Blob Storage en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Azure en funciones de Azure."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: b123578dbac48018f674f85ec923e4c6e65fb9f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Enlaces de Blob Storage en Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar enlaces de Azure Blob Storage y trabajar con ellos en Azure Functions. Azure Functions admite enlaces de desencadenador, entrada y salida para Azure Blob Storage. Para las características que estén disponibles en todos los enlaces, consulte [conceptos sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> No se admite una [cuenta de almacenamiento solo de blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts). Los desencadenadores y enlaces de Blob Storage necesitan una cuenta de almacenamiento de uso general. 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>Desencadenadores y enlaces de Blob Storage

Al usar el desencadenador de Azure Blob Storage, se llama al código de la función cuando se detecte un blob nuevo o actualizado. El contenido del blob se proporciona a modo de entrada para la función.

Defina un desencadenador de Blob Storage con la pestaña **Integrar** del portal de Functions. El portal crea la siguiente definición en la sección de **enlaces** de *function.json*:

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

Los enlaces de entrada y salida de blob se definen con `blob` como tipo de enlace:

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* La propiedad `path` admite las expresiones de enlace y los parámetros de filtrado. Consulte [Patrones de nombre](#pattern).
* La propiedad `connection` debe contener el nombre de una configuración de aplicación que contenga una cadena de conexión de almacenamiento. En Azure Portal, el editor estándar de la pestaña **Integrar** permite modificar esta configuración de aplicación al seleccionar una cuenta de almacenamiento.

> [!NOTE]
> Al usar un desencadenador de blobs en un plan de consumo, puede haber un retraso de hasta 10 minutos en el procesamiento de nuevos blobs después de que una aplicación de función quede inactiva. Después de que se ejecute la aplicación de función, los blobs se procesan inmediatamente. Para evitar este retraso inicial, considere una de las siguientes opciones:
> - Usar un plan de App Service con Always On habilitado
> - Usar otro mecanismo para desencadenar el procesamiento de blobs, por ejemplo, un mensaje de la cola que contenga el nombre del blob Para ver un ejemplo, consulte [desencadenador de cola con enlace de entrada de blob](#input-sample).

<a name="pattern"></a>

### <a name="name-patterns"></a>Patrones de nombre
Puede especificar un patrón de nombre de blob en la propiedad `path`, que puede ser una expresión de filtro o de enlace. Consulte [Patrones y expresiones de enlace](functions-triggers-bindings.md#binding-expressions-and-patterns).

Por ejemplo, para filtrar blobs que empiecen por la cadena "original", use la siguiente definición. Esta ruta de acceso encuentra un blob llamado *original-Blob1.txt* en el contenedor *input* y el valor de la variable `name` en el código de la función es `Blob1`.

```json
"path": "input/original-{name}",
```

Para enlazar el nombre de archivo del blob y la extensión por separado, use dos patrones. Esta ruta de acceso también encuentra un blob llamado *original-Blob1.txt* y el valor de las variables `blobname` y `blobextension` del código de la función son *original-Blob1* y *txt*.

```json
"path": "input/{blobname}.{blobextension}",
```

Puede restringir el tipo de archivo de los blobs mediante el uso de un valor fijo para la extensión de archivo. Por ejemplo, para realizar desencadenamientos solo en archivos .png, use el siguiente patrón:

```json
"path": "samples/{name}.png",
```

Las llaves son caracteres especiales en los patrones de nombre. Para especificar nombres de blob que tengan llaves, puede omitirlas mediante dos llaves. En el siguiente ejemplo, se encuentra un blob llamado *{20140101}-soundfile.mp3* en el contenedor *images* y el valor de la variable `name` del código de la función es *soundfile.mp3*. 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>Metadatos de desencadenador

El desencadenador de blobs proporciona varias propiedades de metadatos. Estas propiedades pueden usarse como parte de expresiones de enlace en otros enlaces o como parámetros del código. Estos valores tienen la misma semántica que [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

- **BlobTrigger**. Escriba `string`. Es la ruta de acceso del blob de desencadenamiento.
- **Uri**. Escriba `System.Uri`. Es el identificador URI del blob correspondiente a la ubicación principal.
- **Properties**. Escriba `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`. Son las propiedades del sistema del blob.
- **Metadata**. Escriba `IDictionary<string,string>`. Son los metadatos definidos por el usuario para el blob.

<a name="receipts"></a>

### <a name="blob-receipts"></a>Recepciones de blobs
El entorno en tiempo de ejecución de Azure Functions garantiza que no se llame más de una vez a ninguna función de desencadenador de blobs para un mismo blob, ya sea nuevo o actualizado. Mantiene *recepciones de blobs* para determinar si se ha procesado una determinada versión de blob.

Azure Functions almacena confirmaciones de blobs en un contenedor llamado *azure-webjobs-hosts* en la cuenta de almacenamiento de Azure de la aplicación de función (que se especifica mediante la configuración de la aplicación `AzureWebJobsStorage`). Una recepción de blobs tiene la información siguiente:

* La función desencadenada ("*&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*", por ejemplo: "MyFunctionApp.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre del blob
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar el reprocesamiento de un blob, puede eliminar manualmente la recepción de ese blob desde el contenedor *azure-webjobs-hosts* .

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Control de blobs dudosos
Si se produce un error en una función de desencadenador de blob, Azure Functions vuelve a intentar ejecutar esa función hasta 5 veces de forma predeterminada. 

Si se produce un error en los 5 intentos, Azure Functions agregará un mensaje a una cola de Storage llamada *webjobs-blobtrigger-poison*. El mensaje de cola para los blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>Sondeo de blobs en contenedores grandes
Si el contenedor de blobs supervisado contiene más de 10 000 blobs, el entorno en tiempo de ejecución de Functions examinará los archivos de registro para detectar blobs nuevos o modificados. Este proceso no se lleva a cabo en tiempo real. Una función podría tardar en desencadenarse varios minutos o más después de crear el blob. Además, [se crean registros de almacenamiento basados en el principio del "mejor esfuerzo"](/rest/api/storageservices/About-Storage-Analytics-Logging). No hay ninguna garantía de que todos los eventos se capturen. En algunos casos, podrían faltar registros. Si necesita un procesamiento de blobs más rápido o confiable, considere crear un [mensaje de cola](../storage/queues/storage-dotnet-how-to-use-queues.md) al crear el blob. A continuación, use un [desencadenador de cola](functions-bindings-storage-queue.md) en lugar de un desencadenador de blob para procesar el blob.

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>Uso de un desencadenador de blob y un enlace de entrada
En las funciones de .NET, acceda a los datos del blob mediante un parámetro de método como `Stream paramName`. Aquí, `paramName` es el valor que especificó en la [configuración del desencadenador](#trigger). En las funciones de Node.js, acceda a los datos de entrada del blob mediante `context.bindings.<name>`.

En. NET, puede crear enlaces a cualquiera de los tipos de la siguiente lista. Si se usan como enlace de entrada, algunos de estos tipos necesitan una dirección de enlace `inout` en *function.json*. El editor estándar no admite esta dirección, por lo que debe usar el avanzado.

* `TextReader`
* `Stream`
* `ICloudBlob` (necesita una dirección de enlace "inout")
* `CloudBlockBlob` (necesita una dirección de enlace "inout")
* `CloudPageBlob` (necesita una dirección de enlace "inout")
* `CloudAppendBlob` (necesita una dirección de enlace "inout")

Si se esperan blobs de texto, también puede realizar el enlace a un tipo `string` de .NET. Solo se recomienda hacerlo si los blobs son pequeños, ya que todos sus contenidos se cargan en memoria. Por lo general, es preferible usar un tipo `Stream` o `CloudBlockBlob`.

## <a name="trigger-sample"></a>Ejemplo de desencadenador
Suponga que tiene el siguiente elemento function.json que define un desencadenador de Blob Storage:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

Consulte el ejemplo de código específico del lenguaje que registra el contenido de cada blob que se agrega al contenedor supervisado.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>Ejemplos de desencadenador de blobs en C# #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Ejemplo de desencadenador en Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>Uso de un enlace de salida de blob

En las funciones de .NET, debería usar un parámetro `out string` en la firma de la función o usar uno de los tipos de la siguiente lista. En las funciones de Node.js, puede obtener acceso al blob de salida mediante `context.bindings.<name>`.

En las funciones de .NET, puede enviar a la salida cualquiera de los siguientes tipos:

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>Ejemplo de desencadenador de cola con entrada y salida de blob
Suponga que tiene el siguiente elemento function.json, que define un [desencadenador de Queue Storage](functions-bindings-storage-queue.md) y una entrada y una salida de Blob Storage. Tenga en cuenta el uso de la propiedad de metadatos `queueTrigger`. en las propiedades de entrada y salida de blob `path`:

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

Vea el ejemplo específico del lenguaje que copia el blob de entrada en el blob de salida.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>Ejemplo de enlace de blob en C# #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Ejemplo de enlace de blob en Node.js

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

