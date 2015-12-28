<properties
	pageTitle="Uso del almacenamiento de tablas de Python | Microsoft Azure"
	description="Aprenda a usar el servicio Tabla de Azure desde Python para crear y eliminar una tabla e insertar y consultar una tabla."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="emgerner"/>


# Uso del almacenamiento de tablas de Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Información general

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento Tabla de Azure. Los ejemplos están escritos en Python y usan el [paquete de almacenamiento de Azure para Python][]. Entre los escenarios descritos se incluyen la creación y eliminación de una tabla, la inserción y la consulta de entidades en una tabla.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.NOTE]Si necesita instalar Python o el [paquete de Azure para Python][], consulte la [Guía de instalación de Python](../python-how-to-install.md).


## Creación de una tabla

El objeto **TableService** le permite trabajar con servicios de tabla. El siguiente código crea un objeto **TableService**. Agregue el código cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

	from azure.storage.table import TableService, Entity

El código siguiente crea un objeto **TableService** usando el nombre de la cuenta de almacenamiento y la clave de la cuenta. sustituya "myaccount" y "mykey" por la cuenta real y la clave.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## Adición de una entidad a una tabla

Para agregar una entidad, primero cree un diccionario que defina sus nombres y valores de propiedad de la entidad. Tenga en cuenta que para cada entidad debe especificar valores en **PartitionKey** y **RowKey** Estos son los identificadores únicos de las entidades. Puede consultar estos valores de una forma mucho más rápida que las demás propiedades. El sistema usa **PartitionKey** para distribuir automáticamente las entidades de la tabla entre varios nodos de almacenamiento. Las entidades con la misma **PartitionKey** se almacenan en el mismo nodo. **RowKey** es el identificador exclusivo de la entidad de la partición a la que pertenece.

Para agregar una entidad a la tabla, pase un objeto de diccionario al método **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

También puede pasar una instancia de la clase **Entity** al método **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## Actualización de una entidad

Este código muestra cómo reemplazar la versión anterior de una entidad existente con una versión actualizada.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Si la entidad que se está actualizando no existe, la operación de actualización generará un error. Si desea almacenar una entidad independientemente de si ya existía antes, use **insert\_or\_replace\_entity**. En el siguiente ejemplo, la primera llamada reemplazará la entidad existente. La segunda llamada insertará una nueva entidad, debido a que en la tabla no existe ninguna entidad con **PartitionKey** y **RowKey** especificados.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## Cambio de un grupo de entidades

A veces resulta útil enviar varias operaciones juntas en un lote a fin de garantizar el procesamiento atómico por parte del servidor. Para ello, debe usar el método **begin\_batch** en **TableService** y, a continuación, llamar a las series de operaciones como de costumbre. Cuando quiera enviar el lote, llame a **commit\_batch**. Tenga en cuenta que todas las entidades deben estar en la misma partición para que se pueda cambiar como un lote. El ejemplo siguiente agrega dos entidades juntas en un lote.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## Consulta de una entidad

Para consultar una entidad en una tabla, use el método **get\_entity**, pasando el valor de **PartitionKey** y **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## Consulta de un conjunto de entidades

Este ejemplo busca todas las tareas en Seattle en función de la **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## Consulta de un subconjunto de propiedades de las entidades

Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada *proyección*, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. Use el parámetro **select** y pase los nombres de las propiedades que desea que lleguen al cliente.

La consulta del código siguiente devuelve solo las descripciones de las entidades de la tabla.

[AZURE.NOTE]El siguiente fragmento de código funciona solo con el servicio de almacenamiento de nube. Esto no es compatible con el emulador de almacenamiento.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## Eliminación de una entidad

Puede eliminar una entidad usando la clave de partición y fila.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## Eliminación de una tabla

El código siguiente elimina una tabla de la cuenta de almacenamiento.

	table_service.delete_table('tasktable')

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, use estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas:

-   Vea la referencia de MSDN [Almacenamiento de Azure][].
-   Visite el [Blog del equipo de Almacenamiento de Azure][]

Para obtener más información, consulte también el [Centro para desarrolladores de Python](/develop/python/).

[Blog del equipo de Almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[paquete de Azure para Python]: https://pypi.python.org/pypi/azure
[paquete de almacenamiento de Azure para Python]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=AcomDC_1217_2015-->