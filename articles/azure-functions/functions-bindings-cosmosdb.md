---
title: Enlaces de Azure Cosmos DB para Functions
description: "Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Azure Cosmos DB en Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: ab55281b6adcc8867f207e6887c88a26c1a8616b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-cosmos-db-bindings-for-azure-functions"></a>Enlaces de Azure Cosmos DB para Azure Functions

En este artículo se explica cómo trabajar con enlaces de [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) en Azure Functions. Azure Functions enlaces de desencadenador, de entrada y de salida para Azure Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Desencadenador

El desencadenador de Azure Cosmos DB utiliza la [fuente de cambios de Azure Cosmos DB](../cosmos-db/change-feed.md) para estar atento a los cambios realizados en las particiones. La fuente de cambios publica inserciones y actualizaciones, no eliminaciones. 

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Desencadenador: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que se desencadena a partir de una base de datos y una colección específicas.

```cs
    using System.Collections.Generic;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
            log.Info("Documents modified " + documents.Count);
            log.Info("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de Cosmos DB en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función escribe mensajes de registro cuando se modifican los registros de Cosmos DB.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de script de C#:
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de Cosmos DB en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función escribe mensajes de registro cuando se modifican los registros de Cosmos DB.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>Desencadenador: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/Trigger/CosmosDBTriggerAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

El constructor del atributo toma el nombre de la base de datos y el nombre de la colección. Para información sobre esos valores y otras propiedades que puede configurar, consulte [Desencadenador: configuración](#trigger---configuration). A continuación, se muestra un ejemplo del atributo `CosmosDBTrigger` en una signatura de método:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Para obtener un ejemplo completo, consulte [Desencadenador: ejemplo de C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `CosmosDBTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** || Se debe establecer en `cosmosDBTrigger`. |
|**dirección** || Se debe establecer en `in`. Este parámetro se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** || Nombre de la variable que se utiliza en el código de función y que representa la lista de documentos con los cambios. | 
|**connectionStringSetting**|**ConnectionStringSetting** | Nombre de una configuración de aplicación que contiene la cadena de conexión utilizada para conectarse a la cuenta de Azure Cosmos DB que se está supervisando. |
|**databaseName**|**DatabaseName**  | Nombre de la base de datos de Azure Cosmos DB con la colección que se está supervisando. |
|**collectionName** |**CollectionName** | Nombre de la colección que se está supervisando. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcional) Nombre de una configuración de aplicación que contiene la cadena de conexión al servicio que incluye la colección de concesiones. Si no se establece, se usa el valor `connectionStringSetting`. Este parámetro se establece automáticamente cuando se crea el enlace en el portal. La cadena de conexión para la colección de concesiones debe tener permisos de escritura.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcional) Nombre de la base de datos que contiene la colección que se usa para almacenar las concesiones. Si no se establece, se usa el valor de la configuración `databaseName`. Este parámetro se establece automáticamente cuando se crea el enlace en el portal. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcional) Nombre de la colección que se usa para almacenar concesiones. Si no se establece, se usa el valor `leases`. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcional) Cuando se establece en `true`, la colección de concesiones se crea automáticamente cuando todavía no existe. El valor predeterminado es `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Opcional) Define la cantidad de unidades de solicitud que se asignan cuando se crea la colección de concesiones. Esta configuración solo se usa cuando `createLeaseCollectionIfNotExists` se establece en `true`. Este parámetro se establece automáticamente cuando el enlace se crea con el portal.
| |**LeaseOptions** | Para configurar las opciones de concesión, establezca las propiedades de una instancia de la clase [ChangeFeedHostOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.changefeedprocessor.changefeedhostoptions).

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Desencadenador: uso

El desencadenador requiere una segunda colección que utiliza para almacenar _concesiones_ en las particiones. La colección que se está supervisando y la colección que contiene las concesiones deben estar disponibles para que el desencadenador funcione.

 >[!IMPORTANT]
 > Si varias funciones están configuradas para usar un desencadenador de Cosmos DB para la misma colección, cada una de las funciones debe usar una colección de concesión dedicada. En caso contrario, se desencadenará solo una de las funciones. 

El desencadenador no indica si un documento se actualizó o se insertó; solo proporciona el propio documento. Si tiene que administrar las actualizaciones e inserciones de manera diferente, puede hacerlo mediante la implementación de campos de marca de tiempo para las inserciones o actualizaciones.

## <a name="input"></a>Entrada

El enlace de entrada de Azure Cosmos DB recupera uno o varios documentos de Azure Cosmos DB y los pasa al parámetro de entrada de la función. Se puede determinar el identificador de documento o los parámetros de consulta según el desencadenador que invoca la función. 

>[!NOTE]
> No use enlaces de entrada o salida de Azure Cosmos DB si usa MongoDB API en una cuenta de Cosmos DB. Es posible que se dañen los datos.

## <a name="input---example-1"></a>Entrada: ejemplo 1

Consulte el ejemplo específico del idioma que lee un solo documento:

* [C#](#input---c-example)
* [Script de C# (.csx)](#input---c-script-example)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Entrada: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que recupera un único documento a partir de una base de datos y una colección específicas. 

En primer lugar, los valores `Id` y `Maker` para una instancia `CarReview` se pasan a una cola. El enlace de Cosmos DB utiliza `Id` y `Maker` desde el mensaje de la cola para recuperar el documento de la base de datos.

```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace CosmosDB
    {
        public static class SingleEntry
        {
            [FunctionName("SingleEntry")]
            public static void Run(
                [QueueTrigger("car-reviews", Connection = "StorageConnectionString")] CarReview carReview,
                [DocumentDB("cars", "car-reviews", PartitionKey = "{maker}", Id= "{id}", ConnectionStringSetting = "CarReviewsConnectionString")] CarReview document,
                TraceWriter log)
            {
                log.Info( $"Selected Review - {document?.Review}"); 
            }
        }
    }
```

Este es el objeto POCO `CarReview`:

 ```cs
    public class CarReview
    {
        public string Id { get; set; }
        public string Maker { get; set; }
        public string Description { get; set; }
        public string Model { get; set; }
        public string Image { get; set; }
        public string Review { get; set; }
    }
 ```

### <a name="input---c-script-example"></a>Entrada: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de entrada de Cosmos DB en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función lee un solo documento y actualiza el valor de texto del documento.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de script de C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

<a name="infsharp"></a>

### <a name="input---f-example"></a>Entrada: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de entrada de Cosmos DB en un archivo *function.json* y una [función de C#](functions-reference-fsharp.md) que usa el enlace. La función lee un solo documento y actualiza el valor de texto del documento.

Estos son los datos de enlace del archivo *function.json*:

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

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de F#:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Este ejemplo necesita un archivo `project.json` que especifique las dependencias `FSharp.Interop.Dynamic` y `Dynamitey` de NuGet:

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

### <a name="input---javascript-example"></a>Entrada: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de entrada de Cosmos DB en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función lee un solo documento y actualiza el valor de texto del documento.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

## <a name="input---example-2"></a>Entrada: ejemplo 2

Consulte el ejemplo específico del idioma que lee varios documentos:

* [C#](#input---c-example-2)
* [Script de C# (.csx)](#input---c-script-example-2)
* [JavaScript](#input---javascript-example-2)

### <a name="input---c-example-2"></a>Entrada: ejemplo 2 de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que ejecuta una consulta SQL. Para usar el parámetro `SqlQuery`, debe instalar la versión beta más reciente del paquete NuGet `Microsoft.Azure.WebJobs.Extensions.DocumentDB`.

```csharp
    using System.Net;
    using System.Net.Http;
    using System.Collections.Generic;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;

    [FunctionName("CosmosDBSample")]
    public static HttpResponseMessage Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequestMessage req,
        [DocumentDB("test", "test", ConnectionStringSetting = "CosmosDB", SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")] IEnumerable<object> documents)
    {
        return req.CreateResponse(HttpStatusCode.OK, documents);
    }
```

### <a name="input---c-script-example-2"></a>Entrada: ejemplo 2 de script de C#

En el ejemplo siguiente se muestra un enlace de entrada de Azure Cosmos DB en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función recupera varios documentos especificados por una consulta SQL con un desencadenador de cola para personalizar los parámetros de la consulta.

El desencadenador de cola proporciona un parámetro `departmentId`. Un mensaje de la cola de `{ "departmentId" : "Finance" }` devolvería todos los registros para el departamento de finanzas. 

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de script de C#:

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

### <a name="input---javascript-example-2"></a>Entrada: ejemplo de JavaScript 2

En el ejemplo siguiente se muestra un enlace de entrada de Azure Cosmos DB en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función recupera varios documentos especificados por una consulta SQL con un desencadenador de cola para personalizar los parámetros de la consulta.

El desencadenador de cola proporciona un parámetro `departmentId`. Un mensaje de la cola de `{ "departmentId" : "Finance" }` devolvería todos los registros para el departamento de finanzas. 

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de JavaScript:

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

## <a name="input---attributes"></a>Entrada: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

El constructor del atributo toma el nombre de la base de datos y el nombre de la colección. Para información sobre esos valores y otras propiedades que puede configurar, consulte [la sección de configuración siguiente](#input---configuration). 

## <a name="input---configuration"></a>Entrada: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que establece en el archivo *function.json* y el atributo `DocumentDB`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type**     || Se debe establecer en `documentdb`.        |
|**dirección**     || Se debe establecer en `in`.         |
|**name**     || Nombre del parámetro de enlace que representa al documento en la función.  |
|**databaseName** |**DatabaseName** |Base de datos que contiene el documento.        |
|**collectionName** |**CollectionName** | Nombre de la colección que contiene el documento. |
|**id**    | **Id** | Identificador del documento que se va a recuperar. Esta propiedad admite parámetros de enlaces. Para más información, vea [Enlace a propiedades de entrada personalizadas en una expresión de enlace](functions-triggers-bindings.md#bind-to-custom-input-properties). No establezca las propiedades **id** y **sqlQuery** a la vez. Si no establece alguna de ellas, se recupera toda la colección. |
|**sqlQuery**  |**SqlQuery**  | Consulta SQL de Azure Cosmos DB que se usa para recuperar varios documentos. La propiedad es compatible con los enlaces en tiempo de ejecución, como en este ejemplo: `SELECT * FROM c where c.departmentId = {departmentId}`. No establezca las propiedades **id** y **sqlQuery** a la vez. Si no establece alguna de ellas, se recupera toda la colección.|
|**conexión**     |**ConnectionStringSetting**|Nombre de la configuración de aplicación que contiene la cadena de conexión de Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Especifica el valor de la clave de partición para la búsqueda. Puede incluir parámetros de enlace.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Uso de entradas

En las funciones de C# y F#, cuando se sale de la función correctamente, los cambios realizados en el documento de entrada mediante parámetros de entrada con nombre se guardan automáticamente. 

En las funciones de JavaScript, las actualizaciones no se realizan automáticamente al cerrar la función. Por el contrario, use `context.bindings.<documentName>In` y `context.bindings.<documentName>Out` para realizar las actualizaciones. Consulte el [ejemplo de JavaScript](#input---javascript-example).

## <a name="output"></a>Salida

El enlace de salida de Azure Cosmos DB permite escribir un nuevo documento en una base de datos de Azure Cosmos DB. 

>[!NOTE]
> No use enlaces de entrada o salida de Azure Cosmos DB si usa MongoDB API en una cuenta de Cosmos DB. Es posible que se dañen los datos.

## <a name="output---example"></a>Salida: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#output---c-example)
* [Script de C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Salida: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que agrega un documento a una base de datos mediante los datos que se proporcionan en el mensaje desde Queue Storage.

```cs
    using System;
    using Microsoft.Azure.WebJobs;

    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        document = new { Text = myQueueItem, id = Guid.NewGuid() };
    }
```

### <a name="output---c-script-example"></a>Salida: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de salida de Azure Cosmos DB en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función usa un enlace de entrada de cola para una cola que recibe JSON en el formato siguiente:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La función crea documentos de Azure Cosmos DB en el formato siguiente para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Estos son los datos de enlace del archivo *function.json*:

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

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de script de C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
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

Para crear varios documentos, puede enlazar a `ICollector<T>` o `IAsyncCollector<T>`, donde `T` es uno de los tipos admitidos.

### <a name="output---f-example"></a>Salida: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de salida de Azure Cosmos DB en un archivo *function.json* y una [función de F#](functions-reference-fsharp.md) que usa el enlace. La función usa un enlace de entrada de cola para una cola que recibe JSON en el formato siguiente:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La función crea documentos de Azure Cosmos DB en el formato siguiente para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Estos son los datos de enlace del archivo *function.json*:

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
En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de F#:

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

Este ejemplo necesita un archivo `project.json` que especifique las dependencias `FSharp.Interop.Dynamic` y `Dynamitey` de NuGet:

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

### <a name="output---javascript-example"></a>Salida: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de salida de Azure Cosmos DB en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función usa un enlace de entrada de cola para una cola que recibe JSON en el formato siguiente:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La función crea documentos de Azure Cosmos DB en el formato siguiente para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Estos son los datos de enlace del archivo *function.json*:

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

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de JavaScript:

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

## <a name="output---attributes"></a>Salida: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

El constructor del atributo toma el nombre de la base de datos y el nombre de la colección. Para información sobre esos valores y otras propiedades que puede configurar, consulte [Salida: configuración](#output---configuration). A continuación, se muestra un ejemplo del atributo `DocumentDB` en una signatura de método:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Para obtener un ejemplo completo, consulte [Salida: ejemplo de C#](#output---c-example).

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `DocumentDB`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type**     || Se debe establecer en `documentdb`.        |
|**dirección**     || Se debe establecer en `out`.         |
|**name**     || Nombre del parámetro de enlace que representa al documento en la función.  |
|**databaseName** | **DatabaseName**|Base de datos que contiene la colección en la que se ha creado el documento.     |
|**collectionName** |**CollectionName**  | Nombre de la colección en la que se ha creado el documento. |
|**createIfNotExists**  |**CreateIfNotExists**    | Valor booleano que indica si la colección se ha creado si no existía. El valor predeterminado es *false* porque las colecciones nuevas se crean con rendimiento reservado, lo que afecta el costo. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/documentdb/)para obtener más información.  |
|**partitionKey**|**PartitionKey** |Cuando el valor de `CreateIfNotExists` es true, define la ruta de la clave de partición para la colección que se creó.|
|**collectionThroughput**|**CollectionThroughput**| Cuando el valor de `CreateIfNotExists` es true, define el [rendimiento](../cosmos-db/set-throughput.md) de la colección que se creó.|
|**conexión**    |**ConnectionStringSetting** |Nombre de la configuración de aplicación que contiene la cadena de conexión de Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uso de salidas

De forma predeterminada, cuando se escribe en el parámetro de salida de la función, se crea un documento en la base de datos. Este documento tiene un GUID generado automáticamente como identificador de documento. Puede especificar el identificador de documento del documento de salida si especifica la propiedad `id` en el objeto JSON pasado al parámetro de salida. 

> [!Note]  
> Cuando especifica el identificador de un documento existente, se sobrescribe con el nuevo documento de salida. 

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Ir a una guía de inicio rápido que use un desencadenador de Cosmos DB](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Más información sobre la informática de base de datos sin servidor con Cosmos DB](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
