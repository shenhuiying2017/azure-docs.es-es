---
title: 'Control de eventos externos con Durable Functions: Azure'
description: "Aprenda a controlar eventos externos en la extensión Durable Functions para Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 1f581be0abaff542285abc0d4c2f4bffe7281d20
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Control de eventos externos con Durable Functions (Azure Functions)

Las funciones de orquestador tienen la capacidad de esperar y escuchar eventos externos. Esta característica de [Durable Functions](durable-functions-overview.md) suele ser útil para controlar las interacciones humanas u otros desencadenadores externos.

## <a name="wait-for-events"></a>Espera de eventos

El método [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) permite que una función de orquestador espere y escuche un evento externo de forma asincrónica. La función de orquestador de escucha declara el *nombre* del evento y la *forma de los datos* que espera recibir.

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

El ejemplo anterior escucha un evento único específico y toma medidas cuando se recibe.

Puede escuchar varios eventos al mismo tiempo, al igual que en el ejemplo siguiente, que espera una de tres notificaciones de eventos posibles.

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

El ejemplo anterior escucha *cualquiera* de varios eventos posibles. También es posible esperar *todos* los eventos.

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) espera indefinidamente alguna entrada.  La aplicación de función puede descargarse con seguridad mientras espera. En el momento en que un evento llega a esta instancia de orquestación, esta se activa automáticamente y procesa de inmediato el evento.

> [!NOTE]
> Si la aplicación de función utiliza el plan de consumo, no se aplican costos de facturación mientras una función de orquestador espera una tarea de `WaitForExternalEvent`, independientemente de cuánto tiempo espere.

Si la carga del evento no se puede convertir al tipo `T` esperado, se produce una excepción.

## <a name="send-events"></a>Envío de eventos

El método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) de la clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) envía los eventos que `WaitForExternalEvent` espera.  El método `RaiseEventAsync` toma *eventName* y *eventData* como parámetros. Los datos del evento deben poderse serializar con JSON.

A continuación se muestra una función desencadenada por la cola de ejemplo que envía un evento de aprobación a una instancia de la función de orquestador. El identificador de la instancia de orquestación procede del cuerpo del mensaje de la cola.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

Internamente, `RaiseEventAsync` pone en cola un mensaje que recoge la función de orquestador en espera.

> [!WARNING]
> Si no hay ninguna instancia de orquestación con el *identificador de instancia* especificado o si la instancia no está esperando el *nombre de evento* especificado, se descarta el mensaje de evento. Para más información acerca de este comportamiento, consulte el [problema de GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información acerca de cómo configurar orquestaciones infinitas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Ejecutar un ejemplo que espera eventos externos](durable-functions-counter.md)

> [!div class="nextstepaction"]
> [Ejecutar un ejemplo que espera interacción humana](durable-functions-phone-verification.md)

