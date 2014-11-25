<properties urlDisplayName="Table Service" pageTitle="Uso del almacenamiento de tablas en .NET | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use Microsoft Azure Table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use Microsoft Azure Table storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />



# Uso del almacenamiento de tablas en .NET


Esta guía muestra cómo realizar algunas tareas comunes a través del 
servicio de almacenamiento de tablas de Azure. Los ejemplos están escritos en código C\#
y utilizan la biblioteca del cliente de almacenamiento de Azure para .NET. Dichas tareas comunes incluyen **crear y
eliminar una tabla**, así como **trabajar con entidades de tabla**. Para obtener más
información acerca de las tablas, consulte la sección [Pasos siguientes][Pasos siguientes].

> [WACOM.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento .NET de Azure 2.x y posterior. La versión recomendada es la biblioteca de cliente de almacenamiento 4.x, que está disponible a través de [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o como parte del [SDK de Azure para .NET](/es-es/downloads/). Consulte [Acceso al almacenamiento de tabla mediante programación][Acceso al almacenamiento de tabla mediante programación] a continuación para obtener más información acerca de la obtención de una biblioteca de cliente de almacenamiento.

## Tabla de contenido

-   [Qué es el servicio Tabla][Qué es el servicio Tabla]
-   [Conceptos][Conceptos]
-   [Creación de una cuenta de almacenamiento de Azure][Creación de una cuenta de almacenamiento de Azure]
-   [Configuración de una cadena de conexión de almacenamiento][Configuración de una cadena de conexión de almacenamiento]
-   [Acceso al almacenamiento de tablas mediante programación][Acceso al almacenamiento de tablas mediante programación]
-   [Creación de una tabla][Creación de una tabla]
-   [Incorporación de una entidad a una tabla][Incorporación de una entidad a una tabla]
-   [Inserción de un lote de entidades][Inserción de un lote de entidades]
-   [Recuperación de todas las entidades de una partición][Recuperación de todas las entidades de una partición]
-   [Recuperación de un rango de entidades de una partición][Recuperación de un rango de entidades de una partición]
-   [Recuperación de una sola entidad][Recuperación de una sola entidad]
-   [Reemplazo de una entidad][Reemplazo de una entidad]
-   [Inserción o reemplazo de una entidad][Inserción o reemplazo de una entidad]
-   [Consulta de un subconjunto de propiedades de las entidades][Consulta de un subconjunto de propiedades de las entidades]
-   [Eliminación de una entidad][Eliminación de una entidad]
-   [Eliminación de una tabla][Eliminación de una tabla]
-   [Pasos siguientes][Pasos siguientes]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <h2><a name="create-account"></a><span class="short-header">Creación de una cuenta</span>Creación de una cuenta de almacenamiento de Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a><span class="short-header">Configuración de una cadena de conexión</span>Configuración de una cadena de conexión de almacenamiento</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <h2> <a name="configure-access"> </a><span  class="short-header">Acceso mediante programación</span>Acceso al almacenamiento de tablas mediante programación</h2>

<h3>Obtención del ensamblado</h3>
Puede utilizar NuGet para obtener el ensamblado "Microsoft.WindowsAzure.Storage.dll". Haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y elija **Manage NuGet Packages**.  Busque "Azure.Storage" en línea y haga clic en **Install** para instalar el paquete y las dependencias de almacenamiento de Azure.

`Microsoft.Azure.Storage.dll` también se incluye en el SDK de Azure para .NET, que se puede descargar en <a href="http://www.windowsazure.com/es-es/develop/net/#">.NET Developer Center</a>. El ensamblado se instala en el directorio `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<version-sdk>\ref\`.

<h3>Declaraciones de espacio de nombres</h3>
Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de cualquier archivo de C\#
en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    mediante Microsoft.WindowsAzure.Storage;
	mediante Microsoft.WindowsAzure.Storage.Auth;
    mediante Microsoft.WindowsAzure.Storage.Table;

Asegúrese de hacer referencia al ensamblado `Microsoft.WindowsAzure.Storage.dll`.

<h3>Recuperación de la cadena de conexión</h3>
Puede utilizar el tipo **CloudStorageAccount** para representar 
la información de la cuenta de almacenamiento. Si está utilizando una 
plantilla de proyecto de Azure o hace referencia al espacio de nombres
Microsoft.WindowsAzure.CloudConfigurationManager, puede utilizar 
el tipo **CloudConfigurationManager**
para recuperar la cadena de conexión de almacenamiento y la información de la cuenta
de almacenamiento de la configuración de servicios de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si está creando una aplicación sin ninguna referencia a Microsoft.WindowsAzure.CloudConfigurationManager y la cadena de conexión está situada en el archivo `web.config` o `app.config`, como se muestra anteriormente, puede utilizar **ConfigurationManager** para recuperar dicha cadena.  Deberá agregar una referencia a System.Configuration.dll a su proyecto, así como otra declaración de espacio de nombres para ella:

	mediante System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dependencias ODataLib</h3>
Las dependencias ODataLib de la biblioteca de clientes de almacenamiento para .NET se resuelven mediante los paquetes ODataLib (versión 5.0.2) disponibles a través de NuGet y no a través de los servicios de datos de WCF.  A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto.  Los paquetes ODataLib específicos son [OData], [Edm] y [Spatial].

<h2><a name="create-table"></a><span class="short-header">Creación de una tabla</span>Creación de una tabla</h2>

Los objetos **CloudTableClient** le permiten obtener objetos de referencia para las tablas
 y las entidades. El siguiente código crea un objeto **CloudTableClient**
y lo utiliza para crear una nueva tabla. En todo el código de esta guía se asume que
la aplicación que se está desarrollando es un proyecto del Servicio en la nube de Azure y
se utiliza una cadena de conexión de almacenamiento guardada en la configuración del servicio de la aplicación de Azure.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Cree la tabla si todavía no existe.
    CloudTable table = tableClient.GetTableReference("personas");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a><span class="short-header">Incorporación de una entidad a una tabla</span>Incorporación de una entidad a una tabla</h2>

Las entidades se asignan a objetos C\# utilizando una clase personalizada derivada de
**TableEntity**. Para agregar una entidad a una tabla, cree una
clase que defina las propiedades de la entidad. El código siguiente
define una clase de entidad que utiliza el nombre de pila del cliente como clave de fila
y el apellido como clave de partición. En conjunto, la clave de partición y
la clave de fila de una entidad la identifican inequívocamente en la tabla. Las entidades con la
clave de partición pueden consultarse más rápidamente que aquellas con diferentes
claves de partición, pero el uso de claves de partición diversas permite una mayor escalabilidad
de las operaciones paralelas.  Toda propiedad que deba almacenarse en el servicio Tabla 
debe ser pública, pertenecer a un tipo compatible y exponer tanto `get` como `set`.
Además, el tipo de entidad *debe* exponer un constructor sin parámetros.

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

Las operaciones de tablas que afectan a las entidades se realizan utilizando el objeto **CloudTable**
que se creó en el apartado "Creación de una tabla".  La operación que va a realizarse
se representa con un objeto **TableOperation**.  El ejemplo de código siguiente muestra la creación del objeto **CloudTable** y, a continuación, un objeto **CustomerEntity**.  Para preparar la operación, se crea un objeto **TableOperation** a fin de insertar la entidad del cliente en la tabla.  Finalmente, se ejecuta la operación llamando a **CloudTable.Execute**.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Cree el objeto CloudTable que representa a la tabla "personas".
	CloudTable table = tableClient.GetTableReference("personas");

    // Cree una nueva entidad de cliente.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Cree la TableOperation que inserta la entidad del cliente.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Ejecute la operación de inserción.
    table.Execute(insertOperation);

<h2><a name="insert-batch"></a><span class="short-header">Inserción de un lote de entidades</span>Inserción de un lote de entidades</h2>

Puede insertar un lote de entidades en una tabla mediante una operación
 de escritura. Otras notas acerca de las operaciones
por lotes:

1.  Puede realizar actualizaciones, eliminaciones e inserciones en la misma operación por lotes.
2.  Una única operación por lotes puede incluir hasta 100 entidades.
3.  Todas las entidades de la misma operación por lotes deben compartir la
    clave de partición.
4.  Aunque es posible realizar una consulta a modo de operación por lotes, debe tratarse de la única operación del lote.

<!-- -->
El siguiente ejemplo de código crea dos objetos de entidad y agrega cada uno de ellos
a un objeto **TableBatchOperation** utilizando el método **Insert**. A continuación, se llama a **CloudTable.Execute** para ejecutar la operación.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	// Cree el objeto CloudTable que representa a la tabla "personas".
    CloudTable table = tableClient.GetTableReference("personas");

    // Cree la operación de lote.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Cree la entidad de cliente y agréguela a la tabla.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// Cree otra entidad de cliente y agréguela a la tabla.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// Agregue las dos entidades de cliente a la operación de inserción del lote.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Ejecute la operación de lote.
	table.ExecuteBatch(batchOperation);

<h2><a name="retrieve-all-entities"></a><span class="short-header">Recuperación de todas las entidades</span>Recuperación de todas las entidades de una partición</h2>

Para consultar una tabla a fin de obtener todas las entidades de una partición, utilice un objeto **TableQuery**.
El ejemplo de código siguiente especifica un filtro para las entidades en las que "Smith"
es la clave de partición. En este ejemplo, los campos de
cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Cree el objeto CloudTable que representa a la tabla "personas".
    CloudTable table = tableClient.GetTableReference("personas");

    // Construya la operación de consulta para todas las entidades de cliente en las que PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Imprima los campos de cada cliente.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a><span class="short-header">Recuperación de un rango de entidades</span>Recuperación de un rango de entidades de una partición</h2>

Si no desea consultar todas las entidades de una partición, puede
especificar un rango combinando el filtro de clave de partición con un filtro de clave de fila. El ejemplo de código siguiente
utiliza dos filtros para obtener todas las entidades de la partición "Smith" en las que la clave de fila
(nombre de pila) empieza por una letra anterior a "E" en el alfabeto y, a continuación,
imprime los resultados de la consulta.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Cree el objeto CloudTable que representa a la tabla "personas".
    CloudTable table = tableClient.GetTableReference("personas");

	// Cree la consulta de tabla.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Aplique un bucle a través de los resultados, mostrando información acerca de la entidad.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a><span class="short-header">Recuperación de una sola entidad</span>Recuperación de una sola entidad</h2>

Puede enviar una consulta para recuperar una sola entidad concreta. El
código siguiente utiliza un objeto **TableOperation** para especificar el cliente "Ben Smith".
Este método devuelve solo una entidad, en lugar de una
colección, y el valor devuelto en **TableResult.Result** es **CustomerEntity**.
La forma más rápida de recuperar una sola entidad del servicio Tabla es 
especificar claves tanto de partición como de fila en las consultas.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Cree el objeto CloudTable que representa a la tabla "personas".
    CloudTable table = tableClient.GetTableReference("personas");

    // Cree una operación de recuperación que toma una entidad de cliente.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Ejecute la operación de recuperación.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Imprima el número de teléfono del resultado.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("No se pudo recuperar el número de teléfono.");

<h2><a name="replace-entity"></a><span class="short-header">Reemplazo de una entidad</span>Reemplazo de una entidad</h2>

Para actualizar una entidad, recupérela del servicio Tabla, modifique su
objeto y, a continuación, guarde los cambios de nuevo en el servicio Tabla. El
código siguiente cambia el número de teléfono de un cliente. En lugar de
llamar a **Insert**, este código utiliza 
**Replace**. Esto hace que la entidad se reemplace por completo en el servidor,
a menos que la entidad del servidor cambiara desde que se recuperó, en
cuyo caso la operación dará error.  Este error evita que la aplicación
sobrescriba accidentalmente un cambio realizado entre la recuperación y 
la actualización por otro componente de la misma.  La manera correcta de actuar ante este error
es recuperar de nuevo la entidad, hacer los cambios oportunos (si siguen siendo válidos) y, a continuación, 
realizar otra operación **Replace**.  En la siguiente sección
se muestra cómo invalidar este comportamiento.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Cree el objeto CloudTable que representa a la tabla "personas".
    CloudTable table = tableClient.GetTableReference("personas");

    // Cree una operación de recuperación que toma una entidad de cliente.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Ejecute la operación.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Asigne el resultado a un objeto CustomerEntity.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Cambie el número de teléfono.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Cree TableOperation InsertOrReplace
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Ejecute la operación.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entidad actualizada.");
	}

	else
	   Console.WriteLine("No se pudo recuperar la entidad.");

<h2><a name="insert-or-replace-entity"></a><span class="short-header">Inserción o reemplazo de una entidad</span>Inserción o reemplazo de una entidad</h2>

**Las operaciones Replace** darán error si la entidad se cambió desde
su recuperación del servidor.  Asimismo, primero debe recuperar
la entidad del servidor para que la operación **Replace** se realice correctamente.
Sin embargo, en ocasiones no sabe si la entidad existe en el servidor
y los valores actuales almacenados en él son irrelevantes, ya que la actualización los debe
sobrescribir todos.  Para realizarlo, debe utilizar una operación **InsertOrReplace**
.  Esta operación inserta la entidad si no existe o
la reemplaza si ya existe, con independencia del momento en que se realizó la última actualización.  En el 
ejemplo de código siguiente, la entidad del cliente Ben Smith sigue recuperada pero, a continuación, se guarda de nuevo en el servidor mediante la operación **InsertOrReplace**.  Las actualizaciones
realizadas en la entidad entre la recuperación y la operación se 
sobrescribirán.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Cree el objeto CloudTable que representa a la tabla "personas".
    CloudTable table = tableClient.GetTableReference("personas");

    // Cree una operación de recuperación que toma una entidad de cliente.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Ejecute la operación.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Asigne el resultado a un objeto CustomerEntity.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Cambie el número de teléfono.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Cree TableOperation InsertOrReplace
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Ejecute la operación.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entidad actualizada.");
	}

	else
	   Console.WriteLine("No se pudo recuperar la entidad.");

<h2><a name="query-entity-properties"></a><span class="short-header">Consulta de un subconjunto de propiedades</span>Consulta de un subconjunto de propiedades de las entidades</h2>

Una consulta de tabla puede recuperar tan solo unas cuantas propiedades de una entidad en lugar de todas ellas. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. La consulta del
código siguiente devuelve solo las direcciones de correo electrónico de las entidades de la
tabla. Esto se consigue utilizando una consulta de **DynamicTableEntity** y 
también un objeto **EntityResolver**. Puede obtener más información acerca de la proyección en esta [entrada de blog][entrada de blog] (en inglés). Tenga en cuenta que la proyección no es compatible con el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando se utiliza una cuenta del servicio Tabla.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Cree el objeto CloudTable que representa a la tabla "personas".
    CloudTable table = tableClient.GetTableReference("personas");

    // Defina la query, y seleccione solo la propiedad Correo electrónico.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Defina un solucionador de entidad para que funcione con la entidad tras la recuperación.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

<h2><a name="delete-entity"></a><span class="short-header">Eliminación de una entidad</span>Eliminación de una entidad</h2>

Puede eliminar fácilmente una entidad una vez recuperada utilizando el mismo patrón
mostrado para actualizar una entidad.  El código siguiente
recupera y elimina una entidad de cliente.

    // Recuperar la cuenta de almacenamiento de la cadena de conexión
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Cree el objeto CloudTable que representa a la tabla "personas".
    CloudTable table = tableClient.GetTableReference("personas");

    // Cree una operación de recuperación que espere una entidad de cliente.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Ejecute la operación.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Asigne el resultado a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Cree TableOperation Delete.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Ejecute la operación.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entidad eliminada.");
	}

	else
	   Console.WriteLine("No se pudo recuperar la entidad.");

<h2><a name="delete-table"></a><span class="short-header">Eliminación de una tabla</span>Eliminación de una tabla</h2>

Finalmente, el ejemplo de código siguiente elimina una tabla de una cuenta de almacenamiento. Las
tablas eliminadas no podrán volver a crearse durante un
tiempo tras la eliminación.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Cree el objeto CloudTable que representa a la tabla "personas".
    CloudTable table = tableClient.GetTableReference("personas");

    // Elimine la tabla si existe.
    table.DeleteIfExists();

<h2><a name="next-steps"></a><span class="short-header">Pasos siguientes</span>Pasos siguientes</h2>

Ahora que está familiarizado con los aspectos básicos del almacenamiento en tabla, utilice estos vínculos
para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

<ul>
<li>Consulte la documentación de referencia del servicio de tabla para obtener información detallada acerca de las API disponibles:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referencia acerca de la biblioteca de clientes de almacenamiento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/es-es/library/windowsazure/dd179355">Referencia de la API REST</a></li>
  </ul>
</li>
<li>	Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en <a href="http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx">Almacenamiento de datos y acceso a los mismos en Azure</a>.</li>
<li>Obtenga información acerca de cómo trabajar con Almacenamiento de Azure en procesos de back-end para Sitios web Azure en <a href="/es-es/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Introducción al SDK de WebJobs de Azure</a>.</li>
<li>Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
  <ul>
    <li>Utilice <a href="/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/">Almacenamiento de blobs</a> para almacenar datos no estructurados.</li>
    <li>Utilice <a href="/es-es/documentation/articles/storage-dotnet-how-to-use-queues/">Almacenamiento de colas</a> para almacenar datos estructurados.</li>
    <li>Utilice <a href="/es-es/documentation/articles/sql-database-dotnet-how-to-use/">Base de datos SQL</a> para almacenar datos relacionales.</li>
  </ul>
</li>
</ul>

  [Pasos siguientes]: #next-steps
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Configuración de una cadena de conexión de almacenamiento]: #setup-connection-string
  [Creación de una tabla]: #create-table
  [Incorporación de una entidad a una tabla]: #add-entity
  [Inserción de un lote de entidades]: #insert-batch
  [Recuperación de todas las entidades de una partición]: #retrieve-all-entities
  [Recuperación de un rango de entidades de una partición]: #retrieve-all-entities
  [Recuperación de una sola entidad]: #retrieve-single-entity
  [Reemplazo de una entidad]: #replace-entity
  [Inserción o reemplazo de una entidad]: #insert-or-replace-entity
  [Consulta de un subconjunto de propiedades de las entidades]: #query-entity-properties
  [Eliminación de una entidad]: #delete-entity
  [Eliminación de una tabla]: #delete-table
  
  
  [entrada de blog]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
