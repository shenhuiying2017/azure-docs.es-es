<properties
	pageTitle="Introducción al SDK de .NET de DocumentDB | Microsoft Azure"
	description="Obtenga información acerca de cómo crear y configurar una cuenta de Azure DocumentDB, crear bases de datos, crear colecciones y almacenar documentos JSON en la cuenta de base de datos de documentos NoSQL."
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
	ms.date="09/03/2015"
	ms.author="anhoh"/>

#Introducción al SDK de .NET de DocumentDB  

En esta guía se muestra cómo empezar a usar [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) y el [SDK de .NET de DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/). Va a crear una aplicación de consola que crea y consulta recursos de DocumentDB y escribe el resultado en la ventana de la consola.

DocumentDB es un servicio de base de datos de documento NoSQL, que tiene [varias API y varios SDK disponibles](https://msdn.microsoft.com/library/dn781482.aspx). El código de este artículo está escrito en C# y usa el SDK de .NET de DocumentDB, que se empaqueta y distribuye como paquete de NuGet.

En este artículo se tratan los siguientes escenarios:

- Creación y aprovisionamiento una cuenta de DocumentDB.
- Adición de DocumentDB a la solución de Visual Studio
- Creación de bases de datos
- Creación de colecciones
- Creación de documentos JSON
- Consulta de recursos
- Eliminación de bases de datos

¿No tiene tiempo para completar el tutorial y solo desea obtener una solución que funcione? No se preocupe. La solución completa está disponible en [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Consulte [Obtención de la solución completa](#GetSolution) para obtener instrucciones rápidas.

Actualizamos y mejoramos este tema con frecuencia en función de los comentarios que recibimos. Una vez completado el tutorial, no olvide incluir sus comentarios sobre las mejoras que quiera que se hagan. Para ello, use los botones de voto de la parte superior e inferior de esta página. Si quiere que nos pongamos en contacto directamente con usted, puede incluir su dirección de correo electrónico en los comentarios.

## Requisitos previos

Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener lo siguiente:

- Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/), actualización 4 o superior.

## Paso 1: Creación de una cuenta de DocumentDB

Comenzaremos por crear una cuenta de DocumentDB. Si ya tiene una cuenta, puede ir a [Configuración de la solución de Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Paso 2: Configuración de la solución de Visual Studio

1. Abra **Visual Studio** en el equipo.
2. Seleccione **Nuevo** desde el menú **Archivo** y elija **Proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Plantillas**/**Visual C#**/ **Aplicación de consola**, ponga un nombre al proyecto y, a continuación, haga clic en **Agregar**.
4. En el **Explorador de soluciones**, haga clic con el botón secundario en la nueva aplicación de la consola, que se encuentra en la solución de Visual Studio.
5. A continuación, sin dejar el menú, haga clic en **Administrar paquetes de NuGet...**
6. En el panel de la izquierda de la ventana **Administrar paquetes de NuGet**, haga clic en **En línea** / **nuget.org**.
7. En el cuadro de entrada **Buscar en línea**, busque la **biblioteca de cliente de DocumentDB**.
8. En los resultados, busque la **biblioteca de cliente de Microsoft Azure DocumentDB** y haga clic en **Instalar**. El id. de paquete para la biblioteca de cliente de DocumentDB es [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

Estupendo. Ahora está listo para empezar a trabajar con DocumentDB.

##<a id="Connect"></a> Paso 3: Conexión a una cuenta de DocumentDB

Comenzaremos por crear una nueva instancia de la clase [DocumentClient](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.aspx) para establecer una conexión a nuestra cuenta de DocumentDB. Necesitaremos las siguientes referencias al principio de nuestra aplicación de C#:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

A continuación, se puede crear una instancia de un **DocumentClient** mediante el extremo de la cuenta de DocumentDB y la clave de acceso principal o secundaria asociada a la cuenta. Agregue estas propiedades a la clase.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

Ahora vamos a crear una nueva tarea asincrónica denominada **GetStartedDemo** en su clase. En esta nueva tarea, creará y configurará su **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient.
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Llame a la tarea asincrónica desde el método principal similar al código siguiente.

	public static void Main(string[] args)
    {
		try
    	{
        	GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

> [AZURE.WARNING]Nunca almacene credenciales en código fuente. Para que este ejemplo resulte sencillo, las credenciales se muestran en el código fuente. Consulte [Sitios web de Azure: funcionamiento de las cadenas de aplicación y de las cadenas de conexión](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) para obtener información sobre cómo almacenar credenciales en un entorno de producción. Eche un vistazo a nuestra aplicación de ejemplo de [GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs) para obtener un ejemplo sobre cómo almacenar las credenciales fuera del código fuente.

Los valores de EndpointUrl y AuthorizationKey son URI y CLAVE PRINCIPAL para la cuenta de DocumentDB, que se puede obtener de la hoja [Claves](https://portal.azure.com) para su cuenta de DocumentDB.

![Captura de pantalla del portal de vista previa de Azure, que muestra una cuenta de DocumentDB, con el concentrador ACTIVO resaltado, el botón CLAVES resaltado en la hoja de cuenta de DocumentDB y los valores URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la hoja Claves][keys]

Estas claves conceden acceso administrativo a su cuenta de DocumentDB y los recursos contenidos en ella. DocumentDB también admite el uso de claves de recursos que permiten a los clientes leer, escribir y eliminar recursos de la cuenta de DocumentDB en función de los permisos concedidos, sin necesidad de una clave de cuenta. Para obtener más información acerca de las claves de recursos, consulte [Permisos](documentdb-resources.md#permissions) y [Visualización, copia y regeneración de las claves de acceso](documentdb-manage-account.md#keys).

Ahora que sabe cómo conectarse a una cuenta de DocumentDB y crear una instancia de la clase **DocumentClient**, vamos a echar un vistazo a cómo trabajar con recursos de DocumentDB.

## Paso 4: Creación de una base de datos
Puede crearse una [base de datos](documentdb-resources.md#databases) mediante el método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos particionado en recopilaciones. Cree la base de datos en su método **GetStartedDemo** después de su creación **DocumentClient**.

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Paso 5: Creación de una colección  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** creará una nueva colección de S1, que tiene implicaciones de precios. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).

Una [colección](documentdb-resources.md#collections) puede crearse mediante el método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. La colección recién creada se asignará a un [nivel de rendimiento S1](documentdb-performance-levels.md). La base de datos creada en el paso anterior tiene una serie de propiedades, una de las cuales es [CollectionsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.database.collectionslink.aspx). Con esa información, ahora podemos crear una colección después de crear nuestra base de datos.

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });

##<a id="CreateDoc"></a>Paso 6: Creación de documentos
Un [documento](documentdb-resources.md#documents) puede crearse mediante el método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). La colección creada en el paso anterior tiene una serie de propiedades, una de las cuales es [DocumentsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.documentcollection.documentslink.aspx). Con esa información, puede insertar ahora uno o varios documentos. Si ya dispone de datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](documentdb-import-data.md) de DocumentDB.

En primer lugar, debemos crear una clase **principal**, **secundaria**, **mascota**, **dirección** y **familia**. Cree estas clases mediante la adición de las siguientes subclases internas.

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

A continuación, cree los documentos dentro de su método asincrónico **GetStartedDemo**.

    // Create the Andersen family document.
	Family AndersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[] {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, AndersenFamily);

    // Create the WakeField family document.
    Family WakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        Parents = new Parent[] {
            new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
            new Parent { FamilyName= "Miller", FirstName= "Ben" }
        },
        Children = new Child[] {
            new Child {
                FamilyName= "Merriam",
                FirstName= "Jesse",
                Gender= "female",
                Grade= 8,
                Pets= new Pet[] {
                    new Pet { GivenName= "Goofy" },
                    new Pet { GivenName= "Shadow" }
                }
            },
            new Child {
                FamilyName= "Miller",
                FirstName= "Lisa",
                Gender= "female",
                Grade= 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, WakefieldFamily);

Ahora ha creado la siguiente colección, documentos y bases de datos en la cuenta de DocumentDB.

![Diagrama que muestra la relación jerárquica entre la cuenta, la base de datos, la colección y los documentos](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>Paso 7: Consulta de recursos de DocumentDB

DocumentDB admite [consultas](documentdb-sql-query.md) enriquecidas contra los documentos JSON almacenados en cada colección. En el siguiente ejemplo se muestran diversas consultas (usando tanto la sintaxis SQL de Base de datos de documentos como LINQ) que podemos ejecutar con los documentos que hemos insertado en el paso anterior. Agregue estas consultas para su método asincrónico **GetStartedDemo**.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQL with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

El siguiente diagrama muestra la denominación de la sintaxis de la consulta de SQL DocumentDB en la recopilación creada y se aplica la misma lógica también a la consulta LINQ.

![Diagrama que ilustra el ámbito y el significado de la consulta](./media/documentdb-get-started/collection-documents.png)

La palabra clave [FROM](documentdb-sql-query.md/#from-clause) es opcional en la consulta porque las consultas de DocumentDB ya tienen como ámbito una sola colección. Por lo tanto, «FROM Families f" se puede intercambiar por "FROM root r", o cualquier otra variable de nombre que elija. DocumentDB deducirá la familia, raíz o nombre de variable elegido y hará referencia a la colección actual de forma predeterminada.

##<a id="DeleteDatabase"></a>Paso 8: Eliminación de la base de datos

La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.). Puede eliminar la base de datos y el cliente de documento agregando el siguiente fragmento de código al final del método asincrónico **GetStartedDemo**.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync(database.SelfLink);
	client.Dispose();

##<a id="Run"></a>Paso 9: Ejecución de la aplicación

Ahora ya puede ejecutar la aplicación. Al final de su método **principal**, agregue la siguiente línea de código, que le permitirá leer la salida de la consola antes de que la aplicación deje de ejecutarse.

	Console.ReadLine();

Presione F5 en Visual Studio para compilar la aplicación en modo de depuración.

Ahora debe ver el resultado de la aplicación iniciada get. La salida mostrará los resultados de las consultas que hemos agregado y debe coincidir con el texto de ejemplo siguiente.

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query
	{
	  "id": "AndersenFamily",
 	  "child": "Henriette Thaulow"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Jesse"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Lisa"
	}
	{ family = AndersenFamily, child = Henriette Thaulow }
	{ family = WakefieldFamily, child = Jesse }
	{ family = WakefieldFamily, child = Lisa }


> [AZURE.NOTE]Si ejecuta la aplicación varias veces sin quitar la base de datos, se puede encontrar el problema de crear una nueva base de datos con un identificador ya en uso. Para evitar esto, puede comprobar si ya existe una base de datos, colección o documento con el mismo identificador. Para obtener una referencia sobre cómo lograr esto, visite nuestro [página de GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started).

¡Enhorabuena! Ha creado su primera aplicación de DocumentDB.

##<a id="GetSolution"></a> Obtención de la solución completa
Para compilar la solución GetStarted que contiene todos los ejemplos de este artículo, necesitará lo siguiente:

-   [Cuenta de DocumentDB][documentdb-create-account].
-   La solución [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) está disponible en GitHub.

Para restaurar las referencias al SDK de .NET de DocumentDB de Visual Studio 2013, haga clic con el botón secundario en la solución **GetStarted** en el Explorador de soluciones y, a continuación haga clic en **Habilitar la restauración del paquete NuGet**. A continuación, en el archivo App.config, actualice los valores EndpointUrl y AuthorizationKey como se describe en [Conexión a una cuenta de DocumentDB](#Connect).

## Pasos siguientes

-   ¿Desea un ejemplo más complejo de ASP.NET MVC? Consulte [Creación de una aplicación web con ASP.NET MVC mediante DocumentDB](documentdb-dotnet-application.md).
-	Aprenda a [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
-	Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
-	Obtenga más información sobre el modelo de programación en la sección de desarrollo de la [página de documentación de DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=Sept15_HO2-->