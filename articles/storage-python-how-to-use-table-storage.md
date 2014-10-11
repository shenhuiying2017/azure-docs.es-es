<properties linkid="develop-python-table-service" urlDisplayName="Table Service" pageTitle="How to use table storage (Python) | Microsoft Azure" metaKeywords="Azure table Python, creating table Azure, deleting table Azure, inserting table Azure, querying table Azure" description="Learn how to use the Table service from Python to create and delete a table, and insert, delete, and query the table." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Table Storage Service from Python" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"/>

# Uso del servicio de almacenamiento de tablas desde Python

Esta guía le indicará cómo actuar en situaciones habituales usando el servicio de almacenamiento de tablas de Windows
Azure. Los ejemplos están escritos usando la API
Python. Entre los escenarios descritos se incluyen la **creación y eliminación de una
tabla, la inserción y la consulta de entidades en una tabla**. Para obtener más
información acerca de las tablas, consulte la sección [Pasos siguientes][].

## Tabla de contenido

<p><a href="#what-is">&iquest;Qu&eacute; es el servicio Tabla?</a><br /> <a href="#concepts">Conceptos</a><br /> <a href="#create-account">Creaci&oacute;n de una cuenta de almacenamiento de Azure</a><br /> <a href="#create-table">Creaci&oacute;n de una tabla</a><br /> <a href="#add-entity">Incorporaci&oacute;n de una entidad a una tabla</a><br /> <a href="#update-entity">Actualizaci&oacute;n de una entidad</a><br /> <a href="#change-entities">Cambio de un grupo de entidades</a><br /> <a href="#query-for-entity">Consulta de una entidad</a><br /> <a href="#query-set-entities">Consulta de un conjunto de entidades</a><br /> <a href="#query-entity-properties">Consulta de un subconjunto de propiedades de las entidades</a><br /> <a href="#delete-entity">Eliminaci&oacute;n de una entidad</a><br /> <a href="#delete-table">Eliminaci&oacute;n de una tabla</a><br /> <a href="#next-steps">Pasos siguientes</a></p>

[WACOM.INCLUDE [howto-table-storage][]]

## <a name="create-account"> </a>Creación de una cuenta de almacenamiento de Azure

[WACOM.INCLUDE [create-storage-account][]]

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la [guía de instalación de Python][] (en inglés).

## <a name="create-table"> </a>Creación de una tabla

El objeto **TableService** le permite trabajar con servicios de tabla. El
código siguiente crea un objeto **TableService**. Agregue lo siguiente cerca
de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    from azure.storage import *

El código siguiente crea un objeto **TableService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta: sustituya "myaccount" y "mykey" por la cuenta real y la clave.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-entity"> </a>Incorporación de una entidad a una tabla

Para agregar una entidad, primero cree un diccionario que defina sus nombres y valores de propiedad
de la entidad. Tenga en cuenta que para cada entidad debe
especificar valores en **PartitionKey** y **RowKey**. Estos son los identificadores
exclusivos de sus entidades y son valores que se pueden consultar más rápidamente
que las demás propiedades. El sistema usa **PartitionKey** para
distribuir automáticamente las entidades de tabla entre muchos nodos de almacenamiento.
Las entidades con el mismo valor de **PartitionKey** se almacenan en el mismo nodo.
**RowKey** es el identificador exclusivo de la entidad de la partición a la que
pertenece.

Para agregar una entidad a la tabla, pase un objeto de diccionario
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
por una versión actualizada.

    task = {'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Si la entidad que se está actualizando no existe, la operación de actualización
generará un error. Si desea almacenar una entidad con
independencia de si ya existía antes, use **insert\_or\_replace\_entity**.
En el siguiente ejemplo, la primera llamada reemplazará la entidad existente. La segunda llamada insertará una nueva entidad, debido a que en la tabla no existe ninguna entidad con **PartitionKey** y **RowKey** especificados.

    task = {'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

    task = {'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>Cambio de un grupo de entidades

A veces resulta útil enviar varias operaciones juntas en un
lote a fin de garantizar el procesamiento atómico por parte del servidor. Para ello, debe
usar el método **begin\_batch** en **TableService** y luego llamar a las
series de operaciones como de costumbre. Cuando quiera enviar el
lote, llame a **commit\_batch**. Tenga en cuenta que todas las entidades deben estar en la misma partición para que se pueda cambiar como un lote. El ejemplo siguiente agrega dos entidades juntas en un lote.

    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    table_service.begin_batch()
    table_service.insert_entity('tasktable', task10)
    table_service.insert_entity('tasktable', task11)
    table_service.commit_batch()

## <a name="query-for-entity"> </a>Consulta de una entidad

Para consultar la entidad de una tabla, use el método **get\_entity**,
pasando el valor de **PartitionKey** y de **RpwKey**.

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

Una consulta a una tabla solo puede recuperar algunas propiedades de una entidad.
Esta técnica, conocida como *proyección*, reduce el ancho de banda y puede
mejorar el rendimiento de la consulta, en especial en entidades de gran tamaño. Use el parámetro **select**
 y pase los nombres de las propiedades que desea que lleguen al
cliente.

La consulta del código siguiente devuelve solo las **descripciones** de
las entidades de la tabla.

*Tenga en cuenta que el fragmento siguiente solo funciona con el servicio de almacenamiento en la
nube; el
emulador* de almacenamiento no lo admite.

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

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, utilice estos vínculos
para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][]
-   [Visite el blog del equipo de almacenamiento de Azure][]

  [Pasos siguientes]: #next-steps
  [¿Qué es el servicio Tabla?]: #what-is
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Creación de una tabla]: #create-table
  [Incorporación de una entidad a una tabla]: #add-entity
  [Actualización de una entidad]: #update-entity
  [Cambio de un grupo de entidades]: #change-entities
  [Consulta de una entidad]: #query-for-entity
  [Consulta de un conjunto de entidades]: #query-set-entities
  [Consulta de un subconjunto de propiedades de las entidades]: #query-entity-properties
  [Eliminación de una entidad]: #delete-entity
  [Eliminación de una tabla]: #delete-table
  [howto-table-storage]: ../includes/howto-table-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [guía de instalación de Python]: ../python-how-to-install/
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Visite el blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
