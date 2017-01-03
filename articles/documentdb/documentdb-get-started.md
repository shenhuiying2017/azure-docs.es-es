---
title: 'Tutorial de NoSQL: SDK de .NET para DocumentDB | Microsoft Docs'
description: "Tutorial de NoSQL que crea una base de datos en línea y la aplicación de consola de C# mediante el SDK de DocumentDB para .NET. DocumentDB es una base de datos NoSQL para JSON."
keywords: "tutorial de nosql, base de datos en línea, aplicación de consola de c#"
services: documentdb
documentationcenter: .net
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/16/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 414c41cbccb755e058fb76f10bba62bd1ffa1835
ms.openlocfilehash: d2989a3292117ada029fb26536bfffbce83f5e1b


---
# <a name="nosql-tutorial-build-a-documentdb-c-console-application"></a>Tutorial de NoSQL: Crear una aplicación de consola de C# DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bienvenido al tutorial de NoSQL para el SDK de Azure DocumentDB para .NET. Después de seguir este tutorial, tendrá una aplicación de consola que crea recursos de DocumentDB y realiza consultas en ellos.

Describiremos:

* Creación y aprovisionamiento una cuenta de DocumentDB.
* Configuración de la solución de Visual Studio
* Creación de una base de datos en línea
* Creación de una colección
* Creación de documentos JSON
* Consulta de la colección
* Sustitución de un documento
* Eliminación de un documento
* Eliminación de la base de datos

¿No tiene tiempo? ¡No se preocupe! La solución completa está disponible en [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Para obtener instrucciones rápidas, diríjase a la [sección Obtener la solución completa del tutorial de NoSQL](#GetSolution).

Después, utilice los botones de votación situados en la parte superior o inferior de esta página para proporcionarnos sus comentarios. Si quiere que nos pongamos en contacto directamente con usted, puede incluir su dirección de correo electrónico en los comentarios.

Comencemos.

## <a name="prerequisites"></a>Requisitos previos
Asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/). 
    * Como alternativa, puede usar el [Emulador de Azure DocumentDB](documentdb-nosql-local-emulator.md) en este tutorial.
* [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).
* .NET Framework 4.6

## <a name="step-1-create-a-documentdb-account"></a>Paso 1: Creación de una cuenta de DocumentDB
Creemos una cuenta de DocumentDB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la solución de Visual Studio](#SetupVS). Si está usando el Emulador de DocumentDB, siga los pasos que se indican en [Emulador de Azure DocumentDB](documentdb-nosql-local-emulator.md) para configurar el emulador e ir directamente a la [instalación de su solución de Visual Studio](#SetupVS).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idsetupvsastep-2-setup-your-visual-studio-solution"></a><a id="SetupVS"></a>Paso 2: Configuración de la solución de Visual Studio
1. Abra **Visual Studio 2015** en el equipo.
2. En el menú **Archivo**, seleccione **Nuevo** y elija **Proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Plantillas** / **Visual C#** / **Aplicación de consola**, asigne un nombre al proyecto y haga clic en **Aceptar**.
   ![Captura de pantalla de la ventana Nuevo proyecto](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. En el **Explorador de soluciones**, haga clic con el botón derecho en la nueva aplicación de la consola, que se encuentra en la solución de Visual Studio y, a continuación, haga clic en **Administrar paquetes NuGet...**
    
    ![Captura de pantalla del menú contextual del proyecto](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
5. En la pestaña **Nuget** haga clic en **Examinar** y escriba **azure documentdb** en el cuadro de búsqueda.
6. En los resultados, busque **Microsoft.Azure.DocumentDB** y haga clic en **Instalar**.
   El identificador del paquete de DocumentDB Client Library es [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Captura de pantalla del menú NuGet para encontrar el SDK de cliente de DocumentDB](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

    Si recibe un mensaje acerca de la revisión de los cambios de la solución, haga clic en **Aceptar**. Si recibe un mensaje acerca de la aceptación de licencia, haga clic en **Acepto**.

Estupendo. Ahora que hemos terminado la configuración, comencemos a escribir algo de código. Puede buscar un proyecto con código completo de este tutorial en [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a name="a-idconnectastep-3-connect-to-a-documentdb-account"></a><a id="Connect"></a>Paso 3: Conexión a una cuenta de DocumentDB
En primer lugar, agregue estas referencias al principio de la aplicación de C#, en el archivo Program.cs:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> Para poder completar este tutorial de NoSQL, asegúrese de que agregar las dependencias anteriores.
> 
> 

Ahora, agregue estas dos constantes y la variable *client* debajo de la clase pública *Program*.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUri = "<your endpoint URI>";
        private const string PrimaryKey = "<your key>";
        private DocumentClient client;

Seguidamente, vuelva a [Azure Portal](https://portal.azure.com) para recuperar el identificador URI y la clave principal. El identificador URI y la clave principal de DocumentDB son necesarios para que la aplicación sepa a dónde debe conectarse y para que DocumentDB confíe en la conexión de la aplicación.

En el Portal de Azure, navegue a la cuenta de DocumentDB y, después, haga clic en **Claves**.

Copie el URI desde el Portal y péguelo en `<your endpoint URI>` en el archivo program.cs. Después, copie la CLAVE PRINCIPAL del Portal y péguela en `<your key>`.

![Captura de pantalla del Portal de Azure usada por el tutorial de NoSQL para crear una aplicación de consola de C#. Muestra una cuenta de DocumentDB, con el concentrador ACTIVO resaltado, el botón CLAVES resaltado en la hoja de cuenta de DocumentDB y los valores URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la hoja Claves.][keys]

A continuación, se iniciará la aplicación, para lo que crearemos una nueva instancia de **DocumentClient**.

Debajo del método **Main**, agregue esta nueva tarea asincrónica denominada **GetStartedDemo**, que creará una instancia del nuevo **DocumentClient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
    }

Agregue el siguiente código para ejecutar la tarea asincrónica desde el método **Main** . El método **Main** detectará las excepciones y las escribirá en la consola.

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

Presione **F5** para ejecutar la aplicación. La salida de la ventana de consola muestra el mensaje `End of demo, press any key to exit.` que confirma que se realizó la conexión.  A continuación, puede cerrar la ventana de consola. 

¡Enhorabuena! Se ha conectado correctamente a una cuenta de DocumentDB, y ahora se explicará cómo trabajar con recursos de DocumentDB.  

## <a name="step-4-create-a-database"></a>Paso 4: Creación de una base de datos
Antes de agregar el código para crear una base de datos, agregue un método auxiliar para escribirlo en la consola.

Copie y pegue el método **WriteToConsoleAndPromptToContinue** después del método **GetStartedDemo**.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Puede crearse una [base de datos](documentdb-resources.md#databases) de DocumentDB mediante el método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos JSON particionado en recopilaciones.

Copie y pegue el método **CreateDatabaseIfNotExists** después del método **WriteToConsoleAndPromptToContinue**.

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

Copie y pegue el código siguiente en el método **GetStartedDemo** después de la creación del cliente. Así se creará una base de datos denominada *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha creado correctamente una base de datos de DocumentDB.  

## <a name="a-idcreatecollastep-5-create-a-collection"></a><a id="CreateColl"></a>Paso 5: Creación de una colección
> [!WARNING]
> **CreateDocumentCollectionAsync** creará una nueva colección con un rendimiento reservado, lo que tiene implicaciones en los precios. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Una [colección](documentdb-resources.md#collections) puede crearse mediante el método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript.

Copie y pegue el método **CreateDocumentCollectionIfNotExists** después del método **CreateDatabaseIfNotExists**.

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

Copie y pegue el código siguiente en el método **GetStartedDemo** después de la creación de la base de datos. Esto creará una colección de documentos denominada *FamilyCollection_oa*.

        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

        // ADD THIS PART TO YOUR CODE
        await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha creado correctamente una colección de documentos de DocumentDB.  

## <a name="a-idcreatedocastep-6-create-json-documents"></a><a id="CreateDoc"></a>Paso 6: Creación de documentos JSON
Un [documento](documentdb-resources.md#documents) puede crearse mediante el método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Ahora podemos insertar uno o varios documentos. Si ya dispone de datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](documentdb-import-data.md)de DocumentDB para importar los datos en una base de datos.

En primer lugar, es preciso crear una clase denominada **Family** que represente los objetos almacenados en DocumentDB en este ejemplo. También se crearán las subclases **Parent**, **Child**, **Pet**, **Address** que se usan dentro de **Family**. Tenga en cuenta que los documentos deben tener una propiedad **Id** serializada como **id** en JSON. Para agregar estas clases, agregue las siguientes subclases internas después del método **GetStartedDemo** .

Copie y pegue las clases **Family**, **Parent**, **Child**, **Pet** y **Address** después del método **WriteToConsoleAndPromptToContinue**.

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

Copie y pegue el método **CreateFamilyDocumentIfNotExists** después del método **CreateDocumentCollectionIfNotExists**.

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

Copie y pegue el código siguiente en el método **GetStartedDemo** después de la creación de la colección de documentos.

    await this.CreateDatabaseIfNotExists("FamilyDB_oa");

    await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha creado correctamente dos documentos de DocumentDB.  

![Diagrama que muestra la relación jerárquica entre la cuenta, la base de datos en línea, la colección y los documentos usados por el tutorial NoSQL para crear una aplicación de consola de C#.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a name="a-idqueryastep-7-query-documentdb-resources"></a><a id="Query"></a>Paso 7: Consulta de recursos de DocumentDB
DocumentDB admite [consultas](documentdb-sql-query.md) enriquecidas contra los documentos JSON almacenados en cada colección.  En el siguiente ejemplo se muestran diversas consultas (usando tanto la sintaxis SQL de Base de datos de documentos como LINQ) que podemos ejecutar con los documentos que hemos insertado en el paso anterior.

Copie y pegue el método **ExecuteSimpleQuery** después del método **CreateFamilyDocumentIfNotExists**.

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

Copie y pegue el código siguiente en el método **GetStartedDemo** después de la creación del segundo documento.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

Antes de ejecutar la aplicación, en el menú **Proyecto**, haga clic en *Nombre del proyecto* **Propiedades...** y, a continuación, haga clic en **Generar**. Desactive la casilla **Preferencia de 32 bits**, ya que no se puede extraer información de enrutamiento de partición de la consulta si esta se ejecuta en un proceso de 32 bits.

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha consultado correctamente en una colección de documentos de DocumentDB.

El siguiente diagrama muestra la denominación de la sintaxis de la consulta de SQL DocumentDB en la recopilación creada y se aplica la misma lógica también a la consulta LINQ.

![Diagrama que ilustra el ámbito y el significado de la consulta usada por el tutorial de NoSQL para crear una aplicación de consola de C#.](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

La palabra clave [FROM](documentdb-sql-query.md#from-clause) es opcional en la consulta porque las consultas de DocumentDB ya tienen como ámbito una sola colección. Por lo tanto, «FROM Families f" se puede intercambiar por  "FROM root r", o cualquier otra variable de nombre que elija. DocumentDB deducirá la familia, la raíz o el nombre de variable elegido y hará referencia a la colección actual de forma predeterminada.

## <a name="a-idreplacedocumentastep-8-replace-json-document"></a><a id="ReplaceDocument"></a>Paso 8: Reemplazar un documento JSON
DocumentDB admite el reemplazo de documentos JSON.  

Copie y pegue el método **ReplaceFamilyDocument** después del método **ExecuteSimpleQuery**.

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

Copie y pegue el código siguiente en el método **GetStartedDemo** después de la ejecución de la consulta y al final del método. Después de reemplazar el documento, este volverá a ejecutar la misma consulta para ver el documento modificado.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha creado correctamente un documento de DocumentDB.

## <a name="a-iddeletedocumentastep-9-delete-json-document"></a><a id="DeleteDocument"></a>Paso 9: Eliminar documento JSON
DocumentDB admite la eliminación de documentos JSON.  

Copie y pegue el método **DeleteFamilyDocument** después del método **ReplaceFamilyDocument**.

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

Copie y pegue el código siguiente en el método **GetStartedDemo** después de la ejecución de la segunda consulta y al final del método.

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha eliminado correctamente un documento de DocumentDB.

## <a name="a-iddeletedatabaseastep-10-delete-the-database"></a><a id="DeleteDatabase"></a>Paso 10: Eliminar la base de datos
La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.).

Copie y pegue el código siguiente en el método **GetStartedDemo** después de la sección "delete" del documento para eliminar toda la base de datos y todos los recursos secundarios.

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));

Presione **F5** para ejecutar la aplicación.

¡Enhorabuena! Ha eliminado correctamente una base de datos de DocumentDB.

## <a name="a-idrunastep-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Paso 11: Ejecutar la aplicación de consola de C#
Presione F5 en Visual Studio para compilar la aplicación en modo de depuración.

Ahora debería ver la salida de la aplicación GetStarted. La salida mostrará los resultados de las consultas que hemos agregado y debe coincidir con el texto de ejemplo siguiente.

    Created FamilyDB_oa
    Press any key to continue ...
    Created FamilyCollection_oa
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

## <a name="a-idgetsolutiona-get-the-complete-nosql-tutorial-solution"></a><a id="GetSolution"></a> Obtener la solución completa del tutorial de NoSQL
Si no dispuso de tiempo para completar los pasos de este tutorial, o simplemente desea descargar los ejemplos de código, puede obtenerlos de [Github](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Para compilar la solución GetStarted, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una [cuenta de DocumentDB][documentdb-create-account].
* La solución [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) está disponible en GitHub.

Para restaurar las referencias al SDK de DocumentDB para .NET en Visual Studio, haga clic con el botón derecho en la solución **GetStarted** en el Explorador de soluciones y, después, haga clic en **Habilitar la restauración del paquete NuGet**. A continuación, en el archivo App.config, actualice los valores EndpointUrl y AuthorizationKey como se describe en [Conexión a una cuenta de DocumentDB](#Connect).

Antes de ejecutar la aplicación, en el menú **Proyecto**, haga clic en *Nombre del proyecto* **Propiedades...** y, a continuación, haga clic en **Generar**. Desactive la casilla **Preferencia de 32 bits**.

Y, eso es todo, genérela y habrá terminado.


## <a name="next-steps"></a>Pasos siguientes
* ¿Desea un tutorial de ASP.NET MVC NoSQL más complejo? Consulte [Creación de una aplicación web con ASP.NET MVC mediante DocumentDB](documentdb-dotnet-application.md).
* ¿Desea realizar pruebas de escala y rendimiento con DocumentDB? Consulte [Pruebas de escala y rendimiento con Azure DocumentDB](documentdb-performance-testing.md)
* Aprenda a [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
* Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
* Obtenga más información sobre el modelo de programación en la sección sobre desarrollo de la [página de documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png



<!--HONumber=Dec16_HO3-->


