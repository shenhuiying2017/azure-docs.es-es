---
title: "Compilar una aplicación de .NET de Azure Cosmos DB mediante API Graph | Microsoft Docs"
description: "En este tema se incluye un ejemplo de código .NET que puede usar para conectarse a Azure Cosmos DB y realizar consultas."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 07a13c3e9e2baefe0be7ed417ba105dd23a3708d
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-graph-api"></a>Azure Cosmos DB: Compilar una aplicación de .NET mediante API Graph

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y gráficos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB. 

En esta guía de inicio rápido se muestra cómo crear una cuenta, una base de datos y un gráfico (contenedor) de Azure Cosmos DB mediante Azure Portal. Después, compilará y ejecutará una aplicación de consola compilada en [API Graph](graph-sdk-dotnet.md) (versión preliminar).  

## <a name="prerequisites"></a>Requisitos previos

Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Agregar un gráfico

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a name="add-sample-data"></a>Agregar datos de ejemplo

Ahora puede agregar datos al gráfico mediante el Explorador de datos.

1. En el Explorador de datos, expanda **sample-database**, **sample-graph**, haga clic en **Gráfico** y, después, haga clic en **New Vertex** (Nuevo vértice) y **New Edge** (Nuevo borde) para agregar elementos a su gráfico. En el Explorador de datos también puede escalar el rendimiento y agregar procedimientos almacenados, funciones definidas por el usuario y desencadenadores a su contenedor.

    ![Agregar vértices y bordes a un gráfico en el Explorador de datos](./media/create-graph-dotnet/azure-cosmos-db-graph-sample-data.png)

2. Una vez que haya agregado algunos elementos, haga clic en el botón **Aplicar filtro**, o bien haga clic con el botón derecho en **Gráfico** y haga clic en **New Graph Query** (Nueva consulta de gráfico) para ver el gráfico visual de los datos. Puede cambiar el tipo de etiquetas y de estilos que se aplican a los datos. Para ello, haga clic en el botón **Estilo** y cambie la configuración. A continuación puede ver un gráfico de ejemplo en el Explorador de datos. Todas las etiquetas, colores y datos que se muestran se pueden modificar.

    ![Explorador de gráficos visuales en el Explorador de datos en Azure Portal](./media/create-graph-dotnet/azure-cosmos-db-graph-explorer.png)

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de API Graph desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como Git Bash, y `cd` en un directorio de trabajo.  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. Después, abra el archivo de solución en Visual Studio. 

## <a name="review-the-code"></a>Revisar el código

Vamos a revisar rápidamente lo que sucede en la aplicación. Abra el archivo Program.cs y observe que estas líneas de código crean los recursos de Azure Cosmos DB. 

* Se inicializa DocumentClient. En la vista previa, hemos agregado una API de extensión de gráficos en el cliente de DocumentDB. Estamos trabajando en un cliente de gráficos independiente desacoplado del cliente y los recursos de DocumentDB.

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* Se crea una base de datos.

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* Se crea un gráfico.

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* Se ejecuta una serie de pasos de Gremlin mediante el método `CreateGremlinQuery`.

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>Actualizar la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de Azure Cosmos DB, en el panel de navegación izquierdo, haga clic en **Claves** y en **Claves de lectura y escritura**. Deberá usar los botones de copia del lado derecho de la pantalla para copiar el URI y la clave principal en el archivo `App.config` en el paso siguiente.

    ![Visualización y copia de una clave de acceso en Azure Portal, hoja Claves](./media/create-graph-dotnet/keys.png)

2. En Visual Studio 2017, abra el archivo `App.config`. 

3. Copie el valor del URI del portal (con el botón de copia) y conviértalo en el valor de la clave de punto de conexión en `App.config`. 

    `<add key="Endpoint" value="FILLME.documents.azure.com:443" />`

4. Después, copie el valor de la clave principal del portal y conviértalo en el valor de la clave de autenticación en `App.config`. 

    `<add key="AuthKey" value="FILLME" />`

Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Ejecutar la aplicación de consola

1. En Visual Studio, haga clic con el botón derecho en el proyecto **GraphGetStarted** en el **Explorador de soluciones** y, después, haga clic en **Administrar paquetes NuGet**. 

2. En el cuadro **Examinar** de NuGet, escriba *Microsoft.Azure.Graphs* y active la casilla **Includes prerelease** (Incluye versión preliminar). 

3. En los resultados, instale la biblioteca **Microsoft.Azure.Graphs**. De este modo se instala el paquete de la biblioteca de extensión de gráficos de Azure Cosmos DB y todas las dependencias.

4. Presione Ctrl+F5 para ejecutar la aplicación.

   En la ventana de la consola se muestran los vértices y los bordes que se agregan al gráfico. Cuando se complete el script, presione ENTRAR dos veces para cerrar la ventana de la consola. 

## <a name="browse-using-the-data-explorer"></a>Examinar mediante el Explorador de datos

Ahora puede volver al Explorador de datos en Azure Portal para examinar y consultar los datos del nuevo gráfico.

* En el Explorador de datos, la nueva base de datos aparece en el panel Colecciones. Expanda **graphdb**, **graphcoll** y, después, haga clic en **Gráfico**.

    Los datos generados por la aplicación de ejemplo se muestran en el panel Gráficos.

## <a name="review-slas-in-the-azure-portal"></a>Revisar los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal: 

1. En el menú izquierdo de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que ha creado. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a crear una cuenta de Azure Cosmos DB, crear un gráfico mediante el Explorador de datos y ejecutar una aplicación. Ahora puede crear consultas más complejas e implementar con Gremlin una lógica de recorrido del gráfico eficaz. 

> [!div class="nextstepaction"]
> [Consulta mediante Gremlin](tutorial-query-graph.md)


