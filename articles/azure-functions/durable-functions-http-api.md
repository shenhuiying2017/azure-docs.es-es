---
title: 'API de HTTP en Durable Functions: Azure'
description: "Aprenda a implementar API de HTTP en la extensión Durable Functions para Azure Functions."
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
ms.openlocfilehash: bb5361022e4c9693812753ae33df5aeb037b5aaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>API de HTTP en Durable Functions (Azure Functions)

La extensión Durable Task expone un conjunto de API de HTTP que sirve para realizar las siguientes tareas:

* Capturar el estado de una instancia de orquestación.
* Enviar un evento a una instancia de orquestación en espera.
* Terminar una instancia de orquestación en ejecución.

Cada una de estas API de HTTP son operaciones de webhook que se administran directamente mediante la extensión Durable Task. No son específicas de ninguna función de la aplicación de función.

> [!NOTE]
> Estas operaciones también se pueden invocar directamente mediante las API de administración de instancia de la clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Para más información, consulte el artículo sobre la [administración de instancias](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Detección de la dirección URL de la API de HTTP

La clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) clase expone una API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) que sirve para generar una carga de respuesta HTTP con vínculos a todas las operaciones compatibles. Este ejemplo de función que se desencadena mediante HTTP muestra cómo utilizar la API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Esta función de ejemplo genera los siguientes datos de respuesta JSON. El tipo de datos de todos los campos es `string`.

| Campo             |Descripción                           |
|-------------------|--------------------------------------|
| id                |Identificador de la instancia de orquestación. |
| statusQueryGetUri |Dirección URL del estado de la instancia de orquestación. |
| sendEventPostUri  |Dirección URL de generación del evento de la instancia de orquestación. |
| terminatePostUri  |Dirección URL de finalización de la instancia de orquestación. |

Este es un ejemplo de respuesta:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> El formato de las direcciones URL del webhook puede diferir en función de la versión del host de Azure Functions en ejecución. El ejemplo anterior es para el host de Azure Functions 2.0.

## <a name="async-operation-tracking"></a>Seguimiento de operaciones asincrónicas

La respuesta HTTP que se ha mencionado anteriormente está diseñada para ayudar a implementar API de HTTP asincrónicas de ejecución prolongada con Durable Functions. Esto se conoce a veces como *sondeo del patrón de consumo*. El flujo cliente/servidor funciona del siguiente modo:

1. El cliente emite una solicitud HTTP para iniciar un proceso de ejecución prolongada, como una función de orquestador.
2. El desencadenador HTTP de destino devuelve una respuesta HTTP 202 con un encabezado `Location` con el valor `statusQueryGetUri`.
3. El cliente sondea la dirección URL en el encabezado `Location`. Continúa viendo las respuestas HTTP 202 con encabezado `Location`.
4. Cuando la instancia termina (o se produce un error), el punto de conexión en el encabezado `Location` devuelve HTTP 200.

Este protocolo permite la coordinación de procesos de ejecución prolongada con clientes o servicios externos que admitan el sondeo de puntos de conexión HTTP y sigan al encabezado `Location`. Las partes fundamentales ya están integradas en la API de HTTP de Durable Functions.

> [!NOTE]
> De forma predeterminada, todas las acciones basadas en HTTP proporcionadas por [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) admiten el patrón estándar de operación asincrónica. Esto permite insertar una función durable de ejecución prolongada como parte del flujo de trabajo de Logic Apps. Más detalles sobre la compatibilidad de Logic Apps con los patrones asincrónicos de HTTP en la [documentación de los desencadenadores y las acciones del flujo de trabajo de Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Referencia a las API de HTTP

Todas las API de HTTP que implementa la extensión tienen los siguientes parámetros. El tipo de datos de todos los parámetros es `string`.

| Parámetro  | Tipo de parámetro  | Descripción |
|------------|-----------------|-------------|
| instanceId | URL             | Identificador de la instancia de orquestación. |
| taskHub    | Cadena de consulta    | Nombre de la [central de tareas](durable-functions-task-hubs.md). Si no se especifica, se toma el nombre de la central de tareas de la aplicación de función actual. |
| connection | Cadena de consulta    | **Nombre** de la cadena de conexión de la cuenta de almacenamiento. Si no se especifica, se toma el de la cadena de conexión predeterminada de la aplicación de función. |
| systemKey  | Cadena de consulta    | Clave de autorización necesaria para invocar la API. |

`systemKey`es una clave de autorización que el host de Azure Functions genera automáticamente. En concreto, concede acceso a las API de la extensión Durable Tasks y se administra igual que las [demás claves de autorización](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). La manera más sencilla de detectar el valor `systemKey` consiste en usar la API `CreateCheckStatusResponse` que se ha mencionado anteriormente.

Las siguientes secciones tratan las API de HTTP específicas que admite la extensión y ofrecen ejemplos de uso.

### <a name="get-instance-status"></a>Obtención del estado de la instancia

Para obtener el estado de una instancia de orquestación específica:

#### <a name="request"></a>Solicitud

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
GET /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="response"></a>Response

Se pueden devolver varios valores de código de estado.

* **HTTP 200 (correcto)**: la instancia especificada está en estado completado.
* **HTTP 202 (aceptado)**: la instancia especificada está en curso.
* **HTTP 400 (solicitud incorrecta)**: se produjo un error en la instancia especificada o esta se ha finalizado.
* **HTTP 404 (no se encuentra)**: la instancia especificada no existe o no ha empezado a ejecutarse.

La carga de respuesta para los casos **HTTP 200** y **HTTP 202** es un objeto JSON con los siguientes campos.

| Campo           | Tipo de datos | Descripción |
|-----------------|-----------|-------------|
| runtimeStatus   | cadena    | Estado en tiempo de ejecución de la instancia. Los valores son *En ejecución*, *Pendiente*, *Erróneo*, *Cancelado*, *Finalizado*, *Completado*. |
| input           | JSON      | Datos JSON usados para inicializar la instancia. |
| output          | JSON      | Salida JSON de la instancia. Este campo es `null` si el estado de la instancia no es Completado. |
| createdTime     | cadena    | Hora a la que se creó la instancia. Usa la notación ampliada de ISO 8601. |
| lastUpdatedTime | cadena    | Hora a la que se almacenó la instancia por última vez. Usa la notación ampliada de ISO 8601. |

Esta es una carga de respuesta de ejemplo (con formato para mejorar la legibilidad):

```json
{
  "runtimeStatus": "Completed",
  "input": null,
  "output": [
    "Hello Tokyo!",
    "Hello Seattle!",
    "Hello London!"
  ],
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T18:30:30Z"
}
```

La respuesta **HTTP 202** también incluye un encabezado de respuesta **Location** que hace referencia a la misma dirección URL que el campo `statusQueryGetUri` mencionado anteriormente.

### <a name="raise-event"></a>Generación de eventos

Para enviar un mensaje de notificación de eventos a una instancia de orquestación en ejecución:

#### <a name="request"></a>Solicitud

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
POST /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Los parámetros de solicitud de esta API incluyen el conjunto predeterminado mencionado anteriormente, así como los siguientes parámetros únicos.

| Campo       | Tipo de parámetro  | Tipo de datos | Descripción |
|-------------|-----------------|-----------|-------------|
| eventName   | URL             | cadena    | Nombre del evento al que espera la instancia de orquestación de destino. |
| {content}   | Contenido de la solicitud | JSON      | Carga del evento con formato JSON. |

#### <a name="response"></a>Response

Se pueden devolver varios valores de código de estado.

* **HTTP 202 (aceptado)**: se aceptó el evento generado para su procesamiento.
* **HTTP 400 (solicitud incorrecta)**: el contenido de la solicitud no era del tipo `application/json` o no tenía un valor JSON válido.
* **HTTP 404 (no se encuentra)**: no se encontró la instancia especificada.
* **HTTP 410 (no existe)**: la instancia especificada ha finalizado o se ha producido un error y no puede procesar los eventos generados.

Esta es una solicitud de ejemplo que envía la cadena JSON `"incr"` a una instancia en espera de un evento denominado **operation** (tomado del ejemplo del [contador](durable-functions-counter.md)):

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Las respuestas para esta API no tienen contenido.

### <a name="terminate-instance"></a>Finalización de instancias

Para terminar una instancia de orquestación en ejecución:

#### <a name="request"></a>Solicitud

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
DELETE /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Los parámetros de solicitud de esta API incluyen el conjunto predeterminado mencionado anteriormente, así como el siguiente parámetro único.

| Campo       | Tipo de parámetro  | Tipo de datos | Descripción |
|-------------|-----------------|-----------|-------------|
| reason      | Cadena de consulta    | cadena    | Opcional. Motivo de finalización de la instancia de orquestación. |

#### <a name="response"></a>Response

Se pueden devolver varios valores de código de estado.

* **HTTP 202 (aceptado)**: se aceptó la solicitud de finalización para su procesamiento.
* **HTTP 404 (no se encuentra)**: no se encontró la instancia especificada.
* **HTTP 410 (no existe)**: la instancia especificada se ha completado o ha producido un error.

Esta es una solicitud de ejemplo que finaliza una instancia en ejecución y especifica un motivo **buggy**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Las respuestas para esta API no tienen contenido.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a administrar los errores](durable-functions-error-handling.md)
