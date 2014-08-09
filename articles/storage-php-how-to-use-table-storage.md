<properties linkid="develop-php-table-service" urlDisplayName="Table Service" pageTitle="How to use table storage (PHP) | Microsoft Azure" metaKeywords="Azure Table service PHP, Azure creating table, Azure deleting table, Azure insert table, Azure query table" description="Learn how to use the Table service from PHP to create and delete a table, and insert, delete, and query the table." metaCanonical="" services="storage" documentationCenter="PHP" title="How to use the Table service from PHP" authors="" solutions="" manager="" editor="" />

Uso del servicio Tabla de PHP
=============================

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio Tabla de Azure. Los ejemplos están escritos en PHP y utilizan el [SDK de Azure para PHP](http://go.microsoft.com/fwlink/?LinkID=252473). Entre los escenarios descritos se incluyen la **creación y eliminación de una tabla, y la inserción, eliminación y consulta de entidades de una tabla**. Para obtener más información acerca del servicio Tabla de Azure, consulte la sección [Pasos siguientes](#NextSteps).

Tabla de contenido
------------------

-   [Qué es el servicio Tabla](#what-is)
-   [Conceptos](#concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#CreateAccount)
-   [Creación de una aplicación PHP](#CreateApplication)
-   [Configuración de la aplicación para obtener acceso al servicio Tabla](#ConfigureStorage)
-   [Configuración de una conexión de almacenamiento de Azure](#ConnectionString)
-   [Creación de una tabla](#CreateTable)
-   [Incorporación de una entidad a una tabla](#AddEntity)
-   [Recuperación de una sola entidad](#RetrieveEntity)
-   [Recuperación de todas las entidades de una partición](#RetEntitiesInPartition)
-   [Recuperación de un subconjunto de entidades de una partición](#RetrieveSubset)
-   [Recuperación de un subconjunto de propiedades de las entidades](#RetPropertiesSubset)
-   [Actualización de una entidad](#UpdateEntity)
-   [Procesamiento por lotes de operaciones de tabla](#BatchOperations)
-   [Eliminación de una tabla](#DeleteTable)
-   [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Creación de una cuenta de almacenamiento de Azure
-------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creación de una aplicación PHP
------------------------------

El único requisito a la hora de crear una aplicación PHP para obtener acceso al servicio Tabla de Azure es que el código haga referencia a clases del SDK de Azure para PHP. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

En esta guía, utilizará funciones del servicio Tabla a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

Obtención de las bibliotecas de clientes de Azure
-------------------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Configuración de la aplicación para obtener acceso al servicio Tabla
--------------------------------------------------------------------

Para utilizar las API del servicio Tabla de Azure, necesita:

1.  hacer referencia al archivo autocargador utilizando la instrucción [require\_once](http://php.net/require_once) y
2.  hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServicesBuilder**.

> [WACOM.NOTE] En este ejemplo (así como en otros ejemplos de este artículo), se asume que ya instaló las bibliotecas de clientes PHP para Azure utilizando el compositor. Si las instaló manualmente o como paquete PEAR, deberá hacer referencia al archivo autocargador `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

En los ejemplos que aparecen a continuación, la instrucción `require_once` aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

Configuración de una conexión de almacenamiento de Azure
--------------------------------------------------------

Para crear una instancia de un cliente del servicio Tabla de Azure, primero debe disponer de una cadena de conexión válida. El formato de las cadenas de conexión del servicio Tabla es:

Para obtener acceso a un servicio en directo:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para obtener acceso al emulador de almacenamiento:

    UseDevelopmentStorage=true

Para crear un cliente de cualquier servicio de Azure necesario para utilizar la clase **ServicesBuilder**, puede:

-   pasarle directamente la cadena de conexión, o bien
-   utilizar el **Administrador de configuración de nube (CCM)** (CloudConfigurationManager ) para buscar la cadena de conexión en varios orígenes externos:
    -   De manera predeterminada, admite un origen externo: variables de entorno.
    -   Puede agregar nuevos orígenes ampliando la clase **ConnectionStringSource**.

En los ejemplos descritos aquí, la cadena de conexión se pasará directamente.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

Creación de una tabla
---------------------

Puede crear una tabla con un objeto **TableRestProxy** a través del método **createTable**. Al crear una tabla, puede establecer un tiempo de espera para el servicio Tabla. (Para obtener más información acerca del tiempo de espera del servicio Tabla, consulte [Establecer los tiempos de espera para las operaciones del servicio Tabla](http://msdn.microsoft.com/es-es/library/windowsazure/dd894042.aspx)).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Crear tabla.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se pueden encontrar aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
    }

Para obtener más información acerca de las restricciones que se aplican a los nombres de las tablas, consulte [Introducción al modelo de datos del servicio Tabla](http://msdn.microsoft.com/es-es/library/windowsazure/dd179338.aspx).

Incorporación de una entidad a una tabla
----------------------------------------

Para agregar una entidad a una tabla, cree un nuevo objeto **Entity** y páselo a **TableRestProxy-\>insertEntity**. Tenga en cuenta que al crear una entidad debe especificar valores en `PartitionKey` y `RowKey`. Estos valores son los identificadores exclusivos de la entidad y se pueden consultar más rápidamente que las demás propiedades. El sistema usa `PartitionKey` para distribuir automáticamente las entidades de la tabla entre varios nodos de almacenamiento. Las entidades con la misma `PartitionKey` se almacenan en el mismo nodo. (Las operaciones que afecten a entidades almacenadas en el mismo nodo se realizarán mejor que aquellas que afecten a entidades almacenadas en nodos distintos). El valor `RowKey` es el identificador exclusivo de una entidad dentro de una partición.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", 
                         EdmType::DATETIME, 
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Para obtener más información acerca de las propiedades y los tipos de tabla, consulte [Introducción al modelo de datos del servicio Tabla](http://msdn.microsoft.com/es-es/library/windowsazure/dd179338.aspx).

La clase **TableRestProxy** ofrece dos métodos alternativos para insertar entidades: **insertOrMergeEntity** e **insertOrReplaceEntity**. Para utilizar estos métodos, cree una nueva entidad **Entity** y pásela a modo de parámetro a cualquiera de los métodos. Ambos métodos insertarán la entidad si todavía no existe. Si ya existe, el método **insertOrMergeEntity** actualizará los valores de las propiedades existentes y agregará las propiedades que no existan, mientras que el método **insertOrReplaceEntity** reemplazará por completo la entidad existente. En el siguiente ejemplo se muestra cómo usar el método **insertOrMergeEntity**. Si todavía no existe ninguna entidad cuyo valor para `PartitionKey` sea “tasksSeattle” y cuyo valor para `RowKey` sea “1”, se insertará dicha entidad. Sin embargo, si ya se insertó previamente (como se muestra en el ejemplo anterior), se actualizará la propiedad `DueDate` y se agregará la propiedad `Status`. También se actualizarán las propiedades `Description` y `Location`, pero con valores que a efectos prácticos no provocarán ningún cambio en ellas. Si estas dos últimas propiedades no se agregaron como se muestra en el ejemplo, sino que existían en la entidad objetivo, sus valores actuales permanecerán invariables.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Crear nueva entidad.
    $entity = new Entity();

    // Son necesarios los valores PartitionKey y RowKey.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // Si la entidad ya existe, las propiedades existentes se actualizan con los nuevos valores y
    // las propiedades nuevas se agregan. Las propiedades que faltan permanecen invariables.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Se ha modificado el campo DueDate.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Se agregó el campo Status.

    try {
        // Al llamar al método insertOrReplaceEntity en lugar de al método insertOrMergeEntity, como se muestra,
        // simplemente se reemplazaría la entidad con el valor para PartitionKey “tasksSeattle” y el valor para RowKey “1”.
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Recuperación de una sola entidad
--------------------------------

El método **TableRestProxy-\>getEntity** permite recuperar una sola entidad consultando sus valores para `PartitionKey` y `RowKey`. En el ejemplo siguiente, la clave de partición `tasksSeattle` y la clave de fila `1` se pasan al método **getEntity**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

Recuperación de todas las entidades de una partición
----------------------------------------------------

Las consultas a entidades se basan en filtros (para obtener más información, consulte [Consultar tablas y entidades](http://msdn.microsoft.com/es-es/library/windowsazure/dd894031.aspx)). Para recuperar todas las entidades de una partición, utilice el filtro “PartitionKey eq *partition\_name*”. En el siguiente ejemplo se muestra cómo recuperar todas las entidades de la partición `tasksSeattle` pasando un filtro al método **queryEntities**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

Recuperación de un subconjunto de entidades de una partición
------------------------------------------------------------

El mismo patrón utilizado en el ejemplo anterior se puede aplicar a la recuperación de cualquier subconjunto de entidades de una partición. El subconjunto de entidades que recupere dependerá del filtro que utilice (para obtener más información, consulte [Consultar tablas y entidades](http://msdn.microsoft.com/es-es/library/windowsazure/dd894031.aspx)). En el siguiente ejemplo se muestra cómo utilizar un filtro para recuperar todas las entidades con un valor concreto para `Location` y un valor para `DueDate` anterior a una fecha determinada.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

Recuperación de un subconjunto de propiedades de las entidades
--------------------------------------------------------------

Es posible recuperar un subconjunto de propiedades de las entidades mediante una consulta. Esta técnica, denominada *proyección*, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. Para especificar la propiedad que desea recuperar, pase el nombre de la propiedad al método **Query-\>addSelectField**. Puede llamar a este método varias veces para agregar más propiedades. Tras ejecutar **TableRestProxy-\>queryEntities**, las entidades que se recuperen solo presentarán las propiedades seleccionadas. (Si desea recuperar un subconjunto de entidades de tabla, utilice un filtro tal y como se muestra en las consultas anteriores).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\QueryEntitiesOptions;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // Se recuperan todas las entidades de la tabla, independientemente de si 
    // incluyen el campo Description.
    // Para limitar los resultados recuperados, utilizar un filtro.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

Actualización de una entidad
----------------------------

Es posible actualizar una entidad existente con los métodos **Entity-\>setProperty** y **Entity-\>addProperty** en la entidad y, a continuación, con una llamada a **TableRestProxy-\>updateEntity**. En el siguiente ejemplo se recupera una entidad, se modifica una propiedad, se elimina otra propiedad y se agrega una nueva propiedad. Tenga en cuenta que para eliminar una propiedad debe establecer su valor en **null**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Se modificó la propiedad DueDate.

    $entity->setPropertyValue("Location", null); //Se eliminó la propiedad Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Se agregó la propiedad Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Eliminación de una entidad
--------------------------

Para eliminar una entidad, pase el nombre de la tabla y los valores `PartitionKey` y `RowKey` de la entidad al método **TableRestProxy-\>deleteEntity**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Eliminar entidad.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Tenga en cuenta que en las comprobaciones de simultaneidad puede determinar que se elimine la propiedad Etag de una entidad utilizando el método **DeleteEntityOptions-\>setEtag** y pasando el objeto **DeleteEntityOptions** a **deleteEntity** como cuarto parámetro.

Procesamiento por lotes de operaciones de tabla
-----------------------------------------------

El método **TableRestProxy-\>batch** permite ejecutar varias operaciones en una única solicitud. Este patrón consiste en agregar operaciones al objeto **BatchRequest** y, a continuación, pasar el objeto **BatchRequest** al método **TableRestProxy-\>batch**. Para agregar una operación a un objeto **BatchRequest**, puede llamar a cualquiera de los siguientes métodos varias veces:

-   **addInsertEntity** (agrega una operación insertEntity)
-   **addUpdateEntity** (agrega una operación updateEntity)
-   **addMergeEntity** (agrega una operación mergeEntity)
-   **addInsertOrReplaceEntity** (agrega una operación insertOrReplaceEntity)
-   **addInsertOrMergeEntity** (agrega una operación insertOrMergeEntity)
-   **addDeleteEntity** (agrega una operación deleteEntity)

En el siguiente ejemplo se muestra cómo ejecutar las operaciones **insertEntity** y **deleteEntity** en una única solicitud:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;
    use WindowsAzure\Table\Models\BatchOperations;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Crear lista de operaciones por lotes.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate", 
                          EdmType::DATETIME, 
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Agregar operación a la lista de operaciones por lotes.
    $operations->addInsertEntity("mytable", $entity1);

    // Agregar operación a la lista de operaciones por lotes.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Para obtener más información acerca del procesamiento por lotes de las operaciones de tabla, consulte [Realizar transacciones con grupos de entidades](http://msdn.microsoft.com/es-es/library/windowsazure/dd894038.aspx).

Eliminación de una tabla
------------------------

Finalmente, para eliminar una tabla pase su nombre al método **TableRestProxy-\>deleteTable**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de tablas.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Eliminar tabla.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del servicio Tabla de Azure, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx)
-   Obtenga acceso al blog del equipo de almacenamiento de Azure: &lt;http://blogs.msdn.com/b/windowsazurestorage/\&gt;

