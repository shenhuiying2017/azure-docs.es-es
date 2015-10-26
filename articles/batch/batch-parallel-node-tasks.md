<properties
   pageTitle="Maximizar el uso de nodos de Lote de Azure con tareas en paralelo | Microsoft Azure"
   description="Aumente la eficiencia y reduzca los costos usando menos nodos de proceso al mismo tiempo que ejecuta tareas simultáneas en cada nodo de un grupo de Lote de Azure"
   services="batch"
   documentationCenter=".net"
   authors="mmacy"
   manager="timlt"
   editor=""/>

   <tags
   	ms.service="batch"
   	ms.devlang="multiple"
   	ms.topic="article"
   	ms.tgt_pltfrm="vm-windows"
   	ms.workload="big-compute"
   	ms.date="09/30/2015"
   	ms.author="v-marsma"/>

# Maximizar el uso de recursos de proceso de Lote de Azure con tareas simultáneas de nodo

La ejecución en paralelo de tareas a gran escala es una característica esencial de Lote de Azure, y esta capacidad se extiende no solo a varios nodos de proceso que ejecutan las tareas, sino también a ejecutar tareas simultáneas en esos nodos. Con Lote, la ejecución en paralelo de tareas escala tanto vertical como horizontalmente.

La habilitación de la ejecución de tareas simultáneas en los nodos de proceso de un grupo permite maximizar el uso de recursos en un número menor de nodos del grupo. Si bien algunos escenarios se beneficiarán de que todos los recursos de un nodo estén disponibles para asignarse a una sola tarea, en otras situaciones será conveniente permitir que varias tareas compartan esos recursos:

 - **Minimizar la transferencia de datos** cuando las tareas son capaces de compartir datos. En este escenario, copiar los datos compartidos en un número menor de nodos y ejecutar tareas en paralelo en cada nodo puede reducir drásticamente los cargos de transferencia de datos, especialmente si los datos que se van a copiar a cada nodo deben transferirse entre regiones geográficas.

 - **Maximizar el uso de memoria** cuando las tareas requieren una gran cantidad de memoria, pero solo durante períodos breves y en momentos variables durante la ejecución. Se pueden emplear menos instancias de nodo, pero de mayor tamaño y con más memoria, para controlar de forma eficaz dichos aumentos, con tareas paralelas que se ejecutan en cada nodo para aprovechar la abundante memoria de los nodos en momentos diferentes.

 - **Mitigar los límites de número de nodos** cuando se requiere la comunicación entre nodos dentro de un grupo. Actualmente, los grupos configurados para la comunicación entre nodos están limitados a 50 nodos; por lo tanto, se puede ejecutar un mayor número de tareas simultáneamente si cada nodo de un grupo de este tipo es capaz de ejecutar tareas en paralelo.

 - **Replicar en un clúster de proceso local**, como cuando se mueve por primera vez un entorno de procesos a Azure. Se puede aumentar el número máximo de tareas de nodo para que refleje con mayor fidelidad una configuración física existente si dicha configuración ejecuta actualmente varias tareas por nodo de proceso.

## Escenario de ejemplo

Para ilustrar las ventajas de la ejecución en paralelo de tareas, supongamos que la aplicación de la tarea tiene requisitos de CPU y memoria tales que un tamaño de nodo Standard\_D1 es adecuado, pero para ejecutar el trabajo en el tiempo requerido, se necesitan 1000 nodos de ese tipo. En lugar de usar los nodos Standard\_D1, podría emplear nodos Standard\_D14 con 16 núcleos y habilitar la ejecución en paralelo de tareas. En este caso, se podrían usar *16 veces menos nodos*; es decir, en lugar de 1000 nodos solo serían necesarios 63. Esto mejorará enormemente el tiempo de ejecución del trabajo y la eficacia si se requieren archivos de aplicación de gran tamaño o datos de referencia para cada nodo.

## Habilitación de la ejecución en paralelo de tareas

La configuración de los nodos de proceso en la solución Lote para la ejecución en paralelo de tareas se realiza en el nivel de grupo. Cuando se trabaja con la API de .NET Lote, se establece la propiedad [CloudPool.MaxTasksPerComputeNode][maxtasks_net] al crear un grupo. En la API de REST Lote, se establece el elemento [maxTasksPerNode][maxtasks_rest] en el cuerpo de la solicitud durante la creación del grupo.

Lote de Azure permite una configuración máxima de tareas por nodo que casi cuadriplica el número de núcleos de nodo. Por ejemplo, si el grupo está configurado con nodos de tamaño "Grande" (cuatro núcleos), `maxTasksPerNode` se puede establecer en 16. Consulte más detalles sobre el número de núcleos para cada uno de los tamaños de nodo en [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-size-specs.md), así como más información sobre los límites de servicio en [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

> [AZURE.TIP]Asegúrese de tener en cuenta el valor `maxTasksPerNode` al construir una [fórmula de escalado automático][enable_autoscaling] para el grupo. Por ejemplo, una fórmula que evalúe `$RunningTasks` podría verse afectada considerablemente por un aumento en las tareas por nodo. Consulte [Escalación automática de los nodos de ejecución en un grupo de Lote de Azure](batch-automatic-scaling.md) para obtener más información.

## Distribución de tareas

Cuando los nodos de proceso dentro de un grupo son capaces de ejecutar tareas al mismo tiempo, es importante especificar cómo desea que las tareas se distribuyan entre los nodos del grupo.

Mediante la propiedad [CloudPool.TaskSchedulingPolicy][task_schedule], puede especificar que las tareas se deberían asignar de forma uniforme entre todos los nodos del grupo ("propagación") o que se deberían asignar todas las tareas posibles a cada nodo antes de asignarlas a otro nodo del grupo ("empaquetado").

Como ejemplo de por qué esta característica es importante, considere el grupo de nodos Standard\_D14 del ejemplo anterior, configurado con un valor [CloudPool.MaxTasksPerComputeNode][maxtasks_net] de 16. Si [CloudPool.TaskSchedulingPolicy][task_schedule] se configura con un [ComputeNodeFillType][fill_type] *Pack*, se podría maximizar el uso de los 16 núcleos de cada nodo y permitir que un [grupo con escalado automático](./batch-automatic-scaling.md) elimine los nodos sin usar del grupo (nodos sin tareas asignadas) y así minimizar el uso de recursos y ahorrar dinero.

## Ejemplo de .NET Lote

En este fragmento de código de la API de [.NET Lote][api_net], se muestra una solicitud para crear un grupo que contiene cuatro nodos grandes con un máximo de cuatro tareas por nodo y se especifica una directiva de programación de tareas que llenará cada nodo de tareas antes de asignarlas a otro nodo del grupo. Para obtener más información acerca de cómo agregar grupos mediante la API de .NET Lote, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Ejemplo de REST Lote

En este fragmento de la API de [REST Lote][api_rest], se muestra una solicitud para crear un grupo que contiene dos nodos de gran tamaño con un máximo de cuatro tareas por nodo. Para obtener más información acerca de cómo agregar grupos mediante la API de REST, consulte [Incorporación de grupos][maxtasks_rest].

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE]El elemento `maxTasksPerNode` y la propiedad [MaxTasksPerComputeNode][maxtasks_net] solo se pueden establecer en el momento de crear el grupo. No se pueden modificar después de haberlo creado.

## Exploración del proyecto de ejemplo

Eche un vistazo al proyecto [ParallelNodeTasks][parallel_tasks_sample] en GitHub, un ejemplo de código de trabajo que ilustra el uso de [CloudPool.MaxTasksPerComputeNode][maxtasks_net]. Esta aplicación de consola C# usa la biblioteca de [.NET Lote][api_net] para crear un grupo con uno o varios nodos de proceso y ejecuta un número configurable de las tareas en esos nodos para simular una carga variable. Los resultados de la aplicación ofrecen detalles sobre qué nodos ejecutan cada tarea, así como un resumen de los parámetros de trabajo y la duración. Abajo se muestra la parte de resumen de los resultados de dos ejecuciones diferentes de la aplicación de ejemplo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La primera ejecución de la aplicación de ejemplo muestra que, con un solo nodo en el grupo y el valor predeterminado de una tarea por nodo, la duración del trabajo es superior a treinta minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La segunda ejecución del ejemplo muestra una disminución notable en la duración del trabajo porque se configuró el grupo con cuatro tareas por nodo, lo que permite la ejecución en paralelo de tareas de forma que el trabajo se completa en casi un cuarto del tiempo.

> [AZURE.NOTE]La duración del trabajo en los resúmenes anteriores no incluye el tiempo de creación del grupo. Cada una de las tareas anteriores se enviaron a grupos ya creados cuyos nodos de proceso se encontraban en el estado *inactivo* en el momento del envío.

## Mapa térmico de Batch Explorer

El explorador de Lote, o [Batch Explorer][batch_explorer], una de las [aplicaciones de ejemplo][github_samples] de Lote de Azure, contiene una función de *mapa térmico* que proporciona una visualización del uso de los núcleos de nodo dentro de un grupo. Cuando se ejecuta la aplicación de ejemplo [ParallelTasks][parallel_tasks_sample], use la característica de mapa térmico para visualizar fácilmente la actividad de los núcleos de nodos.

![Mapa térmico de Batch Explorer][1]

*Mapa térmico de Batch Explorer que muestra cuatro nodos con cuatro núcleos por nodo, los cuales están ejecutando una tarea cada uno de manera simultánea*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/es-ES/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=Oct15_HO3-->