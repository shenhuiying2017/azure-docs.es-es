---
title: Enlaces de Cosmos DB para Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Azure Cosmos DB en Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.openlocfilehash: ad058929eb888920823fddf549ada4ce2c6d9eee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-bindings-for-functions"></a>Enlaces de Azure Cosmos DB para Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y programar enlaces de Azure Cosmos DB en Azure Functions. Functions admite desencadenadores y enlaces de entrada y salida para Azure Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Para obtener más información acerca de la informática sin servidor con Azure Cosmos DB, consulte [Azure Cosmos DB: informática de base de datos sin servidor con Azure Functions](..\cosmos-db\serverless-computing-database.md).

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Desencadenador de Azure Cosmos DB

El desencadenador de Azure Cosmos DB utiliza la [fuente de cambios de Azure Cosmos DB](../cosmos-db/change-feed.md) para estar atento a los cambios realizados en las particiones. El desencadenador requiere una segunda colección que utiliza para almacenar _concesiones_ en las particiones.

La colección que se está supervisando y la colección que contiene las concesiones deben estar disponibles para que el desencadenador funcione.

El desencadenador de Azure Cosmos DB admite las siguientes propiedades:

|Propiedad  |Descripción  |
|---------|---------|
|**type** | Se debe establecer en `cosmosDBTrigger`. |
|**name** | Nombre de la variable que se utiliza en el código de función y que representa la lista de documentos con los cambios. | 
|**dirección** | Se debe establecer en `in`. Este parámetro se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**connectionStringSetting** | Nombre de una configuración de aplicación que contiene la cadena de conexión utilizada para conectarse a la cuenta de Azure Cosmos DB que se está supervisando. |
|**databaseName** | Nombre de la base de datos de Azure Cosmos DB con la colección que se está supervisando. |
|**collectionName** | Nombre de la colección que se está supervisando. |
| **leaseConnectionStringSetting** | (Opcional) Nombre de una configuración de aplicación que contiene la cadena de conexión al servicio que incluye la colección de concesiones. Si no se establece, se usa el valor `connectionStringSetting`. Este parámetro se establece automáticamente cuando se crea el enlace en el portal. |
| **leaseDatabaseName** | (Opcional) Nombre de la base de datos que contiene la colección que se usa para almacenar las concesiones. Si no se establece, se usa el valor de la configuración `databaseName`. Este parámetro se establece automáticamente cuando se crea el enlace en el portal. |
| **leaseCollectionName** | (Opcional) Nombre de la colección que se usa para almacenar concesiones. Si no se establece, se usa el valor `leases`. |
| **createLeaseCollectionIfNotExists** | (Opcional) Cuando se establece en `true`, la colección de concesiones se crea automáticamente cuando todavía no existe. El valor predeterminado es `false`. |
| **leaseCollectionThroughput** | (Opcional) Define la cantidad de unidades de solicitud que se asignan cuando se crea la colección de concesiones. Esta configuración solo se usa cuando `createLeaseCollectionIfNotExists` se establece en `true`. Este parámetro se establece automáticamente cuando el enlace se crea con el portal.

>[!NOTE] 
>La cadena de conexión que se use para conectarse a la colección de concesiones debe tener permisos de escritura.

Estas propiedades se pueden establecer en la pestaña Integración para la función en el Azure Portal o mediante la edición del archivo del proyecto `function.json`.

## <a name="using-an-azure-cosmos-db-trigger"></a>Uso de un desencadenador de Azure Cosmos DB

Esta sección contiene ejemplos de cómo usar el desencadenador de Azure Cosmos DB. En los ejemplos se supone que los metadatos de un desencadenador tienen el aspecto siguiente:

```json
{
  "type": "cosmosDBTrigger",
  "name": "documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "<connection-app-setting>",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```
 
Para obtener un ejemplo de cómo crear un desencadenador de Azure Cosmos DB desde una aplicación de función en el portal, consulte [Create a function triggered by Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md) (Crear una función desencadenada por Azure Cosmos DB). 

### <a name="trigger-sample-in-c"></a>Ejemplo de desencadenador en C# #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>Ejemplo de desencadenador en JavaScript
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>Enlace de entrada de API de DocumentDB
El enlace de entrada de API de DocumentDB recupera un documento de Azure Cosmos DB y lo pasa al parámetro de entrada con nombre de la función. Se puede determinar el identificador de documento según el desencadenador que invoca la función. 

El enlace de entrada de API de DocumentDB tiene las siguientes propiedades en *function.json*:

|Propiedad  |Descripción  |
|---------|---------|
|**name**     | Nombre del parámetro de enlace que representa al documento en la función.  |
|**type**     | Se debe establecer en `documentdb`.        |
|**databaseName** | Base de datos que contiene el documento.        |
|**collectionName**  | Nombre de la colección que contiene el documento. |
|**id**     | Identificador del documento que se va a recuperar. Esta propiedad admite parámetros de enlaces. Para más información, vea [Enlace a propiedades de entrada personalizadas en una expresión de enlace](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression). |
|**sqlQuery**     | Consulta SQL de Azure Cosmos DB que se usa para recuperar varios documentos. La consulta admite enlaces en tiempo de ejecución, como en el ejemplo: `SELECT * FROM c where c.departmentId = {departmentId}`.        |
|**conexión**     |Nombre de la configuración de aplicación que contiene la cadena de conexión de Azure Cosmos DB.        |
|**dirección**     | Se debe establecer en `in`.         |

No se pueden establecer las propiedades **id** y **sqlQuery** a la vez. Si ninguna está establecida, se recupera toda la colección.

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

|Propiedad  |Descripción  |
|---------|---------|
|**name**     | Nombre del parámetro de enlace que representa al documento en la función.  |
|**type**     | Se debe establecer en `documentdb`.        |
|**databaseName** | Base de datos que contiene la colección en la que se ha creado el documento.     |
|**collectionName**  | Nombre de la colección en la que se ha creado el documento. |
|**createIfNotExists**     | Valor booleano que indica si la colección se ha creado si no existía. El valor predeterminado es *false*. Esto se debe a que las nuevas colecciones se crean con rendimiento reservado, lo cual afecta al costo. Para obtener más información, visite la [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**conexión**     |Nombre de la configuración de aplicación que contiene la cadena de conexión de Azure Cosmos DB.        |
|**dirección**     | Se debe establecer en `out`.         |

## <a name="using-a-documentdb-api-output-binding"></a>Uso de un enlace de salida de API de DocumentDB
En esta sección se muestra cómo utilizar el enlace de salida de API de DocumentDB en el código de función.

De forma predeterminada, cuando se escribe en el parámetro de salida de la función, se crea un documento en la base de datos. Este documento tiene un GUID generado automáticamente como identificador de documento. Puede especificar el identificador de documento del documento de salida si especifica la propiedad `id` en el objeto JSON pasado al parámetro de salida. 

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

Y quiere crear documentos de Azure Cosmos DB en el formato siguiente para cada registro:

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
