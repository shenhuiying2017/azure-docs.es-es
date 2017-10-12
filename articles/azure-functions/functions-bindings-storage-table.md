---
title: Enlaces de tablas de Storage en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar los enlaces de Azure Storage en Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
ms.openlocfilehash: 486b7c31c914ba7bb2d75e3f83ccf346a09104e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-storage-table-bindings"></a>Enlaces de tablas de Storage en Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo, se explica cómo configurar y codificar enlaces de tablas de Azure Storage en Azure Functions. Azure Functions admite enlaces de entrada y salida para tablas de Azure Storage.

El enlace de tablas de Azure Storage admite los siguientes escenarios:

* **Lectura de una sola fila de una función de C# o Node.js**. Establezca `partitionKey` y `rowKey`. Las propiedades `filter` y `take` no se utilizan en este escenario.
* **Lectura de varias filas de una función de C#**: el tiempo de ejecución de Functions proporciona un objeto `IQueryable<T>` enlazado a la tabla. El tipo `T` debe derivar de `TableEntity` o implementar `ITableEntity`. Las propiedades `partitionKey`, `rowKey`, `filter` y `take` no se utilizan en este escenario; puede utilizar el objeto `IQueryable` para realizar todo el filtrado requerido. 
* **Lectura de varias filas en una función de Node**. Establezca las propiedades `filter` y `take`. No establezca `partitionKey` o `rowKey`.
* **Escritura de una o más filas en una función de C#**. El tiempo de ejecución de Functions proporciona un `ICollector<T>` o `IAsyncCollector<T>` enlazado a la tabla, donde `T` especifica el esquema de las entidades que desea agregar. Normalmente, el tipo `T` deriva de `TableEntity` o implementa `ITableEntity`, pero no tiene por qué ser así. Las propiedades `partitionKey`, `rowKey`, `filter` y `take` no se utilizan en este escenario.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Enlace de entrada de tabla de Azure Storage
El enlace de entrada de tabla de Azure Storage permite utilizar una tabla de almacenamiento en su función. 

La entrada de tabla de Azure Storage a una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

Tenga en cuenta lo siguiente: 

* Use `partitionKey` y `rowKey` de forma conjunta para leer una entidad única. Estas propiedades son opcionales. 
* `connection`: debe contener el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. En Azure Portal, el editor estándar de la pestaña **Integrar** permite modificar esta configuración de aplicación cuando crea una cuenta de Azure Storage o selecciona una ya existente. También puede [configurar esta aplicación manualmente](functions-how-to-use-azure-function-app-settings.md#settings).  

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso de entradas
En las funciones de C#, enlaza con la entidad (o entidades) de la tabla de entradas mediante un parámetro con nombre en la firma de la función, como `<T> <name>`.
Donde `T` es el tipo de datos en el que desea deserializar los datos, y `paramName` es el nombre que especificó en el [enlace de entrada](#input). En las funciones de Node.js, puede acceder a la entidad (o entidades) de la tabla de entradas mediante `context.bindings.<name>`.

Los datos de entrada se pueden deserializar en funciones de Node.js o C#. Los objetos deserializados tienen las propiedades `RowKey` y `PartitionKey`.

En las funciones de C#, también puede enlazar con cualquiera de los siguientes tipos y el tiempo de ejecución de Azure Functions intentará deserializar los datos de la tabla mediante ese tipo:

* Cualquier tipo que implemente `ITableEntity`
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>Ejemplo de entrada
Suponga que tiene el siguiente function.json que usa un desencadenador de cola para leer una única fila de tabla. El archivo JSON especifica `PartitionKey` 
`RowKey`. `"rowKey": "{queueTrigger}"` indica que la clave de fila procede de la cadena del mensaje en la cola.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Consulte el ejemplo de lenguaje específico que lee una única entidad de tabla.

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Ejemplo de entrada en C# #
```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>Ejemplo de entrada en F# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Ejemplo de entrada en Node.js
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Enlace de salida de tabla de Azure Storage
El enlace de salida de tabla de Azure Storage le permite escribir entidades en una tabla de Azure Storage en su función. 

La salida de tabla de Azure Storage para una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

Tenga en cuenta lo siguiente: 

* Use `partitionKey` y `rowKey` de forma conjunta para escribir una entidad única. Estas propiedades son opcionales. También puede especificar `PartitionKey` y `RowKey` al crear los objetos de la entidad en el código de la función.
* `connection`: debe contener el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. En Azure Portal, el editor estándar de la pestaña **Integrar** permite modificar esta configuración de aplicación cuando crea una cuenta de Azure Storage o selecciona una ya existente. También puede [configurar esta aplicación manualmente](functions-how-to-use-azure-function-app-settings.md#settings). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de salidas
En las funciones de C#, puede enlazar a la salida de tabla mediante el parámetro con nombre `out` de la firma de la función, como `out <T> <name>`, donde `T` es el tipo de datos en el que desea serializar los datos y `paramName` es el nombre que especificó en el [enlace de salida](#output). En las funciones de Node.js, puede acceder a la salida de tabla mediante `context.bindings.<name>`.

Puede serializar objetos en las funciones de Node.js o C#. En las funciones de C#, también puede enlazar a los siguientes tipos:

* Cualquier tipo que implemente `ITableEntity`
* `ICollector<T>` (para la salida de varias entidades. Vea el [ejemplo](#outcsharp)).
* `IAsyncCollector<T>` (versión asincrónica de `ICollector<T>`)
* `CloudTable` (con el SDK de Azure Storage. Vea el [ejemplo](#readmulti)).

<a name="outputsample"></a>

## <a name="output-sample"></a>Ejemplo de salida
El siguiente ejemplo de *function.json* y *run.csx* muestra cómo escribir varias entidades de tabla.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Consulte el ejemplo de lenguaje específico que permite crear varias entidades de tabla.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Ejemplo de salida en C# #
```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Ejemplo de salida en F# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Ejemplo de salida en Node.js
```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>Ejemplo: lectura de varias entidades de tabla en C#  #
El siguiente ejemplo de *function.json* y de código de C# lee las entidades de una clave de partición que se especifica en el mensaje en cola.

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

El código de C# agrega una referencia al SDK de Almacenamiento de Azure con el fin de que el tipo de entidad pueda derivar de `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

