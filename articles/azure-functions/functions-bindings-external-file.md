---
title: Enlaces de archivo externo para Azure Functions (experimental)
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
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Enlaces de archivo externo de Azure Functions (experimental)
En este artículo se muestra cómo manipular archivos de distintos proveedores de SaaS (como Dropbox o Google Drive) en Azure Functions. Azure Functions admite enlaces de desencadenador, entrada y salida para archivos externos. Estos enlaces crean conexiones de API para los proveedores de SaaS o usan conexiones de API existentes del grupo de recursos de Function App.

> [!IMPORTANT]
> Los enlaces de archivo externo son experimentales y es posible que nunca alcancen la disponibilidad general (GA). Solo se incluyen en Azure Functions 1.x y no hay planes de agregarlos en Azure Functions 2.x. En el caso de los escenarios que necesitan acceso a los datos de los proveedores de SaaS, considere usar las [aplicaciones lógicas que llaman a las funciones](functions-twitter-email.md). Consulte el [conector del sistema de archivos de Logic Apps](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Conexiones de archivo disponibles

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
> También se pueden usar conexiones de archivos externos en [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Desencadenador

El desencadenador de archivo externo permite supervisar una carpeta remota y ejecutar el código de función cuando se detectan cambios.

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de archivo externo en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función registra el contenido de cada archivo que se agrega a la carpeta supervisada.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de script de C#:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de archivo externo en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función registra el contenido de cada archivo que se agrega a la carpeta supervisada.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json*.

|Propiedad de function.json | DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | Se debe establecer en `apiHubFileTrigger`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | Se debe establecer en `in`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | Nombre de la variable que representa el elemento de evento en el código de la función. | 
|**conexión**| Identifica la configuración de aplicación que almacena la cadena de conexión. La configuración de la aplicación se crea automáticamente cuando se agrega una conexión en la interfaz de usuario integrada en Azure Portal.|
|**path** | La carpeta que se supervisará y, de manera opcional, un patrón de nombre.|

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

## <a name="trigger---usage"></a>Desencadenador: uso

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

<!--- ## Trigger - file receipts
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

## <a name="trigger---poison-files"></a>Desencadenador: archivos dudosos

Si se produce un error en una función del desencadenador de archivo externo, Azure Functions volverá a intentar esa función hasta 5 veces de forma predeterminada (incluido el primer intento) para un determinado archivo.
Si se produce un error en los cinco intentos, Functions agregará un mensaje a una cola de Storage denominada *webjobs-apihubtrigger-poison*. El mensaje de cola para los archivos dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*)
* FileType
* FolderName
* FileName
* ETag (un identificador de la versión del archivo, por ejemplo: "0x8D1DC6E70A277EF")

## <a name="input"></a>Entrada

El enlace de entrada de archivo externo de Azure le permite utilizar un archivo de una carpeta externa en la función.

## <a name="input---example"></a>Ejemplo de entrada

Vea el ejemplo específico del lenguaje:

* [Script de C#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Entrada: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de entrada y salida de un archivo externo en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función copia un archivo de entrada en un archivo de salida.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de script de C#:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Entrada: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de entrada y salida de un archivo externo en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función copia un archivo de entrada en un archivo de salida.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Entrada: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json*.

|Propiedad de function.json | DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | Se debe establecer en `apiHubFile`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | Se debe establecer en `in`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | Nombre de la variable que representa el elemento de evento en el código de la función. | 
|**conexión**| Identifica la configuración de aplicación que almacena la cadena de conexión. La configuración de la aplicación se crea automáticamente cuando se agrega una conexión en la interfaz de usuario integrada en Azure Portal.|
|**path** | Debe contener el nombre de archivo y el nombre de carpeta. Por ejemplo, si tiene un [desencadenador de cola](functions-bindings-storage-queue.md) en la función, puede usar `"path": "samples-workitems/{queueTrigger}"` para apuntar a un archivo de la carpeta `samples-workitems` con un nombre que coincida con el nombre de archivo especificado en el mensaje desencadenador.   

## <a name="input---usage"></a>Uso de entradas

En las funciones de C#, puede enlazar con los datos de entrada de archivo mediante un parámetro con nombre en la firma de la función, como `<T> <name>`. `T` es el tipo de datos en el que desea deserializar los datos y `name` es el nombre que especificó en el enlace de entrada. En las funciones de Node.js, puede obtener acceso a los datos de entrada del archivo mediante `context.bindings.<name>`.

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

## <a name="output"></a>Salida

El enlace de salida de archivo externo de Azure le permite escribir archivos en una carpeta externa en la función.

## <a name="output---example"></a>Salida: ejemplo

Consulte el [ejemplo de enlace de entrada](#input---example).

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json*.

|Propiedad de function.json | DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | Se debe establecer en `apiHubFile`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | Se debe establecer en `out`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | Nombre de la variable que representa el elemento de evento en el código de la función. | 
|**conexión**| Identifica la configuración de aplicación que almacena la cadena de conexión. La configuración de la aplicación se crea automáticamente cuando se agrega una conexión en la interfaz de usuario integrada en Azure Portal.|
|**path** | Debe contener el nombre de archivo y el nombre de carpeta. Por ejemplo, si tiene un [desencadenador de cola](functions-bindings-storage-queue.md) en la función, puede usar `"path": "samples-workitems/{queueTrigger}"` para apuntar a un archivo de la carpeta `samples-workitems` con un nombre que coincida con el nombre de archivo especificado en el mensaje desencadenador.   

## <a name="output---usage"></a>Uso de salidas

En las funciones de C#, puede enlazar al archivo de salida mediante el parámetro con nombre `out` de la firma de la función, como `out <T> <name>`, donde `T` es el tipo de datos en el que desea serializar los datos y `name` es el nombre que especificó en el enlace de salida. En las funciones de Node.js, puede obtener acceso al archivo de salida mediante `context.bindings.<name>`.

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

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
