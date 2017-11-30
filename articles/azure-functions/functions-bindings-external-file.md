---
title: "Enlaces de archivo externo de Azure Functions (versión preliminar) | Microsoft Docs"
description: Uso de enlaces de archivo externo en Azure Functions
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: c7a1ff4d4488b37b1969edfbb6f935eccd63413c
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-external-file-bindings-preview"></a>Enlaces de archivo externo de Azure Functions (versión preliminar)
Este artículo muestra cómo manipular archivos de diferentes proveedores de SaaS (por ejemplo, OneDrive y Dropbox) dentro de la función mediante enlaces integrados. Azure Functions admite enlaces de desencadenador, entrada y salida para un archivo externo.

Este enlace crea conexiones de API para los proveedores de SaaS o utiliza conexiones de API existentes del grupo de recursos de Function App.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>Conexiones de archivos compatibles

|Conector|Desencadenador|Entrada|Salida|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive para la Empresa](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> También se pueden utilizar conexiones de archivos externos en [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="external-file-trigger-binding"></a>Enlace de desencadenador de archivo externo

El desencadenador de archivo externo de Azure permite supervisar una carpeta remota y ejecutar el código de función cuando se detectan cambios.

El desencadenador de archivo externo utiliza los siguientes objetos JSON en la matriz `bindings` de function.json.

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

### <a name="name-patterns"></a>Patrones de nombre
Puede especificar un patrón de nombre de archivo en la propiedad `path` . La carpeta a la que se hace referencia debe existir en el proveedor de SaaS.
Ejemplos:

```json
"path": "input/original-{name}",
```

Esta ruta de acceso encontraría un archivo denominado *original-File1.txt* en la carpeta *input* y el valor de la variable `name` en el código de la función sería `File1.txt`.

Otro ejemplo:

```json
"path": "input/{filename}.{fileextension}",
```

Esta ruta de acceso también encontraría un archivo denominado *original-File1.txt* y el valor de las variables `filename` y `fileextension` en el código de la función sería *original-File1* y *txt*.

Puede restringir el tipo de archivo de los archivos mediante el uso de un valor fijo para la extensión de archivo. Por ejemplo:

```json
"path": "samples/{name}.png",
```

En este caso, solo desencadenarán la función los archivos de tipo *.png* de la carpeta *samples*.

Las llaves son caracteres especiales en los patrones de nombre. Si necesita especificar un nombre de archivo que contiene llaves, duplique las llaves.
Por ejemplo:

```json
"path": "images/{{20140101}}-{name}",
```

Esta ruta encontraría un archivo denominado *{20140101}-soundfile.mp3* en la carpeta *images* y el valor de la variable `name` del código de la función sería *soundfile.mp3*.

<a name="receipts"></a>

<!--- ### File receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->
<a name="poison"></a>

### <a name="handling-poison-files"></a>Control de archivos dudosos
Si se produce un error en una función del desencadenador de archivo externo, Azure Functions volverá a intentar esa función hasta 5 veces de forma predeterminada (incluido el primer intento) para un determinado archivo.
Si se produce un error en los cinco intentos, Functions agregará un mensaje a una cola de Storage denominada *webjobs-apihubtrigger-poison*. El mensaje de cola para los archivos dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*)
* FileType
* FolderName
* FileName
* ETag (un identificador de la versión del archivo, por ejemplo: "0x8D1DC6E70A277EF")


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso del desencadenador
En las funciones de C#, puede enlazar con los datos de entrada de archivo mediante un parámetro con nombre en la firma de la función, como `<T> <name>`.
Donde `T` es el tipo de datos en el que desea deserializar los datos, y `paramName` es el nombre que especificó en el [desencadenador JSON](#trigger). En las funciones de Node.js, puede obtener acceso a los datos de entrada del archivo mediante `context.bindings.<name>`.

El archivo se puede deserializar en cualquiera de los siguientes tipos:

* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): útil para datos de archivos serializados mediante JSON.
  Si declara un tipo de entrada personalizado (por ejemplo, `FooType`), Azure Functions intentará deserializar los datos JSON en el tipo especificado.
* Cadena: útil para los datos de archivo de texto.

En las funciones de C#, también puede enlazar con cualquiera de los siguientes tipos y el entorno de tiempo de ejecución de Functions intenta deserializar los datos del archivo mediante ese tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="trigger-sample"></a>Ejemplo de desencadenador
Suponga que tiene el siguiente function.json que define un desencadenador de archivo externo:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Consulte el ejemplo de código específico del lenguaje que registra el contenido de cada archivo que se agrega a la carpeta supervisada.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Uso del desencadenador en C# #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
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
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>Enlace de entrada de archivo externo
El enlace de entrada de archivo externo de Azure le permite utilizar un archivo de una carpeta externa en la función.

La entrada del archivo externo a una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

Tenga en cuenta lo siguiente:

* `path` debe contener el nombre de archivo y el nombre de carpeta. Por ejemplo, si tiene un [desencadenador de cola](functions-bindings-storage-queue.md) en la función, puede usar `"path": "samples-workitems/{queueTrigger}"` para apuntar a un archivo de la carpeta `samples-workitems` con un nombre que coincida con el nombre de archivo especificado en el mensaje desencadenador.   

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso de entradas
En las funciones de C#, puede enlazar con los datos de entrada de archivo mediante un parámetro con nombre en la firma de la función, como `<T> <name>`.
Donde `T` es el tipo de datos en el que desea deserializar los datos, y `paramName` es el nombre que especificó en el [enlace de entrada](#input). En las funciones de Node.js, puede obtener acceso a los datos de entrada del archivo mediante `context.bindings.<name>`.

El archivo se puede deserializar en cualquiera de los siguientes tipos:

* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): útil para datos de archivos serializados mediante JSON.
  Si declara un tipo de entrada personalizado (por ejemplo, `InputType`), Azure Functions intentará deserializar los datos JSON en el tipo especificado.
* Cadena: útil para los datos de archivo de texto.

En las funciones de C#, también puede enlazar con cualquiera de los siguientes tipos y el entorno de tiempo de ejecución de Functions intenta deserializar los datos del archivo mediante ese tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`


<a name="output"></a>

## <a name="external-file-output-binding"></a>Enlace de salida de archivo externo
El enlace de salida de archivo externo de Azure le permite escribir archivos en una carpeta externa en la función.

La salida de archivo externo para una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

Tenga en cuenta lo siguiente:

* `path` debe contener el nombre de archivo y el nombre de carpeta en la que se va a escribir. Por ejemplo, si tiene un [desencadenador de cola](functions-bindings-storage-queue.md) en la función, puede usar `"path": "samples-workitems/{queueTrigger}"` para apuntar a un archivo de la carpeta `samples-workitems` con un nombre que coincida con el nombre de archivo especificado en el mensaje desencadenador.   

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de salidas
En las funciones de C#, puede enlazar al archivo de salida mediante el parámetro con nombre `out` de la firma de la función, como `out <T> <name>`, donde `T` es el tipo de datos en el que desea serializar los datos y `paramName` es el nombre que especificó en el [enlace de salida](#output). En las funciones de Node.js, puede obtener acceso al archivo de salida mediante `context.bindings.<name>`.

Puede escribir en el archivo de salida mediante cualquiera de los siguientes tipos:

* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): útil para la serialización mediante JSON.
  Si declara un tipo de salida personalizado (por ejemplo, `out OutputType paramName`), Azure Functions intentará serializar el objeto en JSON. Si el parámetro de salida es nulo cuando sale la función, el entorno de tiempo de ejecución de Functions creará un archivo como objeto nulo.
* Cadena: (`out string paramName`) útil para los datos de archivo de texto. El entorno de tiempo de ejecución de Functions genera un archivo solo si el parámetro de cadena no es nulo cuando sale la función.

En las funciones de C# también puede enviar la salida a cualquiera de los siguientes tipos:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>Ejemplo de entrada y salida
Suponga que tiene el siguiente objeto function.json que define un [desencadenador de cola de Storage](functions-bindings-storage-queue.md), una entrada de archivo externo y una salida de archivo externo:

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
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Vea el ejemplo específico del lenguaje que copia el archivo de entrada en el archivo de salida.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>Uso en C# #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>Uso en Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
