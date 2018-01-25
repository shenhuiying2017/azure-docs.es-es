---
title: Uso de Azure Table Storage con Ruby | Microsoft Docs
description: "Almacene datos estructurados en la nube con el Almacenamiento de tablas de Azure, un almacén de datos NoSQL."
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: decc6ffb38a4358d3593642f9cedb59d08f6bfef
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-use-azure-table-storage-with-ruby"></a>Uso de Azure Table Storage con Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Información general
Esta guía muestra cómo realizar algunas tareas comunes a través de Azure Table service. Los ejemplos están escritos usando la API Ruby. Entre los escenarios descritos se incluyen la **creación y eliminación de una tabla, la inserción y la consulta de entidades en una tabla**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Creación de una aplicación de Ruby
Para obtener instrucciones sobre cómo crear una aplicación de Ruby, consulte [Ruby on Rails Web application on an Azure VM](../virtual-machines/linux/classic/ruby-rails-web-app.md) (Aplicación web de Ruby on Rails en una máquina virtual de Azure).

## <a name="configure-your-application-to-access-storage"></a>Configuración de la aplicación para obtener acceso al almacenamiento
Para usar Azure Storage tendrá que descargar y usar el paquete Ruby azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### <a name="use-rubygems-to-obtain-the-package"></a>Uso de RubyGems para obtener el paquete
1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).
2. Escriba **gem install azure** en la ventana de comandos para instalar la gema y las dependencias.

### <a name="import-the-package"></a>Importación del paquete
Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Configuración de una conexión de Azure Storage
El módulo azure leerá las variables de entorno **AZURE\_STORAGE\_ACCOUNT** and **AZURE\_STORAGE\_ACCESS\_KEY** para obtener la información necesaria para conectarse a su cuenta de Azure Storage. Si no se establecen estas variables de entorno, debe especificar la información de la cuenta antes de usar **Azure::TableService** con el siguiente código:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Para obtener estos valores desde una cuenta de almacenamiento de Azure Resource Manager o clásica en el Portal de Azure:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Vaya a la cuenta de almacenamiento que desea utilizar.
3. En la hoja Configuración que se encuentra a la derecha, haga clic en **Claves de acceso**.
4. En la hoja Claves de acceso que aparece, verá la clave de acceso 1 y 2. Puede usar cualquiera de estas.
5. Haga clic en el icono de copia para copiar la clave en el Portapapeles.

## <a name="create-a-table"></a>Creación de una tabla
El objeto **Azure::TableService** permite trabajar con tablas y entidades. Para crear una tabla, use el método **create\_table()**. En el siguiente ejemplo se crea una tabla o se imprime el error, si hay alguno.

```ruby
azure_table_service = Azure::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla
Para agregar una entidad, primero cree un objeto hash que defina las propiedades de la entidad. Tenga en cuenta que para cada entidad debe especificar valores en **PartitionKey** y **RowKey**. Estos son los identificadores exclusivos de sus entidades y son valores que se pueden consultar más rápidamente que las demás propiedades. Azure Storage usa **PartitionKey** para distribuir automáticamente las entidades de la tabla entre varios nodos de almacenamiento. Las entidades con la misma **PartitionKey** se almacenan en el mismo nodo. La **RowKey** es el identificador exclusivo de la entidad de la partición a la que pertenece.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Actualización de una entidad
Hay varios métodos para actualizar una entidad existente:

* **update\_entity():** actualiza una entidad existente reemplazándola.
* **merge\_entity():** actualiza una entidad que ya existe combinando los valores de las nuevas propiedades con la entidad existente.
* **insert\_or\_merge\_entity():** actualiza una entidad existente reemplazándola. Si no hay entidades, se insertará una nueva.
* **insert\_or\_replace\_entity():** actualiza una entidad que ya existe combinando los valores de las nuevas propiedades con la entidad existente. Si no hay entidades, se insertará una nueva.

El ejemplo siguiente demuestra cómo actualizar una entidad usando **update\_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Con **update\_entity()** and **merge\_entity()**, si la entidad que va a actualizar no existe, la operación de actualización dará error. Por lo tanto, si desea almacenar una entidad independientemente de la que ya existe, debe usar **insert\_or\_replace\_entity()** o **insert\_or\_merge\_entity()**.

## <a name="work-with-groups-of-entities"></a>Trabajo con grupos de entidades
A veces resulta útil enviar varias operaciones juntas en un lote a fin de garantizar el procesamiento atómico por parte del servidor. Para ello, primero debe crear un objeto **Batch** y, a continuación, usar el método **execute\_batch()** en **TableService**. El siguiente ejemplo muestra el envío de dos entidades con RowKey 2 y 3 en un lote. Tenga en cuenta que solo funciona para entidades con el mismo valor de PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Consulta de una entidad
Para consultar la entidad de una tabla, use el método **get\_entity()**, pasando el nombre de tabla, **PartitionKey** y **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Consulta de un conjunto de entidades
Para realizar una consulta de un conjunto de entidades de una tabla, cree un objeto hash de consulta y use el método **query\_entities()**. El siguiente ejemplo muestra la obtención de todas las entidades con el mismo valor de **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Si el resultado establecido es demasiado largo para que lo devuelva una única consulta, se devolverá un token de continuación que puede usar para recuperar las páginas siguientes.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Consulta de un subconjunto de propiedades de las entidades
Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada "proyección", reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. Utilice la cláusula select y pase los nombres de las propiedades que quiera que lleguen al cliente.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Eliminación de una entidad
Para eliminar una entidad, use el método **delete\_entity()**. Tiene que pasar el nombre de la tabla que contiene la entidad, la PartitionKey y la RowKey de la entidad.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Eliminación de una tabla
Para eliminar una tabla, use el método **delete\_table()** para pasar el nombre de la tabla que desee eliminar.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>pasos siguientes

* El [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.
* [SDK de Azure para Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) en GitHub

