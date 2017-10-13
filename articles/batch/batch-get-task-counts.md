---
title: "Supervisión del progreso de un trabajo mediante el recuento de tareas por estado: Azure Batch | Microsoft Docs"
description: "Supervise el progreso de un trabajo mediante una llamada a la operación Get Task Counts para hacer el recuento de tareas de un trabajo. Puede obtener un recuento de las tareas activas, en ejecución y completadas, y de las tareas que se realizaron correctamente o no."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: tamram
ms.openlocfilehash: ceff59d7063b60a1344a47489d3d73e0e8ee07df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>Recuento de tareas por estado para supervisar el progreso de un trabajo (versión preliminar)

Azure Batch proporciona una manera eficaz de supervisar el progreso de un trabajo mientras este ejecuta sus tareas. Puede llamar a la operación [Get Task Counts][rest_get_task_counts] para averiguar cuántas tareas están en un estado activo, en ejecución o completado, y cuántas se realizaron correctamente o no. Si cuenta el número de tareas en cada estado, podrá mostrar más fácilmente el progreso del trabajo a un usuario o detectar retrasos o errores inesperados que pueden afectar a la tarea.

> [!IMPORTANT]
> La operación Get Task Counts se encuentra actualmente en la versión preliminar y aún no está disponible en Azure Government, Azure China y Azure Alemania. 
>
>

## <a name="how-tasks-are-counted"></a>Recuento de las tareas

La operación Get Task Counts hace un recuento de las tareas por estado, como se indica a continuación:

- Una tarea se cuenta como **activa** cuando está en cola y se puede ejecutar, pero no está asignada actualmente a ningún nodo de proceso. También se considera una tarea como **activa** si depende de una tarea primaria que no se ha completado. Para más información sobre las dependencias de tareas, consulte [Creación de dependencias de tareas para ejecutar las tareas que dependan de otras tareas](batch-task-dependencies.md). 
- Una tarea se cuenta como **en ejecución** cuando ya se ha asignado a un nodo de proceso, pero no se ha completado. Una tarea se cuenta como **en ejecución** cuando su estado es `preparing` o `running`, tal y como indica la operación [Get information about a task][rest_get_task].
- Una tarea se cuenta como **completada** cuando ya no es apta para su ejecución. Una tarea que se cuenta como **completada** normalmente ha finalizado correctamente, o ha finalizado de forma incorrecta pero ha agotado su límite de reintentos. 

La operación Get Task Counts también informa de cuántas tareas se realizaron correctamente o no. Batch determina si una tarea se ha realizado correctamente o no comprobando la propiedad **result** de la propiedad [executionInfo] [https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo]:

    - Una tarea se cuenta como **correcta** si el resultado de la ejecución de la tarea es `success`.
    - Una tarea se cuenta como **errónea** si el resultado de la ejecución de la tarea es `failure`.

Para más información sobre los estados de las tareas, consulte [Obtención de información sobre una tarea][rest_get_task].

El siguiente ejemplo de código de .NET muestra cómo recuperar los recuentos de tareas por estado: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> Puede usar un patrón similar para REST y otros lenguajes admitidos para obtener los recuentos de tareas de un trabajo. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>Comprobación de coherencia de los recuentos de tareas

El servicio Batch permite agregar recuentos de tareas mediante la recopilación de datos de varias partes de un sistema distribuido asincrónico. Para asegurarse de que los recuentos de tareas son correctos, Batch proporciona una validación adicional para los recuentos de estados mediante la realización de comprobaciones de coherencia en varios componentes del sistema. Batch realizará estas comprobaciones de coherencia siempre y cuando haya menos de 200 000 tareas en el trabajo. En el caso poco probable de que la comprobación de coherencia detecte errores, Batch corregirá el resultado de la operación Get Tasks Counts basándose en los resultados de la comprobación de coherencia. La comprobación de coherencia es una medida adicional para garantizar que los clientes que confían en la operación Get Task Counts obtienen la información adecuada que necesitan para su solución.

La propiedad **validationStatus** de la respuesta indica si Batch ha realizado la comprobación de coherencia. Si Batch no pudo comprobar el recuento de estados con respecto a los estados reales conservados en el sistema, la propiedad **validationStatus** se establece en `unvalidated`. Por motivos de rendimiento, Batch no realizará la comprobación de coherencia si el trabajo incluye más de 200 000 tareas, por lo que la propiedad **validationStatus** se establecerá en `unvalidated` en este caso. Sin embargo, el recuento de tareas no es necesariamente incorrecto en este caso, ya que incluso una pérdida de datos muy limitada es muy improbable. 

Cuando el estado de una tarea cambia, la canalización de agregación procesa el cambio en cuestión de segundos. La operación Get Task Counts refleja los recuentos de tareas actualizadas dentro de ese período. Sin embargo, si la canalización de agregación omite un cambio en el estado de una tarea, ese cambio no se registrará hasta la siguiente validación. Durante este tiempo, los recuentos de tareas pueden resultar ligeramente imprecisos debido al evento que falta, pero se corrigen durante la siguiente validación.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>Procedimientos recomendados para el recuento de las tareas de un trabajo

La llamada a la operación Get Task Counts es la manera más eficaz de obtener un recuento básico de las tareas de un trabajo por estado. Si utiliza la versión 2017-06-01.5.1 del servicio Batch, se recomienda escribir o actualizar el código para que use la operación Get Task Counts.

La operación Get Task Counts no está disponible en versiones de Batch anteriores a 2017-06-01.5.1. Si está utilizando una versión anterior del servicio, utilice una consulta de lista para contar las tareas de un trabajo en su lugar. Para más información, consulte [Creación de consultas para enumerar los recursos de Batch con eficacia](batch-efficient-list-queries.md).

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de los conceptos y las características del servicio de Batch, consulte la [descripción de las características de Batch](batch-api-basics.md). El artículo describe los principales recursos de Batch como los grupos, nodos de proceso, trabajos y tareas, y proporciona información general acerca de las características del servicio.
* Para conocer los aspectos básicos del desarrollo de una aplicación habilitada para Batch, consulte la [biblioteca de cliente de Batch para .NET](batch-dotnet-get-started.md) o [Python](batch-python-tutorial.md). Estos artículos introductorios le guían a través de una aplicación activa que usa el servicio de Batch para ejecutar una carga de trabajo en varios nodos de proceso.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
