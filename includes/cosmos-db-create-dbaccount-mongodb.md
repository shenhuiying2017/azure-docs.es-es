---
title: Creación de una cuenta de API de MongoDB de Azure Cosmos DB
description: Se describe cómo crear una cuenta de API de MongoDB de Azure Cosmos DB en Azure Portal
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
1. En una nueva ventana, inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **Crear un recurso**, luego en **Bases de datos** y, finalmente, en **Azure Cosmos DB**, haga clic en **Crear**.
   
   ![Captura de pantalla de Azure Portal, donde se resaltan Más servicios y Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. En la hoja **Nueva cuenta**, especifique **MongoDB** como la API y rellene la configuración que desee para la cuenta de Azure Cosmos DB.
 
    ![Captura de pantalla de la hoja Nueva cuenta de Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * **ID** tiene que ser un nombre único elegido para identificar la cuenta de Azure Cosmos DB. Solo se admiten minúsculas, números y el carácter "-", y tiene que tener entre 3 y 50 caracteres.
    * **Suscripción** es su suscripción a Azure. Se rellenará automáticamente.
    * **Grupo de recursos** es el grupo de recursos para la cuenta de Azure Cosmos DB.
    * **Ubicación** es la ubicación geográfica donde se encuentra la instancia de Azure Cosmos DB. Elija la ubicación más cercana a los usuarios.

4. Haga clic en **Crear** para crear la cuenta.
5. En la barra de herramientas, haga clic en **Notificaciones** para supervisar el proceso de implementación.

    ![Notificación Implementación iniciada](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  Una vez completada la implementación, abra la nueva cuenta desde el icono Todos los recursos. 

    ![Cuenta de Azure Cosmos DB en el icono All Resources (Todos los recursos)](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
