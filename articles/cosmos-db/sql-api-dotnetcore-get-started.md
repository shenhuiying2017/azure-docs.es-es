---
title: "Azure Cosmos DB: tutorial de introducción a las API de SQL con .NET Core | Microsoft Docs"
description: "Tutorial en el que crea una base de datos en línea y la aplicación de consola de C# mediante el SDK de .NET Core de la API de SQL de Azure Cosmos DB."
services: cosmos-db
documentationcenter: .net
author: arramac
manager: jhubbard
editor: 
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2017
ms.author: arramac
ms.custom: devcenter
ms.openlocfilehash: 0b19071bf871029b488b26d3f125d08d7d2a2dd4
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-cosmos-db-getting-started-with-the-sql-api-and-net-core"></a>Azure Cosmos DB: introducción a la API de SQL y .NET Core
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Le damos la bienvenida al tutorial de introducción de la API de SQL para Azure Cosmos DB con .NET Core. Después de seguir este tutorial, tendrá una aplicación de consola que crea recursos de Azure Cosmos DB y los consulta.

Esta tutorial abarca lo siguiente:

* Creación de una cuenta de Azure Cosmos DB y conexión a ella
* Configuración de la solución de Visual Studio
* Creación de una base de datos en línea
* Creación de una colección
* Creación de documentos JSON
* Consulta de la colección
* Sustitución de un documento
* Eliminación de un documento
* Eliminación de la base de datos

¿No tiene tiempo? ¡No se preocupe! La solución completa está disponible en [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). Para obtener instrucciones rápidas, diríjase a la sección [Obtener la solución completa del tutorial de NoSQL](#GetSolution) .

¿Desea compilar una aplicación de Xamarin iOS, Android o Forms mediante la API de SQL y el SDK de .NET Core? Consulte [Creación de aplicaciones móviles con Xamarin y Azure Cosmos DB](mobile-apps-with-xamarin.md).

> [!NOTE]
> El SDK de .NET Core de Azure Cosmos DB que se usa en este tutorial no es aún compatible con aplicaciones de la Plataforma universal de Windows (UWP). Para obtener una versión preliminar del SDK de .NET Core que admita aplicaciones de UWP, envíe un correo electrónico a [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

Comencemos.

## <a name="prerequisites"></a>requisitos previos

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)] 
    * Si está trabajando en MacOS o Linux, puede desarrollar aplicaciones de .NET Core desde la línea de comandos mediante la instalación del [SDK de .NET Core](https://www.microsoft.com/net/core#macos) para la plataforma que elija. 
    * Si está trabajando en Windows, puede desarrollar aplicaciones de .NET Core desde la línea de comandos mediante la instalación del [SDK de .NET Core](https://www.microsoft.com/net/core#windows). 
    * Puede usar su propio editor o descargar [Visual Studio Code](https://code.visualstudio.com/), que es gratuito y funciona en Windows, Linux y MacOS. 

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Paso 1: Creación de una cuenta de Azure Cosmos DB
Vamos a crear una cuenta de Azure Cosmos DB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la solución de Visual Studio](#SetupVS). Si usa el Emulador de Azure Cosmos DB, siga los pasos que se indican en [Emulador de Azure Cosmos DB](local-emulator.md) para configurar el emulador y vaya directamente a [Configuración de la solución de Visual Studio](#SetupVS).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Paso 2: Configuración de la solución de Visual Studio
1. Abra **Visual Studio 2017** en el equipo.
2. En el menú **Archivo**, seleccione **Nuevo** y elija **Proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Plantillas** / **Visual C#** / **.NET Core**/**Aplicación de consola (.NET Core)**, asigne un nombre a su proyecto **DocumentDBGettingStarted** y luego haga clic en **Aceptar**.

   ![Captura de pantalla de la ventana Nuevo proyecto](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)
4. En el **Explorador de soluciones**, haga clic con el botón derecho en **DocumentDBGettingStarted**.
5. Luego, sin dejar el menú, haga clic en **Administrar paquetes NuGet...**.

   ![Captura de pantalla del menú contextual del proyecto](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. En la pestaña **NuGet** haga clic en **Examinar**, en la parte superior de la ventana, y escriba **azure documentdb** en el cuadro de búsqueda.
7. En los resultados, busque **Microsoft.Azure.DocumentDB.Core** y haga clic en **Instalar**.
   El identificador del paquete para la biblioteca es [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Si su destino es una versión de .NET Framework (como net461) incompatible con este paquete de Nuget para .NET Core, use [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB), que es compatible con todas las versiones de .NET Framework a partir de .NET Framework 4.5.
8. Cuando se le solicite, acepte el contrato de licencia y las instalaciones de los paquetes de Nuget.

Estupendo. Ahora que ha finalizado el programa de instalación, puede empezar a escribir código. Puede buscar un proyecto con código completo de este tutorial en [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Paso 3: Conexión a una cuenta de Azure Cosmos DB
En primer lugar, agregue estas referencias al principio de la aplicación de C#, en el archivo Program.cs:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

> [!IMPORTANT]
> Para poder completar este tutorial, asegúrese de agregar las dependencias anteriores.

Ahora, agregue estas dos constantes y la variable *client* debajo de la clase pública *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Seguidamente, diríjase a [Azure Portal](https://portal.azure.com) para recuperar el identificador URI y la clave principal. El URI y la clave principal de Azure Cosmos DB son necesarios para que la aplicación sepa a dónde debe conectarse y para que Azure Cosmos DB confíe en la conexión de la aplicación.

En Azure Portal, vaya a la cuenta de Azure Cosmos DB y haga clic en **Claves**.

Copie el URI desde el Portal y péguelo en `<your endpoint URI>` en el archivo program.cs. Después, copie la CLAVE PRINCIPAL del Portal y péguela en `<your key>`. Si está usando el Emulador de Azure Cosmos DB, utilice `https://localhost:8081` como punto de conexión y la clave de autorización bien definida del artículo sobre [Desarrollo con el Emulador de Azure Cosmos DB](local-emulator.md). Asegúrese de quitar el < and > pero deje las comillas dobles alrededor de la clave y el punto de conexión.

![Captura de pantalla de Azure Portal, usado por el tutorial de NoSQL para crear una aplicación de consola de C#. Muestra una cuenta de Azure Cosmos DB, con el centro ACTIVO resaltado, el botón CLAVES resaltado en la página de la cuenta de Azure Cosmos DB y los valores de URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la página Claves][keys]

Iniciaremos la aplicación de introducción, para lo que crearemos una nueva instancia de **DocumentClient**.

Debajo del método **Main**, agregue esta nueva tarea asincrónica denominada **GetStartedDemo**, que creará una instancia del nuevo **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Agregue el siguiente código para ejecutar la tarea asincrónica desde el método **Main** . El método **Main** detecta las excepciones y las escribe en la consola.

```csharp
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
```

Presione el botón **DocumentDBGettingStarted** para compilar y ejecutar la aplicación.

Felicidades. Se ha conectado correctamente a una cuenta de Azure Cosmos DB y ahora se explicará cómo trabajar con recursos de Azure Cosmos DB.  

## <a name="step-4-create-a-database"></a>Paso 4: Creación de una base de datos
Antes de agregar el código para crear una base de datos, agregue un método auxiliar para escribirlo en la consola.

Copie y pegue el método **WriteToConsoleAndPromptToContinue** debajo del método **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Para crear una [base de datos](sql-api-resources.md#databases) de Azure Cosmos DB, puede usar el método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos JSON particionado en recopilaciones.

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación del cliente. Esto crea una base de datos denominada *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Presione el botón **DocumentDBGettingStarted** para ejecutar la aplicación.

Felicidades. Ha creado correctamente una base de datos de Azure Cosmos DB.  

## <a id="CreateColl"></a>Paso 5: Creación de una colección
> [!WARNING]
> **CreateDocumentCollectionAsync** crea una nueva colección con un rendimiento reservado, lo que tiene implicaciones en los precios. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/).

Una [colección](sql-api-resources.md#collections) puede crearse mediante el método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript.

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación de la base de datos. Esto crea una colección de documentos denominada *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExists("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Presione el botón **DocumentDBGettingStarted** para ejecutar la aplicación.

Felicidades. Ha creado correctamente una colección de documentos de Azure Cosmos DB.  

## <a id="CreateDoc"></a>Paso 6: Creación de documentos JSON
Un [documento](sql-api-resources.md#documents) puede crearse mediante el método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Ahora podemos insertar uno o varios documentos. Si ya dispone de datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](import-data.md) de Azure Cosmos DB.

En primer lugar, es preciso crear una clase denominada **Family** que represente los objetos almacenados en Azure Cosmos DB en este ejemplo. También se crearán las subclases **Parent**, **Child**, **Pet**, **Address** que se usan dentro de **Family**. Tenga en cuenta que los documentos deben tener una propiedad **Id** serializada como **id** en JSON. Para agregar estas clases, agregue las siguientes subclases internas después del método **GetStartedDemo** .

Copie y pegue las clases **Family**, **Parent**, **Child**, **Pet** y **Address** debajo del método **WriteToConsoleAndPromptToContinue**.

```csharp
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
```

Copie y pegue el método **CreateFamilyDocumentIfNotExists** debajo del método **CreateDocumentCollectionIfNotExists**.

```csharp
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
```

E inserte dos documentos, uno para la familia Andersen y otro para la familia Wakefield.

Copie y pegue el código siguiente `// ADD THIS PART TO YOUR CODE` en el método **GetStartedDemo** debajo de la creación de la colección de documentos.

```csharp
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
```

Presione el botón **DocumentDBGettingStarted** para ejecutar la aplicación.

Felicidades. Ha creado correctamente dos documentos de Azure Cosmos DB.  

![Diagrama que muestra la relación jerárquica entre la cuenta, la base de datos en línea, la colección y los documentos usados por el tutorial NoSQL para crear una aplicación de consola de C#.](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Paso 7: Consulta de los recursos de Azure Cosmos DB
Azure Cosmos DB admite [consultas](sql-api-sql-query.md) enriquecidas en los documentos JSON que se almacenan en las colecciones.  En el siguiente ejemplo, se muestran diversas consultas (usando la sintaxis SQL tanto de Azure Cosmos DB como LINQ) que podemos ejecutar en los documentos que hemos insertado en el paso anterior.

Copie y pegue el método **ExecuteSimpleQuery** debajo del método **CreateFamilyDocumentIfNotExists**.

```csharp
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
```

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación del segundo documento.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Presione el botón **DocumentDBGettingStarted** para ejecutar la aplicación.

Felicidades. Ha consultado correctamente una colección de Azure Cosmos DB.

El siguiente diagrama muestra la denominación de la sintaxis de la consulta SQL de Azure Cosmos DB en la colección creada y se aplica la misma lógica a la consulta LINQ.

![Diagrama que ilustra el ámbito y el significado de la consulta usada por el tutorial de NoSQL para crear una aplicación de consola de C#.](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

La palabra clave [FROM](sql-api-sql-query.md#FromClause) es opcional en la consulta porque las consultas de Azure Cosmos DB ya tienen como ámbito una única colección. Por lo tanto, «FROM Families f" se puede intercambiar por  "FROM root r", o cualquier otra variable de nombre que elija. Azure Cosmos DB deduce la familia, la raíz o el nombre de variable elegido y hará referencia a la colección actual de forma predeterminada.

## <a id="ReplaceDocument"></a>Paso 8: Reemplazar un documento JSON
Azure Cosmos DB admite la sustitución de documentos JSON.  

Copie y pegue el método **ReplaceFamilyDocument** debajo del método **ExecuteSimpleQuery**.

```csharp
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
```

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la ejecución de la consulta. Después de reemplazar el documento, este volverá a ejecutar la misma consulta para ver el documento modificado.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Presione el botón **DocumentDBGettingStarted** para ejecutar la aplicación.

Felicidades. Ha sustituido correctamente un documento de Azure Cosmos DB.

## <a id="DeleteDocument"></a>Paso 9: Eliminar documento JSON
Azure Cosmos DB admite la eliminación de documentos JSON.  

Copie y pegue el método **DeleteFamilyDocument debajo** del método **ReplaceFamilyDocument**.

```csharp
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
```

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la ejecución de la segunda consulta.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Presione el botón **DocumentDBGettingStarted** para ejecutar la aplicación.

Felicidades. Ha eliminado correctamente un documento de Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Paso 10: Eliminar la base de datos
La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.).

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la sección "delete" del documento para eliminar toda la base de datos y todos los recursos secundarios.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Presione el botón **DocumentDBGettingStarted** para ejecutar la aplicación.

Felicidades. Ha eliminado correctamente una base de datos de Azure Cosmos DB.

## <a id="Run"></a>Paso 11: Ejecutar la aplicación de consola de C#
Presione el botón **DocumentDBGettingStarted** en Visual Studio para compilar la aplicación en modo de depuración.

Ahora debería ver la salida de la aplicación de introducción en la ventana de la consola. La salida mostrará los resultados de las consultas que hemos agregado y debe coincidir con el texto de ejemplo siguiente.

```
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
```

Felicidades. Ha completado este tutorial y tiene una aplicación de consola de C# en funcionamiento.

## <a id="GetSolution"></a> Obtención de la solución completa del tutorial
Para compilar la solución GetStarted que contiene todos los ejemplos de este artículo, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una [cuenta de Azure Cosmos DB][create-sql-api-dotnet.md#create-account].
* La solución [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) está disponible en GitHub.

Para restaurar las referencias a la API de SQL para el SDK de .NET Core para Azure Cosmos DB en Visual Studio, haga clic con el botón derecho en la solución **GetStarted** en el Explorador de soluciones y, después, haga clic en **Enable NuGet Package Restore** (Habilitar la restauración del paquete NuGet). A continuación, en el archivo Program.cs , actualice los valores EndpointUrl y AuthorizationKey como se describe en el artículo sobre la [Conexión a una cuenta de Azure Cosmos DB](#Connect).

## <a name="next-steps"></a>pasos siguientes
* ¿Desea un tutorial de ASP.NET MVC más complejo? Consulte el [Tutorial de ASP.NET MVC: desarrollo de aplicaciones web con Azure Cosmos DB](sql-api-dotnet-application.md).
* ¿Quiere desarrollar una aplicación de Xamarin iOS, Android o Forms mediante la API de SQL para el SDK de .NET Core de Azure Cosmos DB? Consulte [Creación de aplicaciones móviles con Xamarin y Azure Cosmos DB](mobile-apps-with-xamarin.md).
* ¿Desea realizar pruebas de escala y de rendimiento con Azure Cosmos DB? Consulte [Pruebas de escala y rendimiento con Azure Cosmos DB](performance-testing.md).
* Obtenga más información sobre cómo [Supervisión de las solicitudes, uso y almacenamiento de Azure Cosmos DB](monitor-accounts.md).
* Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
* Para más información sobre el modelo de programación, consulte [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
