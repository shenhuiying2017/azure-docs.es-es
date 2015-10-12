<properties
	pageTitle="Consultas de lista eficaces en Lote de Azure | Microsoft Azure"
	description="Aprenda a reducir la cantidad de datos devueltos y aumente el rendimiento al consultar los grupos, trabajos, tareas, nodos de ejecución, etc. de Lote de Azure."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="Batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="09/24/2015"
	ms.author="davidmu;v-marsma"/>

# Consultas de lista de Lote eficaces

Lote de Azure es Big Compute, y en un entorno de producción, algunas entidades como los trabajos, las tareas y los nodos de ejecución pueden contarse por millares. Por lo tanto, la obtención de información acerca de estos elementos puede generar una gran cantidad de datos que se deben transferir en cada consulta. La limitación del número de elementos y del tipo de información que se devuelve de cada uno de ellos aumentará la velocidad de las consultas y, por consiguiente, el rendimiento de la aplicación.

Los siguientes métodos de la API de [.NET de Lote](https://msdn.microsoft.com/library/azure/mt348682.aspx) son ejemplos de operaciones que prácticamente todas las aplicaciones que usen Lote de Azure deben realizar, a menudo con frecuencia:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListComputeNodes](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx)

La supervisión es un caso de uso común; por ejemplo, la determinación de la capacidad y estado de un grupo requiere que se consulten todos los nodos de ejecución (máquinas virtuales) de un grupo. Otro ejemplo es la consulta de las tareas de un trabajo para determinar si alguna de ella sigue en cola. En algunos casos se requiere un conjunto de datos enriquecido, mientras que en otros solo se requiere un recuento del número total de elementos o una colección de elementos que están en un estado determinado.

Es importante tener en cuenta que tanto el número de elementos que devueltos como la cantidad de datos requeridos para representar la lista de elementos pueden ser muy grandes. La mera consulta de una gran cantidad de elementos que genere respuestas grandes puede provocar muchos problemas:

- Los tiempos de respuesta de API de lote pueden hacerse demasiado lentos. Cuanto mayor sea que el número de elementos, más tiempo de consulta necesita el servicio Lote. Las grandes cantidades de elementos deben dividirse en fragmentos y, por tanto, la biblioteca del cliente puede tener que realizar varias llamadas de API de servicio al servicio para obtener todos los elementos de una sola lista.
- El procesamiento de API por la aplicación que llama a Lote tardará más tiempo cuantos más elementos haya que procesar.
- Cuando hay más elementos, o elementos más grandes, la aplicación que llama a Lote consume más memoria.
- Un mayor número de elementos o unos elementos de mayor tamaño aumentarán el tráfico de red. Esto tardará más tiempo en transferir y, según la arquitectura de aplicaciones, puede producir un aumento de cargas de red para los datos transferidos fuera de la región de la cuenta de procesamiento por lotes.

> [AZURE.IMPORTANT]Es *muy* recomendable usar *siempre* cláusulas Filter y Select para las llamadas a la API de la lista a fin de garantizar la máxima eficacia y el mejor rendimiento de su aplicación. Estas cláusulas y su uso se describen a continuación.

En todas las API de Lote se aplica lo siguiente:

- Cada nombre de propiedad es una cadena que se asigna a la propiedad del objeto.
- Todos los nombres de propiedad distinguen mayúsculas de minúsculas, pero los valores de propiedad no.
- Los nombres y el uso de mayúsculas o minúsculas en las propiedades son como los elementos aparecen en la API de REST de Lote
- Las cadenas de fecha/hora se pueden especificar en dos formatos y deben ir precedidas de DateTime.
	- W3CDTF (por ejemplo, *creationTime gt DateTime'2011-05-08T08:49:37Z'*)
	- RFC1123 (por ejemplo, *creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'*)
- Las cadenas booleanas son "true" o "false".
- Si se especifica un operador o una propiedad no válidos, aparecerá el error "400 (solicitud incorrecta)"

## Consultas eficaces en .NET de Lote

La API de .NET de Lote proporciona la capacidad de reducir tanto el número de elementos devueltos en una lista como la cantidad de información devuelta de cada elemento, para lo que se especifica la clase [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) de una consulta. DetailLevel es una clase base abstracta y, de hecho, es necesario crear y pasar un objeto [ODATADetailLevel][odata] como parámetro en los métodos apropiados.

Un objeto ODataDetailLevel tiene tres propiedades de cadena públicas que se pueden especificar en el constructor o establecerse directamente:

- [FilterClause](#filter): filtrar y reducir el número de elementos devueltos
- [SelectClause](#select): especificar un subconjunto de los valores de propiedad que se devuelven para cada elemento, lo que reduce el tamaño del elemento y de la respuesta
- [ExpandClause](#expand): devolver todos los datos necesarios en una llamada en lugar de varias llamadas

> [AZURE.TIP]Para limitar la cantidad de datos devueltos, también se puede pasar una instancia de DetailLevel configurada con las cláusulas Select y Expand a los métodos Get apropiados como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx).

### <a id="filter"></a> FilterClause

El número de elementos devuelto puede reducirse mediante una cadena de filtro. Se pueden especificar uno o varios valores de propiedad con calificadores para asegurarse de que solo se devuelven los elementos relevantes para la consulta. Por ejemplo, quizás desee mostrar solo las tareas en ejecución de un trabajo o solo los nodos de ejecución que estén listos para ejecutar tareas.

 [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) es una cadena que se compone de una o varias expresiones, con una expresión que consta de un *nombre de propiedad*, un *operador* y un *valor*. Las propiedades que se pueden especificar son específicas de cada llamada API, como los operadores admitidos para cada propiedad. Con los operadores lógicos **and** y **or** se pueden combinar varias expresiones.

Por ejemplo, esta cadena de filtro devuelve solo las tareas en ejecución cuyo *displayName* comienza por "MyTask":

	startswith(displayName, 'MyTask') and (state eq 'Running')

Todos los artículos sobre la API de REST de Lote siguientes contienen una tabla que especifica las propiedades compatibles y las operaciones sobre dichas propiedades para las operaciones de lista diferente.

- [los grupos de una cuenta](https://msdn.microsoft.com/library/azure/dn820101.aspx)
- [Enumeración de los nodos de ejecución de un grupo](https://msdn.microsoft.com/library/azure/dn820159.aspx)
- [Enumeración de los trabajos de una cuenta](https://msdn.microsoft.com/library/azure/dn820117.aspx)
- [Enumeración del estado de la preparación de trabajo y las tareas de la versión de trabajo de un trabajo](https://msdn.microsoft.com/library/azure/mt282170.aspx)
- [Enumeración de las programaciones de trabajos de una cuenta](https://msdn.microsoft.com/library/azure/mt282174.aspx)
- [Enumeración de los trabajos asociados a una programación de trabajo](https://msdn.microsoft.com/library/azure/mt282169.aspx)
- [Enumeración de las tareas asociadas a un trabajo](https://msdn.microsoft.com/library/azure/dn820187.aspx)
- [Enumeración de los archivos asociados a una tarea](https://msdn.microsoft.com/library/azure/dn820142.aspx)
- [Enumeración de los certificados de una cuenta](https://msdn.microsoft.com/library/azure/dn820154.aspx)
- [Enumeración de los archivos de un nodo](https://msdn.microsoft.com/library/azure/dn820151.aspx)

> [AZURE.IMPORTANT]Al especificar las propiedades de cualquiera de los tres tipos de cláusula, asegúrese de que el nombre de propiedad y el uso de mayúsculas y minúsculas coinciden con el de sus homólogos del elemento de la API de REST de Lote. Por ejemplo, cuando se trabaja con [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) de .NET, es preciso especificar **state** en lugar de **State**, aun cuando la propiedad de .NET sea [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Por ejemplo, para comprobar el nombre y el uso de mayúsculas y minúsculas correctos de la propiedad **state**, debe comprobar el nombre del elemento en [Obtener una tarea](https://msdn.microsoft.com/library/azure/dn820133.aspx) en la documentación de la API de REST de Lote.

### <a id="select"></a> SelectClause

Los valores de propiedad que se devuelven para cada elemento se pueden limitar mediante el uso de una cadena de selección. Puede especificar una lista de propiedades para un elemento y, a continuación, se devuelven únicamente los valores de propiedad.

Un [SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) es una cadena que consta de una lista separada por comas de nombres de propiedades. Se puede especificar cualquier combinación de las propiedades disponibles para un elemento devueltas por una operación de lista.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

El número de llamadas a API se puede reducir con una cláusula Expand. Puede obtener información más detallada sobre cada elemento de la lista con una llamada a API individual, en lugar de obtener la lista y, a continuación, iterar en la lista, mediante la realización de una llamada a cada uno de los elementos de la lista.

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) es similar a la cláusula Select en que controla si se devuelven determinados datos en los resultados. La cláusula Expand solo se admite para las listas de tareas, grupos y trabajos, y actualmente solo admite información de estadísticas. Cuando se requieren todas las propiedades y no se especifica una cláusula Select, la cláusula Expand debe usarse para obtener información de estadísticas. Si se usa una cláusula Select para obtener un subconjunto de propiedades, también se pueden especificar "estadísticas" en la cláusula Select y la cláusula Expand puede dejarse en null.

## Ejemplo de consulta eficaz

A continuación encontrará un fragmento de código que usa la API de .NET de Lote para consultar de forma eficaz en el servicio Lote las estadísticas de un conjunto específico de grupos. En este escenario, el usuario de Lote tiene grupos de prueba y de producción; los Id. del grupo de prueba tienen el prefijo "test" y los Id. del grupo de producción tienen el prefijo "prod". En el fragmento de código, *myBatchClient* es una instancia de [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) inicializada correctamente.

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// We want to pull only the "test" pools, so we limit the items returned by using a Filterclause and
	// specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = myBatchClient.PoolOperations.ListPools(detailLevel).ToList();

## Proyecto de ejemplo

Consulte el proyecto de ejemplo [EfficientListQueries][efficient_query_sample] en GitHub para ver cómo una consulta eficaz de la lista puede afectar al rendimiento de una aplicación. Esta aplicación de consola de C# crea un gran número de tareas y las agrega a un trabajo, después consulta el servicio de Lote con distintas especificaciones [ODATADetailLevel][odata] y muestra una salida similar a la siguiente:

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

## Pasos siguientes

1. Si aún no lo ha hecho, asegúrese de comprobar la documentación de la API de Lote relevante para el escenario de desarrollo
    - [REST de Lote](https://msdn.microsoft.com/library/azure/dn820158.aspx)
    - [.NET de Lote](https://msdn.microsoft.com/library/azure/dn865466.aspx)
2. Consulte los [ejemplos de Lote de Azure](https://github.com/Azure/azure-batch-samples) en GitHub y profundice en el código

[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx

<!---HONumber=Oct15_HO1-->