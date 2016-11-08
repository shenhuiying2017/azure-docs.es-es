---
title: Maximizar el uso de nodos de Lote de Azure con tareas en paralelo | Microsoft Docs
description: Aumente la eficiencia y reduzca los costos usando menos nodos de proceso y ejecutando tareas simultáneas en cada nodo de un grupo de Lote de Azure
services: batch
documentationcenter: .net
author: mmacy
manager: timlt
editor: ''

ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/25/2016
ms.author: marsma

---
# Maximizar el uso de recursos de proceso de Lote de Azure con tareas simultáneas de nodo
A través de la ejecución simultánea de más de una tarea en cada nodo de proceso dentro del grupo de Lote de Azure, puede maximizar el uso de recursos en un menor número de nodos en el grupo. Para algunas cargas de trabajo, esto puede reducir los costos y el tiempo dedicado al trabajo.

Si bien algunos escenarios se beneficiarán de que todos los recursos de un nodo estén dedicados a una sola tarea, en otras situaciones será conveniente permitir que varias tareas compartan esos recursos:

* **Minimizar la transferencia de datos** cuando las tareas son capaces de compartir datos. En este escenario, puede reducir considerablemente los gastos de transferencia de datos copiando datos compartidos en un número menor de nodos y ejecutando tareas en paralelo en cada nodo. Esto es válido especialmente si los datos que se copian en cada nodo deben transferirse entre regiones geográficas.
* **Maximizar el uso de memoria** cuando las tareas requieren una gran cantidad de memoria, pero solo durante períodos breves y en momentos variables durante la ejecución. Puede emplear menos nodos de ejecución, pero de mayor tamaño y con más memoria, para controlar de forma eficaz dichos aumentos. Estos nodos tendrían varias tareas ejecutándose en paralelo en cada nodo, pero cada tarea aprovecharía la abundante memoria de los nodos en distintos momentos.
* **Mitigar los límites de número de nodos** cuando se requiere la comunicación entre nodos dentro de un grupo. Actualmente, los grupos configurados para la comunicación entre nodos están limitados a 50 nodos de ejecución. Por lo tanto, se puede ejecutar un mayor número de tareas simultáneamente si cada nodo de un grupo de este tipo es capaz de ejecutar tareas en paralelo.
* **Replicar en un clúster de proceso local**, como cuando mueve por primera vez un entorno de procesos a Azure. Puede aumentar el número máximo de tareas de nodo para que refleje con mayor fidelidad una configuración física existente si dicha configuración ejecuta actualmente varias tareas por nodo de proceso.

## Escenario de ejemplo
Como ejemplo para ilustrar las ventajas de la ejecución en paralelo de tareas, supongamos que la aplicación de la tarea tiene requisitos de CPU y memoria tales que un tamaño de nodo [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md#general-purpose-d) es suficiente. Pero, para ejecutar el trabajo en el tiempo requerido, se necesitan 1.000 nodos de ese tipo.

En lugar de usar los nodos Standard\_D1, que tienen 1 núcleo de CPU, se pueden emplear nodos [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d), que tienen 16 núcleos cada uno, y habilitar la ejecución de tareas en paralelo. En este caso, se podría usar un *número de nodos 16 veces menor*; es decir, en lugar de 1000 nodos, solo serían necesarios 63. Además, si para cada nodo son necesarios datos de referencia o archivos de aplicación de gran tamaño, la eficiencia y la duración del trabajo también se mejoran, ya que los datos se copian en solo 16 nodos.

## Habilitación de la ejecución en paralelo de tareas
Los nodos de proceso para la ejecución en paralelo de tareas se configuran a nivel de grupo. Con la biblioteca de .NET de Lote, establezca la propiedad [CloudPool.MaxTasksPerComputeNode][maxtasks_net] al crear un grupo. Si usa la API de REST de Lote, establezca el elemento [maxTasksPerNode][rest_addpool] en el cuerpo de la solicitud durante la creación del grupo.

Lote de Azure permite una configuración máxima de tareas por nodo que casi cuadriplica el número de núcleos de nodo. Por ejemplo, si el grupo está configurado con nodos de tamaño "Grande" (cuatro núcleos), `maxTasksPerNode` se puede establecer en 16. Para más información sobre el número de núcleos de cada uno de los tamaños de nodo, consulte [Tamaños de los servicios en la nube](../cloud-services/cloud-services-sizes-specs.md). Para más información sobre los límites del servicio, consulte [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md).

> [!TIP]
> Asegúrese de tener en cuenta el valor `maxTasksPerNode` al construir una [fórmula de escalado automático][enable_autoscaling] para el grupo. Por ejemplo, una fórmula que evalúe `$RunningTasks` podría verse afectada considerablemente por un aumento en las tareas por nodo. Consulte [Escalación automática de los nodos de ejecución en un grupo de Lote de Azure](batch-automatic-scaling.md) para obtener más información.
> 
> 

## Distribución de tareas
Cuando los nodos de proceso dentro de un grupo pueden ejecutar tareas de forma simultánea, es importante especificar cómo desea que se distribuyan las tareas entre los nodos del grupo.

Mediante la propiedad [CloudPool.TaskSchedulingPolicy][task_schedule], puede especificar que las tareas se deberían asignar de manera uniforme entre todos los nodos del grupo ("propagación"). O bien, puede especificar que se deberían asignar todas las tareas posibles a cada nodo antes de asignarlas a otro nodo del grupo ("empaquetado").

Como ejemplo de por qué esta característica es importante, considere el grupo de nodos [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) (en el ejemplo anterior) configurado con un valor [CloudPool.MaxTasksPerComputeNode][maxtasks_net] de 16. Si [CloudPool.TaskSchedulingPolicy][task_schedule] se configura con un [ComputeNodeFillType][fill_type] de *Pack*, se podría maximizar el uso de los 16 núcleos de cada nodo y permitir que un [grupo con escalado automático](batch-automatic-scaling.md) elimine los nodos sin usar del grupo (nodos sin tareas asignadas). Esto minimiza el uso de recursos y permite ahorrar dinero.

## Ejemplo de .NET Lote
En este fragmento del código de la API de [.NET de Lote][api_net], se muestra una solicitud para crear un grupo que contiene cuatro nodos de gran tamaño con un máximo de cuatro tareas por nodo. Se especifica una directiva de programación de tareas que llenará cada nodo de tareas antes de asignarlas a otro nodo del grupo. Para más información acerca de cómo agregar grupos mediante la API de .NET de Lote, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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

## Ejemplo de REST Lote
En este fragmento de la API de [REST de Lote][api_rest], se muestra una solicitud para crear un grupo que contiene dos nodos de gran tamaño con un máximo de cuatro tareas por nodo. Para más información acerca de cómo agregar grupos mediante la API de REST, consulte [Agregar un grupo a una cuenta][rest_addpool].

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
> Solo puede establecer el elemento `maxTasksPerNode` y la propiedad [MaxTasksPerComputeNode][maxtasks_net] en el momento de crear el grupo. No se pueden modificar después de haberlos creado.
> 
> 

## Código de ejemplo
El proyecto [ParallelNodeTasks][parallel_tasks_sample] en GitHub muestra el uso de la propiedad [CloudPool.MaxTasksPerComputeNode][maxtasks_net].

Esta aplicación de consola de C# utiliza la biblioteca [.NET de Lote][api_net] para crear un grupo con uno o más nodos de proceso. Ejecuta un número configurable de tareas en esos nodos para simular una carga variable. Los resultados de la aplicación especifican qué nodos han ejecutado cada tarea. La aplicación también proporciona un resumen de los parámetros de trabajo y la duración. Abajo se muestra la parte de resumen de los resultados de dos ejecuciones diferentes de la aplicación de ejemplo.

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

## Pasos siguientes
### Mapa térmico de Batch Explorer
El [Explorador de Lote][batch_explorer], una de las [aplicaciones de ejemplo][github_samples] de Lote de Azure, contiene una característica denominada *Mapa térmico* que proporciona una visualización de la ejecución de tareas. Cuando ejecuta la aplicación de ejemplo [ParallelTasks][parallel_tasks_sample], puede usar la característica Mapa térmico para visualizar fácilmente la ejecución en paralelo de tareas en cada nodo.

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

<!---HONumber=AcomDC_0727_2016-->