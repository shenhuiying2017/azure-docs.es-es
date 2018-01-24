---
title: Enlaces de Azure Table Storage para Azure Functions
description: "Descubra cómo usar los enlaces de Azure Table Storage en Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: 5cfb968b201f49d5b7029a0b677e3ce2a8aa6cb9
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Enlaces de Azure Table Storage para Azure Functions

En este artículo se explica cómo trabajar con enlaces de Azure Table Storage en Azure Functions. Azure Functions admite enlaces de entrada y salida para Table Storage de Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="input"></a>Entrada

Use el enlace de entrada de Azure Table Storage para leer una tabla de una cuenta de Azure Storage.

## <a name="input---example"></a>Ejemplo de entrada

Vea el ejemplo específico del lenguaje:

* [C#: leer una entidad](#input---c-example-1)
* [C#: leer varias entidades](#input---c-example-2)
* [Script de C#: leer una entidad](#input---c-script-example-1)
* [Script de C#: leer varias entidades](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Entrada: ejemplo 1 de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que lee una única fila de la tabla. 

El valor "{queueTrigger}" de clave de fila indica que la clave de fila procede de la cadena del mensaje en la cola.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
    }
}
```

### <a name="input---c-example-2"></a>Entrada: ejemplo 2 de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que lee varias filas de la tabla. Tenga en cuenta que la clase `MyPoco` deriva de `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
        }
    }
}
```

### <a name="input---c-script-example-1"></a>Entrada: ejemplo 1 de script de C#

En el ejemplo siguiente se muestra un enlace de entrada de la tabla en un archivo *function.json* y código de [script de C#](functions-reference-csharp.md) que usa el enlace. La función usa un desencadenador de cola para leer una fila de tabla única. 

El archivo *function.json* especifica un valor `partitionKey` y un valor `rowKey`. El valor `rowKey` "{queueTrigger}" de clave de fila indica que la clave de fila procede de la cadena del mensaje en la cola.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
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
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de script de C#:

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

### <a name="input---c-script-example-2"></a>Entrada: ejemplo 2 de script de C#

En el ejemplo siguiente se muestra un enlace de entrada de la tabla en un archivo *function.json* y código de [script de C#](functions-reference-csharp.md) que usa el enlace. La función lee las entidades de una clave de partición que se especifica en un mensaje en la cola.

Este es el archivo *function.json*:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#input---configuration) se explican estas propiedades.

El código de script de C# agrega una referencia al SDK de Azure Storage con el fin de que el tipo de entidad pueda derivar de `TableEntity`:

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

### <a name="input---f-example"></a>Entrada: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de entrada de la tabla en un archivo *function.json* y código de [script de F#](functions-reference-fsharp.md) que usa el enlace. La función usa un desencadenador de cola para leer una fila de tabla única. 

El archivo *function.json* especifica un valor `partitionKey` y un valor `rowKey`. El valor `rowKey` "{queueTrigger}" de clave de fila indica que la clave de fila procede de la cadena del mensaje en la cola.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
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
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de F#:

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

### <a name="input---javascript-example"></a>Entrada: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de entrada de la tabla en un archivo *function.json* y [código JavaScript] (functions-reference-node.md) que usa el enlace. La función usa un desencadenador de cola para leer una fila de tabla única. 

El archivo *function.json* especifica un valor `partitionKey` y un valor `rowKey`. El valor `rowKey` "{queueTrigger}" de clave de fila indica que la clave de fila procede de la cadena del mensaje en la cola.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
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
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes"></a>Entrada: atributos
 
Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), utilice los siguientes atributos para configurar un enlace de entrada de la tabla:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), definido en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  El constructor del atributo toma el nombre de la tabla, la clave de partición y la clave de fila. Se puede usar en un parámetro de salida o en el valor devuelto de la función, como se muestra en el ejemplo siguiente:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Para obtener un ejemplo completo, consulte [Entrada: ejemplo de C#](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), definido en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Proporciona otra manera de especificar la cuenta de almacenamiento que se debe usar. El constructor toma el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. El atributo se puede aplicar en el nivel de clase, método o parámetro. En el ejemplo siguiente se muestran el nivel de clase y de método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

La cuenta de almacenamiento que se debe usar se determina en el orden siguiente:

* La propiedad `Connection` del atributo `Table`.
* El atributo `StorageAccount` aplicado al mismo parámetro que el atributo `Table`.
* El atributo `StorageAccount` aplicado a la función.
* El atributo `StorageAccount` aplicado a la clase.
* La cuenta de almacenamiento predeterminada de la aplicación de función (configuración de aplicación de "AzureWebJobsStorage").

## <a name="input---configuration"></a>Entrada: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que establece en el archivo *function.json* y el atributo `Table`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `table`. Esta propiedad se establece automáticamente cuando se crea el enlace en Azure Portal.|
|**dirección** | N/D | Se debe establecer en `in`. Esta propiedad se establece automáticamente cuando se crea el enlace en Azure Portal. |
|**name** | N/D | Nombre de la variable que representa la tabla o la entidad en el código de la función. | 
|**tableName** | **TableName** | El nombre de la tabla.| 
|**partitionKey** | **PartitionKey** |Opcional. Clave de partición de la entidad de tabla que se va a leer. Consulte la sección acerca del [uso](#input---usage) para obtener información acerca de cómo usar esta propiedad.| 
|**rowKey** |**RowKey** | Opcional. Clave de fila de la entidad de tabla que se va a leer. Consulte la sección acerca del [uso](#input---usage) para obtener información acerca de cómo usar esta propiedad.| 
|**take** |**Take** | Opcional. Número máximo de entidades que se van a leer en JavaScript. Consulte la sección acerca del [uso](#input---usage) para obtener información acerca de cómo usar esta propiedad.| 
|**filter** |**Filter** | Opcional. Expresión de filtro de OData en la entrada de la tabla en JavaScript. Consulte la sección acerca del [uso](#input---usage) para obtener información acerca de cómo usar esta propiedad.| 
|**conexión** |**Connection** | El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions usa la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Uso de entradas

El enlace de entrada de Table Storage admite los siguientes escenarios:

* **Leer una fila en C# o en script de C#**

  Establezca `partitionKey` y `rowKey`. Obtenga acceso a los datos de la tabla mediante un parámetro de método `T <paramName>`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. `T` suele ser un tipo que implementa `ITableEntity` o deriva de `TableEntity`. Las propiedades `filter` y `take` no se utilizan en este escenario. 

* **Leer una o varias filas en C# o en script de C#**

  Obtenga acceso a los datos de la tabla mediante un parámetro de método `IQueryable<T> <paramName>`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. `T` debe ser un tipo que implementa `ITableEntity` o deriva de `TableEntity`. Puede usar métodos `IQueryable` para realizar cualquier operación de filtrado necesaria. Las propiedades `partitionKey`, `rowKey`, `filter` y `take` no se utilizan en este escenario.  

> [!NOTE]
> `IQueryable` no funciona en .NET Core, por lo que no funciona en el [tiempo de ejecución de Functions v2](functions-versions.md).

  Una alternativa consiste en usar un parámetro del método `CloudTable paramName` para leer la tabla mediante Azure Storage SDK.

* **Leer una o varias filas en JavaScript**

  Establezca las propiedades `filter` y `take`. No establezca `partitionKey` o `rowKey`. Obtenga acceso a la entidad (o entidades) de la tabla de entradas mediante `context.bindings.<name>`. Los objetos deserializados tienen las propiedades `RowKey` y `PartitionKey`.

## <a name="output"></a>Salida

Use un enlace de salida de Azure Table Storage para escribir entidades en una tabla de una cuenta de Azure Storage.

## <a name="output---example"></a>Salida: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#output---c-example)
* [Script de C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Salida: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que usa un desencadenador HTTP para escribir una única fila de la tabla. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Salida: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de salida de la tabla en un archivo *function.json* y código de [script de C#](functions-reference-csharp.md) que usa el enlace. La función escribe varias entidades de tabla.

Este es el archivo *function.json*:

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de script de C#:

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

### <a name="output---f-example"></a>Salida: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de salida de la tabla en un archivo *function.json* y código de [script de F#](functions-reference-fsharp.md) que usa el enlace. La función escribe varias entidades de tabla.

Este es el archivo *function.json*:

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de F#:

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

### <a name="output---javascript-example"></a>Salida: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de salida de la tabla en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función escribe varias entidades de tabla.

Este es el archivo *function.json*:

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de JavaScript:

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

## <a name="output---attributes"></a>Salida: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), utilice [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

El constructor del atributo toma el nombre de la tabla. Se puede usar en un parámetro `out` o en el valor devuelto de la función, como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Para obtener un ejemplo completo, consulte [Salida: ejemplo de C#](#output---c-example).

Puede usar el atributo `StorageAccount` para especificar la cuenta de almacenamiento en el nivel de clase, método o parámetro. Para obtener más información, consulte [Entrada: atributos](#input---attributes).

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Table`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `table`. Esta propiedad se establece automáticamente cuando se crea el enlace en Azure Portal.|
|**dirección** | N/D | Se debe establecer en `out`. Esta propiedad se establece automáticamente cuando se crea el enlace en Azure Portal. |
|**name** | N/D | Nombre de la variable que se usa en el código de la función que representa la tabla o entidad. Se establece en `$return` para hacer referencia al valor devuelto de la función.| 
|**tableName** |**TableName** | El nombre de la tabla.| 
|**partitionKey** |**PartitionKey** | Clave de partición de la entidad de tabla que se va a escribir. Consulte la [sección acerca del uso](#output---usage) para obtener información acerca de cómo usar esta propiedad.| 
|**rowKey** |**RowKey** | Clave de fila de la entidad de tabla que se va a escribir. Consulte la [sección acerca del uso](#output---usage) para obtener información acerca de cómo usar esta propiedad.| 
|**conexión** |**Connection** | El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions usa la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uso de salidas

El enlace de salida de Table Storage admite los siguientes escenarios:

* **Escribir una fila en cualquier lenguaje**

  En C# y script de C#, obtenga acceso a la entidad de tabla de salida con un parámetro de método como `out T paramName` o el valor devuelto de la función. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. `T` puede ser cualquier tipo serializable si las claves de partición y fila se proporcionan con el archivo *function.json* o el atributo `Table`. En caso contrario, `T` debe ser un tipo que incluya las propiedades `PartitionKey` y `RowKey`. En este escenario, `T` implementa normalmente `ITableEntity` o deriva de `TableEntity`, pero no es necesario.

* **Escribir una o varias filas en C# o en script de C#**

  En C# y script de C#, obtenga acceso a la entidad de tabla de salida con un parámetro `ICollector<T> paramName` o `ICollectorAsync<T> paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. `T` especifica el esquema de las entidades que quiere agregar. Normalmente, `T` deriva de `TableEntity` o implementa `ITableEntity`, pero no tiene por qué ser así. Los valores de las claves de partición y de fila de *function.json* o del constructor del atributo `Table` no se usan en este escenario.

  Una alternativa consiste en usar un parámetro del método `CloudTable paramName` para escribir en la tabla mediante Azure Storage SDK.

* **Escribir una o varias filas en JavaScript**

  En las funciones de JavaScript, obtenga acceso a la salida de tabla mediante `context.bindings.<name>`.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
