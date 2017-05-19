---
title: "Azure Cosmos DB: Compilar una aplicación con Java y la API DocumentDB | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código de Java que se puede usar para conectarse a la API DocumentDB de Azure Cosmos DB y realizar consultas."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c0ed076521291bac61049c13045553c9f64cd565
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-documentdb-api-app-with-java-and-the-azure-portal"></a>Azure Cosmos DB: Compilar una aplicación de API DocumentDB con Java y Azure Portal

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y gráficos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB. 

En esta guía de inicio rápido se muestra cómo crear una cuenta, una base de datos de documentos y una colección de Azure Cosmos DB mediante Azure Portal. Después, compilará y ejecutará una aplicación de consola compilada en la [API DocumentDB de Java](../documentdb/documentdb-sdk-java.md).

## <a name="prerequisites"></a>Requisitos previos

* Antes de ejecutar este ejemplo, debe cumplir los siguientes requisitos previos:
   * JDK 1.7+ (ejecute `apt-get install default-jdk` si no tiene JDK)
   * Maven (ejecute `apt-get install maven` si no tiene Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="add-a-collection"></a>Agregar una colección

[!INCLUDE [cosmosdb-create-collection](../../includes/cosmosdb-create-collection.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de API DocumentDB desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como Git Bash, y `CD` en un directorio de trabajo.  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-java-getting-started.git
    ```

## <a name="review-the-code"></a>Revisar el código

Vamos a revisar rápidamente lo que sucede en la aplicación. Abra el archivo `app.js` y observe que estas líneas de código crean los recursos de Azure Cosmos DB. 

* Se inicializa `DocumentClient`.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* Se crea una base de datos.

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* Se crea una colección.

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    // DocumentDB collections can be reserved with throughput
    // specified in request units/second. 1 RU is a normalized
    // request equivalent to the read of a 1KB document. Here we
    // create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Se crean algunos documentos.

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* Se realiza una consulta SQL en JSON.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>Actualizar la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de Azure Cosmos DB, en el panel de navegación izquierdo, haga clic en **Claves** y en **Claves de lectura y escritura**. Deberá usar los botones de copia del lado derecho de la pantalla para copiar el URI y la clave principal en el archivo `Program.java` en el paso siguiente.

    ![Visualización y copia de una clave de acceso en Azure Portal, hoja Claves](./media/create-documentdb-dotnet/keys.png)

2. Abra el archivo `Program.java`. 

3. Copie el valor del URI del portal (con el botón de copia) y conviértalo en el valor del punto de conexión al constructor DocumentClient en `Program.java`. 

    `"https://FILLME.documents.azure.com"`

4. Después, copie el valor de la clave principal del portal y conviértalo en el valor de la clave maestra del constructor DocumentClient en "Program.java". Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

    `config.primaryKey "FILLME"`
    
## <a name="run-the-app"></a>Ejecución de la aplicación

1. Ejecute `mvn package` en un terminal para instalar los módulos de NPM necesarios.

2. Ejecute `mvn exec:java -D exec.mainClass=GetStarted.Program` en un terminal para iniciar la aplicación de Java.

Ahora puede volver al Explorador de datos y ver, consultar, modificar y trabajar con estos nuevos datos. 

## <a name="review-slas-in-the-azure-portal"></a>Revisar los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal:

1. En el menú izquierdo de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que ha creado. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a crear una cuenta de Azure Cosmos DB, crear una colección mediante el Explorador de datos y ejecutar una aplicación. Ahora puede importar datos adicionales en la cuenta de Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar datos en Azure Cosmos DB](../documentdb/documentdb-import-data.md)



