---
title: "Administración de Azure Cosmos DB en el Explorador de Azure Storage"
description: Aprenda a administrar Azure Cosmos DB en el Explorador de Azure Storage.
Keywords: Azure Cosmos DB, Explorador de Azure Storage, DocumentDB, MongoDB, DocumentDB
services: cosmos-db
documentationcenter: 
author: Jiaj-Li
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: Jiaj-Li
ms.openlocfilehash: 2cd3656156b77c71be85a1a18567232f4466fc68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Administración de Azure Cosmos DB en el Explorador de Azure Storage (versión preliminar)

El uso de Azure Cosmos DB en el Explorador de Azure Storage permite a los usuarios administrar entidades de Azure Cosmos DB, manipular datos, actualizar procedimientos y desencadenadores almacenados, además de otras entidades de Azure, como los blobs y las colas de Storage. Ahora puede usar la misma herramienta para administrar las diferentes entidades de Azure en un solo lugar. En este momento, el Explorador de Azure Storage admite cuentas de SQL (DocumentDB) y MongoDB.

En este artículo, aprenderá a usar el Explorador de Storage para administrar Azure Cosmos DB.


## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure Cosmos DB para una base de datos de MongoDB o SQL (DocumentDB). Si no tiene una cuenta, puede crear una en Azure Portal, tal y como se describe en [Azure Cosmos DB: Compilar una aplicación web de API DocumentDB con .NET y Azure Portal](create-documentdb-dotnet.md).
- Instale la versión más reciente del Explorador de Azure Storage. También puede instalarla a través de estos vínculos: [Linux](https://go.microsoft.com/fwlink/?linkid=858559), [Mac](https://go.microsoft.com/fwlink/?linkid=858561), [Windows](https://go.microsoft.com/fwlink/?linkid=858562).

## <a name="connect-to-an-azure-subscription"></a>Conexión a una suscripción de Azure

1. Después de instalar el **Explorador de Azure Storage**, haga clic en el icono de **complemento** situado a la izquierda como se muestra en la imagen de abajo.
       
   ![Icono de complemento](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. Seleccione **Add an Azure Account** (Agregar una cuenta de Azure) y haga clic en **Iniciar sesión**.

   ![Conectarse a una suscripción de Azure](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. En el cuadro de diálogo **Inicio de sesión en Microsoft Azure**, seleccione **Iniciar sesión** y luego escriba las credenciales de Azure.

    ![Iniciar sesión](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. Seleccione la suscripción en la lista y luego haga clic en **Aplicar**.

    ![Aplicar](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    El panel del Explorador se actualiza y muestra las cuentas en la suscripción seleccionada.

    ![Lista de cuentas](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    Se ha conectado correctamente a su **cuenta de Azure Cosmos DB** en su suscripción de Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Conectarse a Azure Cosmos DB mediante una cadena de conexión

Una manera alternativa de conectarse a Azure Cosmos DB es usar una cadena de conexión. Siga estos pasos para conectarse usando una cadena de conexión.

1. Busque **Local and Attached** (Locales y adjuntas) en el árbol de la izquierda, haga clic con el botón derecho en **Cuentas de Azure Cosmos DB**, elija **Connect to Azure Cosmos DB...** (Conectar a Azure Cosmos DB).

    ![Conectarse a Azure Cosmos DB mediante una cadena de conexión](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. Elija la **Default Experience** (Experiencia predeterminada) adecuada para el tipo de cuenta, ya sea **DocumentDB** o **MongoDB**, péguela en **Cadena de conexión** y haga clic en **Aceptar** para conectarse a una cuenta de Azure Cosmos DB. Para más información sobre cómo recuperar la cadena de conexión, vea [Obtener la cadena de conexión](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Cadena de conexión](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Administración de recursos de Azure Cosmos DB

Realice estas operaciones para administrar una cuenta de Azure Cosmos DB:
* Abrir la cuenta en Azure Portal
* Agregar el recurso a la lista de acceso rápido
* Buscar y actualizar recursos
* Crear y eliminar bases de datos
* Crear y eliminar recopilaciones
* Crear, editar, eliminar y filtrar documentos
* Administrar procedimientos almacenados, desencadenadores y funciones definidas por el usuario

### <a name="quick-access-tasks"></a>Tareas de acceso rápido

Puede realizar muchas tareas de acción rápida haciendo clic con el botón derecho en una suscripción en el panel del Explorador:

* Haga clic con el botón derecho en una cuenta de Azure Cosmos DB o una base de datos, elija **Abrir en el portal** y administre los recursos en el explorador en Azure Portal.

     ![Abrir en el portal](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* También puede agregar la cuenta, la base de datos o la colección de Azure Cosmos DB al **acceso rápido**.
* **Search from Here** (Buscar desde aquí) permite realizar búsquedas de palabras clave en la ruta de acceso seleccionada.

    ![Buscar desde aquí](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Administración de bases de datos y recopilaciones
#### <a name="create-a-database"></a>Crear una base de datos 
Haga clic con el botón derecho en la cuenta de Azure Cosmos DB, elija **Crear base de datos**, escriba el nombre de la base de datos y presione **Entrar** para completar la acción.

![Crear base de datos](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Eliminación de una base de datos
Haga clic con el botón derecho en la base de datos, elija **Eliminar base de datos** y haga clic en **Sí** en la ventana emergente. Se elimina el nodo de la base de datos y la cuenta de Azure Cosmos DB se actualiza automáticamente.

![Eliminar base de datos 1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![Eliminar base de datos 2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Creación de una colección
Haga clic con el botón derecho en la base de datos, elija **Crear colección** y proporcione información como **Id. de colección**, **Capacidad de almacenamiento**, etc. Haga clic en **Aceptar** para finalizar. Para más información sobre la configuración de claves de partición, vea [Uso de las API de Azure Cosmos DB](partition-data.md#designing-for-partitioning).

Si se usa una clave de partición al crear una colección, una vez completada la creación no se podrá cambiar el valor de la clave de partición en la colección.

![Crear colección 1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![Crear colección 2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>Eliminación de una colección
Haga clic con el botón derecho en la colección, elija **Eliminar colección** y luego haga clic en **Sí** en la ventana emergente. 

Se elimina el nodo de colección y la base de datos se actualiza automáticamente.  

![Eliminar colección](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Administración de documentos

#### <a name="create-and-modify-documents"></a>Crear y modificar documentos
Para crear un documento, abra **Documentos** en la ventana izquierda, elija **Nuevo documento**, edite el contenido en el panel derecho y haga clic en **Guardar**. También puede actualizar un documento existente y luego hacer clic en **Guardar**. Para descartar los cambios, haga clic en **Descartar**.

![Documento](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>Eliminar un documento
Haga clic en el botón **Eliminar** para eliminar el documento seleccionado.
#### <a name="query-for-documents"></a>Consulta de documentos
Para editar el filtro de documento, escriba una [Consulta SQL](documentdb-sql-query.md) y haga clic en **Aplicar**.

![Filtrar](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Administrar procedimientos almacenados, desencadenadores y UDF
* Para crear un procedimiento almacenado, en el árbol de la izquierda, haga clic con el botón derecho en **Procedimiento almacenado**, elija **Crear procedimiento almacenado**, escriba un nombre en la ventana de la izquierda, escriba los scripts del procedimiento almacenado en la ventana de la derecha y luego haga clic en **Crear**. 
* Si quiere editar un procedimiento almacenado existente, haga doble clic él, modifíquelo y haga clic en **Actualizar** para guardar o en **Descartar** para cancelar el cambio.

![Procedimiento almacenado](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* Las operaciones para **desencadenadores** y **UDF** son similares a las de los **procedimientos almacenados**.

## <a name="demo"></a>Demostración
* Para ver cómo se usa Azure Cosmos DB en el Explorador de Azure Storage, vea este vídeo: [Use Azure Cosmos DB in Azure Storage Explorer](https://go.microsoft.com/fwlink/?linkid=858710) (Usar Azure Cosmos DB en el Explorador de Azure Storage).

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene el Explorador de Azure Storage conectado a su cuenta de Azure Cosmos DB, amplíe la información sobre el Explorador de Storage y la conexión de otros servicios en [Introducción al Explorador de Storage (versión preliminar)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer).

