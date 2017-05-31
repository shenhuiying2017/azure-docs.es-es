---
title: Uso de Robomongo para Azure Cosmos DB | Microsoft Docs
description: "Obtenga información sobre cómo usar Robomongo con una cuenta de Azure Cosmos DB: API para MongoDB."
keywords: Robomongo
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bad2d57a6252bb30e4c8435c52da19c95304d8da
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Uso de Robomongo con una cuenta de Azure Cosmos DB: API para MongoDB
Para conectarse a una cuenta de Azure Cosmos DB: API para MongoDB con Robomongo, debe hacer lo siguiente:

* Descargar e instalar [Robomongo](https://robomongo.org/)
* Disponer de la información de la [cadena de conexión](documentdb-connect-mongodb-account.md) de la cuenta de Cosmos DB: API para MongoDB

## <a name="connect-using-robomongo"></a>Conectarse mediante Robomongo
Para agregar la cuenta de Azure Cosmos DB: API para MongoDB a las conexiones de MongoDB de Robomongo, siga estos pasos.

1. Recupere la información de conexión de la cuenta de Azure Cosmos DB: API para MongoDB siguiendo [estas](documentdb-connect-mongodb-account.md) instrucciones.

    ![Captura de pantalla de la hoja Cadena de conexión](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. Ejecute *Robomongo.exe*.

3. Haga clic en el botón de conexión, que está bajo **File** (Archivo), para administrar las conexiones. Después, haga clic en la opción **Create** (Create) de la ventana **MongoDB Connections**; se abrirá **Connection Settings** (Configuración de conexiones).

4. En la ventana **Connection Settings** (Configuración de conexiones), elija un nombre. Después, busque el **host** y **puerto** en la información de conexión del paso 1 y escríbalos en **Address** (Dirección) y **Port** (Puerto) respectivamente.

    ![Captura de pantalla de la funcionalidad de administración de conexiones de Robomongo](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. En la pestaña **Authentication** (Autenticación), haga clic en **Perform authentication** (Realizar autenticación). Después, escriba la base de datos (el valor predeterminado es *Admin*), el **nombre de usuario** y la **contraseña**.
Tanto el **nombre de usuario** como la **contraseña** están en la información de conexión del paso 1.

    ![Captura de pantalla de la pestaña de autenticación de Robomongo](./media/documentdb-mongodb-robomongo/authentication.png)
6. En la pestaña **SSL**, marque **Use SSL protocol** (Usar protocolo SSL) y, luego, cambie el **método de autenticación** a **Self-signed Certificate** (Certificado autofirmado).

    ![Captura de pantalla de la pestaña SSL de Robomongo](./media/documentdb-mongodb-robomongo/SSL.png)
7. Por último, haga clic en **Test** (Probar) para comprobar que puede conectarse. Después, seleccione **Save** (Guardar).

## <a name="next-steps"></a>Pasos siguientes
* Explore [ejemplos](documentdb-mongodb-samples.md) de Azure Cosmos DB: API para MongoDB.

