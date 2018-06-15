---
title: Estado de orquestación personalizada en Durable Functions - Azure
description: Obtenga información acerca de cómo configurar y usar el estado de orquestación personalizada en Durable Functions.
services: functions
author: kadimitr
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/24/2018
ms.author: azfuncdf
ms.openlocfilehash: 840b96b9cfdb28ca1b17f54698677f4d491342c8
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2018
ms.locfileid: "32310317"
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Estado de orquestación personalizada en Durable Functions (Azure Functions)

El estado de orquestación personalizada le permite establecer un valor de estado personalizado para la función del orquestador. Este estado se proporciona a través de HTTP GetStatus API o de `DurableOrchestrationClient.GetStatusAsync` API.

## <a name="sample-use-cases"></a>Casos de uso de ejemplo 

### <a name="visualize-progress"></a>Visualizar el progreso

Los clientes pueden comprobar el estado del punto de conexión y ver la interfaz de usuario del progreso que muestra la fase de ejecución actual. En el siguiente ejemplo se muestra el progreso de uso compartido:

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  var outputs = new List<string>();

  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
  context.SetCustomStatus("Tokyo");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
  context.SetCustomStatus("Seattle");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
  context.SetCustomStatus("London");

  // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
  return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
  return $"Hello {name}!";
}
```

A continuación, el cliente recibirá la salida de la orquestación solo si el campo `CustomStatus` está establecido en "London":

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
  [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
  [OrchestrationClient] DurableOrchestrationClientBase starter,
  string functionName,
  ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
      await Task.Delay(200);
      durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
      Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

### <a name="output-customization"></a>Personalización de salida 

Otro escenario interesante es segmentar los usuarios mediante la devolución de resultados de salida personalizados, en función de características o interacciones únicas. Con la ayuda del estado de orquestación personalizada, el código del lado cliente seguirá siendo genérico. Todas las modificaciones principales se realizarán en el lado servidor, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
} 
```

### <a name="instruction-specification"></a>Especificación de instrucciones 

El orquestador puede proporcionar instrucciones únicas a los clientes a través del estado personalizado. Las instrucciones del estado personalizado se asignarán a los pasos descritos en el código de orquestación:

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

## <a name="sample"></a>Muestra

En el ejemplo siguiente, el estado personalizado se establece en primer lugar;

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

Mientras se ejecuta la orquestación, los clientes externos pueden capturar este estado personalizado:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Los clientes verán la siguiente respuesta: 

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
>  La carga de estado personalizada se limita a 16 KB de texto JSON de UTF-16, ya que debe caber en una columna de Azure Table Storage. Los desarrolladores pueden usar el almacenamiento externo si necesitan una carga útil mayor.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtenga más información acerca de las API de HTTP en Durable Functions](durable-functions-http-api.md)


