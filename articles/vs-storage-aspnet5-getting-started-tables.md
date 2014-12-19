<properties title="Getting Started with Azure Storage" pageTitle="Introducción a Almacenamiento de Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introducción](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)
> - [¿Qué ha ocurrido?](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Introducción a Almacenamiento de Azure (proyectos ASP.NET vNext)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Colas](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [Tablas](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

El servicio de almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.  Consulte [Uso de almacenamiento de tabla de .NET](http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET") para obtener más información.

Para obtener acceso mediante programación a las tablas en los proyectos ASP.NET 5, deberá agregar los elementos siguientes, si no están presentes aún.

1. Agregue la siguiente declaración de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;
		using Microsoft.Framework.ConfigurationModel;
		using System.Threading.Tasks;

2. Utilice el comando siguiente para obtener los valores de configuración.

		Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Obtención de la cadena de conexión de almacenamiento
Antes de realizar cualquier acción con una tabla, tendrá que obtener la cadena de conexión para la cuenta de almacenamiento en la que residen las tablas. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. Si está utilizando un proyecto ASP.NET vNext, puede llamar al método get del objeto Configuration para obtener la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

**NOTA:** las API que realizan llamadas a Almacenamiento de Azure en ASP.NET 5 son asincrónicas. Consulte [Programación asincrónica con Async y Await](http://msdn.microsoft.com/library/hh191443.aspx) para obtener más información. El código siguiente supone que se están utilizando métodos de programación asincrónica.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Creación de una tabla
Los objetos **CloudTableClient** le permiten obtener objetos de referencia para las tablas y las entidades. El código siguiente crea un objeto **CloudTableClient** y lo utiliza para crear una nueva tabla. El código intenta hacer referencia a una tabla denominada "people". Si no encuentra ninguna tabla con ese nombre, la crea.

**NOTA:** en todo el código de esta guía se asume que la aplicación que se está desarrollando es un proyecto del Servicio en la nube de Azure y se utiliza una cadena de conexión de almacenamiento guardada en la configuración del servicio de la aplicación de Azure. Además, utilice todo este código delante del código en las secciones siguientes.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	await table.CreateIfNotExistsAsync();

#####Adición de una entidad a una tabla
Para agregar una entidad a una tabla, cree una clase que defina las propiedades de la entidad. El código siguiente define una clase de entidad llamada **CustomerEntity** que utiliza el nombre de pila del cliente como clave de fila y el apellido como clave de partición.

	public class CustomerEntity : TableEntity
	{
	    public CustomerEntity(string lastName, string firstName)
	    {
	        this.PartitionKey = lastName;
	        this.RowKey = firstName;
	    }
	
	    public CustomerEntity() { }
	
	    public string Email { get; set; }
	
	    public string PhoneNumber { get; set; }
	}

Las operaciones de tablas que afectan a las entidades se realizan utilizando el objeto **CloudTable** que se creó anteriormente en el apartado "Creación de una tabla". El objeto **TableOperation** representa la operación que se va a realizar. El ejemplo de código siguiente muestra la creación de un objeto **CloudTable** y un objeto **CustomerEntity**. Para preparar la operación, se crea un objeto **TableOperation** a fin de insertar la entidad del cliente en la tabla. Finalmente, se ejecuta la operación llamando a CloudTable.ExecuteAsync.

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await table.ExecuteAsync(insertOperation);

#####Inserción de un lote de entidades
Puede insertar varias entidades en una tabla mediante una única operación de escritura. El siguiente ejemplo de código crea dos objetos de entidad ("Jeff Smith" y "Ben Smith"), agrega cada uno de ellos a un objeto **TableBatchOperation** utilizando el método Insert y, a continuación, inicia la operación llamando a CloudTable.Execute.

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create the batch operation.
	TableBatchOperation batchOperation = new TableBatchOperation();
	
	// Create a customer entity and add it to the table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
	
	// Create another customer entity and add it to the table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
	
	// Add both customer entities to the batch insert operation.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);
	
	// Execute the batch operation.
	await table.ExecuteBatchAsync(batchOperation);

#####Obtención de todas las entidades en una partición
Para consultar una tabla a fin de obtener todas las entidades de una partición, utilice un objeto **TableQuery**. El ejemplo de código siguiente especifica un filtro para las entidades en las que "Smith" es la clave de partición. En este ejemplo, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await table.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


#####Obtención de una sola entidad
Puede escribir una consulta para obtener una sola entidad concreta. El código siguiente utiliza un objeto **TableOperation** para especificar el cliente llamado "Ben Smith". Este método devuelve solo una entidad, en lugar de una colección, y el valor que aparece devuelto en TableResult.Result es un objeto **CustomerEntity**. La forma más rápida de recuperar una sola entidad del servicio **Tabla** es especificar claves tanto de partición como de fila en las consultas.

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await table.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

#####Eliminación de una entidad
Puede eliminar fácilmente una entidad después de haberla encontrado. El código siguiente busca una entidad de cliente denominada "Ben Smith" y, si la encuentra, la elimina.

	// Create the CloudTable that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   await table.ExecuteAsync(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[Más información sobre Almacenamiento de Azure](http://azure.microsoft.com/documentation/services/storage/)
Consulte también [Búsqueda de recursos de almacenamiento en Server Explorer](http://msdn.microsoft.com/es-es/library/azure/ff683677.aspx) y [ASP.NET 5](http://www.asp.net/vnext).
