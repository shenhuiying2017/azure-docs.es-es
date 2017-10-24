---
title: 'Control de errores con Durable Functions: Azure'
description: "Aprenda a controlar errores en la extensión Durable Functions para Azure Functions."
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
ms.openlocfilehash: ee5362d33bb9dadadb4194457cfd7726f4825f56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Control de errores con Durable Functions (Azure Functions)

Las orquestaciones de Durable Functions se implementan en el código y pueden utilizar las funciones de control de errores del lenguaje de programación. Así, realmente no hay ningún nuevo concepto que deba conocer sobre cuándo incorporar el control de errores y la compensación en las orquestaciones. No obstante, hay algunos comportamientos que deben tenerse en cuenta.

## <a name="errors-in-activity-functions"></a>Errores en funciones de actividad

Cualquier excepción que se produce en una función de actividad se devuelve a la función de orquestador en el búfer y se inicia como `TaskFailedException`. Puede escribir código de control y compensación de errores que se adapte a sus necesidades en la función de orquestador.

Por ejemplo, considere la siguiente función de orquestador que transfiere fondos de una cuenta a otra:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        { 
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

Si se produce un error en la llamada a la función **CreditAccount** para la cuenta de destino, la función de orquestador lo compensa al devolver los fondos a la cuenta de origen.

## <a name="automatic-retry-on-failure"></a>Reintento automático en caso de error

Al llamar a funciones de actividad o funciones de suborquestación, puede especificar una directiva de reintentos automáticos. En el ejemplo siguiente se intenta llamar a una función hasta 3 veces y se espera 5 segundos entre cada reintento:

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions);
    
    // ...
}
```

La API `CallActivityWithRetryAsync` toma un parámetro `RetryOptions`. Las llamadas de suborquestación mediante la API `CallSubOrchestratorWithRetryAsync` pueden usar estas mismas directivas de reintentos.

Existen varias opciones para personalizar la directiva de reintentos automáticos. Entre estas se incluyen las siguientes:

* **Max number of attempts** (Número máximo de intentos): número máximo de reintentos.
* **First retry interval** (Intervalo para el primer reintento): cantidad de tiempo de espera antes del primer reintento.
* **Backoff coefficient** (Coeficiente de retroceso): coeficiente que se usa para determinar la tasa de incremento del retroceso. De manera predeterminada, su valor es 1.
* **Max retry interval** (Intervalo de reintento máximo): cantidad máxima de tiempo de espera entre reintentos.
* **Retry timeout** (Tiempo de espera de reintento): cantidad máxima de tiempo durante el que realizar reintentos. El comportamiento predeterminado es realizar reintentos de manera indefinida.
* **Personalizado**: se puede especificar una devolución de llamada definida por el usuario que determina si se debe reintentar una llamada de función.

## <a name="function-timeouts"></a>Tiempos de espera de función

Es posible que desee abandonar una llamada de función dentro de una función de orquestador si tarda demasiado tiempo en completarse. Actualmente, la manera adecuada de hacerlo es mediante la creación de un [temporizador durable](durable-functions-timers.md) con `context.CreateTimer` junto con `Task.WhenAny`, como en el ejemplo siguiente:

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

## <a name="unhandled-exceptions"></a>Excepciones no controladas

Si se produce un error en una función de orquestador con una excepción no controlada, se registran los detalles de la excepción y la instancia se completa con el estado `Failed`.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Cómo diagnosticar problemas](durable-functions-diagnostics.md)
