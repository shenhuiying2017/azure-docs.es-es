---
title: "Diagnóstico con Durable Functions: Azure"
description: "Aprenda a diagnosticar problemas con la extensión Durable Functions para Azure Functions."
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
ms.openlocfilehash: 5ebab8660dfe21984e1a7f9a1cb925aea60de213
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnóstico con Durable Functions (Azure Functions)

Existen varias opciones para diagnosticar problemas con [Durable Functions](durable-functions-overview.md). Algunas de ellas son las mismas que para las funciones normales y otras son exclusivas de Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../application-insights/app-insights-overview.md) es la manera recomendada para realizar el diagnóstico y la supervisión en Azure Functions. Lo mismo se aplica a Durable Functions. Para introducción al aprovechamiento de Application Insights en las aplicaciones de función, consulte [Supervisión de Azure Functions](functions-monitoring.md).

La extensión Azure Durable Functions también emite *eventos de seguimiento* que permiten realizar el seguimiento de la ejecución de un extremo de una orquestación al otro. Estos se pueden encontrar y consultar mediante la herramienta [Application Insights Analytics](../application-insights/app-insights-analytics.md) de Azure Portal.

### <a name="tracking-data"></a>Datos de seguimiento

Cada evento de ciclo de vida de una instancia de orquestación provoca uno de seguimiento que se escribirá en la colección **Seguimientos** de Application Insights. Este evento contiene una carga **customDimensions** con varios campos.  Los nombres de campo llevan `prop__` antepuesto.

* **hubName**: nombre de la central de tareas donde se ejecutan las orquestaciones.
* **appName**: nombre de la aplicación de función. Es útil cuando tiene varias aplicaciones de función que compartan la misma instancia de Application Insights.
* **slotName**: la [ranura de implementación](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) donde se ejecuta la aplicación de función actual. Es útil cuando se aprovechan las ranuras de implementación para modificar las orquestaciones.
* **functionName**: nombre de la función de actividad o de orquestador.
* **functionType**: tipo de función, como **Orquestador** o **Actividad**.
* **instanceId**: identificador único de la instancia de orquestación.
* **state**: estado de ejecución del ciclo de vida de la instancia. Los valores válidos son:
    * **Scheduled**: la función estaba programada para ejecutarse, pero no se ha iniciado todavía la ejecución.
    * **Started**: la función ha empezado a ejecutarse, pero todavía no está en espera ni completada aún.
    * **Awaited**: el orquestador ha programado trabajo y está esperando a que finalice.
    * **Listening**: el orquestador está escuchando una notificación de eventos externa.
    * **Completed**: la función se ha completado correctamente.
    * **Failed**: se ha producido un error en la función.
* **reason**: información adicional asociada al evento de seguimiento. Por ejemplo, si una instancia está esperando una notificación de eventos externa, este campo indica el nombre del evento al que espera. Si se produce un error en una función, contendrá los detalles del error.
* **isReplay**: valor booleano que indica si el evento de seguimiento va a volver a reproducir la ejecución.
* **extensionVersion**: versión de la extensión Durable Task. Se trata de datos especialmente importantes al informar de posibles errores en la extensión. Las instancias de ejecución prolongada pueden notificar varias versiones si se produce una actualización mientras se están ejecutando. 

El nivel de detalle de los datos de seguimiento que se emiten para Application Insights se configura en la sección `logger` del archivo `host.json`.

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

De forma predeterminada, se emiten todos los eventos de seguimiento. Se puede reducir el volumen de datos; para ello, configure `Host.Triggers.DurableTask` en `"Warning"` o `"Error"`, en cuyo caso los eventos de seguimiento solo se emitirá para las situaciones excepcionales.

> [!WARNING]
> De forma predeterminada, el sistema en tiempo de ejecución de Azure Functions muestrea la telemetría de Application Insights para evitar que se emitan datos con demasiada frecuencia. Esto puede provocar que se pierda información de seguimiento cuando se producen muchos eventos de ciclo de vida en un breve período de tiempo. En el artículo [Supervisión de Azure Functions](functions-monitoring.md#configure-sampling) se explica cómo configurar este comportamiento.

### <a name="single-instance-query"></a>Consultas en una sola instancia

La consulta siguiente muestra datos históricos de seguimiento de una única instancia de la función de orquestación [Hello Sequence](durable-functions-sequence.md). Se escribe con el [lenguaje de consulta de Application Insights](https://docs.loganalytics.io/docs/Language-Reference). Filtra la ejecución de la reproducción de manera que se muestra solo la ruta de ejecución *lógica*.

```AIQL
let targetInstanceId = "bf71335b26564016a93860491aa50c7f";
let start = datetime(2017-09-29T00:00:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| where isReplay == false
| where instanceId == targetInstanceId
| project timestamp, functionName, state, instanceId, appName = cloud_RoleName
```
El resultado es una lista de eventos de seguimiento que muestran la ruta de ejecución de la orquestación, funciones de actividad incluidas.

![Consultas en Application Insights](media/durable-functions-diagnostics/app-insights-single-instance-query.png)

> [!NOTE]
> Algunos de estos eventos de seguimiento pueden estar fuera de servicio por la falta de precisión en la columna `timestamp`. El seguimiento de esto se realiza en GitHub como [problema n.º 71](https://github.com/Azure/azure-functions-durable-extension/issues/71).

### <a name="instance-summary-query"></a>Consultas de resumen de instancias

La consulta siguiente muestra el estado de todas las instancias de orquestación que se ejecutaron en un intervalo de tiempo especificado.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay == false
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
El resultado es una lista de identificadores de instancia y su estado actual en tiempo de ejecución.

![Consultas en Application Insights](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registro

Es importante tener en cuenta el comportamiento de reproducción del orquestador al escribir registros directamente desde una función de orquestador. Por ejemplo, considere la función de orquestador siguiente:

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

Los datos de registro resultantes se parecerán a lo siguiente:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Recuerde que, aunque los registros notifiquen una llamada a F1, F2 y F3, a estas funciones solo se las llama *realmente* la primera vez que se encuentran. Las llamadas posteriores que se producen durante la reproducción se omiten y las salidas se reproducen en la lógica del orquestador.

Si desea registrar únicamente en la ejecución sin reproducción, puede escribir una expresión condicional para que registre solo si `IsReplaying` es `false`. Considere el ejemplo anterior, pero esta vez con comprobaciones de reproducción.

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
Con este cambio, la salida del registro es la siguiente:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="debugging"></a>Depuración

Azure Functions admite la depuración directa de código de función y esa misma compatibilidad se traslada a Durable Functions, tanto si se ejecuta en Azure o localmente. Sin embargo, hay algunos comportamientos que tener en cuenta al depurar:

* **Reproducción**: las funciones de orquestador suelen reproducir cuando se reciben nuevas entradas. Esto significa que una sola ejecución *lógica* de una función de orquestador puede resultar en alcanzar el mismo punto de interrupción varias veces, especialmente si se establece al principio en el código de la función.
* **Espera**: siempre que se encuentre un `await`, devuelve el control al distribuidor de Durable Task Framework. Si se trata de la primera vez que se encuentra una instrucción `await` determinada, la tarea asociada *nunca* se reanuda. En realidad, como la tarea nunca se reanuda, no se puede *omitir* la instrucción await (F10 en Visual Studio). La omisión solo funciona cuando se reproduce una tarea.
* **Tiempo de expiración los mensajes**: Durable Functions usa mensajes de cola internos para dirigir la ejecución de las funciones tanto de orquestador como de actividad. En un entorno de varias máquinas virtuales, iniciar la depuración durante largos períodos de tiempo podría provocar que otra máquina virtual recogiera el mensaje, lo que provocaría la duplicación de la ejecución. Este comportamiento también existe para las funciones con desencadenador de cola normales, pero es importante señalarlo en este contexto, ya que las colas son un detalle de la implementación.

> [!TIP]
> Al establecer puntos de interrupción, si desea interrumpir solo la ejecución sin reproducción, puede establecer un punto de interrupción condicional que solo se active si `IsReplaying` es `false`.

## <a name="storage"></a>Storage

De forma predeterminada, Durable Functions almacena el estado en Azure Storage. Esto significa que puede inspeccionar el estado de las orquestaciones con herramientas como el [Explorador de Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de pantalla del Explorador de Azure Storage](media/durable-functions-diagnostics/storage-explorer.png)

Esto es útil para la depuración, porque verá el estado concreto de las orquestaciones. También se pueden examinar los mensajes de las colas para conocer el trabajo pendiente (o bloqueado, en algunos casos).

> [!WARNING]
> Aunque es conveniente ver el historial de ejecución en Table Storage, no se fie de esta tabla. Puede cambiar con el progreso de la extensión Durable Functions.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a usar los temporizadores de larga duración](durable-functions-timers.md)