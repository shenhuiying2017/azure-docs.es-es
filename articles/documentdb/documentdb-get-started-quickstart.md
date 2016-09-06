<properties
	pageTitle="Tutorial de NoSQL: SDK de DocumentDB para .NET | Microsoft Azure"
	description="Tutorial de NoSQL que crea una base de datos en línea y la aplicación de consola de C# mediante el SDK de DocumentDB para .NET. DocumentDB es una base de datos NoSQL para JSON."
	keywords="tutorial de nosql, base de datos en línea, aplicación de consola de c#"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/29/2016"
	ms.author="anhoh"/>

# Tutorial de NoSQL: Crear una aplicación de consola de C# DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bienvenido al tutorial de NoSQL para el SDK de Azure DocumentDB para .NET. Después de obtener el proyecto QuickStart o completar este tutorial, tendrá una aplicación de consola que crea recursos de DocumentDB y realiza consultas en ellos.

- **[QuickStart](#quickstart)**: descargue el proyecto de ejemplo, agregue la información de conexión y dispondrá de una aplicación en ejecución de DocumentDB en menos de cinco minutos.
- **[Tutorial](#tutorial)**: cree la aplicación QuickStart desde el principio en 30 minutos.

## Requisitos previos

- Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/).
- [Visual Studio 2013 o Visual Studio 2015](http://www.visualstudio.com/).
- .NET Framework 4.6

## QuickStart

1. Descarga del proyecto de ejemplo de [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started-quickstart/archive/master.zip).
2. Use el Portal de Azure para [crear una cuenta de DocumentDB](documentdb-create-account.md).
3. En el archivo App.config, reemplace los valores EndpointUri y PrimaryKey por los valores recuperados del [Portal de Azure](https://portal.azure.com/), navegando a la hoja **DocumentDB (NoSQL)**, después, haga clic en el **nombre de la cuenta** y, finalmente, haga clic en **Claves** en el menú de recursos. ![Captura de pantalla con el valor EndpointUri y PrimaryKey para reemplazar en el archivo App.config](./media/documentdb-get-started-quickstart/nosql-tutorial-documentdb-keys.png)
4. Compile el proyecto. La ventana de consola muestra los recursos que crean, se consultan y se limpian.
    
    ![Captura de pantalla de la salida de consola](./media/documentdb-get-started-quickstart/nosql-tutorial-documentdb-console-output.png)

## <a id="tutorial"></a>Tutorial

Este tutorial le guiará por la creación de una base de datos de DocumentDB, de una colección de DocumentDB y de documentos JSON. Después, podrá consultar la colección, y limpiar y eliminar la base de datos. Este tutorial crea el mismo proyecto que el proyecto QuickStart, pero lo va a crear de forma incremental, mientras recibe explicaciones sobre el código que agrega al proyecto.

## Paso 1: Creación de una cuenta de DocumentDB

Creemos una cuenta de DocumentDB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la solución de Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a> Paso 2: Configuración de la solución de Visual Studio

1. Abra **Visual Studio 2015** en el equipo.
2. En el menú **Archivo**, seleccione **Nuevo** y elija **Proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Plantillas**/**Visual C#**/ **Aplicación de consola**, asigne un nombre al proyecto y haga clic en **Aceptar**. ![Captura de pantalla de la ventana Nuevo proyecto](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. En el **Explorador de soluciones**, haga clic con el botón secundario en la nueva aplicación de la consola, que se encuentra en la solución de Visual Studio.
5. Luego, sin dejar el menú, haga clic en **Administrar paquetes NuGet...** ![Captura de pantalla del menú contextual del proyecto](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. En la pestaña **Nuget** haga clic en **Examinar** y escriba **azure documentdb** en el cuadro de búsqueda.
7. En los resultados, busque **Microsoft.Azure.DocumentDB** y haga clic en **Instalar**. El identificador del paquete de DocumentDB Client Library es [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) ![Captura de pantalla del menú NuGet para encontrar el SDK de cliente de DocumentDB](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

Estupendo. Ahora que hemos terminado la configuración, comencemos a escribir algo de código. Puede buscar un proyecto con código completo de este tutorial en [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a id="Connect"></a> Paso 3: Conexión a una cuenta de DocumentDB

En primer lugar, agregue estas referencias al principio de la aplicación de C#, en el archivo Program.cs:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] Para poder completar este tutorial de NoSQL, asegúrese de que agregar las dependencias anteriores.

Ahora, agregue estas dos constantes y la variable de *cliente* debajo de la clase pública *Program*.

	public class Program
	{
		// ADD THIS PART TO YOUR CODE
		private const string EndpointUri = "<your endpoint URI>";
		private const string PrimaryKey = "<your key>";
		private DocumentClient client;

Seguidamente, diríjase al [Portal de Azure](https://portal.azure.com) para recuperar el identificador URI y la clave principal. El identificador URI y la clave principal de DocumentDB son necesarios para que la aplicación sepa a dónde debe conectarse y para que DocumentDB confíe en la conexión de la aplicación.

En el Portal de Azure, navegue a la cuenta de DocumentDB y, después, haga clic en **Claves**.

Copie el URI desde el Portal y péguelo en `<your endpoint URI>` en el archivo program.cs. Después, copie la CLAVE PRINCIPAL del Portal y péguela en `<your key>`.

![Captura de pantalla del Portal de Azure usada por el tutorial de NoSQL para crear una aplicación de consola de C#. Muestra una cuenta de DocumentDB, con el concentrador ACTIVO resaltado, el botón CLAVES resaltado en la hoja de cuenta de DocumentDB y los valores URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la hoja Claves.][keys]

Iniciaremos la aplicación de introducción, para lo que crearemos una nueva instancia de **DocumentClient**.

Debajo del método **Main**, agregue esta nueva tarea asincrónica denominada **GetStartedDemo**, que creará una instancia del nuevo **DocumentClient**.

	static void Main(string[] args)
	{
	}

	// ADD THIS PART TO YOUR CODE
	private async Task GetStartedDemo()
	{
		this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
	}

Agregue el siguiente código para ejecutar la tarea asincrónica desde el método **Main**. El método **Main** detectará las excepciones y las escribirá en la consola.

	static void Main(string[] args)
	{
			// ADD THIS PART TO YOUR CODE
			try
			{
					Program p = new Program();
					p.GetStartedDemo().Wait();
			}
			catch (DocumentClientException de)
			{
					Exception baseException = de.GetBaseException();
					Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
			}
			catch (Exception e)
			{
					Exception baseException = e.GetBaseException();
					Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
			}
			finally
			{
					Console.WriteLine("End of demo, press any key to exit.");
					Console.ReadKey();
			}

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Se ha conectado correctamente a una cuenta de DocumentDB, y ahora se explicará cómo trabajar con recursos de DocumentDB.

## Paso 4: Creación de una base de datos
Antes de agregar el código para crear una base de datos, agregue un método auxiliar para escribirlo en la consola.

Copie y pegue el método **WriteToConsoleAndPromptToContinue** debajo del método **GetStartedDemo**.

	// ADD THIS PART TO YOUR CODE
	private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
	{
			Console.WriteLine(format, args);
			Console.WriteLine("Press any key to continue ...");
			Console.ReadKey();
	}

Puede crearse una [base de datos](documentdb-resources.md#databases) de DocumentDB mediante el método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos JSON particionado en recopilaciones.

Copie y pegue el método **CreateDatabaseIfNotExists** debajo del método **WriteToConsoleAndPromptToContinue**.

	// ADD THIS PART TO YOUR CODE
	private async Task CreateDatabaseIfNotExists(string databaseName)
	{
			// Check to verify a database with the id=FamilyDB does not exist
			try
			{
					await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(databaseName));
					this.WriteToConsoleAndPromptToContinue("Found {0}", databaseName);
			}
			catch (DocumentClientException de)
			{
					// If the database does not exist, create a new database
					if (de.StatusCode == HttpStatusCode.NotFound)
					{
							await this.client.CreateDatabaseAsync(new Database { Id = databaseName });
							this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
					}
					else
					{
							throw;
					}
			}
	}

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación del cliente. Así se creará una base de datos denominada *FamilyDB*.

	private async Task GetStartedDemo()
	{
		this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

		// ADD THIS PART TO YOUR CODE
		await this.CreateDatabaseIfNotExists("FamilyDB_va");

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha creado correctamente una base de datos de DocumentDB.

## <a id="CreateColl"></a>Paso 5: Creación de una colección  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** creará una nueva colección con un rendimiento reservado, lo que tiene implicaciones en los precios. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).

Una [colección](documentdb-resources.md#collections) puede crearse mediante el método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript.

Copie y pegue el método **CreateDocumentCollectionIfNotExists** debajo del método **CreateDatabaseIfNotExists**.

	// ADD THIS PART TO YOUR CODE
	private async Task CreateDocumentCollectionIfNotExists(string databaseName, string collectionName)
	{
		try
		{
			await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
			this.WriteToConsoleAndPromptToContinue("Found {0}", collectionName);
		}
		catch (DocumentClientException de)
		{
			// If the document collection does not exist, create a new collection
			if (de.StatusCode == HttpStatusCode.NotFound)
			{
				DocumentCollection collectionInfo = new DocumentCollection();
				collectionInfo.Id = collectionName;

				// Configure collections for maximum query flexibility including string range queries.
				collectionInfo.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

				// Here we create a collection with 400 RU/s.
				await this.client.CreateDocumentCollectionAsync(
					UriFactory.CreateDatabaseUri(databaseName),
					collectionInfo,
					new RequestOptions { OfferThroughput = 400 });

				this.WriteToConsoleAndPromptToContinue("Created {0}", collectionName);
			}
			else
			{
				throw;
			}
		}
	}

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación de la base de datos. Esto creará una colección de documentos denominada *FamilyCollection\_va*.

		this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

		await this.CreateDatabaseIfNotExists("FamilyDB_oa");

		// ADD THIS PART TO YOUR CODE
		await this.CreateDocumentCollectionIfNotExists("FamilyDB_va", "FamilyCollection_va");

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha creado correctamente una colección de documentos de DocumentDB.

## <a id="CreateDoc"></a>Paso 6: Creación de documentos JSON
Un [documento](documentdb-resources.md#documents) puede crearse mediante el método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Ahora podemos insertar uno o varios documentos. Si ya dispone de datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](documentdb-import-data.md) de DocumentDB.

En primer lugar, es preciso crear una clase denominada **Family** que represente los objetos almacenados en DocumentDB en este ejemplo. También se crearan las subclases denominadas **Parent**, **Child**, **Pet** y **Address** que se utilizan dentro de **Family**. Tenga en cuenta que los documentos deben tener una propiedad de **Id** serializada como **id** en JSON. Para agregar estas clases, agregue las siguientes subclases internas después del método **GetStartedDemo**.

Copie y pegue las clases **Family**, **Parent**, **Child**, **Pet** y **Address** debajo del método **WriteToConsoleAndPromptToContinue**.

	private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
	{
		Console.WriteLine(format, args);
		Console.WriteLine("Press any key to continue ...");
		Console.ReadKey();
	}

	// ADD THIS PART TO YOUR CODE
	public class Family
	{
		[JsonProperty(PropertyName = "id")]
		public string Id { get; set; }
		public string LastName { get; set; }
		public Parent[] Parents { get; set; }
		public Child[] Children { get; set; }
		public Address Address { get; set; }
		public bool IsRegistered { get; set; }
		public override string ToString()
		{
				return JsonConvert.SerializeObject(this);
		}
	}

	public class Parent
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
	}

	public class Child
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
		public string Gender { get; set; }
		public int Grade { get; set; }
		public Pet[] Pets { get; set; }
	}

	public class Pet
	{
		public string GivenName { get; set; }
	}

	public class Address
	{
		public string State { get; set; }
		public string County { get; set; }
		public string City { get; set; }
	}

Copie y pegue el método **CreateFamilyDocumentIfNotExists** debajo del método **CreateDocumentCollectionIfNotExists**.

	// ADD THIS PART TO YOUR CODE
	private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
	{
		try
		{
			await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
			this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
		}
		catch (DocumentClientException de)
		{
			if (de.StatusCode == HttpStatusCode.NotFound)
			{
				await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
				this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
			}
			else
			{
				throw;
			}
		}
	}

E inserte dos documentos, uno para la familia Andersen y otro para la familia Wakefield.

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación de la colección de documentos.

	await this.CreateDatabaseIfNotExists("FamilyDB_va");

	await this.CreateDocumentCollectionIfNotExists("FamilyDB_va", "FamilyCollection_va");

	// ADD THIS PART TO YOUR CODE
	Family andersenFamily = new Family
	{
			Id = "Andersen.1",
			LastName = "Andersen",
			Parents = new Parent[]
			{
					new Parent { FirstName = "Thomas" },
					new Parent { FirstName = "Mary Kay" }
			},
			Children = new Child[]
			{
					new Child
					{
							FirstName = "Henriette Thaulow",
							Gender = "female",
							Grade = 5,
							Pets = new Pet[]
							{
									new Pet { GivenName = "Fluffy" }
							}
					}
			},
			Address = new Address { State = "WA", County = "King", City = "Seattle" },
			IsRegistered = true
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", andersenFamily);

	Family wakefieldFamily = new Family
	{
			Id = "Wakefield.7",
			LastName = "Wakefield",
			Parents = new Parent[]
			{
					new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
					new Parent { FamilyName = "Miller", FirstName = "Ben" }
			},
			Children = new Child[]
			{
					new Child
					{
							FamilyName = "Merriam",
							FirstName = "Jesse",
							Gender = "female",
							Grade = 8,
							Pets = new Pet[]
							{
									new Pet { GivenName = "Goofy" },
									new Pet { GivenName = "Shadow" }
							}
					},
					new Child
					{
							FamilyName = "Miller",
							FirstName = "Lisa",
							Gender = "female",
							Grade = 1
					}
			},
			Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
			IsRegistered = false
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha creado correctamente dos documentos de DocumentDB.

![Diagrama que muestra la relación jerárquica entre la cuenta, la base de datos en línea, la colección y los documentos usados por el tutorial NoSQL para crear una aplicación de consola de C#.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Paso 7: Consulta de recursos de DocumentDB

DocumentDB admite [consultas](documentdb-sql-query.md) enriquecidas contra los documentos JSON almacenados en cada colección. En el siguiente ejemplo se muestran diversas consultas (usando tanto la sintaxis SQL de Base de datos de documentos como LINQ) que podemos ejecutar con los documentos que hemos insertado en el paso anterior.

Copie y pegue el método **ExecuteSimpleQuery** debajo del método **CreateFamilyDocumentIfNotExists**.

	// ADD THIS PART TO YOUR CODE
	private void ExecuteSimpleQuery(string databaseName, string collectionName)
	{
		// Set some common query options
		FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

			// Here we find the Andersen family via its LastName
			IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
					UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
					.Where(f => f.LastName == "Andersen");

			// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
			Console.WriteLine("Running LINQ query...");
			foreach (Family family in familyQuery)
			{
					Console.WriteLine("\tRead {0}", family);
			}

			// Now execute the same query via direct SQL
			IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
					UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
					"SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
					queryOptions);

			Console.WriteLine("Running direct SQL query...");
			foreach (Family family in familyQueryInSql)
			{
					Console.WriteLine("\tRead {0}", family);
			}

			Console.WriteLine("Press any key to continue ...");
			Console.ReadKey();
	}

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación del segundo documento.

	await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

	// ADD THIS PART TO YOUR CODE
	this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha consultado correctamente en una colección de documentos de DocumentDB.

El siguiente diagrama muestra la denominación de la sintaxis de la consulta de SQL DocumentDB en la recopilación creada y se aplica la misma lógica también a la consulta LINQ.

![Diagrama que ilustra el ámbito y el significado de la consulta usada por el tutorial de NoSQL para crear una aplicación de consola de C#.](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

La palabra clave [FROM](documentdb-sql-query.md#from-clause) es opcional en la consulta porque las consultas de DocumentDB ya tienen como ámbito una sola colección. Por lo tanto, «FROM Families f" se puede intercambiar por "FROM root r", o cualquier otra variable de nombre que elija. DocumentDB deducirá la familia, la raíz o el nombre de variable elegido y hará referencia a la colección actual de forma predeterminada.

##<a id="ReplaceDocument"></a>Paso 8: Reemplazar un documento JSON

DocumentDB admite el reemplazo de documentos JSON.

Copie y pegue el método **ReplaceFamilyDocument** debajo del método **ExecuteSimpleQuery**.

	// ADD THIS PART TO YOUR CODE
	private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
	{
		try
		{
			await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
			this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
		}
		catch (DocumentClientException de)
		{
			throw;
		}
	}

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la ejecución de la consulta. Después de reemplazar el documento, este volverá a ejecutar la misma consulta para ver el documento modificado.

	await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

	this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

	// ADD THIS PART TO YOUR CODE
	// Update the Grade of the Andersen Family child
	andersenFamily.Children[0].Grade = 6;

	await this.ReplaceFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1", andersenFamily);

	this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha creado correctamente un documento de DocumentDB.

##<a id="DeleteDocument"></a>Paso 9: Eliminar documento JSON

DocumentDB admite la eliminación de documentos JSON.

Copie y pegue el método **DeleteFamilyDocument** debajo del método **ReplaceFamilyDocument**.

	// ADD THIS PART TO YOUR CODE
	private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
	{
		try
		{
			await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
			Console.WriteLine("Deleted Family {0}", documentName);
		}
		catch (DocumentClientException de)
		{
			throw;
		}
	}

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la ejecución de la segunda consulta.

	await this.ReplaceFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1", andersenFamily);

	this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

	// ADD THIS PART TO CODE
	await this.DeleteFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1");

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha eliminado correctamente un documento de DocumentDB.

##<a id="DeleteDatabase"></a>Paso 10: Eliminar la base de datos

La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.).

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la sección "delete" del documento para eliminar toda la base de datos y todos los recursos secundarios.

	this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

	await this.DeleteFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1");

	// ADD THIS PART TO CODE
	// Clean up/delete the database
	await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_va"));

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha eliminado correctamente una base de datos de DocumentDB.

##<a id="Run"></a>Paso 11: Ejecutar la aplicación de consola de C#

Presione F5 en Visual Studio para compilar la aplicación en modo de depuración.

Ahora debería ver la salida de la aplicación GetStarted. La salida mostrará los resultados de las consultas que hemos agregado y debe coincidir con el texto de ejemplo siguiente.

	Created FamilyDB_va
	Press any key to continue ...
	Created FamilyCollection_va
	Press any key to continue ...
	Created Family Andersen.1
	Press any key to continue ...
	Created Family Wakefield.7
	Press any key to continue ...
	Running LINQ query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Running direct SQL query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Replaced Family Andersen.1
	Press any key to continue ...
	Running LINQ query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Running direct SQL query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Deleted Family Andersen.1
	End of demo, press any key to exit.

¡Enhorabuena! Ha completado este tutorial de NoSQL y tiene una aplicación de consola de C# en funcionamiento.

## Pasos siguientes

- ¿Desea un tutorial de ASP.NET MVC NoSQL más complejo? Consulte [Creación de una aplicación web con ASP.NET MVC mediante DocumentDB](documentdb-dotnet-application.md).
- ¿Desea realizar pruebas de escala y rendimiento con DocumentDB? Consulte [Pruebas de escala y rendimiento con Azure DocumentDB](documentdb-performance-testing.md).
-	Aprenda a [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
-	Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
-	Obtenga más información sobre el modelo de programación en la sección sobre desarrollo de la [página de documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started-quickstart/nosql-tutorial-keys.png

<!---HONumber=AcomDC_0831_2016-->