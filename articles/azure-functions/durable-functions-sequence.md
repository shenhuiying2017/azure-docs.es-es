---
title: 'Encadenamiento de funciones en Durable Functions: Azure'
description: "Obtenga información acerca de cómo ejecutar un ejemplo de Durable Functions que ejecute una secuencia de funciones."
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
ms.openlocfilehash: 0eb031893d53f09097b696e18f6c129be061136a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Encadenamiento de funciones en Durable Functions: Hello Sequence de ejemplo

El encadenamiento de funciones hace referencia al patrón de ejecución de una secuencia de funciones en un orden concreto. A menudo la salida de una función tiene que aplicarse a la entrada de otra función. En este artículo se explica un ejemplo que usa [Durable Functions](durable-functions-overview.md) para implementar un escenario de encadenamiento de funciones.

## <a name="prerequisites"></a>Requisitos previos

* Siga las instrucciones del artículo de [instalación de Durable Functions](durable-functions-install.md) para configurar el ejemplo.

## <a name="the-functions"></a>Funciones

En este artículo se explican las funciones siguientes en la aplicación de ejemplo:

* `E1_HelloSequence`: una función de orquestador que llama a `E1_SayHello` varias veces en una secuencia. Almacena las salidas de las llamadas de `E1_SayHello` y registra los resultados.
* `E1_SayHello`: una función de actividad que antepone una cadena con "Hello".

En las siguientes secciones se explican la configuración y el código que se utilizan para el desarrollo de Azure Portal. Al final del artículo se muestra el código para el desarrollo de Visual Studio.
 
## <a name="functionjson-file"></a>Archivo function.json

Si utiliza Azure Portal para el desarrollo, aquí tiene el contenido del archivo *function.json* para la función de orquestador. La mayoría de los archivos *function.json* de orquestador presentan un aspecto prácticamente idéntico a este.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Lo importante es el tipo de enlace `orchestrationTrigger`. Todas las funciones de orquestador deben usar este tipo de desencadenador.

> [!WARNING]
> Para cumplir la regla de "ninguna E/S" de las funciones de orquestador, no use ningún enlace de entrada o salida al utilizar el enlace de desencadenador `orchestrationTrigger`.  Si se necesitan otros enlaces de entrada o salida, deberían utilizarse en el contexto de funciones `activityTrigger` en su lugar.

## <a name="c-script"></a>Script de C#

El siguiente es el código fuente:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Todas las funciones de orquestación de C# deben tener un parámetro `DurableOrchestrationContext`, que existe en el ensamblado `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Si utiliza un script de C#, se puede hacer referencia al ensamblado mediante la notación `#r`. Este objeto de contexto le permite llamar a otras funciones de *actividad* y pasar parámetros de entrada mediante su método [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

El código llama a `E1_SayHello` tres veces en secuencia con distintos valores de parámetro. El valor devuelto de cada llamada se agrega a la lista `outputs`, que se devuelve al final de la función.

El archivo *function.json* para la función de actividad `E1_SayHello` es similar al de `E1_HelloSequence` salvo en que usa un tipo de enlace `activityTrigger` en lugar de un tipo de enlace `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Cualquier función llamada por una función de orquestación debe utilizar el enlace `activityTrigger`.

La implementación de `E1_SayHello` es una operación de formato de cadena relativamente sencilla.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Esta función tiene un parámetro [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), que usa para obtener la entrada que se le pasó mediante la llamada de la función de orquestador a [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_)>.

## <a name="running-the-orchestration"></a>Ejecución de la orquestación

Para ejecutar la orquestación `E1_HelloSequence`, realice la siguiente llamada HTTP.

```
POST http://{app-name}.azurewebsites.net/orchestrators/E1_HelloSequence
```
El resultado es una respuesta HTTP 202, similar a la siguiente (acortada para simplificar):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

En este punto, la orquestación se pone en cola y comienza a ejecutarse inmediatamente. La dirección URL en el encabezado `Location` puede usarse para comprobar el estado de la ejecución.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

El resultado es el estado de la orquestación. Se ejecuta y se completa con rapidez, por lo que se muestra con el estado *Completed* (Completado) con una respuesta similar a la siguiente (acortada para simplificar):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Como puede ver, el valor de `runtimeStatus` de la instancia es *Completed* (Completado) y `output` contiene el resultado serializado con JSON de la ejecución de la función de orquestador.

> [!NOTE]
> El punto de conexión HTTP POST que inició la función de orquestador se implementa en la aplicación de ejemplo como una función de desencadenador HTTP denominada "HttpStart". Puede implementar una lógica de inicio similar para otros tipos de desencadenadores, como `queueTrigger`, `eventHubTrigger` o `timerTrigger`.

Observe los registros de ejecución de la función. La función `E1_HelloSequence` se ha iniciado y completado varias veces debido al comportamiento de reproducción descrito en la [introducción](durable-functions-overview.md). Por otro lado, solo ha habido tres de ejecuciones de `E1_SayHello`, puesto que esas ejecuciones de función no se reproducen.

## <a name="visual-studio-sample-code"></a>Código de ejemplo de Visual Studio

Esta es la orquestación como archivo único de C# en un proyecto de Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Pasos siguientes

En este punto, tiene un conocimiento básico de los mecanismos principales de Durable Functions. Este ejemplo es muy básico y solo muestra algunas de las características disponibles. Los ejemplos siguientes son más realistas y muestran una mayor variedad de funciones.

> [!div class="nextstepaction"]
> [Ejecución del ejemplo de distribución ramificada de salida y de entrada](durable-functions-cloud-backup.md)
