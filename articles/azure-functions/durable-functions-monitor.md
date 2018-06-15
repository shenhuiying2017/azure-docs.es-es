---
title: 'Supervisiones en Durable Functions: Azure'
description: Aprenda a implementar un monitor de estado con la extensión Durable Functions para Azure Functions.
services: functions
author: kashimiz
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
ms.openlocfilehash: 9cb7a076ea922b9868bd439d160aec96f044e3b6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32157482"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Escenario de supervisión en Durable Functions: ejemplo de supervisión meteorológica

El patrón de supervisión hace referencia a un proceso *periódico* flexible de un flujo de trabajo; por ejemplo, realizar un sondeo hasta que se cumplan determinadas condiciones. En este artículo se explica un ejemplo que usa [Durable Functions](durable-functions-overview.md) para implementar la supervisión.

## <a name="prerequisites"></a>requisitos previos

* [Instale Durable Functions](durable-functions-install.md).
* Complete el tutorial de [Hello Sequence](durable-functions-sequence.md).

## <a name="scenario-overview"></a>Información general de escenario

En este ejemplo se supervisan las condiciones meteorológicas actuales de una ubicación y alerta al usuario por SMS cuando el cielo está despejado. Puede utilizar una función regular desencadenada por temporizador para comprobar el tiempo y enviar alertas. Sin embargo, un problema con este enfoque es la **administración del ciclo de vida**. Si solo se debe enviar una alerta, la supervisión necesita deshabilitarse después de que se detecte un clima despejado. El patrón de supervisión puede finalizar su propia ejecución, entre otras ventajas:

* Las supervisiones se ejecutan en intervalos, no en programaciones: un desencadenador en temporizador *se ejecuta* cada hora; una supervisión *espera* una hora entre acciones. Las acciones de una supervisión no se solaparán a menos que se especifique, lo que puede ser importante para tareas de ejecución prolongada.
* Las supervisiones pueden tener intervalos dinámicos: el tiempo de espera puede cambiar en función de alguna condición.
* Las supervisiones pueden finalizar cuando se cumple alguna condición o cuando otro proceso las finaliza.
* Las supervisiones pueden aceptar parámetros. El ejemplo muestra cómo se puede aplicar el mismo proceso de supervisión meteorológica a cualquier ubicación y número de teléfono solicitados.
* Las supervisiones son escalables. Debido a que cada supervisión es una instancia de orquestación, se pueden crear múltiples supervisiones sin tener que crear nuevas funciones ni definir más código.
* Las supervisiones se integran fácilmente en flujos de trabajo mayores. Una supervisión puede ser una sección de una función de orquestación más compleja, o una [suborquestación](https://docs.microsoft.com/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Configuración de la integración de Twilio

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Configuración de la integración de Weather Underground

Este ejemplo implica el uso de la API de Weather Underground para comprobar las condiciones meteorológicas actuales de una ubicación.

Lo primero que necesita es una cuenta de Weather Underground. Puede crear una gratis en [https://www.wunderground.com/signup](https://www.wunderground.com/signup). Cuando tenga la cuenta, tendrá que adquirir una clave de API. Puede hacerlo si visita [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api) y, después, si selecciona la configuración de la clave. El plan para desarrolladores Stratus es gratis y suficiente para ejecutar este ejemplo.

Cuando tenga una clave de API, agregue la siguiente **configuración de la aplicación** a la aplicación de función.

| Nombre del valor de configuración de la aplicación | Descripción del valor |
| - | - |
| **WeatherUndergroundApiKey**  | La clave de API de Weather Underground. |

## <a name="the-functions"></a>Funciones

En este artículo se explican las funciones siguientes en la aplicación de ejemplo:

* `E3_Monitor`: una función del orquestador que llama a `E3_GetIsClear` periódicamente. Llama a `E3_SendGoodWeatherAlert` si `E3_GetIsClear` devuelve true.
* `E3_GetIsClear`: una función de actividad que comprueba las condiciones meteorológicas actuales de una ubicación.
* `E3_SendGoodWeatherAlert`: una función de la actividad que envía un mensaje SMS por Twilio.

En las siguientes secciones se explican la configuración y el código que se utilizan para el scripting C#. Al final del artículo se muestra el código para el desarrollo de Visual Studio.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>La orquestación de supervisión meteorológica (código de ejemplo de Azure Portal y Visual Studio Code)

La función **E3_Monitor** utiliza la norma *function.json* para las funciones de orquestador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Este es el código que implementa la función:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

Esta función de orquestador realiza las acciones siguientes:

1. Obtiene la función **MonitorRequest** que consta de la *ubicación* para supervisar y el  *número de teléfono* al que se enviará una notificación por SMS.
2. Determina el tiempo de expiración de la supervisión. El ejemplo utiliza un valor modificable por brevedad.
3. Llama a **E3_GetIsClear** para determinar si el cielo está despejado en la ubicación solicitada.
4. Si está despejado, llama a **E3_SendGoodWeatherAlert** para enviar una notificación por SMS al número de teléfono solicitado.
5. Crea un temporizador duradero para reanudar la orquestación en el siguiente intervalo de sondeo. El ejemplo utiliza un valor modificable por brevedad.
6. Continúa ejecutándose hasta que la función [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) pasa la hora de expiración de la supervisión o una alerta por SMS.

Para ejecutar simultáneamente varias instancias del orquestador, envíe varias funciones **MonitorRequests**. Se puede especificar la ubicación que se va a supervisar, así como el número de teléfono para enviar una alerta por SMS.

## <a name="strongly-typed-data-transfer"></a>Transferencia de datos fuertemente tipados

El orquestador requiere varios fragmentos de datos, por lo que se utilizan [objetos POCO compartidos](functions-reference-csharp.md#reusing-csx-code) para la transferencia de datos fuertemente tipados: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

## <a name="helper-activity-functions"></a>Funciones auxiliares de actividad

Al igual que otros ejemplos, las funciones auxiliares de actividad son básicamente funciones normales que usan el enlace del desencadenador `activityTrigger`. La función **E3_GetIsClear** obtiene las condiciones meteorológicas actuales utilizando la API de Weather Underground y determina si el cielo está despejado. *function.json* se define como sigue:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

Y esta es la implementación. Al igual que los objetos POCO utilizados para la transferencia de datos, la lógica para controlar la llamada a la API y analizar el JSON de respuesta se abstrae en una clase compartida. Puede encontrarlo como parte del [código de ejemplo de Visual Studio](#run-the-sample).

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

La función **E3_SendGoodWeatherAlert** usa el enlace de Twilio para enviar un mensaje SMS que notifica al usuario final que es un buen momento para dar un paseo. La *function.json* es simple:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

Y este es el código que envía el mensaje SMS:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

## <a name="run-the-sample"></a>Ejecución del ejemplo

Con las funciones desencadenadas mediante HTTP del ejemplo, puede iniciar la orquestación con el envío de la siguiente solicitud HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "Location": { "City": "Redmond", "State": "WA" }, "Phone": "+1425XXXXXXX" }
```
```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Se inicia la instancia de **E3_Monitor** y consulta las condiciones meteorológicas actuales para la ubicación solicitada. Si el tiempo está despejado, llama a una función de actividad para enviar una alerta; de lo contrario, establece un temporizador. Cuando expire el temporizador, se reanudará la orquestación.

Puede ver los resultados de la actividad de orquestación al examinar los registros de la función en el portal de Azure Functions.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

La orquestación [finalizará](durable-functions-instance-management.md#terminating-instances) una vez que se alcance el tiempo de espera o se detecten cielos despejados. También puede usar `TerminateAsync` dentro de otra función o invocar el webhook HTTP POST **terminatePostUri** al que se hace referencia en la respuesta 202 anterior y sustituir `{text}` por el motivo de la finalización:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Código de ejemplo de Visual Studio

Esta es la orquestación como archivo único de C# en un proyecto de Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Pasos siguientes

Este ejemplo ha demostrado cómo usar Durable Functions para supervisar el estado de un origen externo mediante [temporizadores durables](durable-functions-timers.md) y lógica condicional. En el ejemplo siguiente se muestra cómo usar eventos externos y [temporizadores duraderos](durable-functions-timers.md) para controlar la interacción humana.

> [!div class="nextstepaction"]
> [Ejecución de la interacción humana de ejemplo](durable-functions-phone-verification.md)
