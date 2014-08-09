<properties linkid="dev-java-how-to-use-table-storage" urlDisplayName="Table Service" pageTitle="How to use table storage (Java) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Java, table storage Java" description="Learn how to use the table storage service in Azure. Code samples are written in Java code." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Table storage service from Java" authors="" solutions="" manager="" editor="" />

Uso del servicio de almacenamiento de tablas en Java
====================================================

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de tablas de Azure. Los ejemplos están escritos en código Java. Entre los escenarios descritos se incluyen la **creación y eliminación de una tabla, la inserción y la consulta de entidades en una tabla**. Para obtener más información acerca de las tablas, consulte la sección [Pasos siguientes](#NextSteps).

Tabla de contenido
------------------

-   [Qué es el almacenamiento de tablas](#what-is)
-   [Conceptos](#Concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#CreateAccount)
-   [Creación de una aplicación Java](#CreateApplication)
-   [Configuración de su aplicación para obtener acceso al almacenamiento de tablas](#ConfigureStorage)
-   [Configuración de una cadena de conexión de almacenamiento de Azure](#ConnectionString)
-   [Creación de una tabla](#CreateTable)
-   [Incorporación de una entidad a una tabla](#AddEntity)
-   [Inserción de un lote de entidades](#InsertBatch)
-   [Recuperación de todas las entidades de una partición](#RetrieveEntities)
-   [Recuperación de un rango de entidades de una partición](#RetrieveRange)
-   [Recuperación de una sola entidad](#RetriveSingle)
-   [Modificación de una entidad](#ModifyEntity)
-   [Consulta de un subconjunto de propiedades de las entidades](#QueryProperties)
-   [Inserción o reemplazo de una entidad](#InsertOrReplace)
-   [Eliminación de una entidad](#DeleteEntity)
-   [Eliminación de una tabla](#DeleteTable)
-   [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Creación de una cuenta de almacenamiento de Azure
-------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creación de una aplicación Java
-------------------------------

En esta guía utilizará funciones del almacenamiento que puede ejecutar en una aplicación Java localmente o bien mediante código a través de un rol web o un rol de trabajo de Azure. Suponemos que ha descargado e instalado el Kit de desarrollo de Java (JDK), que ha seguido las instrucciones de la página del [SDK de Azure para Java](http://www.windowsazure.com/es-es/develop/java/) para instalar las bibliotecas de Azure para Java y el SDK de Azure y que ha creado una cuenta de almacenamiento de Azure en su suscripción de Azure.

Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas. Todo lo que necesita es la capacidad para compilar un proyecto de Java y crear referencias a las bibliotecas de Azure para Java.

Configuración de su aplicación para obtener acceso al almacenamiento de tablas
------------------------------------------------------------------------------

Agregue las siguientes instrucciones de importación en la parte superior del archivo Java en el que desea utilizar las API de almacenamiento de Azure para obtener acceso a las tablas:

    // Incluir las siguientes importaciones para utilizar las API de las tablas
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.table.client.*;
    import com.microsoft.windowsazure.services.table.client.TableQuery.*;

Configuración de una cadena de conexión de almacenamiento de Azure
------------------------------------------------------------------

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, utilizando el nombre de su cuenta de almacenamiento y la clave de acceso principal de la cuenta de almacenamiento que se muestra en el Portal de administración para los valores *AccountName* y *AccountKey*. En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

    // Definir de la cadena de conexión con sus valores
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

En una aplicación que se esté ejecutando en un rol de Azure, esta cadena se puede almacenar en el archivo de configuración del servicio, ServiceConfiguration.cscfg, y se puede obtener acceso a él con una llamada al método **RoleEnvironment.getConfigurationSettings**. A continuación se muestra un ejemplo de cómo obtener la cadena de conexión desde un elemento de **configuración** denominado *StorageConnectionString* en el archivo de configuración del servicio:

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

En los ejemplos siguientes se supone que ha usado una de estas dos definiciones para obtener la cadena de conexión de almacenamiento.

Creación de una tabla
---------------------

Los objetos **CloudTableClient** le permiten obtener objetos de referencia para las tablas y las entidades. El código siguiente crea un objeto **CloudTableClient** y lo utiliza para crear una nueva tabla. Todo el código que contiene esta guía utiliza una cadena de conexión de almacenamiento almacenada en la configuración de servicios de la aplicación de Azure. Además, existen otros métodos de creación de un objeto **CloudStorageAccount**.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Cree la tabla si todavía no existe.
    String tableName = "people";
    tableClient.createTableIfNotExists(tableName);

Incorporación de una entidad a una tabla
----------------------------------------

Las entidades se asignan a objetos de Java utilizando una clase personalizada que implementa **TableEntity**. Para mayor comodidad, la clase **TableServiceEntity** implementa **TableEntity** y usa la reflexión para asignar propiedades a los métodos de captador y establecedor con nombre para las propiedades. Para agregar una entidad a una tabla, cree primero una clase que defina las propiedades de la entidad. El código siguiente define una clase de entidad que utiliza el nombre de pila del cliente como clave de fila y el apellido como clave de partición. En conjunto, la clave de partición y la clave de fila de una entidad la identifican inequívocamente en la tabla. Puede realizarse una consulta en las entidades con la misma clave de partición de manera más rápida que en aquellas que tienen claves de partición distintas.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;
        
        public String getEmail() {
            return this.email;
        }
        
        public void setEmail(String email) {
            this.email = email;
        }
        
        public String getPhoneNumber() {
            return this.phoneNumber;
        }
        
        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Las operaciones de tabla que afectan a las entidades requieren un objeto **TableOperation**. Este objeto define la operación que va a realizarse en una entidad, que puede ejecutarse con un objeto **CloudTableClient**. El código siguiente crea una instancia nueva de la clase **CustomerEntity** con algunos datos de los clientes que se van a almacenar. El código siguiente llama a **TableOperation.insert** para crear un objeto **TableOperation** a fin de insertar una entidad en una tabla y asocia el objeto **CustomerEntity** a ella. Por último, el código llama al método **execute** en **CloudTableClient**, especificando la tabla "people" y el nuevo objeto **TableOperation**, que posteriormente envía una solicitud al servicio de almacenamiento para insertar la nueva entidad de cliente en la tabla "people".

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Cree una nueva entidad de cliente.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Crear una operación para agregar el nuevo cliente a la tabla "people".
    TableOperation insertCustomer1 = TableOperation.insert(customer1);

    // Enviar la operación al servicio Tabla.
    tableClient.execute("people", insertCustomer1);

Inserción de un lote de entidades
---------------------------------

Puede insertar un lote de entidades en el servicio Tabla mediante una operación de escritura. El siguiente código crea un objeto **TableBatchOperation** y, a continuación, le agrega tres operaciones de inserción. Cada operación de inserción se agrega mediante la creación de un nuevo objeto de entidad, se configuran sus valores y, a continuación, se llama al método **insert** en el objeto **TableBatchOperation** para asociar la entidad a una nueva operación de inserción. A continuación, el código llama a **execute** en **CloudTableClient**, especificando la tabla "people" y el objeto **TableBatchOperation**, que envía el lote de operaciones de tabla al servicio de almacenamiento en una única solicitud. Algunos aspectos que cabe tener en cuenta acerca de las operaciones por lotes:

1.  Puede ejecutar cualquier combinación de 100 operaciones de inserción, eliminación, combinación, reemplazo, inserción o combinación e inserción o reemplazo en un único lote.
2.  Una operación por lotes puede tener una operación de recuperación, si es que es la única operación del lote.
3.  Todas las entidades de la misma operación por lotes deben compartir la clave de partición.
4.  Una operación por lotes se limita a una carga de datos de 4 MB.

<!-- -->

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Definir una operación por lotes.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Crear una entidad de cliente para agregarla a la tabla.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insert(customer);

    // Crear otra entidad de cliente para agregarla a la tabla.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insert(customer2);

    // Crear una tercera entidad de cliente para agregarla a la tabla.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insert(customer3);

    // Ejecutar el lote de operaciones en la tabla "people".
    tableClient.execute("people", batchOperation);

Recuperación de todas las entidades de una partición
----------------------------------------------------

Para consultar una tabla a fin de obtener las entidades de una partición, use un objeto **TableQuery**. Llame a **TableQuery.from** para crear una consulta en una tabla concreta que devuelva un tipo de resultado específico. El código siguiente especifica un filtro para las entidades en las que "Smith" es la clave de partición. **TableQuery.generateFilterCondition** es un método auxiliar para crear filtros para las consultas. Llame a **where** en la referencia devuelta por el método **TableQuery.from** para aplicar el filtro a la consulta. Si una consulta se ejecuta con una llamada a **execute** en el objeto **CloudTableClient**, devuelve un **iterador** con el tipo de resultado **CustomerEntity** especificado. A continuación, puede usar el **iterador** devuelto para cada bucle para consumir los resultados. En este código, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Crear una condición de filtro donde la clave de partición sea "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Especificar una consulta de partición con "Smith" como filtro de clave de partición.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(partitionFilter);

    // Aplique un bucle a través de los resultados, mostrando información acerca de la entidad.
    for (CustomerEntity entity : tableClient.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

Recuperación de un rango de entidades de una partición
------------------------------------------------------

Si no quiere consultar todas las entidades de una partición, puede especificar un rango mediante el uso de operadores de comparación en un filtro. El código siguiente combina dos filtros para obtener todas las entidades de la partición "Smith" en las que la clave de fila (nombre de pila) empieza por una letra hasta "E" en el alfabeto. A continuación, imprime los resultados de la consulta. Si usa las entidades agregadas a la tabla en la sección de inserción por lotes de esta guía, solo se devuelven dos entidades en este momento (Ben y Denise Smith); Jeff Smith no se incluye.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Crear una condición de filtro donde la clave de partición sea "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Crear una condición de filtro en la que la clave de fila sea inferior a la letra "E".
    String rowFilter = TableQuery.generateFilterCondition(
        TableConstants.ROW_KEY, 
        QueryComparisons.LESS_THAN,
        "E");

    // Combinar las dos condiciones en una expresión de filtro.
    String combinedFilter = TableQuery.combineFilters(partitionFilter, 
            Operators.AND, rowFilter);

    // Especificar una consulta por rango, usando "Smith" como clave de partición,
    // con la clave de fila hasta la letra "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(combinedFilter);

    // Aplique un bucle a través de los resultados, mostrando información acerca de la entidad.
    for (CustomerEntity entity : tableClient.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

Recuperación de una sola entidad
--------------------------------

Puede enviar una consulta para recuperar una sola entidad concreta. El código siguiente llama a **TableOperation.retrieve** con los parámetros de clave de partición y clave de fila para especificar el cliente "Jeff Smith", en lugar de crear un elemento **TableQuery** y usar filtros para realizar la misma operación. Cuando se ejecuta, la operación de recuperación devuelve solo una entidad, en lugar de una colección de entidades. El método **getResultAsType** convierte el resultado en el tipo de objetivo de asignación, un objeto **CustomerEntity**. Si este tipo no es compatible con el tipo especificado para la consulta, se mostrará una excepción. Se devuelve un valor nulo si no coincide exactamente la clave de fila y de partición de ninguna entidad. La forma más rápida de recuperar una sola entidad del servicio Tabla es especificar claves tanto de partición como de fila en las consultas.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Recuperar la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Enviar la operación al servicio Tabla y obtener la entidad específica.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();

Modificación de una entidad
---------------------------

Para modificar una entidad, recupérela del servicio Tabla, realice los cambios en el objeto de entidad y vuelva a guardar los cambios en dicho servicio con una operación de reemplazo o combinación. El código siguiente cambia el número de teléfono de un cliente. En lugar de llamar a **TableOperation.insert** como hicimos para la inserción, este código llama a **TableOperation.replace**. El método **CloudTableClient.execute** llama al servicio Tabla y la entidad se reemplaza, a no ser que otra aplicación la haya modificado desde que la aplicación la recuperó. Cuando se produce esa situación, se muestra una excepción y la entidad debe recuperarse, modificarse y guardarse de nuevo. Este patrón de reintento de simultaneidad optimista es común en un sistema de almacenamiento distribuido.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Recuperar la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Enviar la operación al servicio Tabla y obtener la entidad específica.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();
        
    // Especificar un nuevo número de teléfono.
    specificEntity.setPhoneNumber("425-555-0105");

    // Crear una operación para reemplazar la entidad.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Enviar la operación al servicio Tabla.
    tableClient.execute("people", replaceEntity);

Consulta de un subconjunto de propiedades de las entidades
----------------------------------------------------------

Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. La consulta del código siguiente usa el método **select** para devolver solo las direcciones de correo electrónico de las entidades de la tabla. Los resultados se proyectan en una colección de propiedades **String** con la ayuda de un objeto **EntityResolver**, que hace la conversión del tipo de entidades que el servidor devuelve. Puede obtener más información acerca de la proyección en esta [entrada de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx) (en inglés). Tenga en cuenta que la proyección no es compatible con el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando se utiliza una cuenta del servicio Tabla.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Defina una consulta de proyección que recupere solo la propiedad Email.
    TableQuery<CustomerEntity> projectionQuery = 
        TableQuery.from("people", CustomerEntity.class)
        .select(new String[] {"Email"});

    // Definir un solucionador de entidad para proyectar la entidad en el valor Email.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp,
                HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Aplicar un bucle a través de los resultados mostrando los valores Email.
    for (String projectedString : 
            tableClient.execute(projectionQuery, emailResolver)) {
        System.out.println(projectedString);
    }

Inserción o reemplazo de una entidad
------------------------------------

En ocasiones, es posible que desee agregar una entidad a una tabla sin saber si ya existe en la tabla. Una operación de inserción o reemplazo le permite realizar una consulta única que insertará la entidad si no existe o que reemplazará la existente si la hubiera. Según los ejemplos anteriores, el siguiente código inserta o reemplaza la entidad de "Walter Harp". Después de crear una nueva entidad, este código llama al método **TableOperation.insertOrReplace**. A continuación, este código llama a **execute** en **CloudTableClient** con la tabla y las operaciones de inserción o reemplazo de tabla como parámetros. Para actualizar solo parte de una entidad, en su lugar, se puede usar el método **TableOperation.insertOrMerge**. Tenga en cuenta que la inserción o el reemplazo no son compatibles con el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando se utiliza una cuenta del servicio Tabla. Puede obtener más información sobre la inserción o el reemplazo y sobre la inserción o la fusión en esta [entrada de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx).

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Cree una nueva entidad de cliente.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Crear una operación para agregar el nuevo cliente a la tabla "people".
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Enviar la operación al servicio Tabla.
    tableClient.execute("people", insertCustomer5);

Eliminación de una entidad
--------------------------

Puede eliminar fácilmente una entidad después de que la haya recuperado. Cuando se recupere la entidad, llame a **TableOperation.delete** con la entidad que desea eliminar. A continuación, llame a **execute** en **CloudTableClient**. El código siguiente recupera y elimina una entidad de cliente.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Crear una operación para recuperar la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Recuperar la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
    CustomerEntity entitySmithJeff =
            tableClient.execute("people", retrieveSmithJeff).getResultAsType();

    // Crear una operación para eliminar la entidad.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Enviar la operación de eliminación al servicio Tabla.
    tableClient.execute("people", deleteSmithJeff);

Eliminación de una tabla
------------------------

Finalmente, el código siguiente elimina una tabla de una cuenta de almacenamiento. Las tablas eliminadas no podrán volver a crearse durante un tiempo tras la eliminación, que normalmente suele ser de menos de 40 segundos.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Eliminar la tabla y todos si datos, en caso de que existan.
    tableClient.deleteTableIfExists("people");

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure]
-   Visite el [blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/) (en inglés).

