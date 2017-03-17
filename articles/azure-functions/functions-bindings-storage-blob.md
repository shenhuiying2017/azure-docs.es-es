---
title: Enlaces de blob de Storage en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Azure en funciones de Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 1c071390fd6cd9bb5889cb225696b7782fe2bd6b
ms.lasthandoff: 03/14/2017


---
# <a name="azure-functions-storage-blob-bindings"></a>Enlaces de blob de Storage en Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y codificar enlaces de blob de Azure Storage en Azure Functions. Azure Functions admite enlaces de desencadenador, entrada y salida para blob de Azure Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> No se admite una [cuenta de almacenamiento solo de blob](../storage/storage-create-storage-account.md#blob-storage-accounts). Azure Functions requiere una cuenta de almacenamiento general para usar con los blobs. 
> 
> 

<a name="trigger"></a>

## <a name="storage-blob-trigger"></a>Desencadenador de blobs de Storage
El desencadenador de blobs de Azure Storage le permite supervisar un contenedor de almacenamiento para comprobar los blobs nuevos y actualizados y ejecutar el código de función cuando se detectan cambios. 

El desencadenador de blob de Azure Storage para una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection":"<Name of app setting - see below>"
}
```

Tenga en cuenta lo siguiente:

* Para `path`, consulte [Patrones de nombre](#pattern) para averiguar cómo dar formato a los patrones de nombre de los blobs.
* `connection` debe contener el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. En Azure Portal, el editor estándar de la pestaña **Integrar** permite modificar esta configuración de aplicación cuando crea una cuenta de Azure Storage o selecciona una ya existente. Para crear manualmente esta configuración de aplicación, vea cómo [configurar manualmente esta configuración de aplicación](). 

Cuando se ejecuta en un plan de consumo, si una aplicación de función se ha vuelto inactiva, los nuevos blobs podrían tardar en procesarse hasta 10 minutos al día. Una vez que se ejecuta la aplicación de función, los blobs se procesan más rápidamente. Para evitar este retraso inicial, use un plan de App Service normal con AlwaysOn habilitado o utilice otro mecanismo para desencadenar el procesamiento de blobs, por ejemplo, un mensaje de la cola que contenga el nombre del blob. 

Además, consulte uno de los subtítulos siguientes para obtener más información:

* [Patrones de nombre](#pattern)
* [Recepciones de blobs](#receipts)
* [Control de blobs dudosos](#poison)

<a name="pattern"></a>

### <a name="name-patterns"></a>Patrones de nombre
Puede especificar un patrón de nombre de blob en la propiedad `path` . Por ejemplo:

```json
"path": "input/original-{name}",
```

Esta ruta de acceso encontraría un blob denominado *original-Blob1.txt* en el contenedor *input* y el valor de la variable `name` en el código de la función sería `Blob1`.

Otro ejemplo:

```json
"path": "input/{blobname}.{blobextension}",
```

Esta ruta de acceso también encontraría un blob denominado *original-Blob1.txt* y el valor de las variables `blobname` y `blobextension` en el código de la función sería *original-Blob1* y *txt*.

Puede restringir el tipo de archivo de los blobs mediante el uso de un valor fijo para la extensión de archivo. Por ejemplo:

```json
"path": "samples/{name}.png",
```

En este caso, solo desencadenarán la función los blobs de tipo *.png* del contenedor *samples*.

Las llaves son caracteres especiales en los patrones de nombre. Si necesita especificar un nombre de blob que contiene llaves, duplique las llaves. Por ejemplo:

```json
"path": "images/{{20140101}}-{name}",
```

Esta ruta encontraría un blob denominado *{20140101}-soundfile.mp3* en el contenedor *images* y el valor de la variable `name` del código de la función sería *soundfile.mp3*. 

<a name"receipts"></a>

### <a name="blob-receipts"></a>Recepciones de blobs
El sistema en tiempo de ejecución de Funciones de Azure garantiza que a ninguna función de desencadenador de blobs se le llamará más de una vez para el mismo blob nuevo o actualizado. Para hacerlo, mantiene *recepciones de blobs* con el fin de determinar si se ha procesado alguna versión de blob determinada.

Las recepciones de blobs se almacenan en un contenedor llamado *azure-webjobs-hosts* en la cuenta de Azure Storage de la aplicación de función (que se especifica mediante la configuración de la aplicación `AzureWebJobsStorage`). Una recepción de blobs tiene la información siguiente:

* La función desencadenada ("*&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*", por ejemplo: "functionsf74b96f7.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre del blob
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar el reprocesamiento de un blob, puede eliminar manualmente la recepción de ese blob desde el contenedor *azure-webjobs-hosts* .

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Control de blobs dudosos
Si se produce un error en una función del desencadenador de blob, Azure Functions volverá a tratar de ejecutar esa función hasta 5 veces de forma predeterminada (incluida la primera) para un determinado blob. Si se produce un error en los 5 intentos, Azure Functions agregará un mensaje a una cola de Storage denominada *webjobs-blobtrigger-poison*. El mensaje de cola para los blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>Sondeo de blobs en contenedores grandes
Si el contenedor de blobs supervisado por el enlace contiene más de 10 000 blobs, el entorno de tiempo de ejecución de Functions examinará los archivos de registro para detectar los blobs nuevos o modificados. Este proceso no se lleva a cabo en tiempo real. Una función podría tardar en desencadenarse varios minutos o más después de crear el blob. Además, [se crean registros de almacenamiento basados en el principio del "mejor esfuerzo"](https://msdn.microsoft.com/library/azure/hh343262.aspx). No hay ninguna garantía de que todos los eventos se capturen. En algunos casos, podrían faltar registros. Si los límites de velocidad y confiabilidad de los desencadenadores de blobs para contenedores grandes no son aceptables para su aplicación, el método recomendado consiste en crear un [mensaje en cola](../storage/storage-dotnet-how-to-use-queues.md) al crear el blob y usar un [desencadenador de cola](functions-bindings-storage-queue.md) en lugar de un desencadenador de blobs para procesar el blob.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso del desencadenador
En las funciones de C#, puede enlazar con los datos de entrada de blob mediante un parámetro con nombre en la firma de la función, como `<T> <name>`.
Donde `T` es el tipo de datos en el que desea deserializar los datos, y `paramName` es el nombre que especificó en el [desencadenador JSON](#trigger). En las funciones de Node.js, puede obtener acceso a los datos de entrada del blob mediante `context.bindings.<name>`.

El blob se puede deserializar en cualquiera de los siguientes tipos:

* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): útil para datos de blob serializados mediante JSON.
  Si declara un tipo de entrada personalizado (por ejemplo, `FooType`), Azure Functions intentará deserializar los datos JSON en el tipo especificado.
* Cadena: útil para los datos de blob de texto.

En las funciones de C#, también puede enlazar con cualquiera de los siguientes tipos y el entorno de tiempo de ejecución de Azure Functions tratará de deserializar los datos del blob mediante ese tipo:

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

## <a name="trigger-sample"></a>Ejemplo de desencadenador
Suponga que tiene el siguiente objeto function.json que define un desencadenador de blob de Storage:

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

Consulte el ejemplo de código específico del lenguaje que registra el contenido de cada blob que se agrega al contenedor supervisado.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Uso del desencadenador en C# #

```cs
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

``` 
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Uso del desencadenador en Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

<a name="input"></a>

## <a name="storage-blob-input-binding"></a>Enlace de entrada de blob de Storage
El enlace de entrada de blob de Azure Storage permite utilizar un blob de un contenedor de almacenamiento en la función. 

La entrada de blob de Azure Storage a una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "blob",
  "direction": "in"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

Tenga en cuenta lo siguiente:

* `path` debe contener el nombre del contenedor y el nombre del blob. Por ejemplo, si tiene un [desencadenador de cola](functions-bindings-storage-queue.md) en la función, puede usar `"path": "samples-workitems/{queueTrigger}"` para apuntar a un blob del contenedor `samples-workitems` con un nombre que coincida con el nombre de blob especificado en el mensaje desencadenador.   
* `connection` debe contener el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. En Azure Portal, el editor estándar de la pestaña **Integrar** permite modificar esta configuración de aplicación cuando crea una cuenta de Azure Storage o selecciona una ya existente. Para crear manualmente esta configuración de aplicación, vea cómo [configurar manualmente esta configuración de aplicación](). 

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso de entradas
En las funciones de C#, puede enlazar con los datos de entrada de blob mediante un parámetro con nombre en la firma de la función, como `<T> <name>`.
Donde `T` es el tipo de datos en el que desea deserializar los datos, y `paramName` es el nombre que especificó en el [enlace de entrada](#input). En las funciones de Node.js, puede obtener acceso a los datos de entrada del blob mediante `context.bindings.<name>`.

El blob se puede deserializar en cualquiera de los siguientes tipos:

* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): útil para datos de blob serializados mediante JSON.
  Si declara un tipo de entrada personalizado (por ejemplo, `InputType`), Azure Functions intentará deserializar los datos JSON en el tipo especificado.
* Cadena: útil para los datos de blob de texto.

En las funciones de C#, también puede enlazar con cualquiera de los siguientes tipos y el entorno de tiempo de ejecución de Azure Functions tratará de deserializar los datos del blob mediante ese tipo:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="inputsample"></a>

## <a name="input-sample"></a>Ejemplo de entrada
Suponga que tiene el siguiente objeto function.json que define un [desencadenador de cola de Storage](functions-bindings-storage-queue.md), una entrada y una salida de blob de Storage:

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

### <a name="input-usage-in-c"></a>Uso de entradas en C# #

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

``` 
-->

<a name="innodejs"></a>

### <a name="input-usage-in-nodejs"></a>Uso de entradas en Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

<a name="output"></a>

## <a name="storage-blob-output-binding"></a>Enlace de salida de blob de Storage
El enlace de salida de blob de Azure Storage permite escribir blobs en un contenedor Storage de la función. 

La salida de blob de Azure Storage para una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "blob",
  "direction": "out",
  "path": "<Path of input blob - see below>",
  "connection": "<Name of app setting - see below>"
}
```

Tenga en cuenta lo siguiente:

* `path` debe contener el nombre del contenedor y el nombre del blob en que se va a escribir. Por ejemplo, si tiene un [desencadenador de cola](functions-bindings-storage-queue.md) en la función, puede usar `"path": "samples-workitems/{queueTrigger}"` para apuntar a un blob del contenedor `samples-workitems` con un nombre que coincida con el nombre de blob especificado en el mensaje desencadenador.   
* `connection` debe contener el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. En Azure Portal, el editor estándar de la pestaña **Integrar** permite modificar esta configuración de aplicación cuando crea una cuenta de Azure Storage o selecciona una ya existente. Para crear manualmente esta configuración de aplicación, vea cómo [configurar manualmente esta configuración de aplicación](). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de salidas
En las funciones de C#, puede enlazar al blob de salida mediante el parámetro con nombre `out` de la firma de la función, como `out <T> <name>`, donde `T` es el tipo de datos en el que desea serializar los datos y `paramName` es el nombre que especificó en el [enlace de salida](#output). En las funciones de Node.js, puede obtener acceso al blob de salida mediante `context.bindings.<name>`.

Puede escribir en el blob de salida mediante cualquiera de los siguientes tipos:

* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): útil para la serialización mediante JSON.
  Si declara un tipo de salida personalizado (por ejemplo, `out OutputType paramName`), Azure Functions intentará serializar el objeto en JSON. Si el parámetro de salida es nulo cuando sale la función, el entorno de tiempo de ejecución de Azure Functions creará un blob como objeto nulo.
* Cadena: (`out string paramName`) útil para los datos de blob de texto. el entorno de tiempo de ejecución de Azure Functions genera un blob solo si el parámetro de cadena no es nulo cuando sale la función.

En las funciones de C# también puede enviar la salida a cualquiera de los siguientes tipos:

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="outputsample"></a>

## <a name="output-sample"></a>Ejemplo de salida
Consulte [Ejemplo de entrada](#inputsample).

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


