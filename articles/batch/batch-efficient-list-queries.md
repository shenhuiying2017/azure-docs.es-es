---
title: "Diseño de consultas de lista eficaces: Azure Batch | Microsoft Docs"
description: "Mejore el rendimiento filtrando las consultas cuando solicite información en recursos de Batch, como grupos, trabajos, tareas y nodos de proceso."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 08/02/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a80b207f591bd888d4749287527013c5e554fb6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Creación de consultas para enumerar los recursos de Batch con eficacia

Aquí obtendrá información sobre cómo mejorar el rendimiento de la aplicación de Azure Batch reduciendo la cantidad de datos que devuelve el servicio al consultar trabajos, tareas, y nodos de proceso mediante la biblioteca de [.NET de Batch][api_net].

Casi todas las aplicaciones de Batch realizan algún tipo de supervisión u otra operación que consulta el servicio Batch, a menudo a intervalos regulares. Por ejemplo, para determinar si quedan tareas en cola en un trabajo, debe obtener información de cada una de las tareas del trabajo. Para determinar el estado de los nodos de un grupo, tiene que obtener los datos de cada nodo del grupo. En este artículo, se explica cómo ejecutar este tipo de consultas de la manera más eficaz.

> [!NOTE]
> El servicio Batch proporciona compatibilidad con la API especial para el escenario común en el que se cuentan las tareas en un trabajo. En lugar de usar una consulta de lista para ello, puede llamar a la operación de obtención de recuentos de tarea [Get Task Counts] [rest_get_task_counts]. Get Task Counts indica cuántas tareas están pendientes, en ejecución o completadas, y cuántas finalizaron correctamente o con errores. Esta operación es más eficaz que una consulta de lista. Para más información, consulte [Recuento de tareas de un trabajo por estado (versión preliminar)](batch-get-task-counts.md). 
>
> La operación Get Task Counts no está disponible en versiones de Batch anteriores a 2017-06-01.5.1. Si está utilizando una versión anterior del servicio, utilice una consulta de lista para contar las tareas de un trabajo en su lugar.
>
> 

## <a name="meet-the-detaillevel"></a>Presentación de DetailLevel
En una aplicación de Batch de producción, algunas entidades como los trabajos, las tareas y los nodos de ejecución pueden contarse por millares. Cuando solicita información sobre estos recursos, es posible que cada consulta envíe un gran volumen de información desde el servicio de Batch hasta la aplicación. Si limita el número de elementos y el tipo de información que va a devolver una consulta, podrá acelerar las consultas y, por tanto, mejorar el rendimiento de la aplicación.

Este fragmento de código de la API de [.NET de Batch][api_net] enumera *todas* las tareas asociadas a un trabajo, junto con *todas* las propiedades de cada tarea:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Sin embargo, puede realizar una consulta de lista mucho más eficiente, aplicando un "nivel de detalle" a la consulta. Para ello, puede suministrar un objeto [ODATADetailLevel][odata] al método [JobOperations.ListTasks][net_list_tasks]. Este fragmento de código devuelve solo las propiedades de identificador, línea de comandos e información de nodo de proceso para las tareas completadas:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

En este caso de ejemplo, si hay miles de tareas en el trabajo, los resultados de la segunda consulta normalmente se devolverán más rápido que en la primera consulta. [A continuación](#efficient-querying-in-batch-net)encontrará más información sobre el uso de ODATADetailLevel al enumerar elementos con la API de Lote de .NET.

> [!IMPORTANT]
> Es muy recomendable suministrar *siempre* un objeto ODATADetailLevel a las llamadas de la lista de la API de .NET para garantizar la máxima eficacia y rendimiento de su aplicación. La especificación de un nivel de detalle puede ayudarle a reducir los tiempos de respuesta del servicio de Batch, a mejorar la utilización de la red y a minimizar el uso de la memoria por parte de las aplicaciones cliente.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrado, selección y expansión
Las API de [.NET de Batch][api_net] y de [REST de Batch][api_rest] ofrecen la posibilidad de reducir tanto el número de elementos que se devuelven en una lista como la cantidad de información que se devuelve para cada uno de ellos. Para ello, debe especificar **filter**, **select** y **expand strings** cuando realice consultas de lista.

### <a name="filter"></a>Filtrar
La cadena filter es una expresión que reduce el número de elementos que se devuelven. Por ejemplo, muestre solo las tareas en ejecución de un trabajo o solo los nodos de proceso que estén listos para ejecutar tareas.

* La cadena filter se compone de una o varias expresiones, una de la cuales consta de un nombre de propiedad, un operador y un valor. Las propiedades que se pueden indicar son específicas de cada tipo de entidad que consulta, al igual que los operadores compatibles con cada propiedad.
* Puede combinar varias expresiones con los operadores lógicos `and` y `or`.
* Esta cadena filter de ejemplo solo muestra las tareas "render" en ejecución: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Seleccionar
La cadena select limita los valores de propiedad que se devuelven para cada elemento. Especifique una lista de nombres de propiedad, y se devolverán únicamente los valores de propiedad devueltos para los elementos de los resultados de la consulta.

* La cadena select consta de una lista de nombres de propiedades separados por comas. Puede especificar cualquiera de las propiedades del tipo de entidad que está consultando.
* Esta cadena select de ejemplo especifica que solo se deben devolver tres valores de propiedades para cada tarea: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expanda
La cadena expand reduce el número de llamadas API necesarias para obtener determinada información. Cuando se utiliza una cadena expand, se puede obtener más información sobre cada elemento con una sola llamada API. En lugar de obtener primero la lista de entidades para después solicitar información de cada elemento de la lista, use una cadena expand para obtener la misma información en una sola llamada API. Menos llamadas API supone un rendimiento mayor.

* De forma similar a la cadena select, la cadena expand controla si se incluyen determinados datos en los resultados de la consulta de lista.
* La cadena expand solo se admite cuando se usa para enumerar trabajo, programaciones de trabajos, tareas y grupos. En la actualidad, solo admite información estadística.
* Cuando se requieren todas las propiedades y no se especifica ninguna cadena select, se *debe* usar la cadena expand para obtener información estadística. Si se usa una cadena select para obtener un subconjunto de propiedades, se puede especificar `stats` en la cadena select y no es preciso especificar la cadena expand.
* Esta cadena expand de ejemplo especifica que se debe devolver información estadística de cada elemento de la lista: `stats`.

> [!NOTE]
> Al construir cualquiera de los tres tipos de cadena de consulta (filter, select y expand), debe asegurarse de que los nombres de las propiedades y el uso de mayúsculas y minúsculas coinciden con los elementos homólogos de la API de REST. Por ejemplo, si trabaja con la clase .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask), debe especificar **state** en lugar de **State**, aunque la propiedad de .NET sea [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consulte en las tablas siguientes las asignaciones de propiedad entre la API de REST y la de .NET.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Reglas de las cadenas filter, select y expand
* Los nombres de propiedades de las cadenas filter, select y expand deben aparecer exactamente igual que en la API [Batch REST][api_rest], aunque se use [Batch .NET][api_net] o uno de los SDK de Batch.
* Todos los nombres de propiedades distinguen mayúsculas y minúsculas, pero los valores de propiedad no lo hacen.
* Las cadenas de fecha y hora pueden tener uno de los dos formatos y deben ir precedidas por `DateTime`.
  
  * Ejemplo de formato W3C-DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Ejemplo de formato RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Las cadenas booleanas son `true` o `false`.
* Si se especifica una propiedad o un operador no válidos, aparecerá el error `400 (Bad Request)` .

## <a name="efficient-querying-in-batch-net"></a>Consultas eficaces en .NET de Batch
En la API de [.NET de Batch][api_net], se usa la clase [ODATADetailLevel][odata] para suministrar cadenas filter, select y expand a las operaciones de lista. La clase ODataDetailLevel tiene tres propiedades de cadena públicas que se pueden especificar en el constructor, o bien establecerse directamente en el objeto. Luego el objeto ODataDetailLevel pasa como parámetro a las distintas operaciones de lista como [ListPools][net_list_pools], [ListJobs][net_list_jobs] y [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[FilterClause][odata_filter]: limita el número de elementos que se devuelven.
* [ODATADetailLevel.SelectClause][odata].[SelectClause][odata_select]: especifica los valores de propiedad devueltos con cada elemento.
* [ODATADetailLevel][odata].[ExpandClause][odata_expand]: recupera datos para todos los elementos en una sola llamada API en lugar de en llamadas independientes para cada elemento.

El siguiente fragmento de código usa la API de .NET para Batch para consultar de forma eficaz las estadísticas de un conjunto específico de grupos en el servicio Batch. En este escenario, el usuario de Batch tiene grupos de prueba y de producción. Los identificadores del grupo de prueba tienen el prefijo "test", mientras que los del grupo de producción tienen el prefijo "prod". En el fragmento de código, *myBatchClient* es una instancia de la clase [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) inicializada correctamente.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Para limitar la cantidad de datos devueltos, también se puede pasar una instancia de [ODATADetailLevel][odata] configurada con las cláusulas Select y Expand a los métodos Get apropiados, como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx).
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Correspondencia entre la API de REST y la API de .NET para Batch
Los nombres de propiedades en las cadenas filter, select y expand *deben* reflejar sus homólogos en la API de REST, tanto en el nombre en sí como en el uso de mayúsculas y minúsculas. En las tablas siguientes, se proporcionan asignaciones entre los homólogos de la API de .NET y la de REST.

### <a name="mappings-for-filter-strings"></a>Asignaciones de las cadenas filter
* **Métodos de lista de .NET**: todos los métodos de la API de .NET de esta columna aceptan un objeto [ODATADetailLevel][odata] como parámetro.
* **Solicitudes de lista de REST**: todas las páginas de la API de REST de esta columna contienen una tabla que especifica las propiedades y operaciones permitidas en las cadenas *filter* . Estos nombres de propiedad y estas operaciones se usarán al construir una cadena [ODATADetailLevel.FilterClause][odata_filter].

| Métodos de lista de .NET | Solicitudes de lista de REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Enumeración de los certificados de una cuenta][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Enumeración de los archivos asociados a una tarea][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Enumeración del estado de la preparación de trabajo y las tareas de la versión de trabajo de un trabajo][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Enumeración de los trabajos de una cuenta][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Enumeración de los archivos de un nodo][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Enumeración de las tareas asociadas a un trabajo][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Enumeración de las programaciones de trabajos de una cuenta][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Enumeración de los trabajos asociados a una programación de trabajo][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Enumeración de los nodos de proceso de un grupo][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Enumeración de los grupos de una cuenta][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Asignaciones de las cadenas select
* **Tipos de .NET de Batch**: tipos de la API de .NET para Batch.
* **Entidades de la API de REST**: todas las páginas de esta columna contienen una o varias tablas que enumeran los nombres de propiedades de la API de REST para el tipo. Estos nombres de propiedades se usan al construir cadenas *select* . Estos mismos nombres se usarán al construir una cadena [ODATADetailLevel.SelectClause][odata_select].

| Tipos de .NET de Batch | Entidades de la API de REST |
| --- | --- |
| [Certificate][net_cert] |[Obtención de información sobre un certificado][rest_get_cert] |
| [CloudJob][net_job] |[Obtención de información sobre un trabajo][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Obtención de información sobre una programación de trabajo][rest_get_schedule] |
| [ComputeNode][net_node] |[Obtención de información sobre un nodo][rest_get_node] |
| [CloudPool][net_pool] |[Obtención de información sobre un grupo][rest_get_pool] |
| [CloudTask][net_task] |[Obtención de información sobre una tarea][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Ejemplo: construcción de una cadena filter
Al construir una cadena filter para [ODATADetailLevel.FilterClause][odata_filter], consulte la tabla en la sección "Asignaciones de las cadenas filter" para buscar la página de documentación de la API de REST correspondiente a la operación de lista que desea realizar. Encontrará las propiedades filtrables y sus operadores admitidos en la primera tabla de varias filas de dicha página. Por ejemplo, si desea recuperar todas las tareas cuyo código de salida era distinto de cero, en [Lista de las tareas asociadas a un trabajo][rest_list_tasks] esta fila especifica la cadena de propiedad aplicable y los operadores permitidos:

| Propiedad | Operaciones permitidas | Tipo |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Por lo tanto, la cadena filter para enumerar todas las tareas con un código de salida distinto de cero sería:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Ejemplo: construcción de una cadena select
Para construir [ODATADetailLevel.SelectClause][odata_select], consulte la tabla de "Asignaciones de las cadenas select" y navegue a la página de la API de REST correspondiente al tipo de entidad que vaya a enumerar. Encontrará las propiedades seleccionables y sus operadores admitidos en la primera tabla de varias filas de dicha página. Por ejemplo, si desea recuperar solo el identificador y la línea de comandos para cada tarea de una lista, encontrará estas filas en la tabla correspondiente en la página sobre cómo [obtener información acerca de una tarea][rest_get_task]:

| Propiedad | Tipo | Notas |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

La cadena select para incluir solo el identificador y la línea de comandos con cada tarea de la lista sería:

`id, commandLine`

## <a name="code-samples"></a>Ejemplos de código
### <a name="efficient-list-queries-code-sample"></a>Código de ejemplo de consultas de lista eficaces
Consulte el proyecto de ejemplo [EfficientListQueries][efficient_query_sample] en GitHub para ver cómo una consulta de lista eficaz puede afectar al rendimiento de una aplicación. Esta aplicación de consola de C# crea y agrega un gran número de tareas a un trabajo. Luego realiza varias llamadas al método [JobOperations.ListTasks][net_list_tasks] y pasa objetos [ODATADetailLevel][odata] que se configuran con distintos valores de propiedad para variar la cantidad de datos que se van a devolver. Genera una salida similar a la siguiente:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Como se muestra en los tiempos transcurridos, se pueden reducir considerablemente los tiempos de respuesta, para lo que es preciso limitar las propiedades y el número de elementos que se devuelven. Puede encontrar este y otros proyectos de ejemplo en el repositorio [azure-batch-samples][github_samples] de GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Biblioteca y código de ejemplo de BatchMetrics
Además del ejemplo de código EfficientListQueries anterior, puede encontrar el proyecto [BatchMetrics][batch_metrics] en el repositorio de GitHub [azure-batch-samples][github_samples]. El proyecto de ejemplo BatchMetrics demuestra cómo supervisar de forma eficiente el progreso del trabajo de Azure Batch mediante la API de Batch.

El ejemplo [BatchMetrics][batch_metrics] incluye un proyecto de biblioteca de clases de .NET que puede incorporar en sus propios proyectos y un programa de línea de comandos simple para probar y demostrar el uso de la biblioteca.

La aplicación de ejemplo del proyecto muestra las siguientes operaciones:

1. Selección de atributos específicos para descargar solo las propiedades que necesita
2. Filtrado en tiempo de transición de estado para descargar solo los cambios desde la última consulta

Por ejemplo, el siguiente método aparece en la biblioteca BatchMetrics. Devuelve un objeto ODATADetailLevel que establece que solo deben obtenerse las propiedades `id` y `state` en las consultas realizadas. También especifica que solo las entidades cuyo estado ha cambiado desde que se ha especificado el parámetro `DateTime` se deben devolver.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Pasos siguientes
### <a name="parallel-node-tasks"></a>Tareas paralelas de nodo
[Maximizar el uso de recursos de proceso de Lote de Azure con tareas simultáneas nodo](batch-parallel-node-tasks.md) es otro artículo relacionada con rendimiento de aplicaciones de Lote. Algunos tipos de cargas de trabajo pueden beneficiarse de la ejecución de tareas paralelas en una menor cantidad de nodos de proceso que, sin embargo, sean de mayor tamaño. Consulte la sección [Escenario de ejemplo](batch-parallel-node-tasks.md#example-scenario) en el artículo para obtener detalles sobre dicho escenario.

### <a name="batch-forum"></a>Foro de Batch
El [foro de Azure Batch][forum] en MSDN es un lugar excelente para debatir y formular preguntas sobre el servicio. Lea los mensajes útiles publicados y envíe sus preguntas a medida que surjan mientras compila sus soluciones del servicio Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
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

[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job