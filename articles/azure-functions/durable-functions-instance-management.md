---
title: 'Administración de instancias con Durable Functions: Azure'
description: Aprenda a administrar instancias en la extensión Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 0e573b4973ea30b990043b54c5cdcf0805135a40
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Administración de instancias con Durable Functions (Azure Functions)

Las instancias de orquestación de [Durable Functions](durable-functions-overview.md) pueden iniciarse, finalizarse, consultarse y recibir eventos de notificación. Toda la administración de instancias se realiza mediante el [enlace de clientes de orquestación](durable-functions-bindings.md). En este artículo se describen los detalles de cada operación de administración de instancias.

## <a name="starting-instances"></a>Inicio de instancias

El método [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) en [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) inicia una nueva instancia de una función de orquestador. Las instancias de esta clase pueden adquirirse mediante el enlace `orchestrationClient`. Internamente, este método pone un mensaje en la cola de control, que, a su vez, desencadena el inicio de una función con el nombre especificado que utiliza el enlace de desencadenador `orchestrationTrigger`. 

La tarea se completa cuando se inicia el proceso de orquestación. El proceso de orquestación debería comenzar en 30 segundos. Si tarda más tiempo, se produce una excepción `TimeoutException`. 

Los parámetros de [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) son los siguientes:

* **Name**: el nombre de la función de orquestador que programar.
* **Input**: todos los datos serializables con JSON que deben pasarse como entrada a la función de orquestador.
* **InstanceId** (opcional): el identificador único de la instancia. Si no se especifica, se generará un identificador de instancia aleatorio.

A continuación, se muestra un ejemplo de C# sencillo:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

Para los lenguajes que no son de .NET, el enlace de salida de la función también se puede usar para iniciar nuevas instancias. En este caso, puede utilizarse cualquier objeto que se pueda serializar con JSON que tenga como campos los tres parámetros anteriores. Por ejemplo, considere la siguiente función de JavaScript:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Se recomienda que use un identificador aleatorio para el identificador de la instancia. Esto ayudará a garantizar una distribución equitativa de la carga al escalar las funciones de orquestador entre varias máquinas virtuales. El momento adecuado para usar identificadores de instancia no aleatorios es cuando el identificador debe proceder de un origen externo o cuando se implementa el patrón de [orquestador singleton](durable-functions-singletons.md).

## <a name="querying-instances"></a>Consulta de instancias

El método [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) de la clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) consulta el estado de una instancia de orquestación. Toma los valores `instanceId` (obligatorio), `showHistory` (opcional) y `showHistoryOutput` (opcional) como parámetros. Si `showHistory` se establece en `true`, la respuesta contendrá el historial de ejecución. Si `showHistoryOutput` se establece en `true`, el historial de ejecución contendrá salidas de actividad. El método devuelve un objeto con las siguientes propiedades:

* **Name**: el nombre de la función de orquestador.
* **InstanceId**: el identificador de instancia de la orquestación (debe ser el mismo que la entrada `instanceId`).
* **CreatedTime**: la hora en que la función de orquestador empezó a ejecutarse.
* **LastUpdatedTime**: la hora a la que la orquestación estableció el último punto de control.
* **Input**: la entrada de la función como un valor JSON.
* **CustomStatus**: estado de orquestación personalizada en formato JSON. 
* **Output**: la salida de la función como un valor JSON (si se ha completado la función). Si se produce un error en la función de orquestador, esta propiedad incluirá los detalles del error. Si se finaliza la función de orquestador, esta propiedad incluirá el motivo de la finalización indicado (si lo hubiera).
* **RuntimeStatus**: uno de los siguientes valores:
    * **Running**: la instancia ha empezado a ejecutarse.
    * **Completed**: la instancia se ha completado con normalidad.
    * **ContinuedAsNew**: la instancia se ha reiniciado con un nuevo historial. Este es un estado transitorio.
    * **Failed**: se ha producido un error en la instancia.
    * **Terminated**: la instancia se ha finalizado abruptamente.
* **History**: el historial de ejecución de la orquestación. Este campo solo se rellena si `showHistory` está establecido en `true`.
    
Este método devuelve `null` si la instancia no existe o no ha empezado a ejecutarse.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

## <a name="terminating-instances"></a>Finalización de instancias

Una instancia de orquestación en ejecución se puede finalizar mediante el método [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) de la clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Los dos parámetros son `instanceId` y una cadena `reason`, que se escribirá en los registros y en el estado de la instancia. Una instancia finalizada dejará de ejecutarse tan pronto como alcance el siguiente punto `await` o bien se finalizará inmediatamente si ya está en un punto `await`. 

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> La finalización de la instancia no se propaga actualmente. Las funciones de actividad y las suborquestaciones se ejecutarán hasta completarse, independientemente de si ha finalizado la instancia de orquestación que las llamó.

## <a name="sending-events-to-instances"></a>Envío de eventos a instancias

Pueden enviarse notificaciones de eventos a instancias en ejecución mediante el método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) de la clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Las instancias que pueden controlar estos eventos son aquellas que están en espera de una llamada a [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

Los parámetros de [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) son los siguientes:

* **InstanceId**: el identificador único de la instancia.
* **EventName**: el nombre del evento que se va a enviar.
* **EventData**: una carga que se puede serializar con JSON que enviar a la instancia.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!WARNING]
> Si no hay ninguna instancia de orquestación con el *identificador de instancia* especificado o si la instancia no está esperando el *nombre de evento* especificado, se descarta el mensaje de evento. Para más información acerca de este comportamiento, consulte el [problema de GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Esperar a que finalice la orquestación

La clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expone una API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) que se puede usar para obtener la salida real de manera sincrónica de una instancia de orquestación. El método usa un valor predeterminado de 10 segundos para `timeout` y de 1 segundo para `retryInterval` cuando no se establecen.  

Este ejemplo de función que se desencadena mediante HTTP muestra cómo utilizar la API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

La función se puede llamar con la siguiente línea mediante el tiempo de espera de 2 segundos y el intervalo de reintento de 0,5 segundos:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependiendo del tiempo necesario para obtener la respuesta de la instancia de orquestación, se dan dos casos:

1. Las instancias de orquestación se completan dentro del tiempo de espera definido (en este caso 2 segundos); la respuesta es la salida de la instancia de orquestación real entregada de manera sincrónica:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. Las instancias de orquestación no se pueden completar dentro del tiempo de espera definido (en este caso 2 segundos); la respuesta es la predeterminada descrita en **HTTP API URL discovery** (Detección de la URL de API HTTP):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> El formato de las direcciones URL del webhook puede diferir en función de la versión del host de Azure Functions en ejecución. El ejemplo anterior es para el host de Azure Functions 2.0.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información acerca de cómo usar las API HTTP para la instancia de administración](durable-functions-http-api.md)
