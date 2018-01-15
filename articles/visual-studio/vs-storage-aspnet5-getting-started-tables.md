---
title: "Introducción a Table Storage y los servicios conectados de Visual Studio (ASP.NET Core) | Microsoft Docs"
description: "Introducción a Azure Table Storage en un proyecto de ASP.NET Core en Visual Studio después de conectarse a una cuenta de almacenamiento mediante los servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: 4693c637597346e5c72bef6ddca9369e67b740f3
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Introducción al almacenamiento Tabla de Azure y los servicios conectados de Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

En este artículo se describe cómo empezar a usar Azure Table Storage en Visual Studio después de crear una cuenta de Azure Storage en un proyecto de ASP.NET Core mediante la característica **Servicios conectados** de Visual Studio. La operación **Servicios conectados** instala los paquetes de NuGet adecuados para acceder a Azure Storage en el proyecto y agrega la cadena de conexión de la cuenta de almacenamiento a los archivos de configuración del proyecto. (Vea [Documentación sobre Storage](https://azure.microsoft.com/documentation/services/storage/) para información general sobre Azure Storage).

El servicio de almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales. Para más información general sobre el uso de Almacenamiento de tablas de Azure, consulte [Introducción al Almacenamiento de tablas de Azure mediante .NET](../storage/storage-dotnet-how-to-use-tables.md).

Para comenzar, primero debe crear una tabla en la cuenta de almacenamiento. En este artículo se explica cómo crear una tabla en C# y cómo realizar operaciones básicas de tabla, como agregar, modificar, leer y quitar entradas de tablas.  El código usa la biblioteca cliente de Azure Storage para .NET. Para obtener más información acerca de ASP.NET, consulte [ASP.NET](http://www.asp.net).

Algunas de las API de Azure Storage son asincrónicas, y el código de este artículo asume que se usan métodos asincrónicos. Vea [Programación asincrónica](https://docs.microsoft.com/dotnet/csharp/async) para más información.

## <a name="access-tables-in-code"></a>Acceso a tablas en código

Para obtener acceso a las tablas de los proyectos de ASP.NET Core, deberá incluir los elementos siguientes en los archivos de origen de C# que acceden a Azure Table Storage.

1. Agregue las instrucciones `using` necesarias:

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Obtenga un objeto `CloudStorageAccount` que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenga un objeto `CloudTableClient` para hacer referencia a los objetos de tabla en la cuenta de almacenamiento:

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenga un objeto de referencia `CloudTable` para hacer referencia a una tabla y a entidades específicas:

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Crear una tabla en el código

Para crear la tabla de Azure, llame a "CreateIfNotExistsAsync()".

```cs
// Create the CloudTable if it does not exist
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla

Para agregar una entidad a una tabla, cree una clase que defina las propiedades de la entidad. El código siguiente define una clase de entidad llamada `CustomerEntity` que utiliza el nombre de pila del cliente como clave de fila y el apellido como clave de partición.

```cs
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
```

Las operaciones de tablas que afectan a las entidades se realizan con el objeto `CloudTable` que se creó anteriormente en el apartado [Acceso a tablas en código](#access-tables-in-code). El objeto `TableOperation` representa la operación que se va a realizar. En el ejemplo de código siguiente se muestra cómo crear un objeto `CloudTable` y un objeto `CustomerEntity`. Para preparar la operación, se crea un objeto `TableOperation` para insertar la entidad de cliente en la tabla. Por último, se ejecuta la operación con una llamada a `CloudTable.ExecuteAsync`.

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserción de un lote de entidades

Puede insertar varias entidades en una tabla mediante una única operación de escritura. En el ejemplo de código siguiente se crean dos objetos de entidad ("Jeff Smith" y "Ben Smith") que se agregan a un objeto `TableBatchOperation` mediante el método `Insert` y, a continuación, se inicia la operación con una llamada a `CloudTable.ExecuteBatchAsync`.

```cs
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
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Obtención de todas las entidades en una partición

Para consultar una tabla a fin de obtener todas las entidades de una partición, use un objeto `TableQuery`. El ejemplo de código siguiente especifica un filtro para las entidades en las que “Smith” es la clave de partición. En este ejemplo, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Obtención de una sola entidad

Puede escribir una consulta para obtener una sola entidad concreta. El código siguiente utiliza un objeto `TableOperation` para especificar el cliente llamado "Ben Smith". Este método devuelve una sola entidad, en lugar de una colección, y el valor devuelto en `TableResult.Result` es un objeto `CustomerEntity`. La forma más rápida de recuperar una sola entidad de `Table` service es especificar claves tanto de partición como de fila en una consulta.

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Eliminación de una entidad

Puede eliminar fácilmente una entidad después de haberla encontrado. El código siguiente busca y elimina una entidad de cliente denominada "Ben Smith":

```cs
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
```

## <a name="next-steps"></a>pasos siguientes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
