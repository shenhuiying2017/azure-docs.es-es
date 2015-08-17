<properties 
	pageTitle="Introducción al almacenamiento Tabla de Azure y los servicios conectados de Visual Studio" 
	description="Cómo empezar a usar el almacenamiento de tablas de Azure en un proyecto de ASP.NET en Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="patshea123"/>

# Introducción al almacenamiento Tabla de Azure y los servicios conectados de Visual Studio

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-tables.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

## Información general
En este artículo se describe cómo empezar a usar almacenamiento Tabla de Azure en Visual Studio después de crear una cuenta de almacenamiento de Azure en un proyecto de ASP.NET mediante el cuadro de diálogo **Agregar servicios conectados** de Visual Studio, o después de hacer referencia a una. En este artículo se muestra cómo realizar tareas comunes en las tablas de Azure, lo que incluye crear y eliminar una tabla, así como trabajar con entidades de tabla. Los ejemplos están escritos en código C# y usan la [biblioteca del cliente de Almacenamiento de Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Para obtener más información general acerca del uso del almacenamiento de tablas de Azure, consulte [Uso del almacenamiento de tablas de .NET](storage-dotnet-how-to-use-tables.md).


El servicio de almacenamiento Tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.




##Acceso a tablas en código 

1. Asegúrese de que las declaraciones de espacio de nombres de la parte superior del archivo C# incluyan estas instrucciones `using`.

	using Microsoft.Azure; using Microsoft.WindowsAzure.Storage; using Microsoft.WindowsAzure.Storage.Auth; using Microsoft.WindowsAzure.Storage.Table;

2. Obtenga un objeto `CloudStorageAccount` que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **NOTA:** use todo el código anterior delante del código que aparece en las muestras siguientes.

3. Obtenga un objeto `CloudTableClient` para hacer referencia a los objetos de tabla en la cuenta de almacenamiento.

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Obtenga un objeto de referencia `CloudTable` para hacer referencia a una tabla y a entidades específicas.
	
    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

##Crear una tabla en el código

Para crear la tabla de Azure, basta con agregar una llamada a `CreateIfNotExistsAsync()` al código anterior.

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();


##Inserción de un lote de entidades

Puede insertar varias entidades en una tabla mediante una única operación de escritura. En el ejemplo de código siguiente se crean dos objetos de entidad ("Jeff Smith" y "Ben Smith") que se agregan a un objeto `TableBatchOperation` mediante el método Insert y después se inicia la operación llamando a `CloudTable.ExecuteBatchAsync`.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"
	
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
	await peopleTable.ExecuteBatchAsync(batchOperation);

##Obtención de todas las entidades en una partición
Para consultar una tabla a fin de obtener todas las entidades de una partición, use un objeto `TableQuery`. El ejemplo de código siguiente especifica un filtro para las entidades en las que “Smith” es la clave de partición. En este ejemplo, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


##Obtención de una sola entidad
Puede escribir una consulta para obtener una sola entidad concreta. El código siguiente usa un objeto `TableOperation` para especificar el cliente llamado "Ben Smith". Este método devuelve una sola entidad, en lugar de una colección, y el valor devuelto en `TableResult.Result` es un objeto `CustomerEntity`. La forma más rápida de recuperar una sola entidad del servicio Tabla es especificar claves tanto de partición como de fila en las consultas.

        // Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

        // Create a retrieve operation that takes a customer entity.
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

##Eliminación de una entidad
Puede eliminar fácilmente una entidad después de haberla encontrado. El código siguiente busca una entidad de cliente denominada "Ben Smith" y, si la encuentra, la elimina.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = peopleTable.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   await peopleTable.ExecuteAsync(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

## Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]



  

<!---HONumber=August15_HO6-->