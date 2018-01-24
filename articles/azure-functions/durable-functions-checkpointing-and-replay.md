---
title: "Puntos de control y reproducción en Durable Functions: Azure"
description: "Aprenda cómo funcionan el establecimiento de puntos de control y la reproducción en la extensión Durable Functions para Azure Functions."
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
ms.openlocfilehash: b1bca62e256c1ede5df6888dd7c47ce2aa816bb9
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Puntos de control y reproducción en Durable Functions (Azure Functions)

Uno de los atributos clave de Durable Functions es la **ejecución confiable**. Las funciones de orquestador y de actividad pueden ejecutarse en máquinas virtuales distintas de un centro de datos y que estas o la infraestructura de red subyacente no sean confiables al 100 %.

A pesar de ello, Durable Functions garantiza una ejecución confiable de las orquestaciones. Lo hace mediante el uso de colas de almacenamiento para dirigir la invocación de las funciones y la ejecución del establecimiento de puntos de control periódico en tablas de almacenamiento (con un patrón de diseño en la nube conocido como [Abastecimiento de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Ese historial se puede reproducir para recompilar automáticamente el estado de una función de orquestador en memoria.

## <a name="orchestration-history"></a>Historial de orquestación

Supongamos que tiene la función de orquestador siguiente.

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

En cada instrucción `await`, Durable Task Framework establece los puntos de control del estado de ejecución de la función en Table Storage. Este estado es lo que se conoce como *historial de orquestación*.

## <a name="history-table"></a>Tabla del historial

Por lo general, Durable Task Framework hace lo siguiente en cada punto de control:

1. Guarda el historial de ejecución en las tablas de Azure Storage.
2. Pone en cola los mensajes para las funciones que el orquestador desea invocar.
3. Pone en cola los mensajes para el orquestador en sí,&mdash; por ejemplo, los de temporizador durable.

Una vez completado el punto de control, la función de orquestador se puede quitar de la memoria hasta que tenga más trabajo.

> [!NOTE]
> Azure Storage no proporciona garantías para la transacción de guardado de datos en Table Storage y las colas. Para controlar los errores, el proveedor de almacenamiento de Durable Functions usa patrones de *posible coherencia*. Estos patrones garantizan la ausencia de pérdida de datos en caso de bloqueo o pérdida de conectividad en medio de un punto de control.

Tras la finalización, el historial de la función mostrada anteriormente tiene un aspecto similar al siguiente Azure Table Storage (abreviado con fines ilustrativos):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Entrada | NOMBRE             | Resultado                                                    | Status | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Completed           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Notas sobre los valores de las columnas:
* **PartitionKey**: contiene el identificador de instancia de la orquestación.
* **EventType**: representa el tipo de evento. Puede ser uno de los siguientes tipos:
    * **OrchestrationStarted**: la función de orquestador se reanuda desde una instrucción await o se ejecuta por primera vez. La columna `Timestamp` se usa para rellenar el valor determinista para la API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime).
    * **ExecutionStarted**: la función de orquestador comenzó a ejecutarse por primera vez. Este evento también contiene la entrada de función en la columna `Input`.
    * **TaskScheduled**: se programó una función de actividad. El nombre de la función de actividad se captura en la columna `Name`.
    * **TaskCompleted**: función de actividad completada. El resultado de la función está en la columna `Result`.
    * **TimerCreated**: se ha creado un temporizador durable. La columna `FireAt` contiene la hora UTC programada a la que expira el temporizador.
    * **TimerFired**: se ha desencadenado un temporizador durable.
    * **EventRaised**: se envió un evento externo a la instancia de orquestación. La columna `Name` captura el nombre del evento y `Input`, la carga del evento.
    * **OrchestratorCompleted**: función de orquestador esperada.
    * **ContinueAsNew**: la función de orquestador se ha completado y se ha reiniciado con el nuevo estado. La columna `Result` contiene el valor, que se utiliza como entrada en la instancia reiniciada.
    * **ExecutionCompleted**: la función de orquestador se ejecutó hasta finalizar o que se produjera un error. Las salidas de la función o los detalles del error se almacenan en la columna `Result`.
* **Marca de tiempo**: marca de tiempo UTC del evento del historial.
* **Nombre**: nombre de la aplicación de función invocada.
* **Entrada**: entrada de la función con formato JSON.
* **Salida**: resultado de la función; es decir, el valor devuelto.

> [!WARNING]
> Aunque es útil como herramienta de depuración, no confíe en esta tabla. Puede cambiar con el progreso de la extensión Durable Functions.

Cada vez que se reanuda la función desde una instrucción `await`, Durable Task Framework vuelve a ejecutar la función de orquestador desde el principio. Con cada nueva ejecución, consulta el historial de ejecución para determinar si se ha realizado la operación asincrónica actual.  En caso afirmativo, el marco reproduce la salida de esa operación inmediatamente y pasa a la siguiente instrucción `await`. Este proceso continúa hasta que se haya reproducido todo el historial, momento en que todas las variables locales de la función de orquestador se restablecen los valores anteriores.

## <a name="orchestrator-code-constraints"></a>Restricciones de código del orquestador

El comportamiento de reproducción crea restricciones sobre el tipo de código que se puede escribir en una función de orquestador:

* El código de orquestador debe ser **determinista**. Se reproducirá varias veces y debe producir el mismo resultado siempre. Por ejemplo, sin llamadas directas para obtener la fecha y hora actuales o números aleatorios, generar identificadores únicos globales aleatorios ni llamadas a puntos de conexión remotos.

  Si el código de orquestador necesita la fecha y hora actuales, debe usar la API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime), cuya reproducción es segura.

  Las operaciones no deterministas deben realizarse en las funciones de actividad. Esto incluye cualquier interacción con otros enlaces de entrada o salida. Así se garantiza que los valores no deterministas se generan una vez durante la primera ejecución y se guardan en el historial de ejecución. Las ejecuciones posteriores usarán el valor guardado automáticamente.

* El código de orquestador debe ser **sin bloqueo**. Por ejemplo, eso significa ninguna E/S y ninguna llamada a `Thread.Sleep` o API equivalentes.

  Si un orquestador necesita un retraso,puede usar la API [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_).

* El código de orquestador **no debe iniciar operaciones asincrónicas**, excepto con la API [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). `Task.Run`, `Task.Delay` o `HttpClient.SendAsync` son algunos ejemplos. Durable Task Framework ejecuta código de orquestador en un solo subproceso y no puede interactuar con otros puedan estar programados por otras API asincrónicas.

* **Deben evitarse los bucles infinitos** en el código de orquestador. Como Durable Task Framework guarda el historial de ejecución a medida que avanza la función de orquestación, un bucle infinito podría provocar que una instancia de orquestador se quedara sin memoria. Para escenarios de bucle infinito, use API como [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) para reiniciar la ejecución de la función y descartar el historial de ejecución anterior.

Aunque estas restricciones pueden intimidar al principio, en la práctica se siguen fácilmente. Durable Task Framework intenta detectar las infracciones de las reglas anteriores y produce `NonDeterministicOrchestrationException`. Sin embargo, este comportamiento de detección no es confiable al 100 % y no debe depender de él.

> [!NOTE]
> Todas estas reglas se aplican solo a funciones que desencadena el enlace `orchestrationTrigger`. Las funciones de actividad que desencadena el enlace `activityTrigger` y las que usan el enlace `orchestrationClient` no presentan estas limitaciones.

## <a name="durable-tasks"></a>Tareas durables

> [!NOTE]
> En esta sección se describen los detalles internos de implementación de Durable Task Framework. Puede utilizar Durable Functions sin necesidad de conocer esta información. Su único fin es ayudarle a entender el comportamiento de reproducción.

Las tareas que se pueden esperar de forma segura en las funciones de orquestador en ocasiones se conocen como *tareas durables*. Son tareas que se crean y se administran mediante Durable Task Framework. Las tareas que devuelven `CallActivityAsync`, `WaitForExternalEvent`, y `CreateTimer` son ejemplos de ello.

Estas *tareas durables* se administran internamente mediante una lista de objetos `TaskCompletionSource`. Durante la reproducción, estas tareas se crean como parte de la ejecución del código de orquestador y se llevan a cabo según el distribuidor va enumerando los eventos correspondientes del historial. Todo esto se realiza sincrónicamente con un único subproceso hasta que se reproduce todo el historial. Las tareas durables, que no se completan al final de la reproducción del historial, llevan las acciones correspondientes asociadas. Por ejemplo, un mensaje puede ponerse en cola para llamar a una función de actividad.

El comportamiento de ejecución descrito aquí le ayudará a entender por qué el código de la función de orquestador no debe ejecutar la instrucción `await` para una tarea que no sea durable: el subproceso del distribuidor no puede esperar a que finalice y cualquier devolución de llamada de esa tarea podría dañar el estado de seguimiento de la función de orquestador. Existen comprobaciones en tiempo de ejecución disponibles para evitar esto.

Si desea más información acerca de cómo Durable Task Framework ejecuta funciones de orquestador, lo mejor es consultar el [código fuente de la instancia de Durable Task en GitHub](https://github.com/Azure/durabletask). En concreto, consulte [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) y [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Información acerca de la administración de instancias](durable-functions-instance-management.md)
