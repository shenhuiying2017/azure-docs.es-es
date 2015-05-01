<properties 
	pageTitle="Introducción al SDK de .NET de DocumentDB | Azure" 
	description="Obtenga información acerca de cómo crear y configurar una cuenta de Microsoft Azure DocumentDB, crear bases de datos, crear colecciones y almacenar documentos JSON en la cuenta de DocumentDB NoSQL." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="anhoh"/>

#Introducción al SDK de .NET de DocumentDB  

En esta guía se muestra cómo empezar a usar [DocumentDB de Microsoft Azure (vista previa)](https://portal.azure.com/#gallery/Microsoft.DocumentDB) y el [SDK de .NET de DocumentDB](https://go.microsoft.com/fwlink/p/?linkid=402989). DocumentDB es un servicio de base de datos de documento NoSQL, [que tiene varias API y varios SDK disponibles](https://go.microsoft.com/fwlink/p/?linkid=522476). Los ejemplos de código de este artículo están escritos en C# y usan el SDK de .NET de DocumentDB, que se empaqueta y distribuye como paquete de NuGet. 

Entre los escenarios descritos en este artículo se incluyen la creación y la configuración de una cuenta de DocumentDB, la creación de bases de datos, la creación de colecciones y el almacenamiento de documentos JSON dentro de la cuenta. Cada uno de estos ejemplos forma parte de una solución completa disponible en [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). También puede [descargar la solución](#GetSolution) para ver el código de ejemplo en contexto, o bien puede revisar los ejemplos de este artículo.

##<a id="CreateAccount"></a>Creación de una cuenta de DocumentDB

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

##<a id="Connect"></a>Conexión a una cuenta de DocumentDB

Comenzaremos por crear una nueva instancia de la clase [DocumentClient](https://go.microsoft.com/fwlink/p/?linkid=522477) para establecer una conexión a nuestra cuenta de DocumentDB. Necesitaremos las siguientes referencias en nuestra aplicación de C#:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
A continuación, se puede crear una instancia de un **DocumentClient** mediante el extremo de la cuenta de DocumentDB y la clave de acceso principal o secundaria asociada a la cuenta. Cree el cliente con un código similar al siguiente:   

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

> [AZURE.WARNING] Nunca almacene credenciales en código fuente. Para que este ejemplo resulte sencillo, las credenciales se muestran en el código fuente. Consulte [Sitios Web Azure: Funcionamiento de las cadenas de aplicación y las cadenas de conexión](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) para obtener información sobre cómo almacenar credenciales en un entorno de producción. 

Los valores de EndpointUrl y AuthorizationKey son URI y PRIMARY KEY para la cuenta de DocumentDB, que se puede obtener de la hoja [Portal de administración de vista previa de Azure](https://portal.azure.com) para su cuenta de DocumentDB. 

![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the KEYS button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade][1]
 
Estas claves conceden acceso administrativo a su cuenta de DocumentDB y los recursos contenidos en ella. DocumentDB también admite el uso de claves de recursos que permiten a los clientes leer, escribir y eliminar recursos de la cuenta de DocumentDB en función de los permisos concedidos, sin necesidad de una clave de cuenta. Para obtener más información acerca de las claves de recursos, vea la sección Permisos de [Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md).

Ahora que sabe cómo conectarse a una cuenta de DocumentDB y crear una instancia de la clase **DocumentClient**, vamos a echar un vistazo a cómo trabajar con recursos de DocumentDB.  

##<a id="CreateDB"></a>Creación de una base de datos
Puede crearse una base de datos mediante el método [CreateDatabaseAsync](https://go.microsoft.com/fwlink/p/?linkid=522478) de la clase **DocumentClient**.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Creación de una colección  

Una colección puede crearse mediante el método [CreateDocumentCollectionAsync](https://go.microsoft.com/fwlink/p/?linkid=522479) de la clase **DocumentClient**.  La base de datos creada en el paso anterior tiene una serie de propiedades, una de las cuales es [CollectionsLink](https://go.microsoft.com/fwlink/p/?linkid=522481).  Con esa información, ahora podemos crear una colección.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Creación de documentos	
Un documento puede crearse mediante el método [CreateDocumentAsync](https://go.microsoft.com/fwlink/p/?linkid=522482) de la clase **DocumentClient**.  La colección creada en el paso anterior tiene una serie de propiedades, una de las cuales es [DocumentsLink](https://go.microsoft.com/fwlink/p/?linkid=522483).  Con esa información, puede insertar ahora uno o varios documentos.  En este ejemplo, vamos a suponer que tenemos una clase Family que describe los atributos de una familia como nombre, género y edad. Si desea ver nuestra clase de familia de ejemplo, visite nuestro [repositorio de GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs). 

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
 

##<a id="Query"></a>Consulta de recursos de DocumentDB

DocumentDB admite consultas enriquecidas contra los documentos JSON almacenados en cada colección.  En el siguiente ejemplo se muestran diversas consultas (usando tanto la sintaxis SQL de DocumentDB como LINQ) que podemos ejecutar con los documentos que hemos insertado en el paso anterior. 

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

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
	
##<a id="GetSolution"></a>Obtención de la solución completa
Para compilar la solución GetStarted que contiene todos los ejemplos de este artículo, necesitará lo siguiente:

-   [Cuenta de la DocumentDB][documentdb-create-account]
-   La solución [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) está disponible en GitHub. 

Para restaurar las referencias al SDK de .NET de DocumentDB de Visual Studio 2013, haga clic con el botón secundario en la solución GetStarted en el Explorador de soluciones y, a continuación haga clic en Habilitar la restauración del paquete NuGet. A continuación, en el archivo App.config, actualice los valores EndpointUrl y AuthorizationKey como se describe en [Conexión a una cuenta de DocumentDB](#Connect). 

##<a id="NextSteps"></a>Pasos siguientes
-	Aprenda a [supervisar una cuenta de DocumentDB](https://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](http://www.documentdb.com/sql/demo).
-	Obtenga más información sobre el modelo de programación en la sección de desarrollo de la [página de documentación de DocumentDB](../documentation/services/documentdb/).


[Conexión a una cuenta de DocumentDB]: #Connect
[Creación de una base de datos]: #CreateDB
[Creación de una colección]: #CreateColl
[Creación de documentos]: #CreateDoc
[Consulta de recursos de DocumentDB]: #Query
[Pasos siguientes]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[1]: ../includes/media/documentdb-keys/keys.png


<!--HONumber=49-->