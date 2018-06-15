---
title: 'Temporizadores en Durable Functions: Azure'
description: Aprenda a implementar temporizadores durables en la extensión Durable Functions para Azure Functions.
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
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: 4fd86b70965a7be84c72e265af798292819cbe96
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762275"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores en Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) proporciona *temporizadores durables* para usarlos en funciones de orquestador para implementar retrasos o configurar tiempos de expiración en acciones asincrónicas. Los temporizadores durables deben usarse en funciones de orquestador en lugar de `Thread.Sleep` y `Task.Delay` (C#), o `setTimeout()` y `setInterval()` (JavaScript).

Se crea un temporizador durable mediante una llamada al método [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) en [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). El método devuelve una tarea que se reanuda en una fecha y hora especificadas.

## <a name="timer-limitations"></a>Limitaciones de los temporizadores

Cuando se crea un temporizador que expira a las 4:30 p. m., la instancia de Durable Task Framework subyacente pone en cola un mensaje que se vuelve visible solo a las 4:30 p. m. Al ejecutarse en el plan de consumo de Azure Functions, el mensaje del temporizador recientemente visible garantizará que la aplicación de función se active en una máquina virtual adecuada.

> [!NOTE]
> * Los temporizadores durables no pueden durar más de siete días debido a limitaciones en Azure Storage. Se está trabajando en una [solicitud de característica para ampliar los temporizadores más allá de siete días](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Utilice siempre [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) en lugar de `DateTime.UtcNow` tal como se muestra en los ejemplos siguientes al calcular una fecha límite relativa de un temporizador durable.

## <a name="usage-for-delay"></a>Uso para retraso

En el ejemplo siguiente se muestra cómo utilizar temporizadores durables para retrasar la ejecución. En el ejemplo se emite una notificación de facturación cada día durante diez días.

#### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivityAsync("SendBillingEvent");
    }
});
```

> [!WARNING]
> Evite bucles infinitos en funciones de orquestador. Para más información acerca de cómo implementar de forma segura y eficaz escenarios de bucle infinito, consulte [Orquestaciones infinitas](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Uso para tiempo de expiración

En este ejemplo se muestra cómo utilizar temporizadores durables para implementar tiempos de expiración.

#### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, 's');

    const activityTask = context.df.callActivityAsync("GetQuote");
    const timeoutTask = context.df.createTimer(deadline);

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Use `CancellationTokenSource` para cancelar un temporizador durable (C#) o una llamada `cancel()` en el `TimerTask` devuelto (JavaScript) si el código no va a esperar a que se complete. Durable Task Framework no cambiará el estado de una orquestación a "Completed" (Completado) hasta que todas las tareas pendientes se hayan completado o cancelado.

Este mecanismo no finaliza realmente la ejecución de la función de actividad en curso. En su lugar, simplemente permite que la función de orquestador pase por alto el resultado y continúe. Si la aplicación de función usa su plan de consumo, se le seguirá facturando por el tiempo y la memoria consumidos por la función de actividad abandonada. De manera predeterminada, las funciones en ejecución en el plan de consumo tienen un tiempo de expiración de cinco minutos. Si se supera este límite, el host de Azure Functions se recicla para detener toda la ejecución y evitar una situación de facturación descontrolada. El [tiempo de expiración de la función se puede configurar](functions-host-json.md#functiontimeout).

Para obtener un ejemplo más detallado de cómo implementar los tiempos de expiración en funciones de orquestador, consulte el tutorial [Las interacciones humanas en Durable Functions: comprobación telefónica de ejemplo](durable-functions-phone-verification.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información acerca de cómo generar y controlar eventos externos](durable-functions-external-events.md)

