<properties urlDisplayName="Table Service" pageTitle="Uso del almacenamiento de tablas (Python) | Microsoft Azure" metaKeywords="Azure table Python, creating table Azure, deleting table Azure, inserting table Azure, querying table Azure" description="Aprenda a utilizar el servicio Tabla de Azure de Python para crear y eliminar una tabla e insertar, eliminar y consultar la tabla." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Table Storage Service from Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="robmcm" />





# Uso del servicio de almacenamiento de tablas desde Python 
Esta guía le indicará cómo actuar en situaciones habituales usando el servicio de almacenamiento de tablas de Microsoft Azure. Los ejemplos están escritos usando la API Python. Entre los escenarios descritos se incluyen la **creación y eliminación de una tabla, la inserción y la consulta de entidades en una tabla**. Para obtener más información acerca de las tablas, consulte la sección [Pasos siguientes][].

## Tabla de contenido

[¿Qué es el servicio Tabla?][]   
 [Conceptos][]   
 [Creación de una cuenta de almacenamiento de Azure][]   
 [Carga una tabla][]   
 [Carga una entidad a una tabla][]   
 [Carga de una entidad][]   
 [Cambio de un grupo de entidades][]   
 [Cambio una entidad][]   
 [Cambio un conjunto de entidades][]   
 [Cambio de un subconjunto de propiedades de las entidades][]   
 [Cambio de una entidad][]   
 [Cambio de una tabla][]   
 [Pasos siguientes][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"> </a>Creación de una cuenta de almacenamiento de Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la  [guía de instalación de Python](../python-how-to-install/).


## <a name="create-table"> </a>Creación de una tabla

El objeto **TableService** le permite trabajar con servicios de tabla. El siguiente código crea un objeto **TableService**. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

	from azure.storage import TableService, Entity

El código siguiente crea un objeto **TableService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta:  sustituya "myaccount" y "mykey" por la cuenta real y la clave.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## <a name="add-entity"> </a>Incorporación de una entidad a una tabla

Para agregar una entidad, primero cree un diccionario que defina los
valores y nombres de las propiedades de la entidad. Tenga en cuenta que para cada entidad debe
especificar un valor para **PartitionKey** y **RowKey**. Se trata de los identificadores exclusivos
de las entidades, que son valores que se pueden consultar con mucha más rapidez
que otras propiedades. El sistema usa **PartitionKey** para
distribuir automáticamente las entidades de tabla entre muchos nodos de almacenamiento.
Las entidades con la misma **PartitionKey** se almacenan en el mismo nodo. El valor
**RowKey** es el identificador exclusivo de la entidad de la partición a la que
pertenece.

Para agregar una entidad a su tabla, pase un objeto de diccionario
al método **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

También puede pasar una instancia de la clase **Entity** al método **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>Actualización de una entidad

Este código muestra cómo reemplazar la versión anterior de una entidad existente
con una versión actualizada.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Si la entidad que se está actualizando no existe, la operación de actualización generará un error. Si desea almacenar una entidad sin considerar si ya existía antes, utilice **insert\_or\_replace_entity**.  En el siguiente ejemplo, la primera llamada reemplazará la entidad existente. La segunda llamada insertará una nueva entidad, debido a que en la tabla no existe ninguna entidad con **PartitionKey** y **RowKey** especificados.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>Cambio de un grupo de entidades

A veces resulta útil enviar varias operaciones juntas en un lote a fin de garantizar el procesamiento atómico por parte del servidor. Para ello, debe usar el método **begin\_batch** en **TableService** y, a continuación, llamar a las series de operaciones como de costumbre. Cuando quiera enviar el lote, llame a **commit\_batch**. Tenga en cuenta que todas las entidades deben estar en la misma partición para que se pueda cambiar como un lote. El ejemplo siguiente agrega dos entidades juntas en un lote.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## <a name="query-for-entity"> </a>Consulta de una entidad

Para consultar una entidad en una tabla, use el método **get\_entity**; para ello, pase
los valores **PartitionKey** y **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## <a name="query-set-entities"> </a>Consulta de un conjunto de entidades

Este ejemplo busca todas las tareas de Seattle en función de la **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## <a name="query-entity-properties"> </a>Consulta de un subconjunto de propiedades de las entidades

Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada *proyección*, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. Utilice el parámetro **select** y pase los nombres de las propiedades que desea que lleguen al cliente.

La consulta del código siguiente devuelve solo las **descripciones** de las entidades de la tabla.

Tenga en cuenta que el fragmento siguiente solo funciona con el servicio de almacenamiento en la nube; el emulador de almacenamiento
no lo admite.*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## <a name="delete-entity"> </a>Eliminación de una entidad

Puede eliminar una entidad usando la clave de partición y fila.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-table"> </a>Eliminación de una tabla

El código siguiente elimina una tabla de la cuenta de almacenamiento.

	table_service.delete_table('tasktable')

## <a name="next-steps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][]
-   [Visite el Blog del equipo de almacenamiento de Azure][]

  [Pasos siguientes]: #next-steps
  [¿Qué es el servicio Tabla?]: #what-is
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Carga una tabla]: #create-table
  [Carga una entidad a una tabla]: #add-entity
  [Carga de una entidad]: #update-entity
  [Cambio de un grupo de entidades]: #change-entities
  [Cambio una entidad]: #query-for-entity
  [Cambio un conjunto de entidades]: #query-set-entities
  [Cambio de un subconjunto de propiedades de las entidades]: #query-entity-properties
  [Cambio de una entidad]: #delete-entity
  [Cambio de una tabla]: #delete-table
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Visite el Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35.1-->
