---
title: "Introducción a Azure Table Storage mediante Python | Microsoft Docs"
description: "Almacene datos estructurados en la nube con el Almacenamiento de tablas de Azure, un almacén de datos NoSQL."
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/08/2018
ms.author: mimig
ms.openlocfilehash: 2c8c7dc6d3bdb6ba34818d7e36739297cffbe2d2
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="get-started-with-azure-table-storage-using-python"></a>Introducción a Azure Table Storage mediante Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage es un servicio que almacena datos NoSQL estructurados en la nube y proporciona un almacén de claves y atributos con un diseño sin esquema. Como Almacenamiento de tablas carece de esquema, es fácil adaptar los datos a medida que evolucionan las necesidades de la aplicación. El acceso a los datos de Table Storage es rápido y rentable para muchos tipos de aplicaciones y, por lo general, el costo es normalmente menor que con el SQL tradicional para volúmenes parecidos de datos.

Table Storage se puede usar para almacenar conjuntos de datos flexibles, como datos de usuarios para aplicaciones web, libretas de direcciones, información de dispositivos u otros tipos de metadatos que el servicio requiera. Una tabla puede almacenar un número cualquiera de entidades y una cuenta de almacenamiento puede incluir un número cualquiera de tablas, hasta alcanzar el límite de capacidad de este tipo de cuenta.

### <a name="about-this-tutorial"></a>Acerca de este tutorial
En este tutorial se muestra cómo usar el [SDK de Table de Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) en algunos escenarios habituales de Azure Table Storage. El nombre del SDK indica que es para su uso con Azure Cosmos DB, pero funciona con Azure Cosmos DB y con Azure Table Storage, cada servicio solo tiene un único punto de conexión. Estos escenarios se describen mediante ejemplos de Python que muestran cómo:
* Crear y eliminar tablas
* Insertar y consultar entidades
* Modificar entidades

Se recomienda que consulte la [referencia de la API del SDK de Azure Cosmos DB para Python](https://azure.github.io/azure-cosmosdb-python/) mientras trabaja con los escenarios de este tutorial.

## <a name="prerequisites"></a>requisitos previos

Necesitará lo siguiente para completar este tutorial correctamente:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 o 3.6
- [SDK de Table de Azure Cosmos DB 1.01 para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Este SDK se conecta con Azure Table Storage y con Table API de Azure Cosmos DB.
- [Cuenta de Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account#create-a-storage-account) o [Cuenta de Azure Cosmos DB](https://azure.microsoft.com/en-us/try/cosmosdb/)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Creación de una cuenta de servicio de Azure

Puede trabajar con tablas mediante Azure Table Storage o Azure Cosmos DB. Para más información acerca de las diferencias entre los servicios, lea [Ofertas de Table](table-introduction.md#table-offerings). Debe crear una cuenta para el servicio que se va a utilizar. 

### <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage
La forma más fácil de crear la primera cuenta de Azure Storage es a través de [Azure Portal](https://portal.azure.com). Para obtener más información, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account).

También se puede crear una cuenta de Azure Storage mediante [Azure PowerShell](../storage/common/storage-powershell-guide-full.md) o la [CLI de Azure](../storage/common/storage-azure-cli.md).

Si no desea crear una cuenta de almacenamiento en este momento, también puede utilizar el emulador de Almacenamiento de Azure para ejecutar y probar el código en un entorno local. Para más información, consulte [Uso del emulador de Azure Storage para desarrollo y pruebas](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Creación de una cuenta de Table API de Azure Cosmos DB

Para obtener instrucciones sobre cómo crear una cuenta de Table API de Azure Cosmos DB, consulte [Creación de una cuenta de Table API](create-table-dotnet.md#create-a-database-account).

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Instalación del SDK de Table de Azure Cosmos DB para Python

Cuando haya creado una cuenta de Storage, el paso siguiente consiste en instalar el [SDK de Table de Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Para obtener más información sobre la instalación del SDK, consulte el archivo [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) del SDK de Table de Cosmos DB para el repositorio de Python en GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importación de las clases Entity y TableService

Para trabajar con entidades en Azure Table service de Python, use las clases [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) y [Entity] [ py_Entity]. Agregue este código cerca de la parte superior del archivo de Python para importar ambos:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Conexión a Azure Table service

Para conectarse a Azure Storage Table service, cree un objeto [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) y pase la clave de cuenta y el nombre de la cuenta de Storage. Reemplace `myaccount` y `mykey` por el nombre y la clave de la de cuenta.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Conexión a Azure Cosmos DB

Para conectarse a Azure Cosmos DB, copie la cadena de conexión principal de Azure Portal y cree un objeto [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) con la cadena de conexión que ha copiado:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Creación de una tabla

Llame a [create_table] [ py_create_table] para crear la tabla.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla

Para agregar una entidad, cree primero un objeto que represente a la entidad y pase luego el objeto al método [TableService.insert_entity][py_TableService]. El objeto de la entidad puede ser un diccionario o un objeto del tipo [Entidad][py_Entity] y define los nombres de propiedad y los valores de la entidad. Cada entidad debe incluir las propiedades [PartitionKey y RowKey](#partitionkey-and-rowkey) necesarias, además de cualquier otra propiedad que se defina para la entidad.

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

Table service usa **PartitionKey** para distribuir de forma inteligente las entidades de tabla entre los nodos de almacenamiento. Las entidades con la misma **PartitionKey** se almacenan en el mismo nodo. **RowKey** es el identificador exclusivo de la entidad de la partición a la que pertenece.

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

Para garantizar el procesamiento atómico de una solicitud por parte de Table service, puede enviar varias operaciones en un lote. En primer lugar, use la clase [TableBatch][py_TableBatch] para agregar varias operaciones a un único lote. A continuación, llame a [TableService][py_TableService].[commit_batch][py_commit_batch] para enviar las operaciones de una operación atómica. Todas las entidades que desea modificar del lote deben estar en la misma partición.

En este ejemplo se agregan dos entidades juntas en un lote:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
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

Elimine una entidad pasando sus propiedades **PartitionKey** y **RowKey** al método [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Eliminación de una tabla

Si ya no necesita una tabla ni ninguna de las entidades dentro de ella, llame al método [delete_table][py_delete_table] para eliminar permanentemente la tabla de Azure Storage.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>pasos siguientes

* [Preguntas más frecuentes sobre desarrollo con Table API](https://docs.microsoft.com/en-us/azure/cosmos-db/faq#develop-with-the-table-api)
* [Referencia de la API del SDK de Azure Cosmos DB para Python](https://azure.github.io/azure-cosmosdb-python/)
* [Centro para desarrolladores de Python](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): una aplicación gratuita y multiplataforma para trabajar visualmente con datos de Azure Storage en Windows, Mac OS y Linux.
* [Uso de Python en Visual Studio (Windows)](https://docs.microsoft.com/en-us/visualstudio/python/overview-of-python-tools-for-visual-studio)

[py_commit_batch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_create_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_Entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.models.html
[py_get_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_or_replace_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableService]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableBatch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tablebatch.html
[py_merge_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_update_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
