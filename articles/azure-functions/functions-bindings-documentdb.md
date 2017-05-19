---
title: Enlaces de Cosmos DB en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar enlaces de Cosmos DB en Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2016
ms.author: chrande; glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2c0cb8ee1690f9b36b76c87247e3c7223876b269
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="azure-functions-cosmos-db-bindings"></a>Enlaces de Cosmos DB en Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y programar enlaces de Azure Cosmos DB en Azure Functions. Azure Functions admite enlaces de entrada y salida para Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Para más información sobre Cosmos DB, consulte [Introducción a Cosmos DB](../documentdb/documentdb-introduction.md) y [Compilación de una aplicación de consola de Cosmos DB](../documentdb/documentdb-get-started.md).

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>Enlace de entrada de API de DocumentDB
El enlace de entrada de API de DocumentDB recupera un documento de Cosmos DB y lo pasa al parámetro de entrada con nombre de la función. Se puede determinar el identificador de documento según el desencadenador que invoca la función. 

El enlace de entrada de API de DocumentDB tiene las siguientes propiedades en *function.json*:

- `name`: nombre del identificador que se usa en el código de la función para el documento
- `type`: se debe establecer en "documentdb"
- `databaseName`: base de datos que contiene el documento
- `collectionName`: colección que contiene el documento
- `id` : el identificador del documento que se debe recuperar. Esta propiedad admite parámetros de enlaces; vea [Bind to custom input properties in a binding expression](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression) (Enlace a propiedades de entrada personalizadas en una expresión de enlace) en el artículo [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md) (Conceptos básicos sobre los enlaces y desencadenadores de Azure Functions).
- `sqlQuery`: consulta SQL de Cosmos DB que se usa para recuperar varios documentos. La consulta admite enlaces en tiempo de ejecución. Por ejemplo: `SELECT * FROM c where c.departmentId = {departmentId}`
- `connection`: nombre de la configuración de aplicación que contiene la cadena de conexión de Cosmos DB
- `direction`: se debe establecer en `"in"`.

No se pueden establecer las propiedades `id` y `sqlQuery` al mismo tiempo. Si no están establecidas `id` ni `sqlQuery`, se recupera toda la colección.

## <a name="using-a-documentdb-api-input-binding"></a>Uso de un enlace de entrada de API de DocumentDB

* En las funciones de C# y F#, cuando se sale de la función correctamente, los cambios realizados en el documento de entrada mediante parámetros de entrada con nombre se guardan automáticamente. 
* En las funciones de JavaScript, las actualizaciones no se realizan automáticamente al cerrar la función. Por el contrario, use `context.bindings.<documentName>In` y `context.bindings.<documentName>Out` para realizar las actualizaciones. Vea el [ejemplo de JavaScript](#injavascript).

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>Ejemplo de entrada de documento único
Suponga que tiene el siguiente enlace de entrada de API de DocumentDB en la matriz `bindings` de function.json:

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_COSMOSDB",     
  "direction": "in"
}
```

Vea el ejemplo específico del idioma que utiliza este enlace de entrada para actualizar el valor de texto del documento.

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Ejemplo de entrada en C# #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Ejemplo de entrada en F# #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

Este ejemplo necesita un archivo `project.json` que especifique las dependencias de NuGet `FSharp.Interop.Dynamic` y `Dynamitey`:

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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>Ejemplo de entrada en JavaScript

```javascript
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>Ejemplo de entrada con varios documentos

Imagine que quiere recuperar varios documentos especificados por una consulta SQL con un desencadenador de cola para personalizar los parámetros de la consulta. 

En este ejemplo, el desencadenador de cola proporciona un parámetro `departmentId`. Un mensaje de cola de `{ "departmentId" : "Finance" }` devolvería todos los registros del departamento de finanzas. Use lo siguiente en *function.json*:

```
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}"
    "connection": "CosmosDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>Ejemplo de entrada con varios documentos en C#

```csharp
public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
{   
    foreach (var doc in documents)
    {
        // operate on each document
    }    
}

public class QueuePayload
{
    public string departmentId { get; set; }
}
```

### <a name="input-sample-with-multiple-documents-in-javascript"></a>Ejemplo de entrada con varios documentos en JavaScript

```javascript
module.exports = function (context, input) {    
    var documents = context.bindings.documents;
    for (var i = 0; i < documents.length; i++) {
        var document = documents[i];
        // operate on each document
    }        
    context.done();
};
```

## <a id="docdboutput"></a>Enlace de salida de API de DocumentDB
El enlace de salida de API de DocumentDB permite escribir un nuevo documento en una base de datos de Azure Cosmos DB. Tiene las siguientes propiedades en *function.json*:

- `name`: identificador que se usa en el código de la función para el nuevo documento
- `type`: se debe establecer en `"documentdb"`
- `databaseName` : la base de datos que contiene la colección en la que se creará el nuevo documento.
- `collectionName` : la colección en la que se creará el nuevo documento.
- `createIfNotExists`: valor booleano que indica si la colección se creará si no existe. El valor predeterminado es *false*. Esto se debe a que se crean nuevas colecciones con rendimiento reservado, lo cual tiene implicaciones de precios. Para obtener más información, visite la [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: nombre de la configuración de aplicación que contiene la cadena de conexión de Cosmos DB
- `direction`: se debe establecer en `"out"`

## <a name="using-a-documentdb-api-output-binding"></a>Uso de un enlace de salida de API de DocumentDB
En esta sección se muestra cómo utilizar el enlace de salida de API de DocumentDB en el código de función.

Cuando se escribe en el parámetro de salida en la función, de forma predeterminada se genera un nuevo documento en la base de datos, con un GUID generado automáticamente como el identificador de documento. Puede especificar el identificador de documento del documento de salida mediante la especificación de la propiedad JSON `id` en el parámetro de salida. 

>[!Note]  
>Cuando especifica el identificador de un documento existente, se sobrescribe con el nuevo documento de salida. 

Para generar varios documentos, también puede enlazar a `ICollector<T>` o `IAsyncCollector<T>`, donde `T` es uno de los tipos admitidos.

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>Ejemplo de enlace de salida de API de DocumentDB
Suponga que tiene el siguiente enlace de salida de API de DocumentDB en la matriz `bindings` de function.json:

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_COSMOSDB",     
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

Y desea crear documentos de Cosmos DB en el formato siguiente para cada registro:

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
* [JavaScript](#outjavascript)

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

Este ejemplo necesita un archivo `project.json` que especifique las dependencias de NuGet `FSharp.Interop.Dynamic` y `Dynamitey`:

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>Ejemplo de salida en JavaScript

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

