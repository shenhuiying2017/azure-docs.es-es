<properties 
	pageTitle="Introducción al SDK de .NET de Base de datos de documentos | Azure" 
	description="Obtenga información acerca de cómo crear y configurar una cuenta de Base de datos de documentos de Azure, crear bases de datos, crear colecciones y almacenar documentos JSON en la cuenta de base de datos de documentos NoSQL." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="mimig"/>

# Introducción al SDK de .NET de Base de datos de documentos  

En esta guía se muestra cómo empezar a usar [Base de datos de documentos de Microsoft Azure (vista previa)](https://portal.azure.com/#gallery/Microsoft.DocumentDB) y el [SDK de .NET de Base de datos de documentos](http://go.microsoft.com/fwlink/p/?linkid=402989). Base de datos de documentos es un servicio de base de datos de documento NoSQL, [que tiene varias API y varios SDK disponibles](http://go.microsoft.com/fwlink/p/?linkid=522476). Los ejemplos de código de este artículo están escritos en C# y usan el SDK de .NET de Base de datos de documentos, que se empaqueta y distribuye como paquete de NuGet. 

Entre los escenarios descritos en este artículo se incluyen la creación y la configuración de una cuenta de Base de datos de documentos, la creación de bases de datos, la creación de colecciones y el almacenamiento de documentos JSON dentro de la cuenta. Cada uno de estos ejemplos forma parte de una solución completa disponible en [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Puede [descargar la solución](#GetSolution) para ver el código de ejemplo en contexto, o bien puede revisar simplemente los ejemplos de este artículo.

## <a id="Connect"></a>Conexión a una cuenta de Base de datos de documentos

Comenzaremos por crear una nueva instancia de la clase [DocumentClient](http://go.microsoft.com/fwlink/p/?linkid=522477) para establecer una conexión a nuestra cuenta de Base de datos de documentos.   Necesitaremos las siguientes referencias en nuestra aplicación de C#:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
A continuación, se puede crear una instancia de un **DocumentClient** mediante el extremo de la cuenta de Base de datos de documentos y la clave de acceso principal o secundaria asociada a la cuenta. Cree el cliente con un código similar al siguiente:   

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

> [AZURE.WARNING] Nunca almacene credenciales en el código fuente. Para que este ejemplo resulte sencillo, las credenciales se muestran en el código fuente. Consulte [Sitios Web Azure: Funcionamiento de las cadenas de aplicación y las cadenas de conexión](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) para obtener información sobre cómo almacenar credenciales en un entorno de producción. 

Los valores de EndpointUrl y AuthorizationKey son URI y PRIMARY KEY para la cuenta de Base de datos de documentos, que se puede obtener de la hoja [Portal de administración de vista previa de Azure](https://portal.azure.com) para su cuenta de Base de datos de documentos. 

![][1]
 
Estas claves conceden acceso administrativo a su cuenta de Base de datos de documentos y los recursos contenidos en ella. Base de datos de documentos también admite el uso de claves de recursos que permiten a los clientes leer, escribir y eliminar recursos de la cuenta de Base de datos de documentos en función de los permisos concedidos, sin necesidad de una clave de cuenta. Para obtener más información acerca de las claves de recursos, vea la sección Permisos de [Modelo de recursos y conceptos de Base de datos de documentos](../documentdb-resources/).

Ahora que sabe cómo conectarse a una cuenta de Base de datos de documentos y crear una instancia de la clase **DocumentClient**, vamos a echar un vistazo a cómo trabajar con recursos de Base de datos de documentos.  

## <a id="CreateDB"></a>Creación de una base de datos
Puede crearse una base de datos de Base de datos de documentos mediante el método [CreateDatabaseAsync](http://go.microsoft.com/fwlink/p/?linkid=522478) de la clase **DocumentClient**.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

## <a id="CreateColl"></a>Creación de una colección  

Una colección de Base de datos de documentos puede crearse mediante el método [CreateDocumentCollectionAsync](http://go.microsoft.com/fwlink/p/?linkid=522479) de la clase **DocumentClient**.  La base de datos creada en el paso anterior tiene una serie de propiedades, una de las cuales es [CollectionsLink](http://go.microsoft.com/fwlink/p/?linkid=522481).  Con esa información, ahora podemos crear una colección.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
## <a id="CreateDoc"></a>Creación de documentos	
Un documento de Base de datos de documentos puede crearse mediante el método [CreateDocumentAsync](http://go.microsoft.com/fwlink/p/?linkid=522482) de la clase **DocumentClient**.  La colección creada en el paso anterior tiene una serie de propiedades, una de las cuales es [DocumentsLink](http://go.microsoft.com/fwlink/p/?linkid=522483).  Con esa información, puede insertar ahora uno o varios documentos.  En este ejemplo, vamos a suponer que tenemos una clase Family que describe los atributos de una familia como nombre, género y edad.  

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
 

## <a id="Query"></a>Consulta de recursos de Base de datos de documentos
Base de datos de documentos admite consultas enriquecidas contra los documentos JSON almacenados en cada colección.  En el siguiente ejemplo se muestran diversas consultas (usando tanto la sintaxis SQL de Base de datos de documentos como LINQ) que podemos ejecutar con los documentos que hemos insertado en el paso anterior.  

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
	
## <a id="GetSolution"></a>Obtención de la solución completa
Para compilar la solución GetStarted que contiene todos los ejemplos de este artículo, necesitará lo siguiente:

-   [Cuenta de Base de datos de documentos][documentdb-create-account].
-   La solución [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) está disponible en GitHub. 

Para restaurar las referencias al SDK de .NET de Base de datos de documentos de Visual Studio 2013, haga clic con el botón secundario en la solución GetStarted en el Explorador de soluciones y, a continuación haga clic en Habilitar la restauración del paquete NuGet, que restaurará las referencias. A continuación, en el archivo App.config, actualice los valores EndpointUrl y AuthorizationKey como se describe en [Conexión a una cuenta de Base de datos de documentos](#Connect). 

## <a id="NextSteps"></a>Pasos siguientes
-	Aprenda a [supervisar una cuenta de Base de datos de documentos](http://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Para obtener detalles sobre el modelo de programación, vea la sección Desarrollar en la [página de documentación de Base de datos de documentos](http://go.microsoft.com/fwlink/p/?LinkID=402319).


[Conexión a una cuenta de Base de datos de documentos]: #Connect
[Creación de una base de datos]: #CreateDB
[Creación de una colección]: #CreateColl
[Creación de documentos]: #CreateDoc
[Consulta de recursos de Base de datos de documentos]: #Query
[Pasos siguientes]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png

<!--HONumber=47-->
