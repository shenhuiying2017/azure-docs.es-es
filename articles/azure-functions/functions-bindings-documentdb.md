---
title: Enlaces de DocumentDB en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar los enlaces de DocumentDB en funciones de Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/10/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: c9e736f7ce5330823f3890c669da40e2bb1ecf43
ms.openlocfilehash: 13b69118c6732ed872bec11e880737db3b8fa3c5


---
# <a name="azure-functions-documentdb-bindings"></a>Enlaces de DocumentDB en funciones de Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y codificar enlaces de Azure DocumentDB en funciones de Azure. Azure Functions admite enlaces de entrada y salida para DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Para más información sobre DocumentDB, consulte [Introducción a DocumentDB](../documentdb/documentdb-introduction.md) y [Crear una aplicación de consola de C# DocumentDB](../documentdb/documentdb-get-started.md).

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>Enlace de entrada de DocumentDB
El enlace de entrada de DocumentDB recupera un documento de DocumentDB y lo pasa al parámetro de entrada con nombre de la función. Se puede determinar el identificador de documento según el desencadenador que invoca la función. 

La entrada de DocumentDB a una función usa el siguiente objeto JSON en la matriz `bindings` de function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "id": "<Id of the DocumentDB document - see below>",
  "connection": "<Name of app setting with connection string - see below>",
  "direction": "in"
},
```

Tenga en cuenta lo siguiente:

* `id` admite enlaces similares a `{queueTrigger}`, que usa el valor de cadena del mensaje en cola como identificador del documento.
* `connection` debe ser el nombre de una configuración de aplicación que señala al punto de conexión para la cuenta de DocumentDB (con el valor `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). Si crea una cuenta de DocumentDB a través de la IU del portal de Functions, el proceso de creación de cuenta crea una configuración de aplicación. Para usar una cuenta de DocumentDB existente, necesita [definir esta configuración de aplicación manualmente](). 
* Si el documento especificado no se encuentra, el parámetro de entrada con nombre a la función se establece en `null`. 

## <a name="input-usage"></a>Uso de entradas
En esta sección se muestra cómo utilizar el enlace de entrada de DocumentDB en el código de función.

En funciones de C# y F#, los cambios realizados en documento de entrada (parámetro de entrada con nombre) se devuelven automáticamente a la colección cuando la función termina correctamente. En las funciones de Node.js, las actualizaciones en el documento en el enlace de entrada no se devuelven a la colección. Sin embargo, puede usar `context.bindings.<documentName>In` y `context.bindings.<documentName>Out` para realizar actualizaciones en documentos de entrada. Vea cómo se hace en el [ejemplo de Node.js](#innodejs).

<a name="inputsample"></a>

## <a name="input-sample"></a>Ejemplo de entrada
Suponga que tiene el siguiente enlace de entrada de DocumentDB en la matriz `bindings` de function.json:

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "in"
}
```

Vea el ejemplo específico del idioma que utiliza este enlace de entrada para actualizar el valor de texto del documento.

* [C#](#incsharp)
* [F#](#infsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Ejemplo de entrada en C# #

```cs
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Ejemplo de entrada en F# #

```fsharp
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

Debe agregar una archivo `project.json` que especifica las dependencias de NuGet `FSharp.Interop.Dynamic` y `Dynamitey`:

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

Para agregar un archivo `project.json`, consulte la [administración de paquetes de F #](functions-reference-fsharp.md#package).

<a name="innodejs"></a>

### <a name="input-sample-in-nodejs"></a>Ejemplo de entrada en Node.js

```javascript
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="a-iddocdboutputadocumentdb-output-binding"></a><a id="docdboutput"></a>Enlace de salida de DocumentDB
El enlace de salida de DocumentDB permite escribir un nuevo documento en una base de datos de Azure DocumentDB. 

El enlace de salida usa el siguiente objeto JSON en la matriz `bindings` de function.json: 

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "createIfNotExists": <true or false - see below>,
  "connection": "<Value of AccountEndpoint in Application Setting - see below>",
  "direction": "out"
}
```

Tenga en cuenta lo siguiente:

* Establezca `createIfNotExists` en `true` para crear la base de datos y la colección si no existe. El valor predeterminado es `false`. Las nuevas colecciones se crean con rendimiento reservado, lo cual afecta al precio. Para obtener más información, consulte [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).
* `connection` debe ser el nombre de una configuración de aplicación que señala al punto de conexión para la cuenta de DocumentDB (con el valor `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). Si crea una cuenta de DocumentDB a través de la IU del portal de Functions, el proceso de creación de cuenta crea una nueva configuración de aplicación. Para usar una cuenta de DocumentDB existente, necesita [definir esta configuración de aplicación manualmente](). 

## <a name="output-usage"></a>Uso de salidas
En esta sección se muestra cómo utilizar el enlace de salida de DocumentDB en el código de función.

Cuando se escribe en el parámetro de salida en la función, de forma predeterminada se genera un nuevo documento en la base de datos, con un GUID generado automáticamente como el identificador de documento. Puede especificar el identificador de documento del documento de salida mediante la especificación de la propiedad JSON `id` en el parámetro de salida. Si ya existe un documento con ese identificador, el documento de salida lo sobrescribe. 

Puede escribir en la salida mediante cualquiera de los siguientes tipos:

* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): útil para la serialización mediante JSON.
  Si declara un tipo de salida personalizado (por ejemplo, `out FooType paramName`), Azure Functions intentará serializar el objeto en JSON. Si el parámetro de salida es nulo cuando sale la función, el entorno de tiempo de ejecución de Azure Functions creará un blob como objeto nulo.
* Cadena: (`out string paramName`) útil para los datos de blob de texto. el entorno de tiempo de ejecución de Azure Functions genera un blob solo si el parámetro de cadena no es nulo cuando sale la función.

En las funciones de C# también puede enviar la salida a cualquiera de los siguientes tipos:

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

Para generar varios documentos, también puede enlazar a `ICollector<T>` o `IAsyncCollector<T>`, donde `T` es uno de los tipos admitidos.


<a name="outputsample"></a>

## <a name="output-sample"></a>Ejemplo de salida
Suponga que tiene el siguiente enlace de salida de DocumentDB en la matriz `bindings` de function.json:

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "out"
}
```

Y tienen un enlace de entrada de cola para una cola que recibe JSON en el formato siguiente:

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Y desea crear documentos de DocumentDB en el formato siguiente para cada registro:

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Vea el ejemplo específico del idioma que utiliza este enlace de salida para agregar documentos a la base de datos.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Ejemplo de salida en C# #

```cs
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
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Ejemplo de salida en F# #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

Debe agregar una archivo `project.json` que especifica las dependencias de NuGet `FSharp.Interop.Dynamic` y `Dynamitey`:

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

Para agregar un archivo `project.json`, consulte la [administración de paquetes de F #](functions-reference-fsharp.md#package).

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Ejemplo de salida en Node.js

```javascript
module.exports = function (context) {

  context.bindings.employeeDocument = JSON.stringify({ 
    id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
    name: context.bindings.myQueueItem.name,
    employeeId: context.bindings.myQueueItem.employeeId,
    address: context.bindings.myQueueItem.address
  });

  context.done();
};
```



<!--HONumber=Dec16_HO1-->


