---
title: "Cómo usar el servicio de tabla de almacenamiento de Azure o Azure Cosmos DB Table API de PHP | Microsoft Docs"
description: Aprenda a utilizar la API de Table service de PHP para crear y eliminar una tabla e insertar, eliminar y consultar la tabla.
services: cosmos-db
documentationcenter: php
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/22/2018
ms.author: mimig
ms.openlocfilehash: 64ce8206b4b0ab3c831417d6e478f9c059188345
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="how-to-use-azure-storage-table-service-or-cosmos-db-table-api-from-php"></a>Cómo usar el servicio de tabla de almacenamiento de Azure o Cosmos DB Table API de PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Información general
En esta guía se explica cómo ejecutar escenarios comunes con el servicio de tabla de almacenamiento de Azure y Azure Cosmos DB Table API. Los ejemplos están escritos en PHP y usan la [biblioteca de cliente de PHP de la tabla de almacenamiento de Azure][download]. Entre los escenarios descritos se incluyen la **creación y eliminación de una tabla**, y la **inserción, eliminación y consulta de entidades de una tabla**. Para obtener más información acerca de Azure Table service, vea la sección [Pasos siguientes](#next-steps) .

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Creación de una cuenta de servicio de Azure

Puede trabajar con tablas mediante el almacenamiento de tablas de Azure o Azure Cosmos DB Table API. Para más información acerca de las diferencias entre los servicios, lea [Ofertas de Table](table-introduction.md#table-offerings). Debe crear una cuenta para el servicio que se va a utilizar. 

### <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

La manera más fácil de crear la primera cuenta de Storage es mediante [Azure Portal](https://portal.azure.com). Para obtener más información, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account).

También se puede crear una cuenta de Storage mediante [Azure PowerShell](../storage/common/storage-powershell-guide-full.md) o la [CLI de Azure](../storage/common/storage-azure-cli.md).

Si no desea crear una cuenta de Storage en este momento, también puede utilizar el emulador de Azure Storage para ejecutar y probar el código en un entorno local. Para más información, consulte [Uso del emulador de Azure Storage para desarrollo y pruebas](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-account"></a>Creación de una cuenta de Azure Cosmos DB

Para obtener instrucciones sobre cómo crear una cuenta de Azure Cosmos DB, consulte [Creación de una cuenta de Table API](create-table-dotnet.md#create-a-database-account).

## <a name="create-a-php-application"></a>Creación de una aplicación PHP

El único requisito para crear una aplicación PHP para obtener acceso al servicio Storage Table o a Azure Cosmos DB Table API es hacer referencia a las clases del SDK azure-storage-table para PHP desde dentro de su código. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

En esta guía, utilizará características del servicio Storage Table o Azure Cosmos DB a las que se puede llamar desde una aplicación PHP localmente, o bien mediante código que se ejecuta en un rol web, rol de trabajo o sitio web de Azure.

## <a name="get-the-client-library"></a>Obtención de la biblioteca de cliente

1. Cree un archivo con el nombre composer.json en la raíz del proyecto y agréguele el código siguiente:
```json
{
  "require": {
    "microsoft/azure-storage-table": "*"
  }
}
```
2. Descargue [composer.phar](http://getcomposer.org/composer.phar) en la raíz. 
3. Abra un símbolo del sistema y ejecute el siguiente comando en la raíz del proyecto:
```
php composer.phar install
```
También puede ir a la [biblioteca de cliente de PHP de la tabla de almacenamiento de Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) en GitHub para clonar el código fuente.


## <a name="add-required-references"></a>Adición de referencias necesarias
Para usar el servicio Storage Table o las API de Azure Cosmos DB, debe:

* Hacer referencia al archivo autocargador mediante la instrucción [require_once][require_once] y
* Haga referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **TableRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

En los ejemplos que aparecen a continuación, la instrucción `require_once` aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

## <a name="add-a-storage-table-service-connection"></a>Adición de una conexión del servicio Storage Table
Para crear una instancia de un cliente del servicio Storage Table, primero debe disponer de una cadena de conexión válida. El formato de las cadenas de conexión del servicio Storage Table es:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>Adición de una conexión de Azure Cosmos DB
Para crear una instancia de un cliente de Azure Cosmos DB Table, primero debe disponer de una cadena de conexión válida. El formato de la cadena de conexión de Azure Cosmos DB es:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>Adición de una conexión del emulador de Storage
Para obtener acceso al almacenamiento del emulador:

```php
UseDevelopmentStorage = true
```

Para crear un cliente de Azure Table service o Azure Cosmos DB, debe usar la clase **TableRestProxy**. Puede:

* pasarle directamente la cadena de conexión, o bien
* Use **CloudConfigurationManager (CCM)** para buscar la cadena de conexión en varios orígenes externos:
  * de manera predeterminada, admite un origen externo: variables de entorno.
  * Puede agregar nuevos orígenes extendiendo la clase `ConnectionStringSource`.

En los ejemplos descritos aquí, la cadena de conexión se pasa directamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Creación de una tabla
Puede crear una tabla con un objeto A **TableRestProxy** con el método **createTable**. Al crear una tabla, puede establecer un tiempo de espera para Table service. (Para obtener más información sobre el tiempo de espera de Table service, consulte [Establecer los tiempos de espera para las operaciones de Table service][table-service-timeouts]).

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Para obtener más información sobre las restricciones que se aplican a los nombres de las tablas, consulte [Introducción al modelo de datos de Table Service][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla
Para agregar una entidad a una tabla, cree un nuevo objeto **Entity** y páselo a **TableRestProxy->insertEntity**. Tenga en cuenta que al crear una entidad, es necesario especificar los valores de `PartitionKey` y `RowKey`. Estos valores son los identificadores exclusivos de la entidad y se pueden consultar más rápidamente que las demás propiedades. El sistema usa `PartitionKey` para distribuir automáticamente las entidades de la tabla entre numerosos nodos de Storage. Las entidades con la misma `PartitionKey` se almacenan en el mismo nodo. (Las operaciones que afecten a entidades almacenadas en el mismo nodo se realizarán mejor que aquellas que afecten a entidades almacenadas en nodos distintos). `RowKey` es el identificador exclusivo de una entidad dentro de una partición.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Para obtener más información sobre las propiedades y los tipos de tabla, consulte [Introducción al modelo de datos de Table Service][table-data-model].

La clase **TableRestProxy** ofrece dos métodos alternativos para insertar entidades: **insertOrMergeEntity** y **insertOrReplaceEntity**. Para usar estos métodos, cree una nueva **Entity** y pásela como un parámetro para cualquiera de los métodos. Ambos métodos insertarán la entidad si todavía no existe. Si ya existe la entidad, **insertOrMergeEntity** actualizará los valores de las propiedades existentes y agregará las propiedades que no existan, mientras que **insertOrReplaceEntity** reemplazará por completo la entidad existente. En el siguiente ejemplo se muestra cómo usar el método **insertOrMergeEntity**. Si todavía no existe ninguna entidad con `PartitionKey` "tasksSeattle" y `RowKey` "1", se insertará. Sin embargo, si ya se insertó previamente (como se muestra en el ejemplo anterior), se actualizará la propiedad `DueDate` y se agregará la propiedad `Status`. Las propiedades `Description` y `Location` también se actualizan, pero con valores que a efectos prácticos no provocarán ningún cambio en ellas. Si estas dos últimas propiedades no se agregaron como se muestra en el ejemplo, sino que existían en la entidad objetivo, sus valores actuales permanecerán invariables.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>una sola entidad
El método **TableRestProxy->getEntity** permite recuperar una sola entidad consultando sus valores de `PartitionKey` y `RowKey`. En el ejemplo siguiente, la clave de partición `tasksSeattle` y la clave de fila `1` se pasan al método **getEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>todas las entidades de una partición
Las consultas a entidades se basan en filtros (para obtener más información, consulte [Consultar tablas y entidades][filters]). Para recuperar todas las entidades de una partición, utilice el filtro "PartitionKey eq *partition_name*". En el siguiente ejemplo se muestra cómo recuperar todas las entidades de la partición `tasksSeattle` pasando un filtro al método **queryEntities** .

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>un subconjunto de entidades de una partición
El mismo patrón utilizado en el ejemplo anterior se puede aplicar a la recuperación de cualquier subconjunto de entidades de una partición. El subconjunto de entidades que recupere dependerá del filtro que utilice (para más información, consulte [Consultar tablas y entidades][filters]). En el siguiente ejemplo se muestra cómo usar un filtro para recuperar todas las entidades con un valor concreto para `Location` y un valor para `DueDate` anterior a una fecha determinada.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>un subconjunto de propiedades de las entidades
Es posible recuperar un subconjunto de propiedades de las entidades mediante una consulta. Esta técnica, denominada *proyección*, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. Para especificar la propiedad que desea recuperar, pase el nombre de la propiedad al método **Query->addSelectField**. Puede llamar a este método varias veces para agregar más propiedades. Tras ejecutar **TableRestProxy->queryEntities**, las entidades que se recuperen solo presentarán las propiedades seleccionadas. (Si desea recuperar un subconjunto de entidades de tabla, utilice un filtro tal y como se muestra en las consultas anteriores).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Actualización de una entidad
Puede actualizar una entidad existente con los métodos **Entity->setProperty** y **Entity->addProperty** en la entidad y, a continuación, con una llamada a **TableRestProxy->updateEntity**. En el siguiente ejemplo se recupera una entidad, se modifica una propiedad, se elimina otra propiedad y se agrega una nueva propiedad. Tenga en cuenta que para eliminar una propiedad debe establecer su valor en **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Eliminación de una entidad
Para eliminar una entidad, pase el nombre de la tabla y los valores `PartitionKey` y `RowKey` de la entidad al método **TableRestProxy->deleteEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

En las comprobaciones de simultaneidad, puede determinar que se elimine la propiedad Etag de una entidad utilizando el método **DeleteEntityOptions->setEtag** y pasando el objeto **DeleteEntityOptions** a **deleteEntity** como cuarto parámetro.

## <a name="batch-table-operations"></a>operaciones de tabla
El método **TableRestProxy->batch** permite ejecutar varias operaciones en una única solicitud. Este patrón consiste en agregar operaciones al objeto **BatchRequest** y, a continuación, pasar el objeto **BatchRequest** al método **TableRestProxy->batch**. Para agregar una operación a un objeto **BatchRequest** , puede llamar a cualquiera de los siguientes métodos varias veces:

* **addInsertEntity** (agrega una operación insertEntity)
* **addUpdateEntity** (agrega una operación updateEntity)
* **addMergeEntity** (agrega una operación mergeEntity)
* **addInsertOrReplaceEntity** (agrega una operación insertOrReplaceEntity)
* **addInsertOrMergeEntity** (agrega una operación insertOrMergeEntity)
* **addDeleteEntity** (agrega una operación deleteEntity)

En el siguiente ejemplo se muestra cómo ejecutar las operaciones **insertEntity** y **deleteEntity** en una única solicitud. 

> [!NOTE]
> Azure Cosmos DB aún no admite operaciones por lotes para las tablas. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Para obtener más información sobre el procesamiento por lotes de las operaciones de tabla, consulte [Realizar transacciones con grupos de entidades][entity-group-transactions].

## <a name="delete-a-table"></a>Eliminación de una tabla
Finalmente, para eliminar una tabla, pase su nombre al método **TableRestProxy->deleteTable**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>pasos siguientes
Ahora que está familiarizado con los aspectos básicos de Azure Table service y Azure Cosmos DB, use estos vínculos para obtener más información.

* El [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.

* [Centro para desarrolladores de PHP](/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: http://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
