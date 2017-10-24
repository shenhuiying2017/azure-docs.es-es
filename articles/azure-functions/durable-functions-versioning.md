---
title: 'Control de versiones en Durable Functions: Azure'
description: "Aprenda a implementar el control de versiones en la extensión Durable Functions para Azure Functions."
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
ms.openlocfilehash: 0a86e4a87f5ec23c284aa4e5cfb2c67622b3ebe9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Control de versiones en Durable Functions (Azure Functions)

Resulta inevitable agregar, eliminar y cambiar funciones durante el ciclo de vida de una aplicación. [Durable Functions](durable-functions-overview.md) permite el encadenamiento de funciones entre sí de maneras que no eran posibles anteriormente. Este encadenamiento afecta a la manera de controlar las versiones.

## <a name="how-to-handle-breaking-changes"></a>Cómo controlar los cambios importantes

Hay varios ejemplos de cambios importantes que se deben tener en cuenta. En este artículo se abordan los más habituales. Todos ellos tienen en común que tanto las orquestaciones de función nuevas como las ya existentes se ven afectadas por los cambios realizados en el código de función.

### <a name="changing-activity-function-signatures"></a>Cambiar las firmas de función de actividad

Un cambio de firma hace referencia a un cambio en el nombre, la entrada o la salida de una función. Si este tipo de cambio se realiza en una función de actividad, podría interrumpir la función de orquestador que dependa de él. Si actualiza la función de orquestador para dar cabida a este cambio, se podrían interrumpir las instancias en curso existentes.

Por ejemplo, supongamos que tiene la siguiente función:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Esta sencilla función toma los resultados de **Foo** y los pasa a **Bar**. Supongamos que es necesario cambiar el valor devuelto de **Foo** de `bool` a `int` para admitir una mayor variedad de valores de resultado. El resultado se parecerá al siguiente:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Este cambio funciona bien en todas las nuevas instancias de la función de orquestador, pero interrumpe todas las instancias en curso. Por ejemplo, considere el caso en el que una instancia de orquestación llama a **Foo**, obtiene un valor booleano y, a continuación, establece puntos de control. Si el cambio de firma se implementa en este momento, se producirá un error en la instancia con los puntos de control inmediatamente después de que se reanude y reproduzca la llamada a `context.CallActivityAsync<int>("Foo")`. Esto se debe a que el resultado de la tabla de historial es `bool`, pero el nuevo código intenta deserializarlo en `int`.

Esta es solo una de las muchas maneras en las que un cambio de firma puede interrumpir las instancias existentes. En general, si un orquestador necesita cambiar la manera en que llama a una función, probablemente el cambio sea problemático.

### <a name="changing-orchestrator-logic"></a>Cambiar la lógica del orquestador

El otro tipo de problemas de versiones se produce al cambiar el código de función del orquestador de forma que confunda la lógica de reproducción con instancias en curso.

Considere la función de orquestador siguiente:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ahora supongamos que desea realizar un cambio en apariencia inofensivo para agregar otra llamada de función.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Este cambio agrega una nueva llamada de función a **SendNotification** entre **Foo** y **Bar**. No hay ningún cambio de firma. El problema surge cuando se reanuda una instancia existente desde la llamada a **Bar**. Durante la reproducción, si la llamada original a **Foo** devolvió `true`, la reproducción del orquestador llamará a **SendNotification**, que no está en su historial de ejecución. En consecuencia, se produce un error de Durable Task Framework con la excepción `NonDeterministicOrchestrationException` porque ha encontrado una llamada a **SendNotification** cuando esperaba una llamada a **Bar**.

## <a name="mitigation-strategies"></a>Estrategias de mitigación

Estas son algunas de las estrategias para tratar los desafíos que plantea el control de versiones:

* No hacer nada
* Detener todas las instancias en curso
* Implementaciones en paralelo

### <a name="do-nothing"></a>No hacer nada

La manera más sencilla de controlar un cambio importante es dejar que se produzca el error en las instancias de orquestación en curso. Las nuevas instancias ejecutan correctamente el código modificado.

Que esto suponga un problema dependerá de la importancia de sus instancias en curso. Si se encuentra en una fase de desarrollo activo y no le preocupan las instancias en curso, esto podría ser suficiente. Sin embargo, deberá tener en cuenta que su canalización de diagnóstico presentará errores y excepciones. Si desea evitar estos inconvenientes, considere la posibilidad de utilizar otras opciones de control de versiones.

### <a name="stop-all-in-flight-instances"></a>Detener todas las instancias en curso

Otra opción es detener todas las instancias en curso. Esto puede realizarse borrando el contenido de las colas **de control** y **de elementos de trabajo** internas. Las instancias se quedarán definitivamente donde están, pero esto evitará que su telemetría se llene de mensajes de error. Esta opción es la ideal para un desarrollo de prototipos rápido.

> [!WARNING]
> Los detalles de estas colas pueden cambiar con el tiempo, por lo que no debe dependerse de esta técnica para cargas de trabajo de producción.

### <a name="side-by-side-deployments"></a>Implementaciones en paralelo

El método menos propenso a errores para garantizar que los cambios importantes se implementen con seguridad consiste en implementarlos en paralelo con sus versiones anteriores. Esto puede realizarse mediante cualquiera de las técnicas siguientes:

* Implemente todas las actualizaciones como funciones completamente nuevas (nuevos nombres).
* Implemente todas las actualizaciones como una nueva aplicación de función con una cuenta de almacenamiento diferente.
* Implemente una nueva copia de la aplicación de función, pero con un nombre `TaskHub` actualizado. Este es la técnica recomendada.

### <a name="how-to-change-task-hub-name"></a>Cómo cambiar el nombre de la central de tareas

La central de tareas se puede configurar en el archivo *host.json* de la siguiente manera:

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

El valor predeterminado es `DurableFunctionsHub`.

Todas las entidades de Azure Storage reciben su nombre según el valor de configuración de `HubName`. Al asignar un nuevo nombre a la central de tareas, garantiza que se creen colas y una tabla de historial independientes para la nueva versión de la aplicación.

Se recomienda que implemente la nueva versión de la aplicación de función en una nueva [ranura de implementación](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Las ranuras de implementación permiten ejecutar varias copias de la aplicación de función en paralelo con solo una de ellas como ranura de *producción* activa. Cuando desee exponer la nueva lógica de orquestación en su infraestructura existente, será tan sencillo como intercambiar la nueva versión en la ranura de producción.

> [!NOTE]
> Esta estrategia funciona mejor cuando se usan desencadenadores HTTP y webhook para las funciones de orquestador. Para desencadenadores que no son HTTP, como las colas o Event Hubs, la definición del desencadenador debe derivar de una configuración de aplicación que se actualiza como parte de la operación de intercambio.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información acerca de cómo controlar el rendimiento y escalar problemas](durable-functions-perf-and-scale.md)
