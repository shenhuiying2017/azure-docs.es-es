---
title: 'Singletons con estado en Durable Functions: Azure'
description: "Aprenda a implementar un singleton con estado en la extensión Durable Functions para Azure Functions."
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
ms.openlocfilehash: 05099e868e62f612be0a3354eb8b339507ac7e4a
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Singletons con estado en Durable Functions: ejemplo de contador

Los singletons con estado son funciones de orquestador de larga duración (quizá permanentes) que almacenan el estado; otras funciones las pueden invocar y consultar. Los singletons con estado se parecen al [modelo de actor](https://en.wikipedia.org/wiki/Actor_model) en computación distribuida.

Aunque no son una implementación "de actor" en sí, las funciones de orquestador comparten muchas características en tiempo de ejecución. Por ejemplo, son globales, confiables y su ubicación es transparente; además, tienen estado y un único subproceso. Se trata de características que hacen que las implementaciones de actor auténticas sean especialmente útiles, pero sin la necesidad de un marco independiente.

Este artículo muestra cómo ejecutar el *contador* de ejemplo. El ejemplo muestra un objeto singleton que admite operaciones de *incremento* y *decremento* operaciones y se actualiza el estado interno en consecuencia.

## <a name="prerequisites"></a>Requisitos previos

* Siga las instrucciones del artículo de [instalación de Durable Functions](durable-functions-install.md) para configurar el ejemplo.
* En este artículo se da por supuesto que ya se ha leído el tutorial de [Hello Sequence](durable-functions-sequence.md) de ejemplo.

## <a name="scenario-overview"></a>Información general de escenario

El escenario de contador es sorprendentemente difícil de implementar con funciones sin estado normales. Una de las principales dificultades es administrar la **simultaneidad**. Las operaciones como el *incremento* y el *decremento* tienen que ser atómicas, de lo contrario, podría haber condiciones de carrera que provocaran su sobreescritura.

Usar una sola máquina virtual para hospedar los datos del contador es una opción, pero es costoso y la administración de la **confiabilidad** puede suponer una dificultad, ya que una sola máquina virtual podría reiniciarse periódicamente. Como alternativa, puede usar una plataforma distribuida con herramientas de sincronización como las concesiones de blobs para facilitar la administración de la simultaneidad, pero esto agrega gran **complejidad**.

Durable Functions facilita en gran medida este tipo de escenario, ya que las instancias de orquestación establecen afinidad con una sola máquina virtual y la ejecución de la función de orquestador siempre tiene un único subproceso. No solo eso, sino que además son de larga duración, tienen estado y responden a eventos externos. El código de ejemplo siguiente muestra cómo implementar este tipo de contador como función de orquestador de larga duración.

## <a name="the-sample-function"></a>Función de ejemplo

Este artículo le guía con el uso de la función **E3_Counter** en la aplicación de ejemplo.



## <a name="the-counter-orchestration"></a>Orquestación del contador

En las siguientes secciones se explica el código que se usa para el desarrollo de Visual Studio Code y Azure Portal.

### <a name="c-script"></a>Script de C#

Archivo function.json:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Counter/function.json)]

Archivo run.csx:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Counter/run.csx)]

### <a name="precompiled-c"></a>C# precompilado 

En las siguientes secciones se explica el código que se usa para el desarrollo de Visual Studio.

Este es el código que implementa la función de orquestador:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)]

### <a name="explanation-of-the-code"></a>Explicación del código

Esta función de orquestador básicamente hace lo siguiente:

1. Realiza escuchas de un evento externo denominado *operación* mediante [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_).
2. Incrementa o disminuye el variable local `counterState` dependiendo de la operación solicitada.
3. Reinicia el orquestador con el método [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_), al configurar el valor más reciente de `counterState` como nueva entrada.
4. Continúa ejecutándose indefinidamente o hasta que se reciba un mensaje *end*.

Este es un ejemplo de *orquestación infinita* &mdash;, es decir, que quizá no termina nunca. Responde a los mensajes que se le envían con el método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_), al que puede llamar cualquier función que no sea de orquestador.

Una característica única de esta función de orquestador es que, en realidad, no tiene historial: el método `ContinueAsNew` lo restablece después del procesamiento de cada evento. Es la mejor manera de implementar un orquestador de duración cualquiera. Mediante un bucle `while` se podría hacer que el historial de la función de orquestador creciera sin límites, para lo que se resultaría en un uso de memoria innecesariamente alto.

> [!NOTE]
> El método `ContinueAsNew` tiene otros casos de uso además de las orquestaciones infinitas. Para más información, consulte el artículo sobre las [orquestaciones infinitas](durable-functions-eternal-orchestrations.md).

## <a name="run-the-sample"></a>Ejecución del ejemplo

Puede iniciar la orquestación mediante el envío de la siguiente solicitud HTTP POST. Para permitir que `counterState` comience en cero (el valor predeterminado de `int`), no hay contenido en esta solicitud.

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

La instancia de **E3_Counter** se inicia e inmediatamente espera a que se le envíe un evento mediante `RaiseEventAsync` o el webhook HTTP POST **sendEventUrl** al que se hace referencia en la respuesta 202. Los valores válidos de `eventName` incluyen *incr*, *decr* y *end*.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

Puede ver los resultados de la operación "incr" al examinar los registros de la función en el portal de Azure Functions.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

Análogamente, si comprueba el estado del orquestador, verá que el campo `input` se ha establecido en el valor actualizado (1).

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

Puede seguir enviando nuevas operaciones a esta instancia; observará que su estado se actualiza en consecuencia. Si desea terminar la instancia, puede hacerlo mediante el envío de una operación *end*.

> [!WARNING]
> En el momento de escribir este artículo, hay condiciones de carrera conocidas al llamar a `ContinueAsNew` al procesar simultáneamente los mensajes, como eventos externos o solicitudes de terminación. Para la información más reciente sobre estas condiciones de carrera, consulte este [problema en GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/67).

## <a name="next-steps"></a>Pasos siguientes

En este ejemplo se muestra cómo controlar [eventos externos](durable-functions-external-events.md) e implementar [orquestaciones externas](durable-functions-eternal-orchestrations.md) en [singletons con estado](durable-functions-singletons.md). En el ejemplo siguiente se muestra cómo usar eventos externos y [temporizadores duraderos](durable-functions-timers.md) para controlar la interacción humana.

> [!div class="nextstepaction"]
> [Ejecución de la interacción humana de ejemplo](durable-functions-phone-verification.md)
