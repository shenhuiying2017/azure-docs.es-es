---
title: 'Azure Cosmos DB: compilación de una aplicación de lista de tareas con Xamarin | Microsoft Docs'
description: En este tema se incluye un ejemplo de código Xamarin que puede usar para conectarse a Azure Cosmos DB y realizar consultas
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/04/2018
ms.author: sngun
ms.openlocfilehash: 1fec2604dc2aee412e73f5ca332d2852bf7e58bd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-build-a-todo-app-with-xamarin"></a>Azure Cosmos DB: compilación de una aplicación de lista de tareas con Xamarin

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y grafos, y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB.

> [!NOTE]
> [Aquí](https://github.com/xamarinhq/app-geocontacts), en GitHub, se puede encontrar código de muestra para una aplicación completa de Xamarin de ejemplo canónico que ilustra varias ofertas de Azure, incluido CosmosDB. Esta aplicación muestra contactos de visualización dispersos geográficamente y permite que estos actualicen su ubicación.

En esta guía de inicio rápido se muestra cómo crear una cuenta de SQL API de Azure Cosmos DB, una base de datos de documentos y una colección mediante Azure Portal. Luego, compilará e implementará una aplicación web de lista de tareas integrada en la [API de .NET de SQL](sql-api-sdk-dotnet.md) y [Xamarin](https://docs.microsoft.com/xamarin/#pivot=platforms&panel=Cross-Platform) mediante [Xamarin.Forms](https://docs.microsoft.com/xamarin/#pivot=platforms&panel=XamarinForms) y el [modelo arquitectónico de MVVM ](https://docs.microsoft.com/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm).

![Aplicación de lista de tareas que se ejecuta en iOS](./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png)

## <a name="prerequisites"></a>requisitos previos

Si va a realizar un desarrollo en Windows y no tiene Visual Studio 2017 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita las cargas de trabajo **Desarrollo de Azure** y **Desarrollo móvil con .NET** durante la instalación de Visual Studio.

Si utiliza un equipo Mac, puede descargar **Visual Studio para Mac**, que es [gratis](https://www.visualstudio.com/vs/mac/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Agregar una colección

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adición de datos de ejemplo

Ahora puede agregar datos a la nueva colección mediante el Explorador de datos.

1. En el Explorador de datos > expanda la base de datos **Tareas** > expanda la colección **Elementos** > haga clic en **Documentos** y, después, haga clic en **Nuevos documentos**.

   ![Creación de documentos en el Explorador de datos en Azure Portal](./media/create-sql-api-xamarin-dotnet/azure-cosmosdb-data-explorer-new-document.png)

2. Ahora agregue un documento a la colección con la estructura siguiente.

     ```json
     {
         "id": "1",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "completed": false
     }
     ```

3. Cuando haya agregado el archivo JSON a la pestaña **Documentos**, haga clic en **Guardar**.

    ![Copiar los datos JSON y hacer clic en Guardar en el Explorador de datos en Azure Portal](./media/create-sql-api-xamarin-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4. Cree y guarde un documento más donde insertará un valor único para la propiedad `id` y cambie las demás propiedades como corresponda. Los nuevos documentos pueden tener la estructura que quiera, ya que Azure Cosmos DB no impone ningún esquema en los datos.

     Ahora puede usar consultas en el Explorador de datos para recuperar los datos. De forma predeterminada, el Explorador de datos usa `SELECT * FROM c` para recuperar todos los documentos de la colección, pero puede cambiarlo por una [consulta SQL](sql-api-sql-query.md) diferente, como `SELECT * FROM c ORDER BY c._ts DESC`, para devolver todos los documentos en orden descendente en función de su marca de tiempo.

     También puede usar el Explorador de datos para crear procedimientos almacenados, UDF y desencadenadores para realizar la lógica de negocios del servidor, así como escalar el rendimiento. El Explorador de datos expone todo el acceso a datos mediante programación integrado que está disponible en las API, pero permite un acceso fácil a los datos de Azure Portal.

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar la aplicación de la API de SQL Xamarin de github, revisar el código, obtener las claves de API y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación.

1. Abra una ventana de terminal de Git, como Git Bash, y `cd` en un directorio de trabajo.

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo.

    ```bash
    git clone https://github.com/Azure/azure-documentdb-dotnet.git
    ```

3. Luego, abra el archivo ToDoItems.sln desde la carpeta samples/xamarin/ToDoItems en Visual Studio.

## <a name="obtain-your-api-keys"></a>Obtención de las claves de API

Vuelva a Azure Portal para obtener la información de la clave de API y cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de la API de SQL de Azure Cosmos DB, en el panel de navegación izquierdo, haga clic en **Claves** y en **Claves de lectura y escritura**. Deberá usar los botones de copia del lado derecho de la pantalla para copiar el URI y la clave principal en el archivo APIKeys.cs en el paso siguiente.

    ![Visualización y copia de una clave de acceso en Azure Portal, hoja Claves](./media/create-sql-api-xamarin-dotnet/keys.png)

2. En Visual Studio 2017 o en Visual Studio para Mac, abra el archivo APIKeys.cs de la carpeta azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Helpers.

3. Copie el valor de URI del portal (con el botón de copia) y conviértalo en el valor de la variable `CosmosEndpointUrl` de APIKeys.cs.

    `public static readonly string CosmosEndpointUrl = "{Azure Cosmos DB account URL}";`

4. Después, copie el valor de la clave principal del portal y conviértalo en el valor de `Cosmos Auth Key` en APIKeys.cs.

    `public static readonly string CosmosAuthKey = "{Azure Cosmos DB secret}";`

## <a name="review-the-code"></a>Revisión del código

Esta solución muestra cómo crear una aplicación de lista de tareas mediante la API de SQL de Azure Cosmos DB y Xamarin.Forms. La aplicación tiene dos pestañas. La primera contiene una vista de lista que muestra los elementos de la lista de tareas que aún no están completos y la segunda muestra los que se han completado. Además de ver los elementos de la lista de tareas que no se han completado en la primera pestaña, también puede agregar nuevos elementos de la lista de tareas, editar los existentes y marcar elementos como completados.

![Copiar los datos JSON y hacer clic en Guardar en el Explorador de datos en Azure Portal](./media/create-sql-api-xamarin-dotnet/android-todo-screen.png)

El código de la solución ToDoItems contiene:

* ToDoItems.Core: es un proyecto estándar de .NET que contiene un proyecto de Xamarin.Forms y el código de lógica de aplicaciones compartido que mantiene los elementos de lista de tareas en Azure Cosmos DB.
* ToDoItems.Android: este proyecto contiene la aplicación de Android.
* ToDoItems.Android: este proyecto contiene la aplicación iOS.

Ahora vamos a examinar rápidamente cómo se comunica la aplicación con Azure Cosmos DB.

* Es preciso que el paquete NuGet [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) se agregue a todos los proyectos.
* La clase `ToDoItem` de la carpeta azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Models modela los documentos de la colección **Items** creada anteriormente. Tenga en cuenta que la nomenclatura de la propiedad distingue mayúsculas de minúsculas.
* La clase `CosmosDBService` de la carpeta azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Services encapsula la comunicación con Azure Cosmos DB.
* En la clase `CosmosDBService` hay una variable del tipo `DocumentClient`. `DocumentClient` se usa para configurar y ejecutar solicitudes en la cuenta de Azure Cosmos DB y se crea una instancia en la línea 31:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Al consultar una colección de documentos, se usa el método `DocumentClient.CreateDocumentQuery<T>`, como se muestra a continuación en la función `CosmosDBService.GetToDoItems`:

    ```csharp
    public async static Task<List<ToDoItem>> GetToDoItems()
    {
        var todos = new List<ToDoItem>();

        var todoQuery = docClient.CreateDocumentQuery<ToDoItem>(
                                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                                .Where(todo => todo.Completed == false)
                                .AsDocumentQuery();

        while (todoQuery.HasMoreResults)
        {
            var queryResults = await todoQuery.ExecuteNextAsync<ToDoItem>();

            todos.AddRange(queryResults);
        }

        return todos;
    }
    ```

    `CreateDocumentQuery<T>` toma un identificador URI que señala a la colección creada en la sección anterior. Y también puede especificar operadores de LINQ, como una cláusula `Where`. En ese caso se devuelven únicamente los elementos de la lista de tareas que no se han completado.

    La función `CreateDocumentQuery<T>` se ejecuta de forma sincrónica y devuelve `IQueryable<T>`. Sin embargo, el método `AsDocumentQuery` convierte `IQueryable<T>` en un objeto `IDocumentQuery<T>` que se puede ejecutar de forma asincrónica. Por consiguiente no bloquea el subproceso de la interfaz de usuario para aplicaciones móviles.

    La función `IDocumentQuery<T>.ExecuteNextAsync<T>` recupera la página de resultados de Azure Cosmos DB, que `HasMoreResults` comprueba para ver si quedan resultados adicionales que devolver.

> [!TIP]
> Varias funciones que operan en los documentos y las colecciones de Azure Cosmos DB toman un URI como parámetro que especifica la dirección de la colección o el documento. Este identificador URI se construye mediante la clase `URIFactory`. Todos los identificadores URI de bases de datos, colecciones y documentos pueden crearse con esta clase.

* La función `ComsmosDBService.InsertToDoItem` de la línea 107 muestra cómo insertar un documento nuevo:

    ```csharp
    public async static Task InsertToDoItem(ToDoItem item)
    {
        ...
        await docClient.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), item);
        ...
    }
    ```

    Se especifican tanto el identificador URI de la colección de documentos como el elemento que se va a insertar.

* La función `CosmosDBService.UpdateToDoItem` de la línea 124 muestra cómo reemplazar un documento existente por otro nuevo:

    ```csharp
    public async static Task UpdateToDoItem(ToDoItem item)
    {
        ...
        var docUri = UriFactory.CreateDocumentUri(databaseName, collectionName, item.Id);

        await docClient.ReplaceDocumentAsync(docUri, item);
    }
    ```

    Aquí se necesita un nuevo URI para identificar de forma única el documento que se va a reemplazar. Se obtiene mediante `UriFactory.CreateDocumentUri` y se le pasan los nombres de base de datos y de las colecciones y el identificador del documento.

    `DocumentClient.ReplaceDocumentAsync` reemplaza el documento identificado por el identificador URI por el que se especifica como parámetro.

* La eliminación de un elemento se muestra con la función `CosmosDBService.DeleteToDoItem` de la línea 115:

    ```csharp
    public async static Task DeleteToDoItem(ToDoItem item)
    {
        ...
        var docUri = UriFactory.CreateDocumentUri(databaseName, collectionName, item.Id);

        await docClient.DeleteDocumentAsync(docUri);
    }
    ```

    Observe otra vez el identificador URI del documento único que se crea y se pasa a la función `DocumentClient.DeleteDocumentAsync`.

## <a name="run-the-app"></a>Ejecución de la aplicación

Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB.

Los pasos siguientes mostrarán cómo se ejecuta la aplicación con el depurador de Visual Studio para Mac.

> [!NOTE]
> El uso de la aplicación de la versión de Android es exactamente el mismo, las diferencias se indicarán en los pasos siguientes. Si desea realizar la depuración con Visual Studio en Windows, la documentación para hacerlo se puede encontrar para [iOS aquí](https://docs.microsoft.com/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) y para [Android aquí](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/).

1. En primer lugar, seleccione la plataforma de destino, para lo que debe hacer clic en la lista desplegable resaltada y seleccionar ToDoItems.iOS para iOS o ToDoItems.Android para Android.

    ![Selección de una plataforma para depurar en Visual Studio para Mac](./media/create-sql-api-xamarin-dotnet/ide-select-platform.png)

2. Para iniciar la depuración de la aplicación, presione cmd + Entrar o haga clic en el botón de reproducir.

    ![Inicio de la depuración en Visual Studio para Mac](./media/create-sql-api-xamarin-dotnet/ide-start-debug.png)

3. Cuando el simulador de iOS o el emulador Android acaben de iniciarse, la aplicación mostrará 2 pestañas en la parte inferior de la pantalla, en el caso de iOS, y en la parte superior de la pantalla en el caso de Android. La primera muestra los elementos de las lista de tareas que no se han completado y la segunda los que se han completado.

    ![Pantalla de inicio de aplicación de lista de tareas](./media/create-sql-api-xamarin-dotnet/ios-droid-started.png)

4. Para completar un elemento de la lista de tareas en iOS, deslícelo hacia la izquierda > pulse el botón **Complete** (Completar). Para completar un elemento de la lista de tareas en Android, mantenga presionado el elemento > pulse el botón Complete (Completar).

    ![Completar un elemento de la lista de tareas](./media/create-sql-api-xamarin-dotnet/simulator-complete.png)

5. Para editar un elemento de la lista de tareas > pulse en el elemento > aparece una nueva pantalla que le permite escribir nuevos valores. Al pulsar el botón de guardar, los cambios realizados se guardarán en Azure Cosmos DB.

    ![Editar un elemento de la lista de tareas](./media/create-sql-api-xamarin-dotnet/simulator-edit.png)

6. Para agregar un elemento a la lista de tareas > pulse el botón **Add** (Agregar) de la esquina superior derecha de la pantalla principal > aparecerá una nueva página de edición en blanco.

    ![Agregar un elemento de la lista de tareas](./media/create-sql-api-xamarin-dotnet/simulator-add.png)

## <a name="review-slas-in-the-azure-portal"></a>Revisión de los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal:

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó.
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a crear una cuenta de Azure Cosmos DB, crear una colección mediante el Explorador de datos y compilar e implementar una aplicación Xamarin. Ahora puede importar datos adicionales en la cuenta de Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importación de datos a Azure Cosmos DB](import-data.md)
