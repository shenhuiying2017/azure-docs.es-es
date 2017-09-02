---
title: "Compilación de una aplicación de .NET de Azure Cosmos DB mediante API Graph | Microsoft Docs"
description: "Introducción a Tables API para Azure Cosmos DB mediante .NET"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/22/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 29e7eebda5177d6e852ef04ad82d9d38a8d30ed8
ms.contentlocale: es-es
ms.lasthandoff: 06/23/2017

---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: Compilación de una aplicación de .NET mediante Table API

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y gráficos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escalado horizontal en Azure Cosmos DB. 

En esta guía de inicio rápido se muestra cómo crear una cuenta de Azure Cosmos DB y una tabla dentro de esa cuenta mediante Azure Portal. A continuación, podrá escribir código para insertar, actualizar y eliminar entidades, y ejecutar algunas consultas mediante el nuevo paquete [Premium Table de Windows Azure Storage](https://aka.ms/premiumtablenuget) de NuGet (versión preliminar). Esta biblioteca tiene las mismas firmas de método y clases que el [SDK de Windows Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage) público, pero también se puede conectar a cuentas de Azure Cosmos DB mediante [Table API](table-introduction.md) (versión preliminar). 

## <a name="prerequisites"></a>Requisitos previos

Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adición de una tabla

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Adición de datos de ejemplo

Ahora puede agregar datos a la nueva tabla mediante el Explorador de datos (versión preliminar).

1. En el Explorador de datos, expanda **sample-table** y haga clic en **Entidades** y en **Agregar entidad**.

   ![Creación de nuevas entidades en el Explorador de datos en Azure Portal](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Ahora agregue datos al cuadro de valores de PartitionKey y RowKey y haga clic en **Agregar entidad**.

   ![Establecimiento de la clave de partición y la clave de fila para una nueva entidad](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Ahora puede agregar más entidades a la tabla, editar las entidades o consultar los datos en el Explorador de datos. En el Explorador de datos también puede escalar el rendimiento y agregar procedimientos almacenados, funciones definidas por el usuario y desencadenadores a su tabla.

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de Table desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

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

Ahora se actualizará la información de la cadena de conexión para que la aplicación pueda comunicarse con Azure Cosmos DB. 

1. En Visual Studio, abra el archivo app.config. 

2. En [Azure Portal](http://portal.azure.com/), en el menú de navegación izquierdo de Azure Cosmos DB, haga clic en **Cadena de conexión**. A continuación, en el nuevo panel, haga clic en el botón Copiar para la cadena de conexión. 

    ![Visualización y copia del punto de conexión y la clave de cuenta en el panel Cadena de conexión](./media/create-table-dotnet/keys.png)

3. Pegue el valor en el archivo app.config como valor de PremiumStorageConnectionString. 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    Puede dejar StandardStorageConnectionString como está.

Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Ejecución de la aplicación web

1. En Visual Studio, haga clic con el botón derecho en el proyecto **PremiumTableGetStarted** en el **Explorador de soluciones** y haga clic en **Administrar paquetes NuGet**. 

2. En el cuadro **Examinar** de NuGet, escriba *WindowsAzure.Storage-PremiumTable*.

3. Active la casilla **Incluir versión preliminar**. 

4. Desde los resultados, instale la biblioteca **WindowsAzure.Storage-PremiumTable**. De este modo, se instalan el paquete de Table API de Azure Cosmos DB (versión preliminar) y todas las dependencias. Tenga en cuenta que se trata de un paquete de NuGet diferente del paquete de Windows Azure Storage que se usa para Azure Table Storage. 

5. Haga clic en CTRL + F5 para ejecutar la aplicación.

    La ventana de consola muestra los datos que se están agregando, recuperando, consultando, reemplazando y eliminando de la tabla. Cuando se complete el script, presione cualquier tecla para cerrar la ventana de la consola. 
    
    ![Salida de la consola del tutorial rápido](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. Si desea ver las nuevas entidades en el Explorador de datos, convierta en comentario las líneas de 188 a 208 en program.cs, para que no se eliminen, y vuelva a ejecutar el ejemplo. 

    Ahora vuelva al Explorador de datos, haga clic en **Actualizar**, expanda la tabla **people**, haga clic en **Entidades** y trabaje con estos nuevos datos. 

    ![Nuevas entidades en el Explorador de datos](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Revisión de los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal: 

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha obtenido información sobre cómo crear una cuenta de Azure Cosmos DB, crear una tabla mediante el Explorador de datos y ejecutar una aplicación.  Ahora ya puede consultar los datos mediante Table API.  

> [!div class="nextstepaction"]
> [Consultar mediante Table API](tutorial-query-table.md)


