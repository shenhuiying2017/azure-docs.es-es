---
title: 'Suborquestaciones en Durable Functions: Azure'
description: "Aprenda a llamar orquestaciones desde otras orquestaciones en la extensión Durable Functions para Azure Functions."
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
ms.openlocfilehash: 5184bef81d1cd6ca7b41c1634def24031a4a5942
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2017
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Suborquestaciones en Durable Functions (Azure Functions)

Además de llamar a funciones de actividad, las funciones de orquestador pueden llamar a otras funciones de orquestador. Por ejemplo, puede crear una orquestación mayor a partir de una biblioteca de funciones de orquestador. También puede ejecutar varias instancias de una función de orquestador en paralelo.

Una función de orquestador puede llamar a otra función de orquestador mediante una llamada a los métodos [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) o [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_). En el artículo [Control de errores y compensación](durable-functions-error-handling.md#automatic-retry-on-failure) se ofrece más información sobre los reintentos automáticos.

Las funciones de suborquestador se comportan como funciones de actividad desde la perspectiva del llamador. Pueden devolver un valor, producir una excepción y ser esperadas por la función de orquestador primaria.

## <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un escenario de IoT ("Internet de las cosas") donde hay varios dispositivos que deben aprovisionarse. Para cada uno de los dispositivos, debe producirse una orquestación concreta, lo que podría asemejarse a lo siguiente:

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

Esta función de orquestador puede utilizarse tal cual para un aprovisionamiento de dispositivos único o puede ser parte de una orquestación mayor. En el último caso, la función de orquestador primaria puede programar instancias de `DeviceProvisioningOrchestration` mediante la API `CallSubOrchestratorAsync`.

El siguiente es un ejemplo que muestra cómo ejecutar varias funciones de orquestador en paralelo.

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información acerca de las centrales de tareas y cómo configurarlas](durable-functions-task-hubs.md)
