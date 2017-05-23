---
title: "Compilación de una aplicación de .NET de Azure Cosmos DB mediante API Graph | Microsoft Docs"
description: "Introducción a Tables API para Azure Cosmos DB mediante .NET"
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 9bbf188b0080b8b1cf71423023645f54f1f823e5
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: Compilación de una aplicación de .NET mediante Table API

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y gráficos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escalado horizontal en Azure Cosmos DB. 

En esta guía de inicio rápido se muestra cómo crear una cuenta de Azure Cosmos DB y una tabla dentro de esa cuenta mediante Azure Portal. A continuación, escribirá código para insertar, actualizar, eliminar entidades y ejecutar algunas consultas. Puede descargar el [SDK de la versión preliminar de Azure Storage](https://aka.ms/premiumtablenuget) en NuGet, que tiene las mismas firmas de método y clases que el [SDK de Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage), pero que también se puede conectar a cuentas de Azure Cosmos DB mediante [Table API](table-introduction.md) (versión preliminar). 

## <a name="prerequisites"></a>Requisitos previos

Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmosdb-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adición de una tabla

[!INCLUDE [cosmosdb-create-table](../../includes/cosmosdb-create-table.md)]

## <a name="add-sample-data"></a>Adición de datos de ejemplo

Ahora puede agregar datos a la nueva tabla mediante el Explorador de datos.

1. En el Explorador de datos, expanda **sample-database**, **sample-table**, haga clic en **Entidades**y, a continuación, haga clic en **Agregar entidad**.
2. Ahora agregue datos al cuadro de valores de PartitionKey y RowKey y haga clic en **Agregar entidad**.

   ![Creación de documentos en el Explorador de datos en Azure Portal](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    Ahora puede agregar más entidades a la tabla, editar las entidades o consultar los datos en el Explorador de datos. En el Explorador de datos también puede escalar el rendimiento y agregar procedimientos almacenados, funciones definidas por el usuario y desencadenadores a su tabla.

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de API de DocumentDB desde GitHub, a establecer la cadena de conexión y a ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como Git Bash, y `cd` en un directorio de trabajo.  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Después, abra el archivo de solución en Visual Studio. 

## <a name="review-the-code"></a>Revisión del código

Vamos a revisar rápidamente lo que sucede en la aplicación. Abra el archivo Program.cs y observe que estas líneas de código crean los recursos de Azure Cosmos DB. 

* Se inicializa CloudTableClient.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* Si no existe, se crea una nueva tabla.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* Se crea un nuevo contenedor de tabla. Observará que este código es muy similar al habitual en el SDK de Azure Table Storage. 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

## <a name="update-your-connection-string"></a>Actualización de la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de Azure Cosmos DB, en el panel de navegación izquierdo, haga clic en **Claves** y en **Claves de lectura y escritura**. Deberá usar los botones de copia del lado derecho de la pantalla para copiar el URI y la clave principal en el archivo app.config en el paso siguiente.

    ![Visualización y copia de una clave de acceso en Azure Portal, hoja Claves](./media/create-documentdb-dotnet-core/keys.png)

2. En Visual Studio, abra el archivo app.config. 

3. Copie el nombre de la cuenta de Azure Cosmos DB desde el portal y conviértalo en el valor de AccountName en el valor de cadena de PremiumStorageConnection del archivo app.config. En la captura de pantalla anterior, el nombre de cuenta es cosmos-db-quickstart. El nombre de la cuenta se muestra en la parte superior del portal.

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`

4. Después, copie el valor de la clave principal del portal y conviértalo en el valor de accountKey en PremiumStorageConnectionString. 

    `AccountKey=AUTHKEY`

5. Por último, copie el valor del identificador URI desde la página Claves del portal (mediante el botón de copia) y conviértalo en el valor de TableEndpoint de PremiumStorageConnectionString.

    `TableEndpoint=https://COSMOSDB.documents.azure.com`

    Puede dejar StandardStorageConnectionString como está.

Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Ejecución de la aplicación web

1. En Visual Studio, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y, después, haga clic en **Administrar paquetes NuGet**. 

2. En el cuadro **Examinar** de NuGet, escriba *WindowsAzure.Storage* y seleccione el cuadro **Includes prerelease** (Incluye versión preliminar). 

3. De los resultados, instale la biblioteca **WindowsAzure.Storage**. De este modo, se instalan el paquete de Table API de Azure Cosmos DB (versión preliminar) y todas las dependencias.

4. Haga clic en CTRL + F5 para ejecutar la aplicación.

    En la ventana de la consola se muestran los datos que se agregan a la tabla. Cuando el script se complete, cierre la ventana de la consola. 

Ahora puede volver al Explorador de datos y ver, consultar, modificar y trabajar con estos nuevos datos. 

## <a name="review-slas-in-the-azure-portal"></a>Revisión de los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal: 

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha obtenido información sobre cómo crear una cuenta de Azure Cosmos DB, crear una tabla mediante el Explorador de datos y ejecutar una aplicación.  Ahora ya puede consultar los datos mediante Table API.  

> [!div class="nextstepaction"]
> [Consultar mediante Table API](tutorial-query-table.md)


