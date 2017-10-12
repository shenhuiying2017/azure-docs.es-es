---
title: Uso de Azure Table Storage con Python | Microsoft Docs
description: "Almacene datos estructurados en la nube con el Almacenamiento de tablas de Azure, un almacén de datos NoSQL."
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/16/2017
ms.author: mimig
ms.openlocfilehash: 0c46f04786ba4b62bd7ca22c5e25643123e6e136
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-table-storage-in-python"></a>Uso de Table Storage en Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

En esta guía se explica cómo realizar escenarios comunes de Azure Table Storage en Python con la [SDK de Microsoft Azure Storage para Python](https://github.com/Azure/azure-storage-python). Entre los escenarios descritos se incluyen la creación y eliminación de una tabla, la inserción y la consulta de entidades.

Mientras trabaja con los escenarios de este tutorial, puede que desee consultar la [referencia de SDK de Azure Storage para la API de Python](https://azure-storage.readthedocs.io/en/latest/index.html).

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="install-the-azure-storage-sdk-for-python"></a>Instalación del SDK de Azure Storage para Python

Una vez que ha creado una cuenta de almacenamiento, el paso siguiente consiste en instalar el [SDK de Microsoft Azure Storage para Python](https://github.com/Azure/azure-storage-python). Para más información acerca de la instalación del SDK, consulte el archivo [README.rst](https://github.com/Azure/azure-storage-python/blob/master/README.rst) del SDK de Storage del repositorio de Python en GitHub.

## <a name="create-a-table"></a>Creación de una tabla

Para trabajar con Azure Table Service en Python, debe importar el módulo [TableService][py_TableService]. Dado que trabajará con entidades de Table, también se necesitará la clase [Entidad][py_Entity]. Agregue este código cerca de la parte superior del archivo de Python para importar ambos:

```python
from azure.storage.table import TableService, Entity
```

Cree un objeto [TableService][py_TableService] mediante la transmisión del nombre de la cuenta de almacenamiento y la clave de cuenta. Reemplace `myaccount` y `mykey` por su nombre y clave de cuenta, y llame a [create_table][py_create_table] para crear la tabla en Azure Storage.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla

Para agregar una entidad, cree primero un objeto que represente a la entidad y, a continuación, pase el objeto al método [TableService][py_TableService].[insert_entity][py_insert_entity]. El objeto de la entidad puede ser un diccionario o un objeto del tipo [Entidad][py_Entity] y define los nombres de propiedad y los valores de la entidad. Cada entidad debe incluir las propiedades [PartitionKey y RowKey](#partitionkey-and-rowkey) necesarias, además de cualquier otra propiedad que se defina para la entidad.

En este ejemplo se crea un objeto de diccionario que representa a una entidad y, a continuación, lo pasa al método [insert_entity][py_insert_entity] para agregarlo a la tabla:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

En este ejemplo se crea un objeto de [Entidad][py_Entity] y, a continuación, lo pasa al método [insert_entity][py_insert_entity] para agregarlo a la tabla:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey y RowKey

Para cada entidad debe especificar una propiedad **PartitionKey** y **RowKey**. Estos son los identificadores únicos de las entidades y juntos forman la clave principal de una entidad. Puede realizar consultas mediante estos valores de forma mucho más rápida que si consulta cualquier otra propiedad de la entidad ya que estas propiedades son las únicas que se indexan.

Table Service usa **PartitionKey** para distribuir de forma inteligente las entidades de tabla entre los nodos de almacenamiento. Las entidades con la misma **PartitionKey** se almacenan en el mismo nodo. **RowKey** es el identificador exclusivo de la entidad de la partición a la que pertenece.

## <a name="update-an-entity"></a>Actualización de una entidad

Para actualizar todos los valores de propiedad de una entidad, llame al método [update_entity][py_update_entity]. Este ejemplo muestra cómo reemplazar una entidad existente con una versión actualizada:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Si la entidad que se está actualizando no existe, la operación de actualización generará un error. Si desea almacenar una entidad independientemente de si existe o no, use [insert_or_replace_entity][py_insert_or_replace_entity]. En el siguiente ejemplo, la primera llamada reemplazará la entidad existente. La segunda llamada insertará una nueva entidad, debido a que en la tabla no existe ninguna entidad con PartitionKey y RowKey especificados.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> El método [update_entity][py_update_entity] reemplaza todas las propiedades y valores de una entidad existente y también se puede usar para quitar las propiedades de otra de ellas. Puede usar el método [merge_entity][py_merge_entity] para actualizar una entidad existente con valores de propiedad nuevos o modificados sin reemplazar completamente la entidad.

## <a name="modify-multiple-entities"></a>Modificación de varias entidades

Para garantizar el procesamiento atómico de una solicitud por parte de Table Service, puede enviar varias operaciones en un lote. En primer lugar, use la clase [TableBatch][py_TableBatch] para agregar varias operaciones a un único lote. A continuación, llame a [TableService][py_TableService].[commit_batch][py_commit_batch] para enviar las operaciones de una operación atómica. Todas las entidades que desea modificar del lote deben estar en la misma partición.

En este ejemplo se agregan dos entidades juntas en un lote:

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Los lotes también pueden usarse con la sintaxis de administrador de contexto:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Consulta de una entidad

Para consultar una entidad de una tabla, pase sus propiedades PartitionKey y RowKey al método [TableService][py_TableService].[get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Consulta de un conjunto de entidades

Puede consultar un conjunto de entidades proporcionando una cadena de filtro con el parámetro **filter**. En este ejemplo se buscan todas las tareas en Seattle mediante la aplicación de un filtro en PartitionKey:

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Consulta de un subconjunto de propiedades de las entidades

También puede restringir qué propiedades se devuelven para cada entidad en una consulta. Esta técnica, denominada *proyección*, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades o conjuntos de resultados de gran tamaño. Use el parámetro **select** y pase los nombres de las propiedades que desea que se devuelvan al cliente.

La consulta del código siguiente devuelve solo las descripciones de las entidades de la tabla.

> [!NOTE]
> El siguiente fragmento de código funciona solo con Azure Storage. Esto no es compatible con el emulador de almacenamiento.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Eliminación de una entidad

Elimine una entidad pasando sus propiedades PartitionKey y RowKey al método [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Eliminación de una tabla

Si ya no necesita una tabla ni ninguna de las entidades dentro de ella, llame al método [delete_table][py_delete_table] para eliminar permanentemente la tabla de Azure Storage.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Pasos siguientes

* [Referencia del SDK de Azure Storage para la API de Python](https://azure-storage.readthedocs.io/en/latest/index.html)
* [SDK de Azure Storage para Python](https://github.com/Azure/azure-storage-python)
* [Centro para desarrolladores de Python](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): una aplicación gratuita y multiplataforma para trabajar visualmente con datos de Azure Storage en Windows, Mac OS y Linux.

[py_commit_batch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.commit_batch
[py_create_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.create_table
[py_delete_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_entity
[py_delete_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_table
[py_Entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.models.html#azure.storage.table.models.Entity
[py_get_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.get_entity
[py_insert_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_entity
[py_insert_or_replace_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_or_replace_entity
[py_merge_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.merge_entity
[py_update_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.update_entity
[py_TableService]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html
[py_TableBatch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tablebatch.html#azure.storage.table.tablebatch.TableBatch
