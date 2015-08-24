<properties
	pageTitle="Consultas de lista eficaces en Lote de Azure | Microsoft Azure"
	description="Obtenga información sobre cómo reducir el número de elementos de Lote de Azure devueltos en una lista, así como de reducir la cantidad de información devuelta para cada elemento."
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
	ms.date="08/04/2015"
	ms.author="davidmu"/>

# Consultas de lista de Lote eficaces

Los métodos siguientes son ejemplos de operaciones que prácticamente todas las aplicaciones mediante el proceso de Azure tienen que realizar, a menudo con frecuencia:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx)

La supervisión es un caso de uso común; la determinación de la capacidad y el estado de un grupo requiere que se consulten todos los nodos de proceso (máquinas virtuales) de un grupo, por ejemplo. Otro ejemplo es consultar las tareas de un trabajo para determinar si aún hay alguna tarea en la cola. En algunos casos se necesita un amplio conjunto de datos, pero en otros casos es necesario únicamente un recuento del número total de elementos o elementos en un estado determinado.

Es importante tener en cuenta que el número de elementos que se pueden devolver y el tamaño de los datos necesarios para representar la lista de elementos puede ser muy grande. Simplemente consultar una gran cantidad de elementos que produzca respuestas grandes puede dar lugar a muchos problemas:

- Los tiempos de respuesta de API de lote pueden hacerse demasiado lentos. Cuanto mayor sea que el número de elementos, más tiempo de consulta necesita el servicio Lote. Las grandes cantidades de elementos deben dividirse en bloques y, por tanto, la biblioteca del cliente puede tener que realizar varias llamadas de API de servicio al servicio para obtener todos los elementos de una lista.
- El procesamiento de API por la aplicación que llama a Lote tardará más tiempo cuantos más elementos haya que procesar.
- Se consume más memoria en el lote de llamadas de aplicación, ya que hay más elementos o elementos más grandes.
- Un mayor número de elementos o unos elementos de mayor tamaño aumentarán el tráfico de red. Esto tardará más tiempo en transferir y, según la arquitectura de aplicaciones, puede producir un aumento de cargas de red para los datos transferidos fuera de la región de la cuenta de procesamiento por lotes.

La API de Lote proporciona la capacidad de reducir el número de elementos devueltos en una lista y la cantidad de información devuelta para cada elemento. Un parámetro de tipo [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) puede especificarse para las operaciones de la lista. DetailLevel es una clase de base abstracta y, de hecho, es necesario crear y pasar un objeto [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) como parámetro.

Para todas las API, se aplica lo siguiente:

- Cada nombre de propiedad es una cadena que se asigna a la propiedad del objeto.
- Todos los nombres de propiedad distinguen mayúsculas de minúsculas, pero los valores de propiedad no.
- Las cadenas de fecha/hora pueden tener uno de dos formatos y deben ir precedidas por DateTime.
	- W3CDTF (por ejemplo, creationTime gt DateTime’2011-05-08T08:49:37Z’)
	- RFC1123 (por ejemplo, creationTime gt DateTime’Sun, 08 May 2011 08:49:37 GMT’)
- Las cadenas booleanas son "true" o "false".
- Si se especifica una propiedad o un operador no válidos, a continuación, se creará una excepción con una excepción interna "400 (solicitud incorrecta)".
- El parámetro DetailLevel con las cláusulas Select y Expand también se puede pasar a los métodos "Get" adecuados; por ejemplo, PoolOperations.GetPool().

El objeto ODataDetailLevel tiene tres propiedades públicas que pueden estar especificadas en el constructor o establecerse directamente. Las tres propiedades son todas las cadenas:

- [FilterClause](#filter): filtrar y reducir el número de elementos devueltos
- [SelectClause](#select): especificar los valores de propiedad específicos que se devuelven, reducir el tamaño del elemento y respuesta
- [ExpandClause](#expand): devolver todos los datos necesarios en una llamada en lugar de varias llamadas

### <a id="filter"></a> FilterClause

El número de elementos devuelto puede reducirse mediante una cadena de filtro. Uno o varios valores de propiedad pueden especificarse para asegurarse de que se devuelven solo los elementos necesarios. Aquí hay un par de ejemplos: enumerar solo las tareas en ejecución para un trabajo o enumerar solo los nodos de proceso que están listos para ejecutar tareas.

Una [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) es una cadena que consta de una o varias expresiones, con una expresión que consta de un nombre de propiedad, un operador y un valor. Las propiedades que se pueden especificar son específicas de cada llamada API, como los operadores admitidos para cada propiedad. Se pueden combinar varias expresiones con operadores lógicos "and" y "o".

Por ejemplo, un filtro para enumerar las tareas podría ser:

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Los valores de propiedad que se devuelven para cada elemento se pueden limitar mediante el uso de una cadena de selección. Puede especificar una lista de propiedades para un elemento y, a continuación, se devuelven únicamente los valores de propiedad.

Un [SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) es una cadena que consta de una lista separada por comas de nombres de propiedades. Se pueden especificar todas las propiedades en el elemento devueltas por la operación de lista.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Se puede reducir el número de llamadas API con una cadena de expansión. Puede obtener información más detallada sobre cada elemento de lista obtenido con la llamada API de una lista en lugar de obtener la lista y, a continuación, realizar una llamada para cada elemento en la lista.

Un [ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) es similar a la cláusula Select, porque controla si determinados datos se devuelven en los resultados. La cláusula Expand solo se admite para las listas de tareas, grupos y trabajos; actualmente solo admite información de estadísticas. Cuando todas las propiedades son necesarias y no hay una cláusula select, la cláusula expand debe utilizarse para obtener información de estadísticas. Si se utiliza una cláusula Select para obtener un subconjunto de propiedades, a continuación, se pueden especificar las estadísticas en la cláusula Select y la cláusula Expand puede dejarse en null.

> [AZURE.NOTE]Se recomienda que siempre utilice el filtro y seleccione cláusulas para las llamadas API de la lista para garantizar la máxima eficacia y el mejor rendimiento de su aplicación.

<!---HONumber=August15_HO7-->