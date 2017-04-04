---
title: 'Tutorial de NoSQL: SDK de Java para Azure DocumentDB | Microsoft Docs'
description: "Un tutorial de NoSQL que crea una base de datos en línea y la aplicación de consola de Java mediante el SDK de Java para DocumentDB. Azure DocumentDB es una base de datos NoSQL para JSON."
keywords: "tutorial de nosql, base de datos en línea, aplicación de consola de java"
services: documentdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: da7907ffc515ea2e3040075c93bcd53840cf3ff5
ms.lasthandoff: 03/29/2017


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>Tutorial de NoSQL: Creación de una aplicación de consola de Java para DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js para MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bienvenido al tutorial de NoSQL para el SDK de Java para Azure DocumentDB. Después de seguir este tutorial, tendrá una aplicación de consola que crea recursos de DocumentDB y realiza consultas en ellos.

Trataremos los siguientes temas:

* Creación y aprovisionamiento una cuenta de DocumentDB.
* Configuración de la solución de Visual Studio
* Creación de una base de datos en línea
* Creación de una colección
* Creación de documentos JSON
* Consulta de la colección
* Creación de documentos JSON
* Consulta de la colección
* Sustitución de un documento
* Eliminación de un documento
* Eliminación de la base de datos

Comencemos.

## <a name="prerequisites"></a>Requisitos previos
Asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/). Como alternativa, puede usar el [Emulador de Azure DocumentDB](documentdb-nosql-local-emulator.md) en este tutorial.
* [Git](https://git-scm.com/downloads)
* <seg>
  [Kit de desarrollo de Java (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).</seg>
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-a-documentdb-account"></a>Paso 1: Creación de una cuenta de DocumentDB
Creemos una cuenta de DocumentDB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Clonar el proyecto de GitHub](#GitClone). Si está usando el Emulador de DocumentDB, siga los pasos que se indican en el [Emulador de Azure DocumentDB](documentdb-nosql-local-emulator.md) para configurar el emulador e ir directamente a [Clonar el proyecto de GitHub](#GitClone).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="GitClone"></a>Paso 2: Clonar el proyecto de GitHub
En primer lugar, clone el repositorio de GitHub para [empezar a trabajar con DocumentDB y Java](https://github.com/Azure-Samples/documentdb-java-getting-started). Por ejemplo, desde un directorio local, ejecute lo siguiente para recuperar el proyecto de ejemplo localmente.

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

El directorio contiene un archivo `pom.xml` para el proyecto y una carpeta `src` que contiene el código fuente de Java, incluido `Program.java`, que muestra cómo realiza operaciones sencillas con Azure DocumentDB, como crear documentos y consultar datos dentro de una colección. El archivo `pom.xml` incluye una dependencia en el [SDK de Java para DocumentDB en Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Paso 3: Conexión a una cuenta de DocumentDB
A continuación, vuelva a [Azure Portal](https://portal.azure.com) para recuperar el punto de conexión y la clave maestra principal. El punto de conexión y la clave maestra principal de DocumentDB son necesarios para que la aplicación sepa a dónde debe conectarse y para que DocumentDB confíe en la conexión de la aplicación.

En el Portal de Azure, navegue a la cuenta de DocumentDB y, después, haga clic en **Claves**. Copie el URI desde el portal y péguelo en `<your endpoint URI>`, en el archivo Program.java. Después, copie la CLAVE PRINCIPAL del Portal y péguela en `<your key>`.

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Captura de pantalla de Azure Portal usado por el tutorial de NoSQL para crear una aplicación de consola de Java. Muestra una cuenta de DocumentDB, con el concentrador ACTIVO resaltado, el botón CLAVES resaltado en la hoja de cuenta de DocumentDB y los valores URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la hoja Claves.][keys]

## <a name="step-4-create-a-database"></a>Paso 4: Creación de una base de datos
Para crear una [base de datos](documentdb-resources.md#databases) de DocumentDB, puede usar el método [createDatabase](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos JSON particionado en recopilaciones.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Paso 5: Creación de una colección
> [!WARNING]
> **createCollection** creará una colección con un rendimiento reservado, lo que tiene implicaciones en los precios. Para más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Para crear una [colección](documentdb-resources.md#collections), puede usar el método [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // DocumentDB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Paso 6: Creación de documentos JSON
Para crear un [documento](documentdb-resources.md#documents), puede usar el método [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Ahora podemos insertar uno o varios documentos. Si ya dispone de datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](documentdb-import-data.md)de DocumentDB para importar los datos en una base de datos.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagrama que muestra la relación jerárquica entre la cuenta, la base de datos en línea, la colección y los documentos usados por el tutorial de NoSQL para crear una aplicación de consola de Java.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Paso 7: Consulta de recursos de DocumentDB
DocumentDB admite [consultas](documentdb-sql-query.md) enriquecidas contra los documentos JSON almacenados en cada colección.  El código de ejemplo siguiente muestra cómo consultar documentos en DocumentDB mediante la sintaxis SQL con el método [queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Paso 8: Reemplazar un documento JSON
DocumentDB permite actualizar documentos JSON con el método [replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Paso 9: Eliminar documento JSON
De forma similar, DocumentDB permite eliminar documentos JSON con el método [deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Paso 10: Eliminar la base de datos
La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Paso 11: Ejecutar la aplicación de consola de Java
Para ejecutar la aplicación desde la consola, primero compílela con Maven:
    
    mvn package

La ejecución de `mvn package` descarga la biblioteca de DocumentDB más reciente de Maven y genera `GetStarted-0.0.1-SNAPSHOT.jar`. A continuación, ejecute la aplicación con:

    mvn exec:java -D exec.mainClass=GetStarted.Program

¡Enhorabuena! Ha completado este tutorial de NoSQL y tiene una aplicación de consola de Java en funcionamiento.

## <a name="next-steps"></a>Pasos siguientes
* ¿Desea un tutorial de las aplicaciones web de Java? Consulte [Creación de una aplicación web de Java mediante DocumentDB](documentdb-java-application.md).
* Aprenda a [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
* Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
* Obtenga más información sobre el modelo de programación en la sección sobre desarrollo de la [página de documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

