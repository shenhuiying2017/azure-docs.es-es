<properties urlDisplayName="Table Service" pageTitle="Uso de almacenamiento de tablas (Java) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Java, table storage Java" description="Learn how to use the table storage service in Azure. Code samples are written in Java code." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Table storage service from Java" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Uso del almacenamiento de tablas en Java

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de tablas de Azure. Los ejemplos están escritos en Java y utilizan el [SDK de almacenamiento de Azure para Java][SDK de almacenamiento de Azure para Java]. 	Entre los escenarios descritos se incluyen la **creación**, **visualización** y **eliminación** de tablas, así como la **inserción**, **consulta**, **modificación** y **eliminación** de las entidades de una tabla. Para obtener más información acerca de las tablas, consulte la sección [Pasos siguientes](#NextSteps).

Nota: hay un SDK disponible para los desarrolladores que usen el almacenamiento de Azure en dispositivos Android. Para obtener más información, consulte el [SDK de almacenamiento de Azure para Android][SDK de almacenamiento de Azure para Android]. 

## <a name="Contents"> </a>Tabla de contenido

* [Qué es el almacenamiento de tablas](#what-is)
* [Conceptos](#Concepts)
* [Crear una cuenta de almacenamiento de Azure](#CreateAccount)
* [Creación de una aplicación Java](#CreateApplication)
* [Configuración de su aplicación para obtener acceso al almacenamiento de tablas](#ConfigureStorage)
* [Configuración de una cadena de conexión de almacenamiento de Azure](#ConnectionString)
* [Direccionamiento del una tabla](#CreateTable)
* [Direccionamiento del las tablas](#ListTables)
* [Direccionamiento del una entidad a una tabla](#AddEntity)
* [Direccionamiento del de un lote de entidades](#InsertBatch)
* [Direccionamiento del todas las entidades de una partición](#RetrieveEntities)
* [Direccionamiento del de un rango de entidades de una partición](#RetrieveRange)
* [Direccionamiento del una sola entidad](#RetriveSingle)
* [Direccionamiento del de una entidad](#ModifyEntity)
* [Direccionamiento del un subconjunto de propiedades de las entidades](#QueryProperties)
* [Direccionamiento del o reemplazo de una entidad](#InsertOrReplace)
* [Direccionamiento del de una entidad](#DeleteEntity)
* [Direccionamiento del una tabla](#DeleteTable)
* [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a name="CreateAccount"></a>Crear una cuenta de almacenamiento de Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"></a>Creación de una aplicación Java

En esta guía utilizará funciones del almacenamiento que puede ejecutar en una aplicación Java localmente o bien mediante código a través de un rol web o un rol de trabajo de Azure.

Para ello, deberá instalar el Kit de desarrollo de Java (JDK) y crear una cuenta de almacenamiento de Azure en su suscripción de Azure. A continuación, deberá verificar que su sistema de desarrollo satisface los requisitos mínimos y las dependencias que se indican en el repositorio del [SDK de almacenamiento de Azure para Java][SDK de almacenamiento de Azure para Java] en GitHub. Si su sistema cumple esos requisitos, puede seguir las instrucciones para descargar e instalar las bibliotecas de almacenamiento de Azure para Java en su sistema desde ese repositorio. Cuando haya completado esas tareas, podrá crear una aplicación Java que use los ejemplos de este artículo.

## <a name="ConfigureStorage"> </a>Configuración de su aplicación para obtener acceso al almacenamiento de tablas

Agregue las siguientes instrucciones de importación en la parte superior del archivo Java en el que desea utilizar las API de almacenamiento de Windows Azure para obtener acceso a las tablas:

    // Incluir las siguientes instrucciones de importación para utilizar las API de las tablas
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="ConnectionString"> </a>Configuración de una cadena de conexión de almacenamiento de Azure

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, utilizando el nombre de su cuenta de almacenamiento y la clave de acceso principal de la cuenta de almacenamiento que se muestra en el Portal de administración para los valores *AccountName* y *AccountKey*. En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

    // Definir la cadena de conexión con sus valores.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

En una aplicación que se esté ejecutando en un rol de Microsoft Azure, esta cadena se puede almacenar en el archivo de configuración del servicio, *ServiceConfiguration.cscfg*, y se puede obtener acceso a él con una llamada al método **RoleEnvironment.getConfigurationSettings**. A continuación se muestra un ejemplo de cómo obtener la cadena de conexión desde un elemento de **configuración** denominado *StorageConnectionString* en el archivo de configuración del servicio:

    // Recuperar la cuenta de almacenamiento de la cadena de conexión.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

En los ejemplos siguientes se supone que ha usado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## <a name="CreateTable"> </a>Direccionamiento del una tabla

Un objeto **CloudTableClient** le permite obtener objetos de referencia para las tablas 
y las entidades. El siguiente código crea un objeto **CloudTableClient**
y lo usa para crear un nuevo objeto **CloudTable** que representa una tabla llamada "people". (Nota: existen otras maneras de crear objetos **CloudStorageAccount**; para obtener más información, consulte **CloudStorageAccount** en la [Referencia del SDK del cliente de almacenamiento de Azure]).

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Crear el cliente de tabla.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Cree la tabla si todavía no existe.
	   String tableName = "people";
	   CloudTable cloudTable = new CloudTable(tableName,tableClient);
	   cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="ListTables"></a>Direccionamiento del las tablas

Para obtener una lista de las tablas, llame al método **CloudTableClient.listTables()** para recuperar una lista que se puede iterar de nombres de tablas.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de tabla.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Aplique un bucle a través de la colección de nombres de tablas.
    	for (String table : tableClient.listTables())
    	{
		  // Consultar el nombre de cada tabla.
		  System.out.println(table);
	   }
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="AddEntity"> </a>Direccionamiento del una entidad a una tabla

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

Las operaciones de tabla que afectan a las entidades requieren un objeto **TableOperation**. Este objeto define la operación que va a realizarse en una entidad, que puede ejecutarse con un objeto **CloudTable**. El código siguiente crea una instancia nueva de la clase **CustomerEntity** con algunos datos de los clientes que se van a almacenar. El código siguiente llama a **TableOperation.insertOrReplace** para crear un objeto **TableOperation** a fin de insertar una entidad en una tabla y asocia el objeto **CustomerEntity** a ella. Por último, el código llama al método **execute** en el objeto **CloudTable**, especificando la tabla "people" y el nuevo objeto **TableOperation**, que posteriormente envía una solicitud al servicio de almacenamiento para insertar la nueva entidad de cliente en la tabla "people" o sustituir la entidad si ya existe.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de tabla.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();
			
    	// Crear un objeto de tabla de nube para la tabla.
    	CloudTable cloudTable = tableClient.getTableReference("people");
			
    	// Cree una nueva entidad de cliente.
    	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    	customer1.setEmail("Walter@contoso.com");
    	customer1.setPhoneNumber("425-555-0101");
			
    	// Crear una operación para agregar el nuevo cliente a la tabla "people".
    	TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    	// Enviar la operación al servicio Tabla.
    	cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="InsertBatch"> </a>Direccionamiento del de un lote de entidades

Puede insertar un lote de entidades en el servicio Tabla mediante una operación de escritura. El siguiente código crea un objeto **TableBatchOperation** y, a continuación, le agrega tres operaciones de inserción. Cada operación de inserción se agrega mediante la creación de un nuevo objeto de entidad, se configuran sus valores y, a continuación, se llama al método **insert** en el objeto **TableBatchOperation** para asociar la entidad a una nueva operación de inserción. A continuación, el código llama a **execute** en el objeto **CloudTable**, especificando la tabla "people" y el objeto **TableBatchOperation**, que envía el lote de operaciones de tabla al servicio de almacenamiento en una única solicitud.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de tabla.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Definir una operación por lotes.
    	TableBatchOperation batchOperation = new TableBatchOperation();

    	// Crear un objeto de tabla de nube para la tabla.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Crear una entidad de cliente para agregarla a la tabla.
    	CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    	customer.setEmail("Jeff@contoso.com");
    	customer.setPhoneNumber("425-555-0104");
    	batchOperation.insertOrReplace(customer);

	   // Crear otra entidad de cliente para agregarla a la tabla.
	   CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	   customer2.setEmail("Ben@contoso.com");
	   customer2.setPhoneNumber("425-555-0102");
	   batchOperation.insertOrReplace(customer2);

	   // Crear una tercera entidad de cliente para agregarla a la tabla.
	   CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
	   customer3.setEmail("Denise@contoso.com");
	   customer3.setPhoneNumber("425-555-0103");
	   batchOperation.insertOrReplace(customer3);

	   // Ejecutar el lote de operaciones en la tabla "people".
	   cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

Algunos aspectos que cabe tener en cuenta acerca de las operaciones por lotes:

- Puede ejecutar cualquier combinación de 100 operaciones de inserción, eliminación, combinación, reemplazo, inserción o combinación e inserción o reemplazo en un único lote.
- Una operación por lotes puede tener una operación de recuperación, si es que es la única operación del lote.
- Todas las entidades de la misma operación por lotes deben compartir la clave de partición.
- Una operación por lotes se limita a una carga de datos de 4 MB.

## <a name="RetrieveEntities"> </a>Direccionamiento del todas las entidades de una partición

Para consultar una tabla a fin de obtener las entidades de una partición, use un objeto **TableQuery**. Llame a **TableQuery.from** para crear una consulta en una tabla concreta que devuelva un tipo de resultado específico. El código siguiente especifica un filtro para las entidades en las que "Smith" es la clave de partición. **TableQuery.generateFilterCondition** es un método auxiliar para crear filtros para las consultas. Llame a **where** en la referencia devuelta por el método **TableQuery.from** para aplicar el filtro a la consulta. Si la consulta se ejecuta con una llamada a **execute** en el objeto **CloudTable**, devuelve un **iterador** con el tipo de resultado **CustomerEntity** especificado. A continuación, puede usar el **iterador** devuelto para cada bucle para consumir los resultados. En este código, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

    try
    {
    	// Definir constantes para los filtros.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";

    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de tabla.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();
			
	   // Crear un objeto de tabla de nube para la tabla.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Crear una condición de filtro donde la clave de partición sea "Smith".
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY, 
	       QueryComparisons.EQUAL,
	       "Smith");

	   // Especificar una consulta de partición con "Smith" como filtro de clave de partición.
	   TableQuery<CustomerEntity> partitionQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(partitionFilter);

        // Aplique un bucle a través de los resultados, mostrando información acerca de la entidad.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() + 
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="RetrieveRange"> </a>Direccionamiento del de un rango de entidades de una partición

Si no quiere consultar todas las entidades de una partición, puede especificar un rango mediante el uso de operadores de comparación en un filtro. El código siguiente combina dos filtros para obtener todas las entidades de la partición "Smith" en las que la clave de fila (nombre de pila) empieza por una letra hasta "E" en el alfabeto. A continuación, imprime los resultados de la consulta. Si usa las entidades agregadas a la tabla en la sección de inserción por lotes de esta guía, solo se devuelven dos entidades en este momento (Ben y Denise Smith); Jeff Smith no se incluye.

    try
    {
    	// Definir constantes para los filtros.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";
			
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Crear el cliente de tabla.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Crear un objeto de tabla de nube para la tabla.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Crear una condición de filtro donde la clave de partición sea "Smith".
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY, 
	       QueryComparisons.EQUAL,
	       "Smith");

    	// Crear una condición de filtro en la que la clave de fila sea inferior a la letra "E".
    	String rowFilter = TableQuery.generateFilterCondition(
	       ROW_KEY, 
	       QueryComparisons.LESS_THAN,
	       "E");

    	// Combinar las dos condiciones en una expresión de filtro.
    	String combinedFilter = TableQuery.combineFilters(partitionFilter, 
	        Operators.AND, rowFilter);

    	// Especificar una consulta por rango, usando "Smith" como clave de partición,
    	// con la clave de fila hasta la letra "E".
    	TableQuery<CustomerEntity> rangeQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(combinedFilter);

    	// Aplique un bucle a través de los resultados, mostrando información acerca de la entidad.
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="RetriveSingle"> </a>Direccionamiento del una sola entidad

Puede enviar una consulta para recuperar una sola entidad concreta. El código siguiente llama a **TableOperation.retrieve** con los parámetros de clave de partición y clave de fila para especificar el cliente "Jeff Smith", en lugar de crear un elemento **TableQuery** y usar filtros para realizar la misma operación. Cuando se ejecuta, la operación de recuperación devuelve solo una entidad, en lugar de una colección de entidades. El método **getResultAsType** convierte el resultado en el tipo de objetivo de asignación, un objeto **CustomerEntity**. Si este tipo no es compatible con el tipo especificado para la consulta, se mostrará una excepción. Se devuelve un valor nulo si no coincide exactamente la clave de fila y de partición de ninguna entidad. La forma más rápida de recuperar una sola entidad del servicio Tabla es especificar claves tanto de partición como de fila en las consultas.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de tabla.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Crear un objeto de tabla de nube para la tabla.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Recuperar la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
    	TableOperation retrieveSmithJeff = 
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

	   // Enviar la operación al servicio Tabla y obtener la entidad específica.
	   CustomerEntity specificEntity =
    		cloudTable.execute(retrieveSmithJeff).getResultAsType();
			
    	// Consultar la entidad.
    	if (specificEntity != null)
    	{
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="ModifyEntity"> </a>Direccionamiento del de una entidad

Para modificar una entidad, recupérela del servicio Tabla, realice los cambios en el objeto de entidad y vuelva a guardar los cambios en dicho servicio con una operación de reemplazo o combinación. El código siguiente cambia el número de teléfono de un cliente. En lugar de llamar a **TableOperation.insert** como hicimos para la inserción, este código llama a **TableOperation.replace**. El método **CloudTable.execute** llama al servicio Tabla y la entidad se reemplaza, a no ser que otra aplicación la haya modificado desde que la aplicación la recuperó. Cuando se produce esa situación, se muestra una excepción y la entidad debe recuperarse, modificarse y guardarse de nuevo. Este patrón de reintento de simultaneidad optimista es común en un sistema de almacenamiento distribuido.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de tabla.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Crear un objeto de tabla de nube para la tabla.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Recuperar la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
    	TableOperation retrieveSmithJeff = 
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    	// Enviar la operación al servicio Tabla y obtener la entidad específica.
    	CustomerEntity specificEntity =
		  cloudTable.execute(retrieveSmithJeff).getResultAsType();

    	// Especificar un nuevo número de teléfono.
    	specificEntity.setPhoneNumber("425-555-0105");

    	// Crear una operación para reemplazar la entidad.
    	TableOperation replaceEntity = TableOperation.replace(specificEntity);

    	// Enviar la operación al servicio Tabla.
    	cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="QueryProperties"> </a>Direccionamiento del un subconjunto de propiedades de las entidades

Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. La consulta del código siguiente usa el método **select** para devolver solo las direcciones de correo electrónico de las entidades de la tabla. Los resultados se proyectan en una colección de propiedades **String** con la ayuda de un objeto **EntityResolver**, que hace la conversión del tipo de entidades que el servidor devuelve. Puede obtener más información acerca de la proyección en esta [entrada de blog][entrada de blog] (en inglés). Tenga en cuenta que la proyección no es compatible con el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando se utiliza una cuenta del servicio Tabla.

    try
    {
        // Recuperar la cuenta de almacenamiento de la cadena de conexión.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de tabla.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Crear un objeto de tabla de nube para la tabla.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Defina una consulta de proyección que recupere solo la propiedad Email.
    	TableQuery<CustomerEntity> projectionQuery = 
	       TableQuery.from(CustomerEntity.class)
	       .select(new String[] {"Email"});

    	// Definir un solucionador de entidad para proyectar la entidad en el valor Email.
    	EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Aplicar un bucle a través de los resultados mostrando los valores Email.
        for (String projectedString : 
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="InsertOrReplace"> </a>Direccionamiento del o reemplazo de una entidad

En ocasiones, es posible que desee agregar una entidad a una tabla sin saber si ya existe en la tabla. Una operación de inserción o reemplazo le permite realizar una consulta única que insertará la entidad si no existe o que reemplazará la existente si la hubiera. Según los ejemplos anteriores, el siguiente código inserta o reemplaza la entidad de "Walter Harp". Después de crear una nueva entidad, este código llama al método **TableOperation.insertOrReplace**. A continuación, este código llama a **execute** en el objeto **CloudTable** con la tabla y las operaciones de inserción o reemplazo de tabla como parámetros. Para actualizar solo parte de una entidad, en su lugar, se puede usar el método **TableOperation.insertOrMerge**. Tenga en cuenta que la inserción o el reemplazo no son compatibles con el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando se utiliza una cuenta del servicio Tabla. Puede obtener más información sobre la inserción o el reemplazo y sobre la inserción o la fusión en esta [entrada de blog][entrada de blog].

    try
    {
        // Recuperar la cuenta de almacenamiento de la cadena de conexión.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Crear el cliente de tabla.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Crear un objeto de tabla de nube para la tabla.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Cree una nueva entidad de cliente.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Crear una operación para agregar el nuevo cliente a la tabla "people".
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Enviar la operación al servicio Tabla.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="DeleteEntity"> </a>Direccionamiento del de una entidad

Puede eliminar fácilmente una entidad después de que la haya recuperado. Cuando se recupere la entidad, llame a **TableOperation.delete** con la entidad que desea eliminar. A continuación, llame a **execute** en el objeto **CloudTable**. El código siguiente recupera y elimina una entidad de cliente.

    try
    {
        // Recuperar la cuenta de almacenamiento de la cadena de conexión.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Crear el cliente de tabla.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Crear un objeto de tabla de nube para la tabla.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Crear una operación para recuperar la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Recuperar la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Crear una operación para eliminar la entidad.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Enviar la operación de eliminación al servicio Tabla.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="DeleteTable"> </a>Direccionamiento del una tabla

Finalmente, el código siguiente elimina una tabla de una cuenta de almacenamiento. Las tablas eliminadas no podrán volver a crearse durante un tiempo tras la eliminación, que normalmente suele ser de menos de 40 segundos.

    try
    {
        // Recuperar la cuenta de almacenamiento de la cadena de conexión.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Crear el cliente de tabla.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Eliminar la tabla y todos si datos, en caso de que existan.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

- [SDK de almacenamiento de Azure para Java]
- [Referencia del SDK de cliente de almacenamiento de Azure]
- [API REST de almacenamiento de Azure]
- [Blog del equipo de almacenamiento de Azure]

[SDK de Azure para Java]: http://www.windowsazure.com/es-es/develop/java/
[SDK de almacenamiento de Azure para Java]: https://github.com/azure/azure-storage-java
[SDK de almacenamiento de Azure para Android]: https://github.com/azure/azure-storage-android
[Referencia del SDK de cliente de almacenamiento de Azure]: http://dl.windowsazure.com/storage/javadoc/
[API REST de almacenamiento de Azure]: http://msdn.microsoft.com/es-es/library/azure/gg433040.aspx
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[publicación en el blog (en inglés)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
