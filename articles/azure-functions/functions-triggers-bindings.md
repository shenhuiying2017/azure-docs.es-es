---
title: Enlaces y desencadenadores de Azure Functions | Microsoft Docs
description: "Descripción sobre cómo utilizar desencadenadores y enlaces en Funciones de Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/30/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: ee24bcff625c5ea28dbf3cbc5332078721544ddc
ms.openlocfilehash: ef6f3de0da6e051826bcb9bf4a6ebaa78fbaac7c


---

# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Referencias para desarrolladores de desencadenadores y enlaces de Funciones de Azure
En este tema se proporciona una referencia general para desencadenadores y enlaces. Además, incluye algunas de las características avanzadas de enlace y la sintaxis compatible con todos los tipos de enlace.  

Para obtener información detallada sobre cómo trabajar con un tipo concreto de enlace o desencadenador, consulte uno de los temas de referencia siguientes:

| | | | |  
| --- | --- | --- | --- |  
| [HTTP/webhook](functions-bindings-http-webhook.md) | [Temporizador](functions-bindings-timer.md) | [Aplicaciones móviles](functions-bindings-mobile-apps.md) | [Bus de servicio](functions-bindings-service-bus.md)  |  
| [DocumentDB](functions-bindings-documentdb.md) |  [Storage Blob](functions-bindings-storage-blob.md) | [Storage Queue](functions-bindings-storage-queue.md) |  [Storage Table](functions-bindings-storage-table.md) |  
| [Centros de eventos](functions-bindings-event-hubs.md) | [Centros de notificaciones](functions-bindings-notification-hubs.md) | [Twilio](functions-bindings-twilio.md) |   
| | | | |  

En estos documentos se da por supuesto que ya ha leído [Referencia para desarrolladores de Azure Functions](functions-reference.md) y los artículos de referencia para desarrolladores de [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) o [Node.js](functions-reference-node.md).

## <a name="overview"></a>Información general
Los desencadenadores son respuestas a eventos que se utilizan para desencadenar el código personalizado. Además, permiten responder a eventos en la plataforma Azure o en entornos locales. Los enlaces representan los metadatos necesarios que se emplean para conectar el código con el desencadenador desead, o bien los datos de entrada o de salida asociados. El archivo *function.json* de cada función contiene todos los enlaces relacionados. No hay ningún límite en el número de enlaces de entrada y salida que una función puede tener. Sin embargo, se admite solo un enlace de desencadenador para cada función.  

Para que tenga más claro cuáles son los distintos enlaces que puede integrar con la aplicación de Azure Function, consulte la tabla siguiente.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Si quiere comprender mejor los conceptos de desencadenador y enlace, supongamos que desea ejecutar código para procesar un nuevo elemento que se ha colocado en una cola de Azure Storage. Para tal fin, Azure Functions proporciona un desencadenador de colas de Azure. Necesitará la siguiente información para supervisar la cola:

* La cuenta de almacenamiento donde se encuentra la cola
* El nombre de la cola
* Un nombre de variable que utilizaría el código para hacer referencia al nuevo elemento que se ha colocado en la cola.  

Un enlace de desencadenador de colas contiene esta información para una función de Azure. A continuación, encontrará un archivo *function.json* de ejemplo con un enlace de desencadenador de colas. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ],
  "disabled": false
}
```

El código puede enviar distintos tipos de salida en función de cómo se procese el nuevo elemento de la cola. Por ejemplo, podría escribir un nuevo registro en una tabla de Azure Storage.  Para ello, cree un enlace de salida a una tabla de Azure Storage. Abajo encontrará un archivo *function.json* de ejemplo que incluye un enlace de salida a una tabla de almacenamiento que podría usarse con un desencadenador de colas. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "name": "myNewUserTableBinding",
      "tableName": "newUserTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La siguiente función de C# responde a un nuevo elemento que se va a colocar en la cola y escribe una nueva entrada de usuario en una tabla de Azure Storage.

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                TraceWriter log)
{
    // In this example the queue item is a JSON string representing an order that contains the name, 
    // address and mobile number of the new customer.
    dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);

    await myNewUserTableBinding.AddAsync(
        new Person() { 
            PartitionKey = "Test", 
            RowKey = Guid.NewGuid().ToString(), 
            Name = order.name,
            Address = order.address,
            MobileNumber = order.mobileNumber }
        );
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string MobileNumber { get; set; }
}
```

Para ver otros ejemplos de código e información más específica sobre los tipos de almacenamiento de Azure compatibles, consulte [Enlaces y desencadenadores de Azure Functions para Azure Storage](functions-bindings-storage.md).

Para usar las características más avanzadas de enlace en Azure Portal, haga clic en la opción **Editor avanzado** de la pestaña **Integrar** de la función. El Editor avanzado permite editar el archivo *function.json* directamente en el portal.

## <a name="random-guids"></a>GUID aleatorios
Azure Functions proporciona una sintaxis para generar los GUID aleatorios con los enlaces. La siguiente sintaxis de enlace escribirá la salida en un BLOB nuevo con un nombre único en un contenedor de Storage: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```


## <a name="returning-a-single-output"></a>Devolución de una única salida
En los casos donde el código de la función devuelva una única salida, puede utilizar un enlace de salida denominado "`$return`" para conservar una firma de la función más natural en el código. Solo puede usarse con lenguajes que admiten un valor devuelto (C#, Node.js y F #). El enlace sería similar al de salida de blob siguiente que se utiliza con un desencadenador de colas.

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "name": "input",
      "direction": "in",
      "queueName": "test-input-node"
    },
    {
      "type": "blob",
      "name": "$return",
      "direction": "out",
      "path": "test-output-node/{id}"
    }
  ]
}
```

El siguiente código de C# devuelve la salida de una forma más natural sin utilizar un parámetro `out` en la firma de la función.

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

Ejemplo asincrónico:

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```


Este mismo enfoque se demuestra con Node.js a continuación:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

Abajo se muestra un ejemplo de F#:

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

Este código también puede utilizarse con varios parámetros de salida designando una salida única con `$return`.

## <a name="resolving-app-settings"></a>Resolver la configuración de la aplicación
Es un procedimiento recomendado para almacenar información confidencial como parte del entorno de tiempo de ejecución mediante la configuración de la aplicación. Al mantener la información confidencial fuera de los archivos de configuración de la aplicación, se limita la exposición cuando se utiliza un repositorio público para almacenar los archivos de la aplicación.  

El tiempo de ejecución de Azure Functions resuelve la configuración de la aplicación para los valores cuando el nombre de la configuración de la aplicación está entre signos de porcentaje, `%your app setting%`. El siguiente [enlace de Twilio](functions-bindings-twilio.md) utiliza una configuración de la aplicación denominada `TWILIO_ACCT_PHONE` para el campo `from` del enlace. 

```json
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received Node.js",
  "direction": "out"
},
```



## <a name="parameter-binding"></a>Enlace de parámetros
En lugar de usar una configuración estática en las propiedades de enlace de salida, puede configurar que los parámetros se enlacen dinámicamente a los datos que forman parte del enlace de entrada del desencadenador. Piense en un escenario en el que los nuevos pedidos se procesan mediante una cola de Azure Storage. Cada nuevo elemento de la cola es una cadena JSON que contiene, al menos, las siguientes propiedades:

```json
{
  "name" : "Customer Name",
  "address" : "Customer's Address".
  "mobileNumber" : "Customer's mobile number in the format - +1XXXYYYZZZZ."
}
```

Tal vez quiera enviar al cliente un mensaje de texto SMS utilizando su cuenta de Twilio para notificarle que ha recibido el pedido.  Puede configurar los campos `body` y `to` del enlace de salida de Twilio para que se enlacen dinámicamente a los campos `name` y `mobileNumber` que formaban parte de la entrada, tal y como se muestra a continuación.

```json
{
  "name": "myNewOrderItem",
  "type": "queueTrigger",
  "direction": "in",
  "queueName": "queue-newOrders",
  "connection": "orders_STORAGE"
},
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received",
  "direction": "out"
},
```

Ahora, el código de la función solo tiene que inicializar el parámetro de salida como se indica abajo. Durante la ejecución, las propiedades de salida se enlazarán a los datos de entrada deseados.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using System.Threading.Tasks;
using Newtonsoft.Json;
using Twilio;

public static async Task<SMSMessage> Run(string myNewOrderItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myNewOrderItem}");

    dynamic order = JsonConvert.DeserializeObject(myNewOrderItem);    

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // The following isn't needed since we use parameter binding for this
    //string msg = "Hello " + order.name + ", thank you for your order.";
    //smsText.Body = msg;
    //smsText.To = order.mobileNumber;

    return smsText;
}
```

Node.js:

```javascript
module.exports = function (context, myNewOrderItem) {    
    context.log('Node.js queue trigger function processed work item', myNewOrderItem);    

    // No need to set the properties of the text, we use parameters in the binding. We do need to 
    // initialize the object.
    var smsText = {};    

    context.done(null, smsText);
}
```

## <a name="advanced-binding-at-runtime-imperative-binding"></a>Enlace avanzado en tiempo de ejecución (enlace imperativo)

El patrón de enlace estándar de entrada y salida que usa *function.json* se llama enlace [*declarativo*](https://en.wikipedia.org/wiki/Declarative_programming), donde la declaración de JSON define el enlace. Sin embargo, se puede utilizar el enlace [imperativo](https://en.wikipedia.org/wiki/Imperative_programming). Con este patrón, se puede enlazar a cualquier número de enlaces compatibles de entrada y salida sobre la marcha en el código de función.
Puede que se necesite un enlace imperativo en casos donde el cálculo de la ruta de acceso al enlace o de otras entradas debe ocurrir en el tiempo de ejecución de la función en lugar de en el tiempo de diseño. 

Defina un enlace imperativo como se indica a continuación:

- **No** incluya una entrada en *function.json* para los enlaces imperativos deseados.
- Pase un parámetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) o [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs). 
- Utilice el siguiente patrón de C# para realizar el enlace de datos.

        using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
        {
                ...
        }

    donde `BindingTypeAttribute` es el atributo de .NET que define el enlace y `T` es el tipo de entrada o de salida compatible con ese tipo de enlace. `T` no puede ser también un tipo de parámetro `out` (como `out JObject`). Por ejemplo, el enlace de salida de la tabla de Mobile Apps admite [seis tipos de salida](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), pero solo se puede utilizar [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) para `T`.
    
El ejemplo de código siguiente crea un [enlace de salida al blob de almacenamiento](functions-bindings-storage-blob.md#storage-blob-output-binding) con la ruta de acceso al blob definida en tiempo de ejecución y, a continuación, escribe una cadena en el blob.

        using Microsoft.Azure.WebJobs;
        using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;
        
        public static async Task Run(string input, Binder binder)
        {
                using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
                {
                        writer.Write("Hello World!!");
                }
        }

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define el enlace de entrada o salida del [blob de almacenamiento](functions-bindings-storage-blob.md), y [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) es un tipo de enlace de salida admitido.
De esta forma, el código obtiene la configuración de la aplicación predeterminada para la cadena de conexión de la cuenta de almacenamiento (que es `AzureWebJobsStorage`). Se puede especificar una configuración personalizada de la aplicación para utilizarla agregando el atributo [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) y pasando la matriz de atributos a `BindAsync<T>()`. Por ejemplo,

        using Microsoft.Azure.WebJobs;
        using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;
        
        public static async Task Run(string input, Binder binder)
        {
                var attributes = new Attribute[]
                {
                        new BlobAttribute("samples-output/path"),
                        new StorageAccountAttribute("MyStorageAccount")
                };
                using (var writer = await binder.BindAsync<TextWriter>(attributes))
                {
                        writer.Write("Hello World!");
                }
        }

En la tabla siguiente se muestra el atributo de .NET correspondiente que se debe utilizar para cada tipo de enlace y a qué paquete hacer referencia.

> [!div class="mx-codeBreakAll"]
| Enlace | Atributo | Agregar referencia |
|------|------|------|
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Cola de almacenamiento | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Blob de almacenamiento | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Tabla de almacenamiento | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Prueba de una función](functions-test-a-function.md)
* [Escalar una función](functions-scale.md)




<!--HONumber=Dec16_HO1-->


