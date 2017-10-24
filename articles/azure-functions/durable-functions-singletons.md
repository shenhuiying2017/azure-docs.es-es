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
ms.openlocfilehash: e82cc53d53a6d0296aaab2c3a76ad4e2f6c12c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestadores singleton con Durable Functions (Azure Functions)

Para trabajos en segundo plano u orquestaciones de tipo actor, a menudo debe asegurarse de que solo una instancia de un determinado orquestador se ejecute a la vez. Esto puede hacerse en [Durable Functions](durable-functions-overview.md) mediante la asignación de un identificador de instancia determinado a un orquestador durante su creación.

## <a name="singleton-example"></a>Ejemplo de singleton

En el ejemplo de C# siguiente se muestra una función de desencadenador de HTTP que crea una orquestación de trabajo en segundo plano de singleton. Utiliza un identificador de instancia conocido para asegurarse de que solo existe una instancia.

```cs
[FunctionName("EnsureSingletonTrigger")]
public static async Task<HttpResponseMessage> Ensure(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    // Ensure only one instance is ever running at a time
    const string OrchestratorName = "MySingletonOrchestrator";
    const string InstanceId = "MySingletonInstanceId";

    var existingInstance = await starter.GetStatusAsync(InstanceId);
    if (existingInstance == null)
    {
        log.Info($"Creating singleton instance with ID = {InstanceId}...");
        await starter.StartNewAsync(OrchestratorName, InstanceId, input: null);
    }

    return starter.CreateCheckStatusResponse(req, InstanceId);
}
```

De forma predeterminada, los identificadores de instancia son identificadores únicos globales generados aleatoriamente. Sin embargo, observe que en este caso la función de desencadenador utiliza una variable `InstanceId` predefinida con un valor de `MySingletonInstanceId` para asignar previamente un identificador de instancia a la función de orquestador. Esto permite que el desencadenador compruebe si ya se está ejecutando la instancia conocida mediante una llamada a [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_).

Los detalles de implementación de la función de orquestador no importan realmente. Podría tratarse de una función de orquestador normal, con un principio y un final, o de una que se ejecuta sin fin (es decir, una [orquestación infinita](durable-functions-eternal-orchestrations.md)). Lo importante es que haya solo una instancia en ejecución cada vez.

> [!NOTE]
> Si la instancia de orquestación singleton se finaliza, tiene un error o se completa, no será posible volver a crearla con el mismo identificador. En esos casos, debe estar preparado para volver a crearla con un nuevo identificador de instancia.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Cómo llamar a suborquestaciones](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Ejecución de un singleton de ejemplo](durable-functions-counter.md)
