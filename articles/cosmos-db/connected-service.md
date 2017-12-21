---
title: Servicio conectado de Visual Studio para Azure Cosmos DB
description: "Permite a los desarrolladores conectarse fácilmente a su cuenta de Azure Cosmos DB y administrar recursos a través de Servicios conectados de Visual Studio."
services: cosmos-db
documentationcenter: 
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: f63c20831a2700f1024507bef614a5a7cc87d00e
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: Servicio conectado de Visual Studio (versión preliminar)

Servicios conectados de Visual Studio permite a los desarrolladores a conectarse fácilmente a su cuenta de Azure Cosmos DB y administrar sus recursos.

También puede usar el Explorador de datos en el servicio conectado para crear procedimientos almacenados, UDF y desencadenadores para realizar la lógica de negocios del servidor. El Explorador de datos expone todo el acceso a datos mediante programación integrado que está disponible en las API, pero permite un acceso fácil a los datos.

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/). 
* Una cuenta de Azure Cosmos DB. Si aún no tiene una, siga los pasos descritos en el artículo para [crear una cuenta de Azure Cosmos DB](create-sql-api-dotnet.md) para crear una en Azure Portal o la sección [Creación de una cuenta de Azure Cosmos DB en la herramienta de servicio conectado](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool). 
* Si desea usar un entorno local con fines de desarrollo, puede utilizar el [emulador de Azure Cosmos DB](local-emulator.md). El entorno emula el servicio de Azure Cosmos DB.
* [Visual Studio](http://www.visualstudio.com/).
* Los bits más recientes del servicio conectado de Azure Cosmos DB. Puede descargar el servicio conectado de Azure Cosmos DB desde Visual Studio Marketplace, tal como se muestra en la siguiente captura de pantalla. Abra **Visual Studio** en el equipo. En el menú **Herramientas**, seleccione **Extensiones y actualizaciones...**  y, después, elija **En línea** / **Visual Studio Marketplace**. Escriba **cosmosdb** para buscar los bits.

    También puede instalar el servicio conectado de Azure Cosmos DB desde [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709).

    ![Captura de pantalla de la descarga del servicio conectado bits.png](./media/connected-service/connected-service-downloadbits.png) 

## <a id="SetupVS"></a>Configuración de la solución de Visual Studio
1. Abra **Visual Studio** en el equipo.
2. En el menú **Archivo**, seleccione **Nuevo** y elija **Proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Visual C#** / **Aplicación de consola (.NET Framework)** o **WPF App (.NET Framework)**, asigne un nombre al proyecto y haga clic en **Aceptar**.

    ![Captura de pantalla de la ventana Nuevo proyecto](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>Adición del servicio conectado y adición de cuenta
1. En el Explorador de soluciones, haga clic con el botón derecho en el nodo del proyecto y seleccione **Agregar** / **Servicio conectado**. O bien haga clic en el menú **Proyecto** y después seleccione **Agregar servicio conectado**.

    ![Captura de pantalla de la ventana Agregar servicio conectado](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. En la página de servicio conectado, haga clic en **Servicios conectados** / **Azure Cosmos DB** para abrir la página **Azure Cosmos DB**.

    ![Captura de pantalla de ventana Azure Cosmos DB](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Haga clic en la flecha abajo para iniciar sesión por primera vez o agregar una cuenta. Después de inicio de sesión, se muestran todas las cuentas de Azure Cosmos DB en el área en blanco. Elija una cuenta de Azure Cosmos DB para agregar al proyecto.

    ![Captura de pantalla de la ventana de inicio de sesión y cuenta DB agregada](./media/connected-service/connected-service-add-db-account.png)
4. Después de agregar una cuenta de Azure Cosmos DB, se agrega una carpeta del servicio conectado de Azure Cosmos DB al proyecto. Para agregar más de una cuenta de Azure Cosmos DB, repita del paso 1 al 3.

    ![Captura de pantalla de la ventana de la carpeta del servicio conectado](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Cuando haya agregado un servicio conectado de Azure Cosmos DB, modifique el proyecto para habilitar el acceso a Azure Cosmos DB de una de las maneras siguientes:

    * Se instalan algunos paquetes nuget que requiere el cliente de Azure Cosmos DB. Puede verlos en el archivo de configuración de paquetes. 

        ![La nueva configuración de paquetes de Azure Cosmos DB](./media/connected-service/connected-service-packages-config.png)   
    
    * El URI de conexión y la clave de Azure Cosmos DB se agregan al archivo de configuración de proyecto, en este caso, App.config. 

        ![Nueva configuración de la aplicación Azure Cosmos DB](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Apertura del Explorador de Azure Cosmos DB
1. Haga clic con el botón derecho en el nodo del proyecto y seleccione **Abrir Explorador de Cosmos DB...**.

    ![Captura de pantalla del intento de abrir la ventana del Explorador de datos](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. En la página **Elegir una cuenta de Cosmos DB**, haga clic en la lista desplegable para seleccionar una cuenta de Azure Cosmos DB.

    ![Captura de pantalla de la ventana Servicio conectado de cuenta agregado](./media/connected-service/connected-service-open-explorer.png)
3. Haga clic en **Abrir** y se muestra la ventana del Explorador de datos.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Creación de una cuenta de Azure Cosmos DB en la herramienta de servicio conectado
1. En la página de servicio conectado, en la parte inferior izquierda del panel, haga clic en **Crear nueva cuenta de Cosmos DB** para abrir la página **Crear cuenta de Cosmos DB**.

    ![Captura de pantalla de la ventana de punto de entrada de abrir la cuenta de Azure Cosmos DB](./media/connected-service/connected-service-click-new-db-account.png)
2. En la página **Crear cuenta de Cosmos DB**, especifique la configuración que quiere para la cuenta de Azure Cosmos DB.

    Rellene los campos en la página **Crear cuenta de Cosmos DB** mediante la información de la captura de pantalla siguiente como referencia. 
 
    ![La nueva página de Azure Cosmos DB](./media/connected-service/connected-service-create-new-account.png)        
3. Haga clic en **Crear** para crear la cuenta.

## <a name="use-data-explorer"></a>Uso del Explorador de datos

Después de abrir el Explorador de datos, puede hacer lo siguiente:
* Crear y eliminar bases de datos
* Crear y eliminar colecciones
* Crear, eliminar y filtrar documentos
* Crear y eliminar procedimientos almacenados
* Cree y elimine desencadenadores y funciones definidas por el usuario para realizar lógica de negocios de servidor. 

![La nueva página de Azure Cosmos DB](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Demostración

Vea el siguiente vídeo para aprender a usar el servicio conectado de Azure Cosmos DB en Visual Studio: [Use Azure Cosmos DB Connected Service in Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711) (Uso del servicio conectado de Azure Cosmos DB en Visual Studio).

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido lo siguiente:

> [!div class="checklist"]
> * Creación de una cuenta de Azure Cosmos DB
> * Adición del servicio conectado y adición de cuenta
> * Apertura del Explorador de Azure Cosmos DB
> * Uso del Explorador de datos

Ahora que tiene Servicios conectados en marcha con su cuenta de Azure Cosmos DB, continúe con uno de los tutoriales para empezar a desarrollar la solución:

* [Develop with the SQL API in .NET](tutorial-develop-sql-api-dotnet.md) (Desarrollo con la API de SQL en .NET).
* [Azure Cosmos DB: SQL API getting started tutorial](sql-api-get-started.md) (Azure Cosmos DB: tutorial de introducción a la API de SQL).
* ¿Desea realizar pruebas de escala y de rendimiento con Azure Cosmos DB? Consulte [Pruebas de escala y rendimiento con Azure Cosmos DB](performance-testing.md).
* Información acerca de cómo [supervisar una cuenta de Azure Cosmos DB](monitor-accounts.md).

