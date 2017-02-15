---
title: Dependencias de tareas en Azure Batch | Microsoft Docs
description: "Cree tareas que dependan de la finalización correcta de otras para procesar grandes cargas de trabajo de datos similares y de estilo MapReduce en Lote de Azure."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 01/05/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: dfcf1e1d54a0c04cacffb50eca4afd39c6f6a1b1
ms.openlocfilehash: 5883417c6f7a0ce45c9c34ac2d37e5c1bea95ab1


---
# <a name="task-dependencies-in-azure-batch"></a>Dependencias de tareas en Lote de Azure
La característica de dependencias de tareas de Lote de Azure es muy útil si desea procesar:

* Cargas de trabajo del estilo MapReduce en la nube.
* Trabajos cuyas tareas de procesamiento de datos se pueden expresar como un gráfico acíclico dirigido (DAG).
* Cualquier otro trabajo en el que las tareas que siguen en la cadena dependen de las tareas que preceden en la cadena.

Las dependencias de la tarea de Batch les permiten crear tareas que están programadas para su ejecución en los nodos de proceso únicamente después de la finalización correcta de una o más tareas. Por ejemplo, puede crear un trabajo que represente cada fotograma de una película 3D con una tarea independiente y paralela. La última tarea (la "tarea de combinación") no combina los fotogramas representados para crear la película completa hasta que todos los fotogramas se hayan representado correctamente.

Puede crear tareas que dependan de otras en una relación de una a una o una a varias. Incluso puede crear una dependencia de intervalo, en la que una tarea depende de la finalización correcta de un grupo de tareas dentro de un intervalo de identificadores de tarea específico. Puede combinar estos tres escenarios básicos para crear relaciones de varios a varios.

## <a name="task-dependencies-with-batch-net"></a>Dependencias de tareas con Lote de .NET
En este artículo se describe cómo configurar las dependencias de tareas mediante la biblioteca de [.NET para Batch][net_msdn]. Primero le mostraremos cómo [habilitar la dependencia de tareas](#enable-task-dependencies) en sus trabajos y, después, le demostraremos cómo [configurar una tarea con dependencias](#create-dependent-tasks). Por último, se tratarán los [escenarios de dependencia](#dependency-scenarios) compatibles con Lote.

## <a name="enable-task-dependencies"></a>Habilitación de dependencias de tareas
Para utilizar la dependencia de tareas en la aplicación Lote, antes es preciso indicar al servicio de Lote que el trabajo va a usar dependencias de tareas. En .NET para Batch, habilítelo en [CloudJob][net_cloudjob] estableciendo su propiedad [UsesTaskDependencies][net_usestaskdependencies] en `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

En el fragmento de código anterior, "batchClient" es una instancia de la clase [BatchClient][net_batchclient].

## <a name="create-dependent-tasks"></a>Creación de tareas dependientes
Para crear una tarea que dependa de la finalización correcta de una o varias tareas, es preciso indicar a Lote que la tarea "depende" de otras. En .NET para Batch, configure la propiedad [CloudTask][net_cloudtask].[DependsOn][net_dependson] con una instancia de la clase [TaskDependencies][net_taskdependencies]:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este fragmento de código crea una tarea con el identificador de "Flowers" que se programará para ejecutarse en un nodo de proceso cuando las tareas con los identificadores de "Rain" y "Sun" se hayan completado correctamente.

> [!NOTE]
> Se considera que una tarea se ha completado cuando se encuentra en estado **completado** y su **código de salida** es `0`. En .NET para Batch, esto significa que el valor de la propiedad [CloudTask][net_cloudtask].[State ][net_taskstate] es `Completed` y el valor de la propiedad [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] de CloudTask es `0`.
> 
> 

## <a name="dependency-scenarios"></a>escenarios de dependencia
Hay tres escenarios de dependencia de tareas básicos que puede usar en Lote de Azure: uno a uno, uno a varios y la dependencia de intervalo de identificadores de tarea. Estos se pueden combinar para proporcionar un cuarto escenario, varios a varios.

| Escenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Ejemplo |  |
|:---:| --- | --- |
|  [Uno a uno](#one-to-one) |*taskB* depende de *taskA* <p/> Se programará que *taskB* no se ejecute hasta que *taskA* se haya completado correctamente |![Diagrama: dependencia de la tarea uno a uno][1] |
|  [Uno a varios](#one-to-many) |*taskC* depende de*taskA* y *taskB*. <p/> Se programará que *taskC* no se ejecute hasta que *taskA* y *taskB* se hayan completado correctamente |![Diagrama: dependencia de la tarea uno a varios][2] |
|  [Intervalo de id. de tarea](#task-id-range) |*taskD* depende de una serie de tareas <p/> Se programará que *taskD* no se ejecute hasta que las tareas con los identificadores del *1* al *10* se hayan completado correctamente. |![Diagrama: dependencia de intervalo de id. de tarea][3] |

> [!TIP]
> Puede crear relaciones de **varios a varios**, por ejemplo, donde las tareas C, D, E y F dependan de las tareas A y B. Esto es útil, por ejemplo, en escenarios de preprocesamiento en paralelo donde las tareas que siguen en la cadena dependen de la salida de varias tareas que preceden en la cadena.
> 
> 

### <a name="one-to-one"></a>Uno a uno
Para crear una tarea que dependa de la correcta finalización de otra, proporcione un identificador de tarea único para el método estático [TaskDependencies][net_taskdependencies].[OnId][net_onid] al rellenar la propiedad [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Uno a varios
Para crear una tarea que dependa de la correcta finalización de otras, proporcione distintos identificadores de tarea para el método estático [TaskDependencies][net_taskdependencies].[OnIds][net_onids] al rellenar la propiedad [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Intervalo de id. de tarea
Para crear una tarea que dependa de la correcta finalización de un grupo de tareas cuyos identificadores se encuentren a un intervalo, proporcione el primer y el último identificador de tarea del intervalo para el método estático [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] al rellenar la propiedad [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Si usa intervalos de identificadores de tarea para las dependencias, dichos identificadores *deben* ser representaciones de cadena de valores enteros. Además, para que se programe la ejecución de la dependiente, deben completarse correctamente todas las tareas del intervalo.
> 
> 

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Código de ejemplo
El proyecto de ejemplo [TaskDependencies][github_taskdependencies] es uno de los ejemplos de código de [Azure Batch][github_samples] de GitHub. Esta solución de Visual Studio 2015 muestra cómo habilitar la dependencia de tareas en un trabajo, crear tareas que dependan de otras y ejecutarlas en un grupo de nodos de proceso.

## <a name="next-steps"></a>Pasos siguientes
### <a name="application-deployment"></a>Implementación de la aplicación
La característica [paquetes de aplicación](batch-application-packages.md) de Lote proporciona una manera sencilla de implementar y de cambiar la versión de las aplicaciones que las tareas ejecutan en los nodos de proceso.

### <a name="installing-applications-and-staging-data"></a>Instalación de aplicaciones y datos de ensayo
Consulte el artículo [Installing applications and staging data on Batch compute nodes][forum_post] (Instalación de aplicaciones y datos de ensayo en nodos de proceso de Batch) en el foro de Azure Batch para ver la información general sobre los diversos métodos de preparación de los nodos para ejecutar tareas. Este artículo lo ha escrito uno de los miembros del equipo de Lote de Azure y constituye una buena toma de contacto con las diferentes maneras de introducir archivos (tanto aplicaciones como datos de entrada de tareas) en los nodos de proceso.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependencia uno a uno"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependencia uno a varios"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependencia de intervalo de id. de tarea"



<!--HONumber=Dec16_HO2-->


