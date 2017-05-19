---
title: "Azure Cosmos DB: Compilar una aplicación web con .NET Core y la API DocumentDB | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código de .NET Core que se puede usar para conectarse a la API DocumentDB de Azure Cosmos DB y realizar consultas."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 52cf1a729e4e86f764f9ded3712eaba558f1fc7f
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-core-and-the-azure-portal"></a>Azure Cosmos DB: Compilar una aplicación web de API DocumentDB con .NET Core y Azure Portal

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y gráficos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB. 

En esta guía de inicio rápido se muestra cómo crear una cuenta, una base de datos de documentos y una colección de Azure Cosmos DB mediante Azure Portal. Después, compilará e implementará una aplicación web de lista de tareas pendientes integrada en la [API DocumentDB de .NET Core](../documentdb/documentdb-introduction.md), tal como se muestra en la captura de pantalla siguiente. 

![Aplicación de tareas pendientes con datos de ejemplo](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

## <a name="prerequisites"></a>Requisitos previos

Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-net-core"></a>Instalar .NET Core

Instale .NET Core tal como indican las instrucciones de la página del [SDK de .NET Core](https://www.microsoft.com/net/download/core). Hay SDK disponibles para Windows, macOS y Linux.

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="add-a-collection"></a>Agregar una colección

[!INCLUDE [cosmosdb-create-collection](../../includes/cosmosdb-create-collection.md)]

## <a name="add-sample-data"></a>Agregar datos de ejemplo

[!INCLUDE [cosmosdb-create-sample-data](../../includes/cosmosdb-create-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de API DocumentDB desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación.

1. Abra una ventana de terminal de Git, como Git Bash, y `CD` en un directorio de trabajo.  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. Después, abra el archivo de solución en Visual Studio. 
    
## <a name="review-the-code"></a>Revisar el código

[!INCLUDE [cosmosdb-tutorial-review-code-dotnet](../../includes/cosmosdb-tutorial-review-code-dotnet.md)]

## <a name="update-your-connection-string"></a>Actualizar la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de Azure Cosmos DB, en el panel de navegación izquierdo, haga clic en **Claves** y en **Claves de lectura y escritura**. Deberá usar los botones de copia del lado derecho de la pantalla para copiar el URI y la clave principal en el archivo web.config en el paso siguiente.

    ![Visualización y copia de una clave de acceso en Azure Portal, hoja Claves](./media/create-documentdb-dotnet-core/keys.png)

2. En Visual Studio 2017, abra el archivo web.config. 

3. Copie el valor del URI del portal (con el botón de copia) y conviértalo en el valor de la clave de punto de conexión en web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Después, copie el valor de la clave principal del portal y conviértalo en el valor de la clave de autenticación en web.config. 

    `<add key="authKey" value="FILLME" />`

Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Ejecutar la aplicación web

1. En Visual Studio, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y, después, haga clic en **Administrar paquetes NuGet**. 

2. En el cuadro **Examinar** de NuGet, escriba *DocumentDB*.

3. En los resultados, instale la biblioteca **Microsoft.Azure.DocumentDB**. De este modo, se instalan el paquete de Azure Cosmos DB y todas las dependencias.

4. Presione Ctrl+F5 para ejecutar la aplicación. La aplicación se muestra en el explorador. 

5. Haga clic en **Crear nuevo** en el explorador y cree algunas tareas en la aplicación de tareas pendientes.

   ![Aplicación de tareas pendientes con datos de ejemplo](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

Ahora puede volver al Explorador de datos y ver, consultar, modificar y trabajar con estos nuevos datos. 

## <a name="review-slas-in-the-azure-portal"></a>Revisar los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal:

1. En el menú izquierdo de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que ha creado. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a crear una cuenta de Azure Cosmos DB, crear una colección mediante el Explorador de datos y ejecutar una aplicación web. Ahora puede importar datos adicionales en la cuenta de Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar datos en Azure Cosmos DB](../documentdb/documentdb-import-data.md)

