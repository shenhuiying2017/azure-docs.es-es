---
title: 'Singletons en Durable Functions: Azure'
description: "Aprenda a utilizar singletons en la extensión Durable Functions para Azure Functions."
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
ms.openlocfilehash: 8384d17405653a29207cdfa4f6143504d0db2022
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestadores singleton con Durable Functions (Azure Functions)

Para trabajos en segundo plano u orquestaciones de tipo actor, a menudo debe asegurarse de que solo una instancia de un determinado orquestador se ejecute a la vez. Esto puede hacerse en [Durable Functions](durable-functions-overview.md) mediante la asignación de un identificador de instancia determinado a un orquestador durante su creación.

## <a name="singleton-example"></a>Ejemplo de singleton

En el ejemplo de C# siguiente se muestra una función de desencadenador de HTTP que crea una orquestación de trabajo en segundo plano de singleton. El código garantiza que solo existe una instancia de un identificador de instancia especificado.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

De forma predeterminada, los identificadores de instancia son identificadores únicos globales generados aleatoriamente. Pero, en este caso, el identificador de instancia se pasa en los datos de ruta desde la dirección URL. El código llama a [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) para comprobar si ya se está ejecutando una instancia con el identificador especificado. Si no es así, se crea una instancia con ese identificador.

Los detalles de implementación de la función de orquestador no importan realmente. Podría tratarse de una función de orquestador normal, con un principio y un final, o de una que se ejecuta sin fin (es decir, una [orquestación infinita](durable-functions-eternal-orchestrations.md)). Lo importante es que haya solo una instancia en ejecución cada vez.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Cómo llamar a suborquestaciones](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Ejecución de un singleton de ejemplo](durable-functions-counter.md)
