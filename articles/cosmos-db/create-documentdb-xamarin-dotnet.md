---
title: "Azure Cosmos DB: Compilación de una aplicación web con la autenticación de Xamarin y Facebook | Microsoft Docs"
description: "En este tema se incluye un ejemplo de código .NET que puede usar para conectarse a Azure Cosmos DB y realizar consultas"
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
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: ed1d30be780e0882b8e75c01bc7822c3350f3fee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-web-app-with-net-xamarin-and-facebook-authentication"></a>Azure Cosmos DB: Compilación de una aplicación web con la autenticación de .NET, Xamarin y Facebook

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y grafos, y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escalado horizontal en Azure Cosmos DB. 

En esta guía de inicio rápido se muestra cómo crear una cuenta, una base de datos de documentos y una colección de Azure Cosmos DB mediante Azure Portal. Podrá compilar e implementar una aplicación web de lista de tareas pendientes integrada en el motor de autorizaciones de [API de .NET de DocumentDB](documentdb-sdk-dotnet.md), [Xamarin](https://www.xamarin.com/) y Azure Cosmos DB. La aplicación web de lista de tareas pendientes implementa un patrón de datos por usuario que permite a los usuarios iniciar sesión con la autenticación de Facebook y administrar sus propias tareas pendientes.

## <a name="prerequisites"></a>Requisitos previos

Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Incorporación de una colección

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de API de DocumentDB desde GitHub, a establecer la cadena de conexión y a ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como Git Bash, y `cd` en un directorio de trabajo.  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure/azure-documentdb-dotnet.git
    ```

3. A continuación, abra el archivo DocumentDBTodo.sln desde la carpeta samples/xamarin/UserItems/xamarin.forms en Visual Studio. 

## <a name="review-the-code"></a>Revisión del código

El código de la carpeta Xamarin contiene:

* Aplicación de Xamarin. La aplicación almacena las tareas pendientes del usuario en una colección con particiones denominada UserItems.
* Resource token broker API. Una sencilla API web de ASP.NET para negociar los tokens de recursos de Azure Cosmos DB para los usuarios de la aplicación que han iniciado sesión. Los tokens de recursos son tokens de acceso de corta duración que proporcionan a la aplicación acceso a los datos del usuario que ha iniciado sesión.

El flujo de autenticación y de datos se ilustra en el siguiente diagrama.

* La colección UserItems se crea con la clave de partición '/userid'. Al especificar una clave de partición para una colección se permite a Azure Cosmos DB escalarse infinitamente a medida que aumenta el número de usuarios y elementos.
* La aplicación de Xamarin permite a los usuarios iniciar sesión con las credenciales de Facebook.
* La aplicación de Xamarin usa el token de acceso de Facebook para autenticarse con ResourceTokenBroker API
* ResourceTokenBroker API autentica la solicitud mediante la característica de autenticación de App Service y solicita un token de recurso de Azure Cosmos DB con acceso de lectura y escritura a todos los documentos que comparten la clave de partición autenticada del usuario.
* ResourceTokenBroker API devuelve el token de recurso a la aplicación cliente.
* La aplicación accede a las tareas pendientes del usuario con el token de recurso.

![Aplicación de tareas pendientes con datos de ejemplo](./media/create-documentdb-xamarin-dotnet/tokenbroker.png)
    
## <a name="update-your-connection-string"></a>Actualización de la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de Azure Cosmos DB, en el panel de navegación izquierdo, haga clic en **Claves** y en **Claves de lectura y escritura**. Deberá usar los botones de copia del lado derecho de la pantalla para copiar el URI y la clave principal en el archivo Web.config en el paso siguiente.

    ![Visualización y copia de una clave de acceso en Azure Portal, hoja Claves](./media/create-documentdb-xamarin-dotnet/keys.png)

2. En Visual Studio 2017, abra el archivo Web.config en la carpeta de azure-documentdb-dotnet/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker. 

3. Copie el valor del URI del portal (con el botón de copia) y conviértalo en el valor de accountUrl en el archivo Web.config. 

    `<add key="accountUrl" value="{Azure Cosmos DB account URL}"/>`

4. Después, copie el valor de la clave principal del portal y conviértalo en el valor de accountKey en el archivo Web.config. 

    `<add key="accountKey" value="{Azure Cosmos DB secret}"/>`

Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

## <a name="build-and-deploy-the-web-app"></a>Compilación e implementación de la aplicación web

1. En Azure Portal, cree un sitio web de App Service para hospedar la Resource token broker API.
2. En Azure Portal, abra la hoja de configuración de la aplicación del sitio web de Resource token broker API. Rellene los siguientes parámetros de la aplicación:

    * accountUrl: dirección URL de la cuenta de Azure Cosmos DB desde la pestaña Claves de esta cuenta.
    * accountKey: la clave maestra de la cuenta de Azure Cosmos DB desde la pestaña Claves de esta cuenta.
    * databaseId y collectionId de la base de datos y de la colección creadas

3. Publique la solución ResourceTokenBroker en el sitio web creado.

4. Abra el proyecto de Xamarin y vaya a TodoItemManager.cs. Rellene los valores para accountURL, collectionId, databaseId, así como resourceTokenBrokerURL como dirección URL HTTPS base para el sitio web de ResourceTokenBroker.

5. Complete el tutorial [Configuración de la aplicación de App Service para usar el inicio de sesión de Facebook](../app-service/app-service-mobile-how-to-configure-facebook-authentication.md) para configurar la autenticación de Facebook y configurar el sitio web de ResourceTokenBroker.

    Ejecute la aplicación Xamarin.

## <a name="review-slas-in-the-azure-portal"></a>Revisión de los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal: 

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que acaba de crear. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a crear una cuenta de Azure Cosmos DB, crear una colección mediante el Explorador de datos y compilar e implementar una aplicación Xamarin. Ahora puede importar datos adicionales en la cuenta de Cosmos DB. 

> [!div class="nextstepaction"]
> [Importación de datos a Azure Cosmos DB](import-data.md)
