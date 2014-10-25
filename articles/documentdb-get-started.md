<properties title="Get started with a DocumentDB account" pageTitle="Get started with a DocumentDB account | Azure" description="Learn how to create and configure an Azure DocumentDB account, create databases, create collections, and store JSON documents within the account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="paulettm" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Primeros pasos con una cuenta de Base de datos de documentos

En esta guía se explica cómo comenzar a usar **Base de datos de documentos de Azure (vista previa)**. Los ejemplos están escritos en código C\# y usan el SDK para .NET de Base de datos de documentos. Los escenarios que aquí se describen incluyen la creación y configuración de una cuenta de Base de datos de documentos, la creación de bases de datos, la creación de colecciones y el almacenamiento de documentos JSON dentro de la cuenta. Para obtener más información acerca del uso de Base de documentos de Azure, consulte la sección Pasos siguientes.

Para usar esta guía de inicio, debe tener una cuenta de Base de datos de documentos y la clave de acceso (primaria o secundaria) de la cuenta. Para obtener más información, consulte:

-   [Creación de una cuenta de Base de datos de documentos][]

## Tabla de contenido

-   [Conexión a una cuenta de Base de datos de documentos][]
-   [Creación de una base de datos][]
-   [Creación de una colección][]
-   [Creación de documentos][]
-   [Consulta de recursos de Base de datos de documentos][]
-   [Pasos siguientes][]

## <span id="Connect"></span></a>Conexión a una cuenta de Base de datos de documentos

Existen varios SDK y API para programar con Base de datos de documentos. Los siguientes ejemplos están escritos en código C\# y usan el SDK para .NET de Base de datos de documentos.

Lo primero que haremos será crear un DocumentClient con la finalidad de establecer una conexión a nuestra cuenta de Base de datos de documentos. Necesitaremos las siguientes referencias en nuestra aplicación de C\#:

    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Microsoft.Azure.Documents;  

Se puede crear una instancia de un DocumentClient mediante el extremo de la cuenta de Base de datos de documentos y la clave de acceso principal o secundaria asociada a la cuenta.

El extremo y las claves se pueden obtener del cuadro del Portal de vista previa de administración de Azure para su cuenta de Base de datos de documentos.

![][]

> Tenga en cuenta que las claves de acceso de Base de datos de documentos disponibles en el cuadro Claves otorgan acceso administrativo a su cuenta de Base de datos de documentos y a los recursos que contiene. Base de datos de documentos admite también el uso de claves de recursos que permiten a los clientes leer, escribir y eliminar recursos de la cuenta de acuerdo con los permisos otorgados, sin necesidad de una clave de cuenta.

Para crear el cliente, use código similar al del ejemplo siguiente.

    private static string endpointUrl = "<your endpoint URI>";
    private static string authorizationKey = "<your key>";

    //Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(endpointUrl), authorizationKey);  

**Advertencia:** nunca almacene credenciales en código fuente. Para que este ejemplo resulte sencillo, se muestra en código fuente. Consulte [Sitios web de Windows Azure: como funcionan las cadenas de aplicación y las cadenas de conexión][] para obtener información sobre cómo almacenar credenciales.

Ahora que sabe cómo conectarse a una cuenta de Base de datos de documentos y crear una instancia de DocumentClient, vamos a echar un vistazo a cómo trabajar con recursos de Base de datos de documentos.

## <span id="CreateDB"></span></a>Creación de una base de datos

Con el SDK para .NET, puede crear una base de datos de Base de datos de documentos mediante el método CreateDatabaseAsync de un DocumentClient.

    //Create a Database
     Database database = await client.CreateDatabaseAsync(
        new Database
        {
        Id = "FamilyRegistry"
        });

## <span id="CreateColl"></span></a>Creación de una colección

Con el SDK para .NET, puede crear una colección de Base de datos de documentos mediante el método CreateDocumentCollectionAsync de un DocumentClient. La base de datos creada en el paso anterior tiene una serie de propiedades, una de las cuales es SelfLink. Con esa información, ahora podemos crear una colección.

        //Create a document collection 
    documentCollection = new DocumentCollection
        {
            Id = "FamilyCollection"
        };

        documentCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,documentCollection); 

## <span id="CreateDoc"></span></a>Creación de documentos

Con el SDK para .NET, puede crear un documento de Base de datos de documentos mediante el método CreateDocumentAsync de un DocumentClient. La colección creada en el paso anterior tiene una serie de propiedades, una de las cuales es DocumentsLink. Con esa información, puede insertar ahora uno o varios documentos. En este ejemplo, vamos a suponer que tenemos una clase Family que describe los atributos de una familia como nombre, género y edad.

    private static async Task CreateDocuments(string    colSelfLink)
    {
        Family AndersonFamily = new Family
        {
            Id = "AndersenFamily",
            LastName = "Andersen",
            Parents =  new Parent[] {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay"}
            },
            Children = new Child[] {
                new Child
                { 
                    FirstName = "Henriette Thaulow", 
                    Gender = "female", 
                    Grade = 5, 
                    Pets = new [] {
                        new Pet { GivenName = "Fluffy" } 
                    }
                } 
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
        };

        await client.CreateDocumentAsync(colSelfLink, AndersonFamily);

        Family WakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new [] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child
                {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child
                {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        await client.CreateDocumentAsync(colSelfLink, WakefieldFamily);



## <span id="Query"></span></a>Consulta de recursos de Base de datos de documentos

Base de datos de documentos admite consultas enriquecidas contra los documentos JSON almacenados en cada colección. El código de ejemplo siguiente muestra varias consultas, usando sintaxis SQL de Base de datos de documentos y LINQ, que se pueden ejecutar contra los documentos que hemos insertado en el paso anterior.

    //
    //Querying the documents using DocumentDB SQL for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink, 
    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
    Console.WriteLine("\tRead {0} from SQL", family);
    }

    //
    //Querying the documents using LINQ for the Andersen family
    //
    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
     Console.WriteLine("\tRead {0} from LINQ", family);
    }

    //
    //Querying the documents using LINQ lambdas for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink)
    .Where(f => f.Id == "AndersenFamily")
    .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    //
    //DocumentDB SQL -  using <> interchangably with != for "not equals"
    //
    families = client.CreateDocumentQuery<Family>(colSelfLink, "SELECT * FROM Families f WHERE f.id <> 'AndersenFamily'");

    //   
    // LINQ - combine equality and inequality
    //
    families = from f in client.CreateDocumentQuery<Family>(colSelfLink)
           where f.Id == "Wakefield" && f.Address.City != "NY"
           select f; 

## <span id="NextSteps"></span></a>Pasos siguientes

-   Aprenda a [supervisar una cuenta de Base de datos de documentos][].
-   Para obtener detalles sobre el modelo de programación, consulte la sección Desarrollar en la [página de documentación de Base de datos de documentos][].

  [Creación de una cuenta de Base de datos de documentos]: ../documentdb-create-account/
  [Conexión a una cuenta de Base de datos de documentos]: #Connect
  [Creación de una base de datos]: #CreateDB
  [Creación de una colección]: #CreateColl
  [Creación de documentos]: #CreateDoc
  [Consulta de recursos de Base de datos de documentos]: #Query
  [Pasos siguientes]: #NextSteps
  []: ./media/documentdb-get-started/gs1.png
  [Sitios web de Windows Azure: como funcionan las cadenas de aplicación y las cadenas de conexión]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [supervisar una cuenta de Base de datos de documentos]: http://go.microsoft.com/fwlink/p/?LinkId=402378
  [página de documentación de Base de datos de documentos]: http://go.microsoft.com/fwlink/p/?LinkID=402319
