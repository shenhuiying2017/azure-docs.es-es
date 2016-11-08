---
title: Enlaces y desencadenadores de funciones de Azure para almacenamiento de Azure | Microsoft Docs
description: Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Azure en funciones de Azure.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: ''
tags: ''
keywords: funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2016
ms.author: chrande

---
# Enlaces y desencadenadores de funciones de Azure para almacenamiento de Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y codificar desencadenadores y enlaces de almacenamiento de Azure en funciones de Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="storagequeuetrigger"></a> Desencadenador de cola de Almacenamiento de Azure
#### function.json con desencadenador de cola de almacenamiento
El archivo *function.json* especifica las siguientes propiedades.

* `name`: nombre de la variable utilizado en el código de la función de la cola o el mensaje de la cola.
* `queueName`: nombre de la cola que se sondea. Para conocer las reglas de nomenclatura de colas, consulte [Asignar nombres a colas y metadatos](https://msdn.microsoft.com/library/dd179349.aspx).
* `connection`: nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el desencadenador funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
* `type`: debe establecerse en *queueTrigger*.
* `direction`: debe establecerse en *in*.

Ejemplo de *function.json* con un desencadenador de la cola de almacenamiento:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### Tipos admitidos de desencadenador de cola
El mensaje de la cola se puede deserializar en cualquiera de los siguientes tipos:

* Object (de JSON)
* String
* Byte array
* `CloudQueueMessage` (C#)

#### Metadatos de desencadenador de cola
Puede obtener metadatos de la cola en la función mediante estos nombres de variable:

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (otra manera de recuperar el texto del mensaje en cola como una cadena)

Este ejemplo de código de C# recupera y registra metadatos de la cola:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### Control de mensajes dudosos en la cola
Los mensajes cuyo contenido produce un error de una función se denominan *mensajes dudosos*. Cuando se produce un error en la función, el mensaje de la cola no se elimina y se recoge de nuevo, provocando que el ciclo se repita. El SDK puede interrumpir automáticamente el ciclo después de un número limitado de iteraciones, o puede hacerlo usted manualmente.

El SDK llamará a una función hasta 5 veces para procesar un mensaje de la cola. Si se produce un error en el quinta intento, el mensaje se mueve a una cola de mensajes dudosos.

La cola de mensajes dudosos se denomina *{originalqueuename}*-poison. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación indicando que se necesita atención manual.

Si desea gestionar manualmente los mensajes dudosos, puede conocer el número de veces que un mensaje se recogió para su procesamiento mediante la comprobación de `dequeueCount`.

## <a id="storagequeueoutput"></a> Enlace de salida de la cola de Almacenamiento de Azure
#### function.json con un enlace de salida de la cola de almacenamiento
El archivo *function.json* especifica las siguientes propiedades.

* `name`: nombre de la variable utilizado en el código de la función de la cola o el mensaje de la cola.
* `queueName`: nombre de la cola. Para conocer las reglas de nomenclatura de colas, consulte [Asignar nombres a colas y metadatos](https://msdn.microsoft.com/library/dd179349.aspx).
* `connection`: nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el desencadenador funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
* `type`: debe establecerse en *queue*.
* `direction`: debe establecerse en *out*.

Ejemplo de *function.json* con un enlace de salida de la cola de almacenamiento que usa un desencadenador de cola y escribe un mensaje de cola:

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
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Tipos admitidos de enlace de salida de la cola
El enlace `queue` puede serializar los siguientes tipos para un mensaje de cola:

* Object (`out T` en C#; crea un mensaje con un objeto nulo si el parámetro es nulo cuando termina la función)
* String (`out string` en C#; crea un mensaje de cola si el valor del parámetro es no nulo cuando termina la función)
* Byte array (`out byte[]` en C#; funciona como una cadena)
* `out CloudQueueMessage` (C#; funciona como una cadena)

En C# también puede enlazar a `ICollector<T>` o `IAsyncCollector<T>`, donde `T` es uno de los tipos admitidos.

#### Ejemplos de código de enlace de salida de cola
Este ejemplo de código de C# escribe un mensaje de cola de salida única para cada mensaje de la cola de entrada.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Este ejemplo de código de C# escribe varios mensajes mediante `ICollector<T>` (use `IAsyncCollector<T>` en una función asincrónica):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a> Desencadenador de blobs de Almacenamiento de Azure
#### function.json con desencadenador de blobs de almacenamiento
El archivo *function.json* especifica las siguientes propiedades.

* `name`: nombre de la variable utilizado en el código de la función con el blob.
* `path`: ruta de acceso que especifica el contenedor que se supervisa y, opcionalmente, un patrón de nombre de blob.
* `connection`: nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el desencadenador funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
* `type`: debe establecerse en *blobTrigger*.
* `direction`: debe establecerse en *in*.

Ejemplo de *function.json* con un desencadenador de blob de almacenamiento que inspecciona los blobs que se agregan al contenedor de elementos de trabajo de ejemplos:

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

#### Tipos admitidos de desencadenador de blobs
El blob se puede deserializar en cualquiera de las siguientes funciones de Node o C#:

* Object (de JSON)
* String

En las funciones de C# también puede enlazar a cualquiera de los siguientes tipos:

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

#### Ejemplo de código de C# de desencadenador de blobs
Este ejemplo de código de C# registra el contenido de cada blob que se agrega al contenedor supervisado.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### Patrones de nombre para desencadenadores de blobs
Puede especificar un patrón de nombre de blob en la propiedad `path`. Por ejemplo:

```json
"path": "input/original-{name}",
```

Esta ruta de acceso encontraría un blob denominado *original-Blob1.txt* en el contenedor *input* y el valor de la variable `name` en el código de la función sería `Blob1`.

Otro ejemplo:

```json
"path": "input/{blobname}.{blobextension}",
```

Esta ruta de acceso también encontraría un blob denominado *original-Blob1.txt* y el valor de las variables `blobname` y `blobextension` en el código de la función sería *original-Blob1* y *txt*.

Puede restringir los tipos de blobs que desencadenan la función especificando un patrón con un valor fijo para la extensión de archivo. Si establece la propiedad `path` en *samples/{nombre}.png*, solo los blobs *.png* del contenedor *samples* desencadenarán la función.

Si necesita especificar un patrón de nombre para nombres de blob que contienen llaves en el nombre, duplique las llaves. Por ejemplo, si desea encontrar blobs en el contenedor *images* que tengan nombres como este:

        {20140101}-soundfile.mp3

úselo para la propiedad `path`:

        images/{{20140101}}-{name}

En el ejemplo, el valor de la variable `name` sería *soundfile.mp3*.

#### Recepciones de blobs
El sistema en tiempo de ejecución de Funciones de Azure garantiza que a ninguna función de desencadenador de blobs se le llamará más de una vez para el mismo blob nuevo o actualizado. Para hacerlo, mantiene *recepciones de blobs* para determinar si se ha procesado alguna versión de blob determinada.

Las recepciones de blobs se almacenan en un contenedor llamado *azure-webjobs-hosts* en la cuenta de almacenamiento de Azure que especifica la cadena de conexión AzureWebJobsStorage. Una recepción de blobs tiene la información siguiente:

* La función que se llamó para el blob ("*{nombre de aplicación de función}*.Functions.*{nombre de función}*", por ejemplo: "functionsf74b96f7.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre del blob
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar el reprocesamiento de un blob, puede eliminar manualmente la recepción de blob de ese blob desde el contenedor *azure-webjobs-hosts*.

#### Control de blobs dudosos
Cuando una función de desencadenador de blobs genera un error, el SDK la llamará de nuevo por si se hubiese producido por un error transitorio. Si el error se produce debido al contenido del blob, la función presentará un error cada vez que intente procesar el blob. De manera predeterminada, el SDK llama una función hasta cinco veces para un blob determinado. Si el quinto intento falla, el SDK agrega un mensaje a una cola llamada *webjobs-blobtrigger-poison*.

El mensaje de cola para los blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *{nombre de aplicación de función}*.Functions.*{nombre de función}*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

#### Sondeo de blobs en contenedores grandes
Si el contenedor de blobs que el desencadenador está supervisando contiene más de 10 000 blobs, el sistema en tiempo de ejecución de Funciones examinará los archivos de registro para detectar los blobs nuevos o modificados. Este proceso no se produce en tiempo real; podrían tardarse varios minutos o más en desencadenar una función después de crear el blob. Además, los [registros de almacenamiento se crean como "el mejor esfuerzo"](https://msdn.microsoft.com/library/azure/hh343262.aspx); no hay ninguna garantía de que se capturarán todos los eventos. En algunos casos, podrían faltar registros. Si los límites de velocidad y confiabilidad de los desencadenadores de blobs para contenedores grandes no son aceptables para su aplicación, el método recomendado consiste en crear un mensaje en cola al crear el blob y usar un desencadenador de cola en lugar de un desencadenador de blobs para procesar el blob.

## <a id="storageblobbindings"></a> Enlaces de entrada y salida de blobs de Almacenamiento de Azure
#### function.json con una enlace de entrada y salida de blob de almacenamiento
El archivo *function.json* especifica las siguientes propiedades.

* `name`: nombre de la variable utilizado en el código de la función con el blob.
* `path`: ruta de acceso que especifica el contenedor del que se lee o en el que se escribe el blob y, opcionalmente, un patrón de nombre de blob.
* `connection`: nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el enlace funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
* `type`: debe establecerse en *blob*.
* `direction`: se establece en *in* u *out*.

Ejemplo de *function.json* con un enlace de entrada o salida de un blob de almacenamiento que emplea un desencadenador de cola para copiar un blob:

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

#### Tipos compatibles de entrada y salida de blobs
El enlace `blob` puede serializar o deserializar los siguientes tipos en las funciones de Node.js o C#:

* Object (`out T` en C# para blobs de salida: crea un blob como objeto nulo si el valor del parámetro es nulo cuando finaliza la función)
* String (`out string` en C# para blobs de salida: crea un blob solo si el parámetro de cadena es no nulo cuando se devuelve la función)

En las funciones de C#, también puede enlazar a los siguientes tipos:

* `TextReader` (solo de entrada)
* `TextWriter` (solo de salida)
* `Stream`
* `CloudBlobStream` (solo de salida)
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`

#### Ejemplo de código de C# de salida de blobs
Este ejemplo de código de C# copia un blob cuyo nombre se recibe en un mensaje en cola.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a> Enlaces de entrada y salida de tablas de Almacenamiento de Azure
#### function.json con tablas de almacenamiento
El archivo *function.json* especifica las siguientes propiedades.

* `name`: nombre de la variable utilizado en el código de la función para el enlace de la tabla.
* `tableName`: nombre de la tabla.
* `partitionKey` y `rowKey`: se utilizan conjuntamente para leer una entidad individual en una función de C# o de Node, o para escribir una entidad individual en una función de Node.
* `take`: número máximo de filas que se leen en la entrada de la tabla en una función de Node.
* `filter`: expresión de filtro de OData en la entrada de la tabla en una función de Node.
* `connection`: nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si `connection` se queda vacía, el enlace funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
* `type`: debe establecerse en *table*.
* `direction`: se establece en *in* u *out*.

En el ejemplo siguiente, *function.json* usa un desencadenador de cola para leer una fila de tabla única. El archivo JSON proporciona un valor de clave de partición codificado de forma rígida y especifica que la clave de fila procede del mensaje en cola.

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

#### Tipos compatibles de entrada y salida de tablas de almacenamiento
El enlace `table` puede serializar o deserializar objetos en las funciones de Node.js o C#. Los objetos tendrán las propiedades RowKey y PartitionKey.

En las funciones de C#, también puede enlazar a los siguientes tipos:

* `T` donde `T` implementa `ITableEntity`
* `IQueryable<T>` (solo de entrada)
* `ICollector<T>` (solo de salida)
* `IAsyncCollector<T>` (solo de salida)

#### Escenarios de enlace de tablas de almacenamiento
El enlace de tablas admite los siguientes escenarios:

* Lectura de una sola fila de una función de C# o Node.
  
    Establezca `partitionKey` y `rowKey`. Las propiedades `filter` y `take` no se utilizan en este escenario.
* Lectura de varias filas en una función de C#.
  
    El tiempo de ejecución de Funciones proporciona un objeto `IQueryable<T>` enlazado a la tabla. El tipo `T` debe derivar de `TableEntity` o implementar `ITableEntity`. Las propiedades `partitionKey`, `rowKey`, `filter` y `take` no se utilizan en este escenario; puede utilizar el objeto `IQueryable` para realizar todo el filtrado requerido.
* Lectura de varias filas en una función de Node.
  
    Establezca las propiedades `filter` y `take`. No establezca `partitionKey` o `rowKey`.
* Escriba una o varias filas en una función de C#.
  
    El tiempo de ejecución de Funciones proporciona un `ICollector<T>` o `IAsyncCollector<T>` enlazado a la tabla, donde `T` especifica el esquema de las entidades que desea agregar. Normalmente, el tipo `T` deriva de `TableEntity` o implementa `ITableEntity`, pero no tiene por qué ser así. Las propiedades `partitionKey`, `rowKey`, `filter` y `take` no se utilizan en este escenario.

#### Ejemplo de tablas de almacenamiento: lectura de una entidad de tabla individual en C# o Node
El siguiente ejemplo de código de C# funciona con el archivo *function.json* anterior para leer una entidad de tabla individual. El mensaje en cola tiene el valor de la clave de fila y la entidad de tabla se lee en un tipo que se define en el archivo *run.csx*. El tipo incluye las propiedades `PartitionKey` y `RowKey`, y no se deriva de `TableEntity`.

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

El siguiente ejemplo de código de F# también funciona con el archivo *function.json* anterior para leer una entidad de tabla individual.

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

El siguiente ejemplo de código de Node también funciona con el archivo *function.json* anterior para leer una entidad de tabla individual.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Ejemplo de tablas de almacenamiento: lectura de varias entidades de tabla en C
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

#### Ejemplo de tablas de almacenamiento: creación de entidades de tabla en C
El siguiente ejemplo de *function.json* y *run.csx* muestra cómo escribir entidades de tabla en C#.

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

#### Ejemplo de tablas de almacenamiento: creación de entidades de tabla en F
El siguiente ejemplo de *function.json* y *run.csx* muestra cómo escribir entidades de tabla en F#.

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

#### Ejemplo de tablas de almacenamiento: creación de una entidad de tabla en Node
El siguiente ejemplo de *function.json* y *run.csx* muestra cómo escribir una entidad de tabla en Node.

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
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Pasos siguientes
[!INCLUDE [pasos siguientes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->