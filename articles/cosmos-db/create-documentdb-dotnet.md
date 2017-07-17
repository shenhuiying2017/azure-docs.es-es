---
title: "Azure Cosmos DB: Compilar una aplicación web con .NET y la API DocumentDB | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código de .NET que se puede usar para conectarse a la API DocumentDB de Azure Cosmos DB y realizar consultas."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: d17f90d5ed5440dc336d1e3ae890a13077e33c4d
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


---
<a id="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-and-the-azure-portal" class="xliff"></a>

# Azure Cosmos DB: Compilar una aplicación web de API DocumentDB con .NET y Azure Portal

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y gráficos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escalado horizontal en Azure Cosmos DB. 

En esta guía de inicio rápido se muestra cómo crear una cuenta, una base de datos de documentos y una colección de Azure Cosmos DB mediante Azure Portal. Después, compilará e implementará una aplicación de web de lista de tareas pendientes integrada en la [API DocumentDB de .NET](documentdb-sdk-dotnet.md), tal como se muestra en la captura de pantalla siguiente. 

![Aplicación de tareas pendientes con datos de ejemplo](./media/create-documentdb-dotnet/azure-comosdb-todo-app-list.png)

<a id="prerequisites" class="xliff"></a>

## Requisitos previos

Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>
<a id="create-a-database-account" class="xliff"></a>

## Creación de una cuenta de base de datos

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection"></a>
<a id="add-a-collection" class="xliff"></a>

## Agregar una colección

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
<a id="add-sample-data" class="xliff"></a>

## Agregar datos de ejemplo

Ahora puede agregar datos a la nueva colección mediante el Explorador de datos.

1. En el Explorador de datos, la nueva base de datos aparece en el panel Colecciones. Expanda la base de datos **Tareas**, expanda la colección **Elementos**, haga clic en **Documentos** y, después, haga clic en **Nuevos documentos**. 

   ![Creación de documentos en el Explorador de datos en Azure Portal](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. Ahora agregue algunos documentos a la colección con la estructura siguiente, en la que debe insertar valores únicos para la identificación de cada documento y puede cambiar las demás propiedades como considere oportuno. Los nuevos documentos pueden tener la estructura que quiera, ya que Azure Cosmos DB no impone ningún esquema en los datos.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Cuando haya agregado el archivo JSON a la pestaña **Documentos**, haga clic en **Guardar**.

    ![Copiar los datos JSON y hacer clic en Guardar en el Explorador de datos en Azure Portal](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

     Ahora puede usar consultas en el Explorador de datos para recuperar los datos. De forma predeterminada, el Explorador de datos usa `SELECT * FROM c` para recuperar todos los documentos de la colección, pero puede cambiar este valor a `SELECT * FROM c ORDER BY c.name ASC` para devolver todos los documentos en orden alfabético ascendente en función de la propiedad name. 
 
     También puede usar el Explorador de datos para crear procedimientos almacenados, UDF y desencadenadores para realizar la lógica de negocios del servidor, así como escalar el rendimiento. El Explorador de datos expone todo el acceso a datos mediante programación integrado que está disponible en las API, pero permite un acceso fácil a los datos de Azure Portal.

<a id="clone-the-sample-application" class="xliff"></a>

## Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de API de DocumentDB desde GitHub, a establecer la cadena de conexión y a ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como Git Bash, y `CD` en un directorio de trabajo.  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. Después, abra el archivo de solución de tareas pendientes en Visual Studio. 

<a id="review-the-code" class="xliff"></a>

## Revisión del código

Vamos a revisar rápidamente lo que sucede en la aplicación. Abra el archivo DocumentDBRepository.cs y observe que estas líneas de código crean los recursos de Azure Cosmos DB. 

* DocumentClient se inicializa en la línea 73.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* Se crea una nueva base de datos en la línea 88.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* Se crea una nueva colección en la línea 107.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```

<a id="update-your-connection-string" class="xliff"></a>

## Actualización de la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de Azure Cosmos DB, en el panel de navegación izquierdo, haga clic en **Claves** y en **Claves de lectura y escritura**. Deberá usar los botones de copia del lado derecho de la pantalla para copiar el URI y la clave principal en el archivo web.config en el paso siguiente.

    ![Visualización y copia de una clave de acceso en Azure Portal, hoja Claves](./media/create-documentdb-dotnet/keys.png)

2. En Visual Studio 2017, abra el archivo web.config. 

3. Copie el valor del URI del portal (con el botón de copia) y conviértalo en el valor de la clave de punto de conexión en web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Después, copie el valor de la clave principal del portal y conviértalo en el valor de la clave de autenticación en web.config. Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

    `<add key="authKey" value="FILLME" />`
    
<a id="run-the-web-app" class="xliff"></a>

## Ejecución de la aplicación web
1. En Visual Studio, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y, después, haga clic en **Administrar paquetes NuGet**. 

2. En el cuadro **Examinar** de NuGet, escriba *DocumentDB*.

3. En los resultados, instale la biblioteca **Microsoft.Azure.DocumentDB**. De este modo, se instalan el paquete Microsoft.Azure.DocumentDB y todas las dependencias.

4. Presione Ctrl+F5 para ejecutar la aplicación. La aplicación se muestra en el explorador. 

5. Haga clic en **Crear nuevo** en el explorador y cree algunas tareas en la aplicación de tareas pendientes.

   ![Aplicación de tareas pendientes con datos de ejemplo](./media/create-documentdb-dotnet/azure-comosdb-todo-app-list.png)

Ahora puede volver al Explorador de datos y ver, consultar, modificar y trabajar con estos nuevos datos. 

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## Revisión de los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-resources" class="xliff"></a>

## Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal:

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

<a id="next-steps" class="xliff"></a>

## Pasos siguientes

En esta guía de inicio rápido, ha aprendido a crear una cuenta de Azure Cosmos DB, crear una colección mediante el Explorador de datos y ejecutar una aplicación web. Ahora puede importar datos adicionales en la cuenta de Cosmos DB. 

> [!div class="nextstepaction"]
> [Importación de datos a Azure Cosmos DB](import-data.md)



