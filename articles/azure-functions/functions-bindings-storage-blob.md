---
title: Enlaces de Azure Blob Storage para Azure Functions
description: "Descubra cómo utilizar desencadenadores y enlaces de Azure Blob Storage en Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 6985d631bdac7114a72f105716c9483d0c5733ba
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Enlaces de Azure Blob Storage para Azure Functions

En este artículo se explica cómo trabajar con enlaces de Azure Blob Storage en Azure Functions. Azure Functions admite enlaces de desencadenador, entrada y salida para blobs. El artículo incluye una sección para cada enlace:

* [Desencadenador de blobs](#trigger)
* [Enlace de entrada de blob](#input)
* [Enlace de salida de blob](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Las [cuentas de almacenamiento de solo blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts) no son compatibles. Los desencadenadores y enlaces de Blob Storage necesitan una cuenta de almacenamiento de uso general. 

## <a name="trigger"></a>Desencadenador

Utilice un desencadenador de Blob Storage para iniciar una función cuando se detecte un blob nuevo o actualizado. El contenido del blob se proporciona a modo de entrada para la función.

> [!NOTE]
> Al usar un desencadenador de blobs en un plan de consumo, puede haber un retraso de hasta 10 minutos en el procesamiento de nuevos blobs después de que una aplicación de función quede inactiva. Después de que se ejecute la aplicación de función, los blobs se procesan inmediatamente. Para evitar este retraso inicial, considere una de las siguientes opciones:
> - Usar un plan de App Service con Always On habilitado
> - Usar otro mecanismo para desencadenar el procesamiento de blobs, por ejemplo, un mensaje de la cola que contenga el nombre del blob Para ver un ejemplo, consulte el [ejemplo de enlaces de entrada de blob más adelante en este artículo](#input---example).

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Desencadenador: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que escribe un registro cuando se agrega o se actualiza un blob en el contenedor `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Para obtener más información sobre el atributo `BlobTrigger`, consulte [Desencadenador: atributos](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de blobs en un archivo *function.json* y código de [script de C# (.csx)](functions-reference-csharp.md) que usa el enlace. La función escribe un registro cuando se agrega o actualiza un blob en el contenedor `samples-workitems`.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de script de C# que se enlaza a `Stream`:

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Este es el código de script de C# que se enlaza a `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de blob en un archivo *function.json* y [código JavaScript] (functions-reference-node.md) que usa el enlace. La función escribe un registro cuando se agrega o actualiza un blob en el contenedor `samples-workitems`.

Este es el archivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>Desencadenador: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), utilice los siguientes atributos para configurar un desencadenador de blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs), definido en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  El constructor del atributo toma una cadena de ruta de acceso que indica al contenedor que inspeccione y, opcionalmente, un [patrón de nombre de blob](#trigger---blob-name-patterns). Este es un ejemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Para obtener un ejemplo completo, consulte [Desencadenador: ejemplo de C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), definido en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Proporciona otra manera de especificar la cuenta de almacenamiento que se debe usar. El constructor toma el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. El atributo se puede aplicar en el nivel de clase, método o parámetro. En el ejemplo siguiente se muestran el nivel de clase y de método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

La cuenta de almacenamiento que se debe usar se determina en el orden siguiente:

* La propiedad `Connection` del atributo `BlobTrigger`.
* El atributo `StorageAccount` aplicado al mismo parámetro que el atributo `BlobTrigger`.
* El atributo `StorageAccount` aplicado a la función.
* El atributo `StorageAccount` aplicado a la clase.
* La cuenta de almacenamiento predeterminada de la aplicación de función (configuración de aplicación "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `BlobTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `blobTrigger`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | N/D | Se debe establecer en `in`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. Las excepciones se indican en la sección [uso](#trigger---usage). |
|**name** | N/D | Nombre de la variable que representa el blob en el código de la función. | 
|**path** | **BlobPath** |El contenedor que se va a supervisar.  Puede ser un [patrón de nombre de blob](#trigger-blob-name-patterns). | 
|**conexión** | **Connection** | El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions utiliza la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.<br><br>La cadena de conexión debe ser para una cuenta de almacenamiento de uso general, no una [cuenta de almacenamiento solo de blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Desencadenador: uso

En C# y script de C#, acceda a los datos del blob mediante un parámetro de método, como `T paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Puede enlazar a cualquiera de los siguientes tipos:

* `Stream`
* `TextReader`
* `Byte[]`
* `string`
* `ICloudBlob` (necesita una dirección de enlace "inout" en *function.json*)
* `CloudBlockBlob` (necesita una dirección de enlace "inout" en *function.json*)
* `CloudPageBlob` (necesita una dirección de enlace "inout" en *function.json*)
* `CloudAppendBlob` (necesita una dirección de enlace "inout" en *function.json*)

Como se indica, algunos de estos tipos necesitan una dirección de enlace `inout` en *function.json*. El editor estándar de Azure Portal no admite esta dirección, por lo que debe usar el editor avanzado.

Si se esperan blobs de texto, puede realizar el enlace al tipo `string`. Solo se recomienda hacerlo si los blobs son pequeños, ya que todos sus contenidos se cargan en memoria. Por lo general, es preferible usar un tipo `Stream` o `CloudBlockBlob`.

En JavaScript, acceda a los datos de blob de entrada mediante `context.bindings.<name>`.

## <a name="trigger---blob-name-patterns"></a>Desencadenador: patrones de nombre de blob

Puede especificar un patrón de nombre de blob en la propiedad `path` en *function.json* o en el constructor de atributos `BlobTrigger`. El patrón de nombre puede ser una [expresión de filtro o enlace](functions-triggers-bindings.md#binding-expressions-and-patterns).

### <a name="filter-on-blob-name"></a>Filtrar por nombre de blob

El ejemplo siguiente se desencadena solo en blobs del contenedor `input` que comienzan con la cadena "original-":

```json
"path": "input/original-{name}",
```
 
Si el nombre de blob es *original Blob1.txt*, el valor de la variable `name` en el código de la función es `Blob1`.

### <a name="filter-on-file-type"></a>Filtrar por tipo de archivo

El siguiente ejemplo se desencadena solo en archivos *.png*:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrar por llaves en nombres de archivo

Para buscar llaves en nombres de archivo, escape las llaves mediante dos llaves. En el ejemplo siguiente se filtran blobs que tienen llaves en el nombre:

```json
"path": "images/{{20140101}}-{name}",
```

Si el blob se denomina *{20140101}-soundfile.mp3*, el valor de variable `name` en el código de la función es *soundfile.mp3*. 

### <a name="get-file-name-and-extension"></a>Obtener el nombre y la extensión de archivo

En el ejemplo siguiente se muestra cómo enlazar a nombre de archivo del blob y la extensión por separado:

```json
"path": "input/{blobname}.{blobextension}",
```
Si el blob se denomina *original-Blob1.txt*, el valor de las variables `blobname` y `blobextension` del código de la función son *original-Blob1* y *txt*.

## <a name="trigger---metadata"></a>Desencadenador: metadatos

El desencadenador de blobs proporciona varias propiedades de metadatos. Estas propiedades pueden usarse como parte de expresiones de enlace en otros enlaces o como parámetros del código. Estos valores tienen la misma semántica que el tipo [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).


|Propiedad  |type  |DESCRIPCIÓN  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Ruta de acceso del blob de desencadenamiento.|
|`Uri`|`System.Uri`|Es el identificador URI del blob correspondiente a la ubicación principal.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Son las propiedades del sistema del blob. |
|`Metadata` |`IDictionary<string,string>`|Son los metadatos definidos por el usuario para el blob.|

## <a name="trigger---blob-receipts"></a>Desencadenador: recepciones de blobs

El entorno en tiempo de ejecución de Azure Functions garantiza que no se llame más de una vez a ninguna función de desencadenador de blobs para un mismo blob, ya sea nuevo o actualizado. Mantiene *recepciones de blobs* para determinar si se ha procesado una determinada versión de blob.

Azure Functions almacena confirmaciones de blobs en un contenedor llamado *azure-webjobs-hosts* en la cuenta de almacenamiento de Azure de la aplicación de función (que se especifica mediante la configuración de la aplicación `AzureWebJobsStorage`). Una recepción de blobs tiene la información siguiente:

* La función desencadenada ("*&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*", por ejemplo: "MyFunctionApp.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre del blob
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar el reprocesamiento de un blob, puede eliminar manualmente la recepción de ese blob desde el contenedor *azure-webjobs-hosts* .

## <a name="trigger---poison-blobs"></a>Desencadenador: blobs dudosos

Si se produce un error en una función de desencadenador de blob, Azure Functions vuelve a intentar ejecutar esa función hasta 5 veces de forma predeterminada. 

Si se produce un error en los 5 intentos, Azure Functions agregará un mensaje a una cola de Storage llamada *webjobs-blobtrigger-poison*. El mensaje de cola para los blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

## <a name="trigger---polling-for-large-containers"></a>Desencadenador: sondeo en contenedores grandes

Si el contenedor de blobs supervisado contiene más de 10 000 blobs, el entorno en tiempo de ejecución de Functions examinará los archivos de registro para detectar blobs nuevos o modificados. Este proceso puede provocar retrasos. Una función podría tardar en desencadenarse varios minutos o más después de crear el blob. Además, [se crean registros de almacenamiento basados en el principio del "mejor esfuerzo"](/rest/api/storageservices/About-Storage-Analytics-Logging). No hay ninguna garantía de que se capturen todos los eventos. En algunos casos, podrían faltar registros. Si necesita un procesamiento de blobs más rápido o confiable, considere crear un [mensaje de cola](../storage/queues/storage-dotnet-how-to-use-queues.md) al crear el blob. A continuación, use un [desencadenador de cola](functions-bindings-storage-queue.md), en lugar de un desencadenador de blob, para procesar el blob. Otra opción consiste en usar Event Grid; consulte el tutorial [Automatizar el cambio de tamaño de imágenes cargadas mediante Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Entrada

Utilice un enlace de entrada de almacenamiento de blobs para leer blobs.

## <a name="input---example"></a>Ejemplo de entrada

Vea el ejemplo específico del lenguaje:

* [C#](#input---c-example)
* [Script de C# (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Entrada: ejemplo de C#

El ejemplo siguiente es una [función de C#](functions-dotnet-class-library.md) que utiliza un desencadenador de cola y un enlaces de blobs de entrada. El mensaje de cola contiene el nombre del blob y la función registra el tamaño del blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");

}
```        

### <a name="input---c-script-example"></a>Entrada: ejemplo de script de C#

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran enlaces de entrada y salida de blobs en un archivo *function.json* y código de [script de C# (.csx)](functions-reference-csharp.md) que usa los enlaces. La función realiza una copia de un blob de texto. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de script de C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Entrada: ejemplo de JavaScript

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran los enlaces de entrada y salida de blobs en un archivo *function.json* y el [código JavaScript] (functions-reference-node.md) que usa los enlaces. La función realiza una copia de un blob. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input---attributes"></a>Entrada: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), utilice [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

El constructor del atributo toma la ruta de acceso al blob y un parámetro `FileAccess` que indica lectura o escritura, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Puede usar el atributo `StorageAccount` para especificar la cuenta de almacenamiento en el nivel de clase, método o parámetro. Para obtener más información, consulte [Desencadenador: atributos](#trigger---attributes).

## <a name="input---configuration"></a>Entrada: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que establece en el archivo *function.json* y el atributo `Blob`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `blob`. |
|**dirección** | N/D | Se debe establecer en `in`. Las excepciones se indican en la sección [uso](#input---usage). |
|**name** | N/D | Nombre de la variable que representa el blob en el código de la función.|
|**path** |**BlobPath** | Ruta de acceso al blob. | 
|**conexión** |**Connection**| El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions utiliza la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.<br><br>La cadena de conexión debe ser para una cuenta de almacenamiento de uso general, no una [cuenta de almacenamiento solo de blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|N/D | **Access** | Indica si va a leer o escribir. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Uso de entradas

En bibliotecas de clases de C# y script de C#, acceda al blob mediante un parámetro de método, como `Stream paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Puede enlazar a cualquiera de los siguientes tipos:

* `TextReader`
* `string`
* `Byte[]`
* `Stream`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob` (necesita una dirección de enlace "inout" en *function.json*)
* `CloudBlockBlob` (necesita una dirección de enlace "inout" en *function.json*)
* `CloudPageBlob` (necesita una dirección de enlace "inout" en *function.json*)
* `CloudAppendBlob` (necesita una dirección de enlace "inout" en *function.json*)

Como se indica, algunos de estos tipos necesitan una dirección de enlace `inout` en *function.json*. El editor estándar de Azure Portal no admite esta dirección, por lo que debe usar el editor avanzado.

Si está leyendo blobs de texto, puede enlazar a un tipo `string`. Este tipo solo se recomienda si el tamaño de blob es pequeño, ya que todo el contenido del blob se carga en memoria. Por lo general, es preferible usar un tipo `Stream` o `CloudBlockBlob`.

En JavaScript, acceda a los datos de blob mediante `context.bindings.<name>`.

## <a name="output"></a>Salida

Use enlaces de salida de almacenamiento de blobs para escribir blobs.

## <a name="output---example"></a>Salida: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#output---c-example)
* [Script de C# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Salida: ejemplo de C#

El ejemplo siguiente es una [función de C#](functions-dotnet-class-library.md) que utiliza un desencadenador de blobs y dos enlaces de blobs de salida. La función se activa por la creación de un blob de imágenes en el contenedor *sample-images*. Crea copias pequeñas y medianas del blob de imágenes. 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="output---c-script-example"></a>Salida: ejemplo de script de C#

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran enlaces de entrada y salida de blobs en un archivo *function.json* y código de [script de C# (.csx)](functions-reference-csharp.md) que usa los enlaces. La función realiza una copia de un blob de texto. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de script de C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Salida: ejemplo de JavaScript

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran los enlaces de entrada y salida de blobs en un archivo *function.json* y el [código JavaScript] (functions-reference-node.md) que usa los enlaces. La función realiza una copia de un blob. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="output---attributes"></a>Salida: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), utilice [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

El constructor del atributo toma la ruta de acceso al blob y un parámetro `FileAccess` que indica lectura o escritura, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Para obtener un ejemplo completo, consulte [Salida: ejemplo de C#](#output---c-example).

Puede usar el atributo `StorageAccount` para especificar la cuenta de almacenamiento en el nivel de clase, método o parámetro. Para obtener más información, consulte [Desencadenador: atributos](#trigger---attributes).

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Blob`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `blob`. |
|**dirección** | N/D | Debe establecerse en `out` para un enlace de salida. Las excepciones se indican en la sección [uso](#output---usage). |
|**name** | N/D | Nombre de la variable que representa el blob en el código de la función.  Se establece en `$return` para hacer referencia al valor devuelto de la función.|
|**path** |**BlobPath** | Ruta de acceso al blob. | 
|**conexión** |**Connection**| El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions utiliza la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.<br><br>La cadena de conexión debe ser para una cuenta de almacenamiento de uso general, no una [cuenta de almacenamiento solo de blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|N/D | **Access** | Indica si va a leer o escribir. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uso de salidas

En bibliotecas de clases de C# y script de C#, acceda al blob mediante un parámetro de método, como `Stream paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Puede enlazar a cualquiera de los siguientes tipos:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob` (necesita una dirección de enlace "inout" en *function.json*)
* `CloudBlockBlob` (necesita una dirección de enlace "inout" en *function.json*)
* `CloudPageBlob` (necesita una dirección de enlace "inout" en *function.json*)
* `CloudAppendBlob` (necesita una dirección de enlace "inout" en *function.json*)

Como se indica, algunos de estos tipos necesitan una dirección de enlace `inout` en *function.json*. El editor estándar de Azure Portal no admite esta dirección, por lo que debe usar el editor avanzado.

Si está leyendo blobs de texto, puede enlazar a un tipo `string`. Este tipo solo se recomienda si el tamaño de blob es pequeño, ya que todo el contenido del blob se carga en memoria. Por lo general, es preferible usar un tipo `Stream` o `CloudBlockBlob`.

En JavaScript, acceda a los datos de blob mediante `context.bindings.<name>`.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Ir a un inicio rápido que use un desencadenador de Blob Storage](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
