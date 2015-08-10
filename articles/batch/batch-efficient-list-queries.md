<properties
	pageTitle="Consultas de lista eficaces"
	description="Aprenda cómo reducir la cantidad de información devuelta para cada elemento."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Consultas de lista eficaces

Los métodos siguientes son ejemplos de operaciones que prácticamente todas las aplicaciones mediante el proceso de Azure tienen que realizar, a menudo con frecuencia:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

La supervisión es un caso común; determinar la capacidad y el estado de un conjunto requiere que se consulten todas las VM del grupo, por ejemplo. Otro ejemplo sería consultar las tareas de un trabajo para determinar si aún haylas tareas en cola. En algunos casos se necesita un amplio conjunto de datos, pero en otros casos es necesario únicamente un recuento del número total de elementos o elementos en un estado determinado.

Es importante tener en cuenta que el número de elementos que se pueden devolver puede ser muy grande y el tamaño de los datos necesarios para representar la lista de elementos también puede ser muy grande. Simplemente consultar una gran cantidad de elementos que produzca respuestas grandes puede dar lugar a muchos problemas:

- Los tiempos de respuesta de API de lote pueden hacerse demasiado lentos. Cuanto mayor sea que el número de elementos, más tiempo de consulta necesita el servicio de proceso por lotes. Las grandes cantidades de elementos deben dividirse en bloques y, por tanto, pueden realizarse varias llamadas de API de servicio desde la biblioteca cliente al servicio para obtener todos los elementos de una lista.
- El procesamiento de API del lote de llamadas de aplicación tardará más tiempo conforme más elementos haya que procesar.
- Se consume más memoria en el lote de llamadas de aplicación, ya que hay más elementos o elementos más grandes.
- Un mayor número de elementos o unos elementos de mayor tamaño aumentarán el tráfico de red. Esto tardará más tiempo en transferir y, según la arquitectura de aplicaciones, puede producir un aumento de cargas de red para los datos transferidos fuera de la región de la cuenta de procesamiento por lotes.

La API de lote proporciona la capacidad de reducir el número de elementos devueltos en una lista, así como de reducir la cantidad de información devuelta para cada elemento. Un parámetro de tipo [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) puede especificarse para las operaciones de la lista. DetailLevel es una clase de base abstracta y un objeto [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) realmente necesita crearse y pasar como parámetro.

Para todas las API, se aplica lo siguiente:

- Cada nombre de propiedad es una cadena que se asigna a la propiedad del objeto.
- Todos los nombres de propiedad distinguen mayúsculas de minúsculas, pero los valores de propiedad no.
- Las cadenas de fecha/hora pueden tener uno de dos formatos y deben ir precedidas por DateTime.
	- W3CDTF (por ejemplo, creationTime gt DateTime’2011-05-08T08:49:37Z’)
	- RFC1123 (por ejemplo, creationTime gt DateTime’Sun, 08 mayo 2011 08:49:37 GMT’)
- Las cadenas booleanas son "true" o "false".
- Si se especifica una propiedad o un operador no válidos, a continuación, se creará una excepción con una excepción interna "400 (solicitud incorrecta)".
- El parámetro DetailLevel con las cláusulas Select y Expand también se puede pasar a los métodos "Get" adecuados; por ejemplo, IPoolManager.GetPool().

El objeto ODataDetailLevel tiene tres propiedades públicas que pueden estar especificadas en el constructor o establecerse directamente. Las tres propiedades son todas las cadenas:

- [FilterClause](#filter): filtrar y reducir el número de elementos devueltos
- [SelectClause](#select): especificar los valores de propiedad específicos que se devuelven, reducir el tamaño del elemento y respuesta
- [ExpandClause](#expand): devolver todos los datos necesarios en una llamada en lugar de varias llamadas

### <a id="filter"></a> FilterClause

El número de elementos devuelto puede reducirse mediante una cadena de filtro. Uno o varios valores de propiedad pueden especificarse para asegurarse de que se devuelven solo los elementos necesarios. Por ejemplo, enumerar solo los elementos de trabajo activos, solo las tareas en ejecución para un trabajo o solo las VM listas para ejecutar tareas.

Una [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) es una cadena que consta de una o varias expresiones, con una expresión que consta de un nombre de propiedad, un operador y un valor. Las propiedades que se pueden especificar son específicas de cada llamada API, como los operadores admitidos para cada propiedad. Se pueden combinar varias expresiones con operadores lógicos "and" y "o".

Por ejemplo, un filtro para enumerar las tareas podría ser:

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Los valores de propiedad que se devuelven para cada elemento se pueden limitar mediante el uso de una cadena de selección. Puede especificar una lista de propiedades para un elemento y, a continuación, se devuelven únicamente los valores de propiedad.

Un [SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) es una cadena que consta de una lista separada por comas de nombres de propiedades. Se pueden especificar todas las propiedades en el elemento devueltas por la operación de lista.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Se puede reducir el número de llamadas API con una cadena de expansión. Puede obtener información más detallada sobre cada elemento de lista obtenido con la llamada API de una lista en lugar de obtener la lista y, a continuación, realizar una llamada para cada elemento en la lista.

Un [ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) es similar a la cláusula Select, la cláusula Expand controla si determinados datos se devuelven en los resultados. La cláusula Expand solo se admite para las listas de workitem, task, pool y job; actualmente solo admite información de estadísticas. Cuando todas las propiedades son necesarias y no hay una cláusula select, la cláusula expand debe utilizarse para obtener información de estadísticas. Si se utiliza una cláusula Select para obtener un subconjunto de propiedades, a continuación, se pueden especificar las estadísticas en la cláusula Select y la cláusula Expand puede dejarse en null.

> [AZURE.NOTE]Se recomienda que siempre utilice el filtro y seleccione cláusulas para las llamadas API de la lista para garantizar la máxima eficacia y el mejor rendimiento de su aplicación.

<!---HONumber=July15_HO5-->