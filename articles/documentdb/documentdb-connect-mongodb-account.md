---
title: "Cadena de conexión de MongoDB para la cuenta de Azure Cosmos DB | Microsoft Docs"
description: "Aprenda a conectar su aplicación de MongoDB a una cuenta de Azure Cosmos DB mediante una cadena de conexión de MongoDB."
keywords: "cadena de conexión de mongodb"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 95fe71c87a3f70174b59cc866d7d399b7e91720c
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Conectar una aplicación de MongoDB a Azure Cosmos DB
Aprenda a conectar su aplicación de MongoDB a una cuenta de Azure Cosmos DB mediante una cadena de conexión de MongoDB. Al conectar la aplicación de MongoDB a una base de datos de Azure Cosmos DB, puede usar una base de datos de Azure Cosmos DB como almacén de datos de la aplicación de MongoDB. 

En este tutorial se proporcionan dos maneras de recuperar información de la cadena de conexión:

- [El método de inicio rápido](#QuickstartConnection), para su uso con controladores de .NET, Node.js, MongoDB Shell, Java y Python.
- [El método de la cadena de conexión personalizada](#GetCustomConnection), para su uso con otros controladores.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Si no tiene una cuenta de Azure, cree ahora una [cuenta de Azure gratuita](https://azure.microsoft.com/free/). 
- Una cuenta de Azure Cosmos DB. Para obtener instrucciones, vea [Create an Azure Cosmos DB account for use with MongoDB apps](documentdb-create-mongodb-account.md) (Creación de una cuenta de Azure Cosmos DB para su uso con aplicaciones de MongoDB).

## <a id="QuickstartConnection"></a>Obtención de la cadena de conexión de MongoDB utilizando el menú de inicio rápido
1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **Azure Cosmos DB**, seleccione la cuenta de MongoDB API. 
3. En la barra de **navegación izquierda** de la hoja de la cuenta, haga clic en **Inicio rápido**. 
4. Elija la plataforma (*controlador .NET*, *controlador Node.js*, *MongoDB Shell*, *controlador Java*, *controlador Python*). Si no ve el controlador o la herramienta en la lista, no se preocupe, documentamos constantemente más fragmentos de código de conexión. Comente a continuación lo que le gustaría ver y leer sobre [obtener información de la cadena de conexión de la cuenta](#GetCustomConnection) para aprender a crear su propia conexión.
5. Copie y pegue el fragmento de código en la aplicación de MongoDB y ya estará listo para empezar.

    ![Captura de pantalla de la hoja de inicio rápido](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obtención de la cadena de conexión de MongoDB para personalizar
1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **Azure Cosmos DB**, seleccione la cuenta de MongoDB API. 
3. En la barra de **navegación izquierda** de la hoja de la cuenta, haga clic en **Cadena de conexión**. 
4. Se abre la hoja **Información de cadena de conexión** con toda la información necesaria para conectarse a la cuenta con un controlador para MongoDB, incluida una cadena de conexión precreada.

    ![Captura de pantalla de la hoja Cadena de conexión](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de la cadena de conexión
> [!Important]
> Azure Cosmos DB tiene estándares y requisitos de seguridad estrictos. Las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura a través de **SSL**.
>
>

Es importante tener en cuenta que Azure Cosmos DB admite el formato estándar de URI de cadena de conexión de MongoDB, con un par de requisitos específicos: las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura mediante SSL.  Por tanto, el formato de la cadena de conexión es:

    mongodb://username:password@host:port/[database]?ssl=true

Donde los valores de esta cadena están disponibles en la hoja Cadena de conexión mostrada antes.

* Username (obligatorio)
  * Nombre de la cuenta de Azure Cosmos DB
* Password (obligatorio)
  * Contraseña de la cuenta de Azure Cosmos DB
* Host (obligatorio)
  * FQDN de la cuenta de Azure Cosmos DB
* Port (obligatorio)
  * 10250
* Database (opcional)
  * La base de datos predeterminada utilizada por la conexión (si no se especifica ninguna base de datos, el valor predeterminado es "test")
* ssl=true (obligatorio)

Por ejemplo, considere la cuenta que aparece en la hoja Información de cadena de conexión mostrada antes.  Una cadena de conexión válida es:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de Azure Cosmos DB: API para MongoDB.
* Explore [ejemplos](documentdb-mongodb-samples.md) de Azure Cosmos DB: API para MongoDB.

