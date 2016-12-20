---
title: "Maximización del uso de nodos de Batch con tareas en paralelo | Microsoft Docs"
description: "Aumente la eficiencia y reduzca los costos usando menos nodos de proceso y ejecutando tareas simultáneas en cada nodo de un grupo de Lote de Azure"
services: batch
documentationcenter: .net
author: mmacy
manager: timlt
editor: 
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 10/25/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: f5bdbd801107650f87993b395338adfb1b26d17e
ms.openlocfilehash: b72da9516bdcc0cd78460815ad258a110298150a


---
# <a name="maximize-azure-batch-compute-resource-usage-with-concurrent-node-tasks"></a>Maximizar el uso de recursos de proceso de Lote de Azure con tareas simultáneas de nodo
A través de la ejecución simultánea de más de una tarea en cada nodo de proceso dentro del grupo de Lote de Azure, puede maximizar el uso de recursos en un menor número de nodos en el grupo. Para algunas cargas de trabajo, esto puede reducir los costos y el tiempo dedicado al trabajo.

Aunque en algunos casos puede resultar beneficioso que todos los recursos de un nodo estén dedicados a una sola tarea, en otras situaciones será conveniente permitir que varias tareas compartan esos recursos:

* **Minimizar la transferencia de datos** cuando las tareas son capaces de compartir datos. En este escenario, puede reducir considerablemente los gastos de transferencia de datos copiando datos compartidos en un número menor de nodos y ejecutando tareas en paralelo en cada nodo. Esto es válido especialmente si los datos que se copian en cada nodo deben transferirse entre regiones geográficas.
* **Maximizar el uso de memoria** cuando las tareas requieren una gran cantidad de memoria, pero solo durante períodos breves y en momentos variables durante la ejecución. Puede emplear menos nodos de ejecución, pero de mayor tamaño y con más memoria, para controlar de forma eficaz dichos aumentos. Estos nodos tendrían varias tareas ejecutándose en paralelo en cada nodo, pero cada tarea aprovecharía la abundante memoria de los nodos en distintos momentos.
* **Mitigar los límites de número de nodos** cuando se requiere la comunicación entre nodos dentro de un grupo. Actualmente, los grupos configurados para la comunicación entre nodos están limitados a 50 nodos de ejecución. Si cada uno de los nodos de este tipo de grupo es capaz de ejecutar tareas en paralelo, el número de tareas que se podrán ejecutar simultáneamente será mayor.
* **Replicar en un clúster de proceso local**, como cuando mueve por primera vez un entorno de procesos a Azure. Si la solución local existente ejecuta varias tareas en cada nodo de proceso, puede aumentar el número máximo de tareas de nodo para que refleje con mayor fidelidad esa configuración.

## <a name="example-scenario"></a>Escenario de ejemplo
Para ilustrar las ventajas de la ejecución de tareas en paralelo, imaginemos que la aplicación de la tarea tiene unos requisitos de CPU y memoria que hacen que el tamaño de nodo [Standard\_StandardD1](../cloud-services/cloud-services-sizes-specs.md) sea suficiente. Pero, para ejecutar el trabajo en el tiempo requerido, se necesitan 1.000 nodos de ese tipo.

En lugar de utilizar nodos Standard\_D1 con un núcleo de CPU, podría utilizar nodos [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) con 16 núcleos en cada nodo y habilitar la ejecución de tareas en paralelo. En este caso, se podría usar un *número de nodos 16 veces menor* ; es decir, en lugar de 1000 nodos, solo serían necesarios 63. Además, si para cada nodo son necesarios datos de referencia o archivos de aplicación de gran tamaño, la eficiencia y la duración del trabajo también se mejoran, ya que los datos se copian en solo 16 nodos.

## <a name="enable-parallel-task-execution"></a>Habilitación de la ejecución en paralelo de tareas
Los nodos de proceso para la ejecución en paralelo de tareas se configuran a nivel de grupo. Cuando cree un grupo, establezca la propiedad [CloudPool.MaxTasksPerComputeNode][maxtasks_net] con la biblioteca Batch .NET. Si utiliza la API de REST de Batch, establezca el elemento [maxTasksPerNode][rest_addpool] en el cuerpo de la solicitud durante la creación del grupo.

Lote de Azure permite una configuración máxima de tareas por nodo que casi cuadriplica el número de núcleos de nodo. Por ejemplo, si el grupo está configurado con nodos de tamaño "Grande" (cuatro núcleos), `maxTasksPerNode` se puede establecer en 16. Para más información sobre el número de núcleos de cada uno de los tamaños de nodo, consulte [Tamaños de los servicios en la nube](../cloud-services/cloud-services-sizes-specs.md). Para más información sobre los límites del servicio, consulte [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md).

> [!TIP]
> No olvide que debe tener en cuenta el valor `maxTasksPerNode` cuando cree una [fórmula de escalado automático][enable_autoscaling] para el grupo. Por ejemplo, una fórmula que evalúe `$RunningTasks` podría verse afectada considerablemente por un aumento en las tareas por nodo. Consulte [Escalación automática de los nodos de ejecución en un grupo de Lote de Azure](batch-automatic-scaling.md) para obtener más información.
>
>

## <a name="distribution-of-tasks"></a>Distribución de tareas
Cuando los nodos de proceso dentro de un grupo pueden ejecutar tareas de forma simultánea, es importante especificar cómo desea que se distribuyan las tareas entre los nodos del grupo.

Mediante la propiedad [CloudPool.TaskSchedulingPolicy][task_schedule], puede especificar las tareas que deben asignarse uniformemente entre todos los nodos del grupo ("propagación"). O bien, puede especificar que se deberían asignar todas las tareas posibles a cada nodo antes de asignarlas a otro nodo del grupo ("empaquetado").

Para ver un ejemplo de la importancia de esta característica, piense en el grupo de nodos [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) del ejemplo anterior. Este grupo está configurado de forma que [CloudPool.MaxTasksPerComputeNode][maxtasks_net] tiene el valor 16. Si [CloudPool.TaskSchedulingPolicy][task_schedule] se configura con la propiedad [ComputeNodeFillType][fill_type] establecida en *Pack*, se maximiza el uso de los 16 núcleos de cada nodo y se permite que un [grupo de escalado automático](batch-automatic-scaling.md) elimine del grupo los nodos no utilizados (los nodos que no tienen tareas asignadas). Esto minimiza el uso de recursos y permite ahorrar dinero.

## <a name="batch-net-example"></a>Ejemplo de .NET Lote
En este fragmento de código de [Batch .NET][api_net] API, se muestra una solicitud para crear un grupo que contiene cuatro nodos grandes con un máximo de cuatro tareas por nodo. Se especifica una directiva de programación de tareas que llenará cada nodo de tareas antes de asignarlas a otro nodo del grupo. Para más información acerca de la adición de grupos mediante Batch .NET API, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicated: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Ejemplo de REST Lote
En este fragmento de código de [Batch REST][api_rest] API, se muestra una solicitud para crear un grupo que contiene dos nodos grandes con un máximo de cuatro tareas por nodo. Para más información sobre la adición de grupos mediante la API de REST, consulte [Agregar un grupo a una cuenta][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicated":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> El elemento `maxTasksPerNode` y la propiedad [MaxTasksPerComputeNode][maxtasks_net] solo se pueden establecer en el momento en que se crea al grupo. No se pueden modificar después de haberlos creado.
>
>

## <a name="code-sample"></a>Código de ejemplo
En el proyecto [ParallelNodeTasks][parallel_tasks_sample] de GitHub, se muestra el uso de la propiedad [CloudPool.MaxTasksPerComputeNode][maxtasks_net].

Esta aplicación de consola de C# utiliza la biblioteca [Batch .NET][api_net] para crear un grupo con uno o varios nodos de proceso. Ejecuta un número configurable de tareas en esos nodos para simular una carga variable. Los resultados de la aplicación especifican qué nodos han ejecutado cada tarea. La aplicación también proporciona un resumen de los parámetros de trabajo y la duración. Abajo se muestra la parte de resumen de los resultados de dos ejecuciones diferentes de la aplicación de ejemplo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La primera ejecución de la aplicación de ejemplo muestra que, con un solo nodo en el grupo y la configuración predeterminada de una tarea por nodo, la duración del trabajo es superior a 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La segunda ejecución del ejemplo muestra una disminución notable en la duración del trabajo. Esto se debe a que el grupo se configuró con cuatro tareas por nodo, lo que permite la ejecución en paralelo de tareas de forma que el trabajo se completa en casi una cuarta parte del tiempo.

> [!NOTE]
> La duración del trabajo en los resúmenes anteriores no incluye el tiempo de creación del grupo. Cada uno de los trabajos anteriores se envió a grupos ya creados cuyos nodos de proceso se encontraban en el estado *inactivo* en el momento del envío.
>
>

## <a name="next-steps"></a>Pasos siguientes
### <a name="batch-explorer-heat-map"></a>Mapa térmico de Batch Explorer
El [explorador de Azure Batch][batch_explorer], una de las [aplicaciones de ejemplo][github_samples] de Azure Batch, contiene una característica llamada *Mapa térmico* donde se muestra gráficamente la ejecución de las tareas. Si ejecuta la aplicación de ejemplo [ParallelTasks][parallel_tasks_sample], puede usar la característica Mapa térmico para ver fácilmente la ejecución de tareas en paralelo de cada nodo.

![Mapa térmico de Batch Explorer][1]

*Mapa térmico de Batch Explorer que muestra un grupo de cuatro nodos, donde cada nodo ejecuta actualmente cuatro tareas*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png



<!--HONumber=Nov16_HO3-->


