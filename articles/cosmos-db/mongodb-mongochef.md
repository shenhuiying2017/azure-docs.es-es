---
title: Uso de MongoChef para Azure Cosmos DB | Microsoft Docs
description: "Obtenga información sobre cómo usar MongoChef con una cuenta de Azure Cosmos DB: API para MongoDB."
keywords: MongoChef
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 54c9799bd646b827f602e2ea2f9a15a4fc853f00
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-mongochef-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Uso de MongoChef con una cuenta de Azure Cosmos DB: API para MongoDB

Para conectarse a una cuenta de Azure Cosmos DB: API para MongoDB, debe hacer lo siguiente:

* Descargar e instalar [MongoChef](http://3t.io/mongochef)
* Disponer de la información de la [cadena de conexión](connect-mongodb-account.md) de la cuenta de Cosmos DB: API para MongoDB

## <a name="create-the-connection-in-mongochef"></a>Crear la conexión en MongoChef
Para agregar la cuenta de Cosmos DB: API para MongoDB al administrador de conexiones de MongoChef, siga estos pasos.

1. Recupere la información de conexión de Azure Cosmos DB: API para MongoDB siguiendo [estas](connect-mongodb-account.md) instrucciones.

    ![Captura de pantalla de la hoja Cadena de conexión](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Haga clic en **Connect** (Conectar) para abrir Connection Manager (Administrador de conexiones) y, después, haga clic en **New Connection** (Nueva conexión).

    ![Captura de pantalla del administrador de conexiones de MongoChef](./media/mongodb-mongochef/ConnectionManager.png)
3. En la ventana **New Connection** (Nueva conexión), en la pestaña **Server** (Servidor), escriba el HOST (FQDN) de la cuenta de Azure Cosmos DB: API para MongoDB y el PUERTO.

    ![Captura de pantalla de la pestaña Server (Servidor) del administrador de conexiones de MongoChef](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. En la ventana **New Connection** (Nueva conexión), en la pestaña **Authentication** (Autenticación), elija el modo de autenticación **Standard (MONGODB-CR or SCARM-SHA-1)** (Estándar [MONGODB-CR o SCARM-SHA-1]) y escriba el NOMBRE DE USUARIO y la CONTRASEÑA.  Acepte la base de datos de autenticación predeterminada (admin) o proporcione su propio valor.

    ![Captura de pantalla de la pestaña Authentication (Autenticación) del administrador de conexiones de MongoChef](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. En la ventana **New Connection** (Nueva conexión), en la pestaña **SSL**, active la casilla **Use SSL protocol to connect** (Usar protocolo SSL para conectar) y el botón de radio **Accept server self-signed SSL certificates** (Aceptar certificados SSL autofirmados del servidor).

    ![Captura de pantalla de la pestaña SSL del administrador de conexiones de MongoChef](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Haga clic en el botón **Test Connection** (Probar conexión) para validar la información de conexión, haga clic en **OK** (Aceptar) para volver a la ventana de la nueva conexión y, finalmente, haga clic en **Save** (Guardar).

    ![Captura de pantalla de la ventana de conexión de prueba de MongoChef](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>Uso de MongoChef para crear una base de datos, una colección y unos documentos
Para crear una base de datos, una colección y unos documentos mediante MongoChef, realice los pasos siguientes.

1. En **Connection Manager** (Administrador de conexiones), resalte la conexión y haga clic en **Connect** (Conectar).

    ![Captura de pantalla del administrador de conexiones de MongoChef](./media/mongodb-mongochef/ConnectToAccount.png)
2. Haga clic con el botón derecho en el host y elija **Add Database**(Agregar base de datos).  Especifique un nombre de base de datos y haga clic en **OK**(Aceptar).

    ![Captura de pantalla de la opción Add Database (Agregar base de datos) de MongoChef](./media/mongodb-mongochef/AddDatabase1.png)
3. Haga clic con el botón derecho en la base de datos y elija **Add Collection**(Agregar colección).  Especifique un nombre para la colección y haga clic en **Create**(Create).

    ![Captura de pantalla de la opción Add Collection (Agregar colección) de MongoChef](./media/mongodb-mongochef/AddCollection.png)
4. Haga clic en el elemento de menú **Collection** (Colección) y en **Add Document** (Agregar documento).

    ![Captura de pantalla del elemento de menú Add Document (Agregar documento) de MongoChef](./media/mongodb-mongochef/AddDocument1.png)
5. En el cuadro de diálogo Add Document (Agregar documento), pegue lo siguiente y haga clic en **Add Document**(Agregar documento).

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Agregue otro documento, esta vez con el contenido siguiente.

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Ejecute una consulta de ejemplo. Por ejemplo, busque familias con el apellido 'Andersen' y devuelva los campos parents (padres) y state (estado).

    ![Captura de pantalla de resultados de la consulta de MongoChef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Pasos siguientes
* Explore [ejemplos](mongodb-samples.md) de Azure Cosmos DB: API para MongoDB.
