---
title: archivo de inclusión
description: archivo de inclusión
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 690c5f07a2b0f7a88e16f0b0bbbaa9ca78e37317
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31429265"
---
1. En una nueva ventana, inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **Crear un recurso**, luego en **Bases de datos** y, finalmente, en **Azure Cosmos DB**, haga clic en **Crear**.
   
   ![Captura de pantalla de Azure Portal, donde se resaltan Más servicios y Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. En la hoja **Nueva cuenta**, especifique **MongoDB** como la API y rellene la configuración que desee para la cuenta de Azure Cosmos DB.
 
    * **ID** tiene que ser un nombre único elegido para identificar la cuenta de Azure Cosmos DB. Solo se admiten minúsculas, números y el carácter "-", y tiene que tener entre 3 y 50 caracteres.
    * **Suscripción** es su suscripción a Azure. Se rellenará automáticamente.
    * **Grupo de recursos** es el grupo de recursos para la cuenta de Azure Cosmos DB. Seleccione **Crear nuevo** y, después, escriba un nombre nuevo de grupo de recursos para la cuenta. Para simplificar, puede usar el mismo nombre del identificador.
    * **Ubicación** es la ubicación geográfica donde se encuentra la instancia de Azure Cosmos DB. Elija la ubicación más cercana a los usuarios.

    A continuación, haga clic en **Crear**.

    ![Página de la nueva cuenta de Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. La cuenta tarda unos minutos en crearse. Espere a que el portal muestre la página **¡Enhorabuena! Su cuenta de Azure Cosmos DB con la API MongoDB está lista**.

    ![El panel de las notificaciones de Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
