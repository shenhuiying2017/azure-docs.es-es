<properties
	pageTitle="Consultas de lista eficaces en Lote de Azure | Microsoft Azure"
	description="Aumente el rendimiento gracias a la reducción de la cantidad de datos devueltos al consultar entidades de Lote de Azure como grupos, trabajos, tareas y nodos de proceso."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/12/2015"
	ms.author="v-marsma"/>

# Consulta eficaz del servicio Lote de Azure

En este artículo aprenderá a reducir el número de elementos y la cantidad de datos devueltos cuando se usa la API de [Lote de .NET][api_net] para consultar listas de trabajos, tareas, nodos de proceso, etc. en el servicio Lote.

Lote de Azure es Big Compute, y en un entorno de producción, algunas entidades como los trabajos, las tareas y los nodos de ejecución pueden contarse por millares. Por lo tanto, la obtención de información acerca de estos elementos puede generar una gran cantidad de datos que se deben transferir en cada consulta. La limitación del número de elementos y del tipo de información que se devuelve de cada uno de ellos aumentará la velocidad de las consultas y, por consiguiente, el rendimiento de la aplicación.

Las listas de trabajos, las tareas o los nodos de proceso son ejemplos de operaciones que prácticamente cada aplicación que use Lote de Azure debe llevar a cabo, a menudo con bastante frecuencia. La supervisión es un caso de uso habitual. Por ejemplo, para determinar la capacidad y el estado de un grupo, es necesario consultar todos los nodos de proceso de ese grupo. Otro ejemplo es la consulta de las tareas de un trabajo para determinar si alguna de ellas sigue en cola.

Este fragmento de código de la API de [Lote de .NET][api_net] recupera todas las tareas asociadas a un trabajo, junto con el conjunto completo de propiedades de esas tareas:

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

No obstante, se puede realizar una consulta de lista mucho más eficaz si se proporciona un objeto [ODATADetailLevel][odata] al método [JobOperations.ListTasks][net_list_tasks]. Este fragmento de código devuelve solo las propiedades de identificador, línea de comandos e información de nodo de proceso exclusivamente para las tareas completadas:

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Si, en el escenario de ejemplo anterior, hay miles de tareas en el trabajo, los resultados de la segunda consulta normalmente se devolverán mucho más rápido que los de la primera. A continuación, se ofrece más información acerca del uso de un objeto ODATADetailLevel al enumerar elementos con la API de Lote de .NET.

> [AZURE.IMPORTANT]Es muy recomendable proporcionar **siempre** un objeto ODATADetailLevel a las llamadas de lista de la API de .NET para garantizar la máxima eficacia y rendimiento de su aplicación. Especificar un nivel de detalle ayuda a reducir los tiempos de respuesta del servicio Lote, a mejorar la utilización de la red y a minimizar el uso de la memoria por las aplicaciones cliente.

## Herramientas de consulta eficaz

Las API [Lote de .NET][api_net] y [Lote de REST][api_rest] proporcionan la capacidad de reducir tanto el número de elementos devueltos en una lista como la cantidad de información devuelta para cada uno si se especifican las cadenas *filter*, *select* y *expand* al realizar consultas de lista.

- **filter**: la *cadena filter* es una expresión que reduce el número de elementos devueltos. Por ejemplo, muestre solo las tareas en ejecución de un trabajo o solo los nodos de proceso que estén listos para ejecutar tareas.
  - La cadena filter se compone de una o varias expresiones, una de la cuales consta de un nombre de propiedad, un operador y un valor. Las propiedades que se pueden indicar son específicas de cada tipo de llamada API, al igual que los operadores admitidos para cada propiedad.
  - Se pueden combinar varias expresiones con los operadores lógicos `and` y `or`.
  - Ejemplo de cadena filter que solo muestra las tareas de representación en ejecución: `startswith(id, 'renderTask') and (state eq 'running')`
- **select**: la *cadena select* limita los valores de propiedad que se devuelven para cada elemento. Se puede especificar una lista de propiedades para un elemento en la cadena select y, entonces, solamente se devolverán esos valores de propiedad para cada elemento con los resultados de la consulta de lista.
  - Un cadena select consta de una lista de nombres de propiedades separados por comas. Se puede especificar cualquiera de las propiedades de un elemento devueltas por la operación de lista.
  - Ejemplo de cadena select que especifica que solo se devuelvan tres propiedades para cada tarea: `id, state, stateTransitionTime`
- **expand**: la *cadena expand* reduce el número de llamadas API necesarias para obtener determinada información. Puede obtener información más detallada sobre cada elemento de lista obtenido con una llamada API de lista en lugar de obtener la lista y después realizar una llamada para cada elemento en la lista.
  - De forma similar a la cadena select, la cadena expand controla si se incluyen determinados datos en los resultados de la consulta de lista.
  - Solo se admite la cadena expand al enumerar trabajos, programaciones de trabajos, tareas y grupos; además, actualmente solo es compatible con información estadística.
  - Ejemplo de cadena expand que especifica que se debe devolver información estadística para cada elemento: `stats`
  - Cuando se requieren todas las propiedades y no se especifica ninguna cadena select, se *debe* usar la cadena expand para obtener información estadística. Si se usa una cadena select para obtener un subconjunto de propiedades, después se puede especificar `stats` en la cadena select y no es necesario especificar la cadena expand.

> [AZURE.NOTE]Al construir cualquiera de los tres tipos de cadena de consulta (filter, select, expand), debe asegurarse de que los nombres de las propiedades y el uso de mayúsculas y minúsculas coincidan con los elementos homólogos de la API de REST. Por ejemplo, cuando se trabaja con [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) de .NET, es preciso especificar **state** en lugar de **State**, aunque la propiedad de .NET sea [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consulte en las tablas siguientes las asignaciones de propiedad entre la API de REST y la de .NET.

### Especificaciones de las cadenas filter, select y expand

- Las propiedades especificadas en las cadenas filter, select y expand equivalen a los nombres de propiedades que aparecen en la API de [Lote de REST][api_rest], incluso si se usa la biblioteca de [Lote de .NET][api_net].
- Todos los nombres de propiedad distinguen mayúsculas de minúsculas, pero los valores de propiedad no.
- Las cadenas de fecha y hora pueden tener uno de dos formatos y deben ir precedidas por `DateTime`.
  - Ejemplo de formato W3CDTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Ejemplo de formato RFC1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Las cadenas booleanas son `true` o `false`.
- Si se especifica una propiedad o un operador no válidos, se producirá un error `400 (Bad Request)`.

## Consultas eficaces en .NET de Lote

Dentro de la API de [Lote de .NET][api_net], se usa el objeto [ODATADetailLevel][odata] para proporcionar cadenas filter, select y expand a las operaciones de lista. Un objeto ODataDetailLevel tiene tres propiedades de cadena públicas que se pueden especificar en el constructor o establecer directamente; después, este objeto se pasa como parámetro a las diversas operaciones de lista, como [ListPools][net_list_pools], [ListJobs][net_list_jobs] y [ListTasks][net_list_tasks].

- [ODATADetailLevel.FilterClause][odata_filter] \: limita el número de elementos devueltos.
- [ODATADetailLevel.SelectClause][odata_select] \: especifica un subconjunto de valores de propiedad devueltos con cada elemento.
- [ODATADetailLevel.ExpandClause][odata_expand] \: recupera datos de elementos en una sola llamada API en lugar de emitir llamadas para cada uno.

En el siguiente fragmento de código, se usa la API de Lote de .NET para consultar de forma eficaz las estadísticas de un conjunto específico de grupos en el servicio Lote. En este escenario, el usuario de Lote tiene grupos de prueba y de producción; los Id. del grupo de prueba tienen el prefijo "test" y los Id. del grupo de producción tienen el prefijo "prod". En el fragmento, *myBatchClient* es una instancia de [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) inicializada correctamente.

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// We want to pull only the "test" pools, so we limit the number of items returned by using a
	// FilterClause and specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();

> [AZURE.TIP]Para limitar la cantidad de datos devueltos, también se puede pasar una instancia de [ODATADetailLevel][odata] configurada con las cláusulas Select y Expand a los métodos Get apropiados como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx).

## Asignaciones de la API de Lote de REST y la de .NET

Los nombres de propiedades en las cadenas filter, select y expand *deben* reflejar sus homólogos en la API de REST, tanto en el nombre en sí como en el uso de mayúsculas y minúsculas. En las tablas siguientes, se proporcionan asignaciones entre los homólogos de la API de .NET y la de REST.

### Asignaciones de las cadenas filter

- **Métodos de lista de .NET**: todos los métodos de la API de .NET en esta columna aceptan un objeto [ODATADetailLevel][odata] como parámetro.
- **Solicitudes de lista de REST** : todas las páginas de la API de REST en esta columna contienen una tabla que especifica las propiedades y las operaciones permitidas en las cadenas *filter*. Usará estos nombres de propiedades y estas operaciones al construir una cadena [ODATADetailLevel.FilterClause][odata_filter].

| Métodos de lista de .NET | Solicitudes de lista de REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Enumeración de los certificados de una cuenta][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Enumeración de los archivos asociados a una tarea][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Enumeración del estado de la preparación de trabajo y las tareas de la versión de trabajo de un trabajo][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Enumeración de los trabajos de una cuenta][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Enumeración de los archivos de un nodo][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Enumeración de las tareas asociadas a un trabajo][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Enumeración de las programaciones de trabajos de una cuenta][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Enumeración de los trabajos asociados a una programación de trabajo][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Enumeración de los nodos de ejecución de un grupo][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [los grupos de una cuenta][rest_list_pools]

### Asignaciones de las cadenas select

- **Tipos de Lote de .NET**: tipos de la API de Lote de .NET.
- **Entidades de la API de REST**: todas las páginas de esta columna contienen una o más tablas con los nombres de propiedades de la API de REST para el tipo. Estos nombres de propiedades se usan al construir cadenas *select*. Usará estos mismos nombres de propiedades al construir una cadena [ODATADetailLevel.SelectClause][odata_select].

| Tipos de Lote de .NET | Entidades de la API de REST |
|---|---|
| [Certificate][net_cert] | [Obtención de información sobre un certificado][rest_get_cert] |
| [CloudJob][net_job] | [Obtención de información sobre un trabajo][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obtención de información sobre una programación de trabajo][rest_get_schedule] |
| [ComputeNode][net_node] | [Obtención de información sobre un nodo][rest_get_node] |
| [CloudPool][net_pool] | [Obtención de información sobre un grupo][rest_get_pool] |
| [CloudTask][net_task] | [Obtención de información sobre una tarea][rest_get_task] |

### Ejemplo: construcción de una cadena filter

Al construir una cadena filter para una cadena [ODATADetailLevel.FilterClause][odata_filter], consulte la tabla anterior en *Asignaciones de las cadenas filter* para buscar la página de documentación de la API de REST correspondiente a la operación de lista que desea realizar. Encontrará las propiedades filtrables y sus operadores admitidos en la primera tabla de varias filas de esa página. Por ejemplo, si desea recuperar todas las tareas cuyo código de salida sea distinto de cero, esta fila en la página sobre la [enumeración de las tareas asociadas a un trabajo][rest_list_tasks] especifica la cadena de propiedad aplicable y los operadores permitidos:

| Propiedad | Operaciones permitidas | Tipo |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Por lo tanto, la cadena filter para mostrar todas las tareas con un código de salida distinto de cero sería:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### Ejemplo: construcción de una cadena select

Para construir una cadena [ODATADetailLevel.SelectClause][odata_select], consulte la tabla anterior en *Asignaciones de las cadenas select* y visite la página de la API de REST correspondiente al tipo de entidad que vaya a enumerar. Encontrará las propiedades seleccionables y sus operadores admitidos en la primera tabla de varias filas de esa página. Por ejemplo, si desea recuperar solo el identificador y la línea de comandos para cada tarea de una lista, encontrará estas filas en la tabla correspondiente en la página sobre cómo [obtener información acerca de una tarea][rest_get_task]\:

| Propiedad | Tipo | Notas |
| :--- | :--- | :--- |
| `id` | `String` | `The id of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

La cadena select para incluir solo el identificador y la línea de comandos con cada tarea de la lista sería:

`id, commandLine`

## Pasos siguientes

Consulte el proyecto de ejemplo [EfficientListQueries][efficient_query_sample] en GitHub para ver cómo una consulta de lista eficaz puede afectar al rendimiento de una aplicación. Esta aplicación de consola de C# crea un gran número de tareas y las agrega a un trabajo, después consulta el servicio Lote con distintas especificaciones de [ODATADetailLevel][odata] y muestra una salida similar a la siguiente:

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

Como se muestra en la información de tiempo transcurrido, limitar las propiedades y el número de elementos devueltos puede reducir en gran medida los tiempos de respuesta de la consulta. Puede encontrar este y otros proyectos de ejemplo en el repositorio [azure-batch-samples][github_samples] de GitHub.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

<!---HONumber=Oct15_HO3-->