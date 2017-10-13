---
title: 'Azure Cosmos DB: desarrollo con API Graph en .NET | Microsoft Docs'
description: "Obtenga información sobre cómo desarrollar con la API de DocumentDB de Azure Cosmos DB con .NET"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.custom: mvc
ms.openlocfilehash: b1419e5aad9446b9d96450cfad79b200cda9a518
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: desarrollo con API Graph en .NET
Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y grafos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB. 

En este tutorial se muestra cómo crear una cuenta de Azure Cosmos DB con Azure Portal y cómo crear un contenedor y una base de datos de grafo. Luego, la aplicación crea una red social sencilla con cuatro personas con la [API Graph](graph-sdk-dotnet.md) (versión preliminar) y después cruza y consulta el grafo con Gremlin.

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Creación de una cuenta de Azure Cosmos DB 
> * Creación de un contenedor y una base de datos de grafo
> * Serialización de vértices y bordes en objetos .NET
> * Incorporación de vértices y bordes
> * Consulta del grafo con Gremlin

## <a name="graphs-in-azure-cosmos-db"></a>Grafos en Azure Cosmos DB
Puede usar Azure Cosmos DB para crear, actualizar y consultar grafos con la biblioteca [Microsoft.Azure.Graphs](graph-sdk-dotnet.md). La biblioteca Microsoft.Azure.Graph proporciona un método de extensión único `CreateGremlinQuery<T>` además de la clase `DocumentClient` para ejecutas consultas Gremlin.

Gremlin es un lenguaje de programación funcional que admite operaciones de escritura (DML) y operaciones de consulta y cruce. En este artículo describiremos algunos ejemplos para que comience a trabajar con Gremlin. Consulte las [consultas de Gremlin](gremlin-support.md) para un tutorial detallado de las funcionalidades de Gremlin disponibles en Azure Cosmos DB. 

## <a name="prerequisites"></a>Requisitos previos
Asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/). 
    * Como alternativa, puede usar el [Emulador de Azure DocumentDB](local-emulator.md) en este tutorial.
* [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Creación de una cuenta de base de datos

Para comenzar, creemos una cuenta de Azure Cosmos DB en Azure Portal.  

> [!TIP]
> * ¿Ya tiene una cuenta de Azure Cosmos DB? Si es así, vaya a [Configuración de la solución de Visual Studio](#SetupVS)
> * ¿Ya tenía una cuenta de Azure DocumentDB? Si es así, ahora es una cuenta de Azure Cosmos DB, por lo que puede ir directamente a [Configuración de la solución de Visual Studio](#SetupVS).  
> * Si usa el Emulador de Azure Cosmos DB, siga los pasos que se indican en [Emulador de Azure Cosmos DB](local-emulator.md) para configurar el emulador y vaya directamente a [Configuración de la solución de Visual Studio](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Configuración de la solución de Visual Studio
1. Abra **Visual Studio** en el equipo.
2. En el menú **Archivo**, seleccione **Nuevo** y elija **Proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Plantillas** / **Visual C#** / **Aplicación de consola (.NET Framework)**, asigne un nombre al proyecto y haga clic en **Aceptar**.
4. En el **Explorador de soluciones**, haga clic con el botón derecho en la nueva aplicación de la consola, que se encuentra en la solución de Visual Studio y, a continuación, haga clic en **Administrar paquetes NuGet...**
5. En la pestaña **NuGet**, haga clic en **Examinar** y escriba **Microsoft.Azure.Graphs** en el cuadro de búsqueda y active la opción **Incluir versiones preliminares**.
6. En los resultados, busque **Microsoft.Azure.Graphs** y haga clic en **Instalar**.
   
   Si recibe un mensaje sobre cómo revisar los cambios en la solución, haga clic en **Aceptar**. Si recibe un mensaje acerca de la aceptación de licencia, haga clic en **Acepto**.
   
    La biblioteca `Microsoft.Azure.Graphs` proporciona un método de extensión único `CreateGremlinQuery<T>` para ejecutar operaciones de Gremlin. Gremlin es un lenguaje de programación funcional que admite operaciones de escritura (DML) y operaciones de consulta y cruce. En este artículo describiremos algunos ejemplos para que comience a trabajar con Gremlin. El artículo sobre las [consultas de Gremlin](gremlin-support.md) ofrece un tutorial detallado de las funcionalidades de Gremlin en Azure Cosmos DB.

## <a id="add-references"></a>Conexión de la aplicación

Agregue estas dos constantes y la variable *client* en la aplicación. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
A continuación, vuelva a [Azure Portal](https://portal.azure.com) para recuperar la dirección URL del punto de conexión y la clave principal. La dirección URL del punto de conexión y la clave principal son necesarias para que la aplicación sepa a dónde debe conectarse y para que Azure Cosmos DB confíe en la conexión de la aplicación. 

En Azure Portal, vaya a la cuenta de Azure Cosmos DB, haga clic en **Claves** y, luego, en **Claves de lectura y escritura**. 

Copie el URI desde el portal y péguelo en `Endpoint` en la propiedad de punto de conexión anterior. Luego copie la CLAVE PRINCIPAL del portal y péguela en la propiedad `AuthKey` anterior. 

![Captura de pantalla de Azure Portal usada por el tutorial para crear una aplicación de C#. Muestra una cuenta de Azure Cosmos DB, con el botón CLAVES resaltado en la navegación de Azure Cosmos DB y los valores de URI y PRIMARY KEY resaltados en la hoja Claves][claves] 
 
## <a id="instantiate"></a>Creación de instancia de DocumentClient 
A continuación, cree una instancia nueva de **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Creación de una base de datos 

Ahora cree una [base de datos](documentdb-resources.md#databases) de Azure Cosmos DB con el método [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) o el método [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) de la clase **DocumentClient** desde el [SDK de .NET de DocumentDB](documentdb-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Creación de un grafo 

A continuación, cree un contenedor de grafos a través del método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) o el método [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) de la clase **DocumentClient**. Una colección es un contenedor de entidades de grafo. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>Serialización de vértices y bordes en objetos .NET
Azure Cosmos DB usa el [formato GraphSON](gremlin-support.md), que define un esquema JSON para los vértices, los bordes y las propiedades. El SDK de .NET de Azure Cosmos DB incluye JSON.NET como dependencia, lo que nos permite serializar/deserializar GraphSON en objetos .NET con los que podemos trabajar en el código.

Como ejemplo, vamos a trabajar con una red social sencilla con cuatro personas. Veremos cómo crear vértices `Person`, agregar relaciones `Knows` entre ellos y, luego, consultar y cruzar el grafo para encontrar relaciones "de amigos de amigos". 

El espacio de nombres `Microsoft.Azure.Graphs.Elements` proporciona las clases `Vertex`, `Edge`, `Property` y `VertexProperty` para deserializar las respuestas de GraphSON en objetos .NET bien definidos.

## <a name="run-gremlin-using-creategremlinquery"></a>Ejecución de Gremlin con CreateGremlinQuery
Al igual que SQL, Gremlin admite operaciones de lectura, escritura y consulta. Por ejemplo, el siguiente fragmento de código muestra cómo crear vértices y bordes; haga algunas consultas de ejemplo con `CreateGremlinQuery<T>` e itere de manera asincrónica en estos resultados con `ExecuteNextAsync` y `HasMoreResults.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Incorporación de vértices y bordes

Analicemos más detalladamente las instrucciones Gremlin mostradas en la sección anterior. En primer lugar, creamos algunos vértices con el método `addV` de Gremlin. Por ejemplo, el fragmento de código siguiente crea un vértice "Thomas Andersen" de tipo "Persona", con propiedades como nombre, apellido y edad.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Luego cree algunos bordes entre estos vértices con el método `addE` de Gremlin. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Podemos usar el paso `properties` en Gremlin para actualizar un vértice existente. Omitimos la llamada para ejecutar la consulta vía `HasMoreResults` y `ExecuteNextAsync` para el resto de los ejemplos.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Puede quitar bordes y vértices con el paso `drop` de Gremlin. El siguiente es un fragmento de código que muestra cómo eliminar un vértice y un borde. Tenga en cuenta que eliminar un vértice genera una reacción en cadena de eliminaciones de los bordes asociados.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>Consulta del grafo

Puede realizar consultas y cruces también con Gremlin. Por ejemplo, en el siguiente fragmento de código se muestra cómo contar el número de vértices del grafo:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Puede aplicar filtros mediante los pasos `has` y `hasLabel` de Gremlin y combinarlos con `and`, `or` y `not` para crear filtros más complejos:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Puede proyectar determinadas propiedades en los resultados de la consulta mediante el paso `values`:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Hasta ahora, solo hemos visto operadores de consulta que funcionan en cualquier base de datos. Los grafos son rápidos y eficientes para operaciones de cruce seguro si tiene que ir a bordes y vértices relacionados. Encontremos a todos los amigos de Thomas. Lo hacemos usando el paso `outE` de Gremlin para encontrar todos los bordes exteriores de Thomas y atravesando a continuación por los vértices interiores de esos bordes mediante el paso `inV` de Gremlin:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

La siguiente consulta realiza dos saltos para encontrar a todos los "amigos de los amigos" de Thomas, llamando a `outE` y `inV` dos veces. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Puede crear consultas más complejas e implementar una lógica de cruce seguro del grafo eficaz con Gremlin, incluidas la combinación de expresiones de filtro, la realización de bucles mediante el paso `loop` y la implementación de la navegación condicional mediante el paso `choose`. Obtenga más información sobre lo que puede hacer con [Compatibilidad con Gremlin](gremlin-support.md).

Eso es todo. Ya completó el tutorial de Azure Cosmos DB 

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, use los pasos siguientes para borrar todos los recursos que se crearon en este tutorial en Azure Portal.  

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Se creó una cuenta de Azure Cosmos DB 
> * Se creó un contenedor y una base de datos de grafo
> * Se serializaron vértices y bordes en objetos .NET
> * Se agregaron vértices y bordes
> * Se consultó el grafo con Gremlin

Ahora puede crear consultas más complejas e implementar con Gremlin una lógica de cruce seguro del grafo eficaz. 

> [!div class="nextstepaction"]
> [Consulta mediante Gremlin](tutorial-query-graph.md)
