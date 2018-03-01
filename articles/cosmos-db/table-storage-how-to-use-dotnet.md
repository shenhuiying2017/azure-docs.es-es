---
title: "Introducción al Azure Table Storage mediante .NET | Microsoft Docs"
description: "Almacene datos estructurados en la nube con el Almacenamiento de tablas de Azure, un almacén de datos NoSQL."
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/30/2018
ms.author: mimig
ms.openlocfilehash: 317adaac1551cf00e25640f1d99429ad76d598a2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-azure-table-storage-using-net"></a>Introducción al Almacenamiento de tablas de Azure mediante .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage es un servicio que almacena datos NoSQL estructurados en la nube y proporciona un almacén de claves y atributos con un diseño sin esquema. Como Almacenamiento de tablas carece de esquema, es fácil adaptar los datos a medida que evolucionan las necesidades de la aplicación. El acceso a los datos de Table Storage es rápido y rentable para muchos tipos de aplicaciones y, por lo general, el costo es normalmente menor que con el SQL tradicional para volúmenes parecidos de datos.

Table Storage se puede usar para almacenar conjuntos de datos flexibles, como datos de usuarios para aplicaciones web, libretas de direcciones, información de dispositivos u otros tipos de metadatos que el servicio requiera. Una tabla puede almacenar un número cualquiera de entidades y una cuenta de almacenamiento puede incluir un número cualquiera de tablas, hasta alcanzar el límite de capacidad de este tipo de cuenta.

### <a name="about-this-tutorial"></a>Acerca de este tutorial
En este tutorial se muestra cómo usar la [biblioteca de tablas de Microsoft Azure CosmosDB para .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) en algunos escenarios habituales de Azure Table Storage. El nombre del paquete indica que es para su uso con Azure Cosmos DB, pero el paquete funciona con Azure Cosmos DB y con Azure Table Storage, cada servicio tiene solo un único punto de conexión. Estos escenarios se describen mediante ejemplos de C# que muestran cómo:
* Crear y eliminar tablas
* Insertar, actualizar y eliminar filas
* Consulta de tablas

## <a name="prerequisites"></a>requisitos previos

Necesitará lo siguiente para completar este tutorial correctamente:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Biblioteca común de Azure Storage para .NET (versión preliminar)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). Se trata de un paquete obligatorio en su versión preliminar que se admite en entornos de producción. 
* [Biblioteca de tablas de Microsoft Azure CosmosDB para .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)
* [Administrador de configuración Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Cuenta de Almacenamiento de Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Más ejemplos
Para más ejemplos de uso de Almacenamiento de tablas, consulte [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)(Introducción a Almacenamiento de tablas de Azure en .NET). Puede descargar la aplicación de ejemplo y ejecutarla, así como ver el código en GitHub.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Creación de una cuenta de servicio de Azure

Puede trabajar con tablas mediante Azure Table Storage o Azure Cosmos DB. Para más información acerca de las diferencias entre los servicios, lea [Ofertas de Table](table-introduction.md#table-offerings). Debe crear una cuenta para el servicio que se va a utilizar. 

### <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage
La forma más fácil de crear la primera cuenta de Azure Storage es a través de [Azure Portal](https://portal.azure.com). Para obtener más información, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Puede crear también una cuenta de Azure Storage mediante [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), la [CLI de Azure](../storage/common/storage-azure-cli.md) o la [biblioteca de cliente de proveedor de recursos de almacenamiento para .NET](/dotnet/api/microsoft.azure.management.storage).

Si no desea crear una cuenta de almacenamiento en este momento, también puede utilizar el emulador de Almacenamiento de Azure para ejecutar y probar el código en un entorno local. Para más información, consulte [Uso del emulador de Azure Storage para desarrollo y pruebas](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Creación de una cuenta de Table API de Azure Cosmos DB

Para obtener instrucciones sobre cómo crear una cuenta de Table API de Azure Cosmos DB, consulte [Creación de una cuenta de Table API](create-table-dotnet.md#create-a-database-account).

## <a name="set-up-your-development-environment"></a>Configuración de su entorno de desarrollo
A continuación, configure el entorno de desarrollo en Visual Studio para poder probar los ejemplos de código de esta guía.

### <a name="create-a-windows-console-application-project"></a>Creación de un proyecto de aplicación de consola de Windows
En Visual Studio, cree una nueva aplicación de consola de Windows. Los siguientes pasos muestran cómo crear una aplicación de consola en Visual Studio 2017. Los pasos son similares en otras versiones de Visual Studio.

1. Seleccione **Archivo** > **Nuevo** > **Proyecto**.
2. Seleccione **Instalado** > **Visual C#** > **Escritorio clásico de Windows**.
3. Seleccione **Aplicación de consola (.NET Framework)**.
4. Escriba el nombre de la aplicación en el campo **Nombre**.
5. Seleccione **Aceptar**.

Todos los ejemplos de código de este tutorial se pueden agregar al método `Main()` del archivo `Program.cs` de la aplicación de consola.

La biblioteca de tablas de Azure CosmosDB se puede usar en cualquier tipo de aplicación. NET, incluidos cualquier servicio en la nube o aplicación web de Azure, y aplicaciones de escritorio o móviles. En esta guía, usamos una aplicación de consola para hacerlo más sencillo.

### <a name="use-nuget-to-install-the-required-packages"></a>Uso de NuGet para instalar los paquetes necesarios
Para completar este tutorial, es preciso que haga referencia a tres paquetes en el proyecto:

* [Biblioteca común de Azure Storage para .NET (versión preliminar 8.6.0)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/8.6.0-preview). 
* [Biblioteca de tablas de Microsoft Azure CosmosDB para .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Este paquete proporciona acceso mediante programación a los recursos de datos de la cuenta de Azure Table Storage o la cuenta de Table API de Azure Cosmos DB.
* [Biblioteca de Administrador de configuración de Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): este paquete proporciona una clase para analizar una cadena de conexión en un archivo de configuración, independientemente del lugar en que se ejecute la aplicación.

Puede usar NuGet para obtener ambos paquetes. Siga estos pasos:

1. Haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y elija **Administrar paquetes NuGet**.
2. Busque en línea "Microsoft.Azure.Storage.Common" y seleccione **Instalar** para instalar la biblioteca común de Azure Storage para .NET (versión preliminar) y sus dependencias. Asegúrese de que la casilla **Incluir versión preliminar** está activada ya que se trata de la versión preliminar de un paquete.
3. Busque en línea "Microsoft.Azure.CosmosDB.Table" y seleccione **Instalar** para instalar la biblioteca de tablas de Microsoft Azure CosmosDB.
4. Busque en línea "WindowsAzure.ConfigurationManager" y seleccione **Instalar** para instalar la biblioteca de Microsoft Azure Configuration Manager.

> [!NOTE]
> Las dependencias de ODataLib en la biblioteca común de Storage para .NET las resuelven los paquetes de ODataLib disponibles en NuGet, no desde WCF Data Services. A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto. Los paquetes ODataLib específicos utilizados por la biblioteca de cliente de almacenamiento son [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) y [Spatial](http://nuget.org/packages/System.Spatial/). Aunque las clases de Azure Table Storage usan estas bibliotecas, son dependencias necesarias para programar con la biblioteca de común de Storage.
> 
> 

### <a name="determine-your-target-environment"></a>Determine su entorno de destino
Tiene dos opciones de entorno para ejecutar los ejemplos de esta guía:

* Puede ejecutar el código en una cuenta de Azure Storage en la nube. 
* Puede ejecutar el código en una cuenta de Azure Cosmos DB en la nube.
* Puede ejecutar el código en el emulador de almacenamiento de Azure. El emulador de almacenamiento es un entorno local que emula una cuenta de Azure Storage en la nube. El emulador es una opción gratis para probar y depurar el código mientras la aplicación está en desarrollo. El emulador usa una cuenta y una clave conocidas. Para más información, consulte [Uso del emulador de Azure Storage para desarrollo y pruebas](../storage/common/storage-use-emulator.md).

Si el destino es una cuenta de Storage en la nube, copie la clave de acceso principal de su cuenta de Storage de Azure Portal. Para obtener más información, consulte [Visualización y copia de las claves de acceso de almacenamiento](../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [!NOTE]
> Puede dirigirse al emulador de almacenamiento para evitar incurrir en cualquier coste asociado con Azure Storage. Sin embargo, si selecciona dirigirse a una cuenta de almacenamiento de Azure en la nube, los costes derivados de la realización de este tutorial serán insignificantes.
> 
> 

Si el destino va a ser una cuenta de Azure Cosmos DB, escriba la clave de acceso principal de su cuenta de Table API desde Azure Portal. Para más información, consulte [Actualización de la cadena de conexión](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.
La biblioteca común de Azure Storage para .NET admite el uso de una cadena de conexión de almacenamiento para configurar puntos de conexión y credenciales a fin de obtener acceso a los servicios de almacenamiento. La mejor manera de conservar la cadena de conexión de almacenamiento es mediante un archivo de configuración. 

Para más información acerca de las cadenas de conexión, consulte [Configuración de una cadena de conexión a Azure Storage](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> La clave de la cuenta es similar a la contraseña raíz de la cuenta de almacenamiento. Siempre debe proteger la clave de la cuenta de almacenamiento. Evite distribuirla a otros usuarios, codificarla de forma rígida o guardarla en un archivo de texto que sea accesible a otros usuarios. Vuelva a generar la clave mediante Azure Portal si cree que puede verse comprometida.
> 
> 

Para configurar la cadena de conexión, abra el archivo `app.config` en el Explorador de soluciones de Visual Studio. Agregue el contenido del elemento `<appSettings>` , que se muestra a continuación. Reemplace `account-name` por el nombre de su cuenta y `account-key` por la clave de acceso:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Por ejemplo, si va a utilizar una cuenta de Azure Storage, las opciones de configuración se parecerán a estas:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Si va a utilizar una cuenta de Azure Cosmos DB, las opciones de configuración se parecerán a estas:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

Para elegir como destino el emulador de almacenamiento, puede utilizar un acceso directo que se asigna al nombre y la clave conocidas de la cuenta. En ese caso, la configuración de la cadena de conexión es:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Adición de directivas using
Agregue las siguientes directivas **using** al principio del archivo `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage.Common; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Análisis de la cadena de conexión
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Creación del cliente de Table service
La clase [CloudTableClient][dotnet_CloudTableClient] le permite recuperar tablas y entidades almacenadas en Table Storage. Esta es una forma de crear el cliente de Table service:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Ahora ya puede escribir código que lee y escribe datos en Almacenamiento de tablas.

## <a name="create-a-table"></a>Creación de una tabla
En este ejemplo se muestra cómo crear una tabla si todavía no existe:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla
Las entidades se asignan a objetos C# mediante una clase personalizada derivada de [TableEntity][dotnet_TableEntity]. Para agregar una entidad a una tabla, cree una clase que defina las propiedades de la entidad. El código siguiente define una clase de entidad que utiliza el nombre de pila del cliente como clave de fila y el apellido como clave de partición. En conjunto, las claves de partición y de fila de una entidad la identifican inequívocamente en la tabla. Las entidades con la misma clave de partición pueden consultarse más rápidamente que aquellas con diferentes claves de partición, pero el uso de claves de partición diversas permite una mayor escalabilidad de las operaciones paralelas. Las entidades que se almacenan en tablas deben ser de un tipo compatible, por ejemplo, derivadas de la clase [TableEntity][dotnet_TableEntity]. Las propiedades de entidad que desee almacenar en una tabla deben ser propiedades públicas del tipo, y deben admitir que se obtengan y establezcan valores. Además, el tipo de entidad *must* expone un constructor sin parámetros.

```csharp
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

Las operaciones de tablas que afectan a las entidades se realizan mediante el objeto [CloudTable][dotnet_CloudTable] que se creó en la sección "Creación de una tabla". La operación que va a realizarse se representa con un objeto [TableOperation][dotnet_TableOperation]. En el ejemplo de código siguiente, se muestra la creación del objeto [CloudTable][dotnet_CloudTable] y, a continuación, un objeto **CustomerEntity**. Para preparar la operación, se crea un objeto [TableOperation][dotnet_TableOperation] para insertar la entidad de cliente en la tabla. Por último, se ejecuta la operación llamando a [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute].

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserción de un lote de entidades
Puede insertar un lote de entidades en una tabla mediante una operación de escritura. Otras notas acerca de las operaciones por lotes:

* Puede realizar actualizaciones, eliminaciones e inserciones en la misma operación por lotes.
* Una única operación por lotes puede incluir hasta 100 entidades.
* Todas las entidades de la misma operación por lotes deben compartir la misma clave de partición.
* Aunque es posible realizar una consulta a modo de operación por lotes, debe tratarse de la única operación del lote.

En el siguiente ejemplo de código, se crean dos objetos de entidad y se agrega cada uno a [TableBatchOperation][dotnet_TableBatchOperation] con el método [Insert][dotnet_TableBatchOperation_Insert]. A continuación, se llama a [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] para ejecutar la operación.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

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
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>todas las entidades de una partición
Para consultar una tabla a fin de obtener todas las entidades de una partición, utilice un objeto [TableQuery][dotnet_TableQuery]. El ejemplo de código siguiente especifica un filtro para las entidades en las que “Smith” es la clave de partición. En este ejemplo, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperación de un rango de entidades de una partición
Si no desea consultar todas las entidades de una partición, puede especificar un intervalo combinando el filtro de clave de partición con un filtro de clave de fila. En el ejemplo de código siguiente, se utilizan dos filtros para obtener todas las entidades de la partición "Smith" en las que la clave de fila (nombre de pila) empieza por una letra anterior a "E" en el alfabeto; a continuación, se imprimen los resultados de la consulta.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>una sola entidad
Puede enviar una consulta para recuperar una sola entidad concreta. El código siguiente utiliza [TableOperation][dotnet_TableOperation] para especificar el cliente "Ben Smith". Este método devuelve solo una entidad, en lugar de una colección, y el valor devuelto en [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] es un objeto **CustomerEntity**. La forma más rápida de recuperar una sola entidad de Table service es especificar claves tanto de partición como de fila en las consultas.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>una entidad
Para actualizar una entidad, recupérela de Table service, modifique su objeto y, luego, guarde los cambios de nuevo en Table service. El código siguiente cambia el número de teléfono de un cliente. En lugar de llamar a [Insert][dotnet_TableOperation_Insert], este código utiliza [Replace][dotnet_TableOperation_Replace]. [Replace][dotnet_TableOperation_Replace] hace que la entidad se reemplace por completo en el servidor, a menos que la entidad del servidor cambiara desde que se recuperó, en cuyo caso la operación dará error. Este error evita que la aplicación sobrescriba accidentalmente un cambio realizado entre la recuperación y la actualización por otro componente de la misma. La manera correcta de actuar ante este error es recuperar de nuevo la entidad, hacer los cambios oportunos (si siguen siendo válidos) y, a continuación, realizar otra operación [Replace][dotnet_TableOperation_Replace]. En la siguiente sección se muestra cómo invalidar este comportamiento.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Inserción o reemplazo de una entidad
Las operaciones [Replace][dotnet_TableOperation_Replace] darán error si se cambió la entidad desde que se recuperó del servidor. Asimismo, primero debe recuperar la entidad del servidor para que la operación [Replace][dotnet_TableOperation_Replace] se realice correctamente. Sin embargo, en ocasiones no sabe si la entidad existe en el servidor y los valores actuales almacenados en él son irrelevantes. La actualización debe sobrescribir todos. Para hacer esto, debe utilizar una operación [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Esta operación inserta la entidad si no existe o la reemplaza si ya existe, con independencia del momento en que se realizó la última actualización.

En el ejemplo de código siguiente, se crea una entidad de cliente para "Fred Jones" y se inserta en la tabla "people". A continuación, se usa la operación [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] para guardar una entidad con la misma clave de partición (Jones) y clave de fila (Fred) en el servidor, esta vez con un valor diferente para la propiedad PhoneNumber. Dado que se ha usado [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], se reemplazan todos los valores de la propiedad. No obstante, si no había una entidad "Fred Jones" en la tabla, se habría insertado.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Consulta de un subconjunto de propiedades de las entidades
Una consulta de tabla puede recuperar tan solo unas cuantas propiedades de una entidad en lugar de todas ellas. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. La consulta del código siguiente devuelve solo las direcciones de correo electrónico de las entidades de la tabla. Esto se consigue utilizando una consulta de [DynamicTableEntity][dotnet_DynamicTableEntity] y también [EntityResolver][dotnet_EntityResolver]. Puede aprender más sobre la proyección en la [entrada de blog de introducción a la proyección de upsert y consulta][blog_post_upsert]. La proyección no se admite en el emulador de almacenamiento, por lo que este código solo se ejecuta cuando está usando una cuenta en Table service.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Eliminación de una entidad
Puede eliminar fácilmente una entidad una vez recuperada utilizando el mismo patrón mostrado para actualizar una entidad. El código siguiente recupera y elimina una entidad de cliente.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Eliminación de una tabla
Finalmente, el ejemplo de código siguiente elimina una tabla de una cuenta de almacenamiento. Una tabla que se ha eliminada no podrá volver a crearse durante un tiempo tras la eliminación.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Recuperación de entidades en páginas de forma asincrónica
Si está leyendo un gran número de entidades y desea procesar/mostrar entidades según se van recuperando en lugar de esperar a que se devuelvan todas, puede recuperarlas mediante una consulta segmentada. En este ejemplo se muestra cómo devolver resultados en páginas mediante el patrón Async-Await de forma que la ejecución no se bloquee mientras se espera que se devuelva un conjunto grande de resultados. Para más información sobre el uso del patrón Async-Await en. NET, consulte [Programación asincrónica con Async y Await (C# y Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>pasos siguientes
Ahora que está familiarizado con los aspectos básicos del almacenamiento de Tabla, siga estos vínculos para obtener más información sobre tareas de almacenamiento más complejas.

* El [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.
* Consulte más ejemplos de uso de Almacenamiento de tablas en [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* Consulte la documentación de referencia de Table service para obtener información detallada acerca de las API disponibles:
* [Referencia de la biblioteca de clientes de almacenamiento para .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [Referencia de API de REST](http://msdn.microsoft.com/library/azure/dd179355)
* Aprenda a simplificar el código que escriba para trabajar con Almacenamiento de Azure mediante [SDK de Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
* [Introducción al Almacenamiento de blobs de Azure mediante .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para almacenar datos estructurados.
* Para almacenar datos relacionales, consulte [Conexión a SQL Database mediante .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md).

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
