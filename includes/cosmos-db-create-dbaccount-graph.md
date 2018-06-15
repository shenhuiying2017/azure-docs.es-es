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
ms.openlocfilehash: 88751efdd5aaceddeed490c95d15d82a263fa81e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31429201"
---
1. En una nueva ventana del explorador, inicie sesión en [Azure Portal](https://portal.azure.com/).

2. Haga clic en **Crear un recurso** > **Bases de datos** > **Azure Cosmos DB**.
   
   ![Panel "Bases de datos" de Azure Portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. En la página **Nueva cuenta**, especifique la configuración de la nueva cuenta de Azure Cosmos DB. 

    Configuración|Valor sugerido|DESCRIPCIÓN
    ---|---|---
    ID|*Escriba un nombre único*|Escriba un nombre único para identificar esta cuenta de Azure Cosmos DB. Como *documents.azure.com* se anexará al identificador que proporcione para crear el URI, debe usar un identificador único pero reconocible.<br><br>El identificador puede contener solo letras minúsculas, números y el carácter guion (-), y debe tener una extensión de entre 3 y 50 caracteres.
    API|Gremlin (graph)|La API determina el tipo de cuenta que se va a crear. Azure Cosmos DB proporciona cinco API que se adaptan a las necesidades de la aplicación: SQL (base de datos de documentos), Gremlin (base de datos de grafos), MongoDB (base de datos de documentos), Azure Table y Cassandra, y cada una de ellas requiere una cuenta independiente. <br><br>Seleccione **Gremlin (grafo)** porque en esta guía de inicio rápido va a crear un grafo que se puede consultar con la sintaxis de Gremlin.<br><br>[Más información acerca de Graph API](../articles/cosmos-db/graph-introduction.md)
    La suscripción|*Su suscripción*|Seleccione la suscripción de Azure que desea usar para esta cuenta de Azure Cosmos DB. 
    Grupo de recursos|Crear nuevo<br><br>*A continuación, escriba el mismo nombre único que se proporcionó anteriormente en el identificador*|Seleccione **Crear nuevo** y, después, escriba un nombre nuevo de grupo de recursos para la cuenta. Para simplificar, puede usar el mismo nombre del identificador.
    Ubicación|*Seleccione la región más cercana a los usuarios*|Seleccione la ubicación geográfica en la que se va a hospedar la cuenta de Azure Cosmos DB. Use la ubicación más cercana a los usuarios para proporcionarles el acceso más rápido a los datos.
    Habilitar redundancia geográfica| Déjelo en blanco | Esto crea una versión replicada de la base de datos en una segunda región (emparejada). Déjelo en blanco.  
    Anclar al panel | Seleccionar | Active esta casilla para que la nueva cuenta de la base de datos se agregue al panel del portal para facilitar el acceso.

    A continuación, haga clic en **Crear**.

    ![Hoja de la nueva cuenta de Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. La cuenta tarda unos minutos en crearse. Espere a que el portal muestre la página **¡Enhorabuena! Se ha creado su cuenta de Azure Cosmos DB**.

    ![El panel de las notificaciones de Azure Portal](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)