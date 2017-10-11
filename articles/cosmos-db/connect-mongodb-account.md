---
title: "Cadena de conexión de MongoDB para una cuenta de Azure Cosmos DB | Microsoft Docs"
description: "Aprenda a conectar su aplicación de MongoDB a una cuenta de Azure Cosmos DB mediante una cadena de conexión de MongoDB."
keywords: "cadena de conexión de mongodb"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.openlocfilehash: 5a47001705531d971d3181df9c0aa8f957168845
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Conectar una aplicación de MongoDB a Azure Cosmos DB
Aprenda a conectar su aplicación de MongoDB a una cuenta de Azure Cosmos DB mediante una cadena de conexión de MongoDB. Después, puede usar una base de datos de Azure Cosmos DB como almacén de datos de la aplicación MongoDB. 

En este tutorial se proporcionan dos maneras de recuperar información de la cadena de conexión:

- [El método de inicio rápido](#QuickstartConnection), para su uso con controladores de .NET, Node.js, MongoDB Shell, Java y Python.
- [El método de la cadena de conexión personalizada](#GetCustomConnection), para su uso con otros controladores.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Si no tiene una cuenta de Azure, cree ahora una [cuenta de Azure gratuita](https://azure.microsoft.com/free/). 
- Una cuenta de Azure Cosmos DB. Para obtener instrucciones, vea [Compilar una aplicación web de API MongoDB con .NET y Azure Portal](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Obtener la cadena de conexión de MongoDB mediante el menú de inicio rápido
1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **Azure Cosmos DB**, seleccione la API de la cuenta de MongoDB. 
3. En el panel izquierdo de la hoja de la cuenta, haga clic en **Inicio rápido**. 
4. Elija la plataforma (**.NET**, **Node.js**, **Shell de MongoDB**, **Java**, **Python**). Si no ve el controlador o la herramienta en la lista, no se preocupe, documentamos constantemente más fragmentos de código de conexión. Comente a continuación lo que le gustaría ver. Para aprender a crear su propia conexión, lea la sección sobre cómo [obtener información de la cadena de conexión de la cuenta](#GetCustomConnection).
5. Copie y pegue el fragmento de código en la aplicación MongoDB.

    ![Hoja de inicio rápido](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obtención de la cadena de conexión de MongoDB para personalizar
1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **Azure Cosmos DB**, seleccione la API de la cuenta de MongoDB. 
3. En el panel izquierdo de la hoja de la cuenta, haga clic en **Cadena de conexión**. 
4. Se abre la hoja **Cadena de conexión**, que contiene toda la información necesaria para conectarse a la cuenta con un controlador para MongoDB, incluida una cadena de conexión precreada.

    ![Hoja Cadena de conexión](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de la cadena de conexión
> [!Important]
> Azure Cosmos DB tiene estándares y requisitos de seguridad estrictos. Las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura a través de *SSL*. 
>
>

Azure Cosmos DB admite el formato estándar de URI de cadena de conexión de MongoDB, con un par de requisitos específicos: las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura mediante SSL. Por tanto, el formato de la cadena de conexión es:

    mongodb://username:password@host:port/[database]?ssl=true

Los valores de esta cadena están disponibles en la hoja **Cadena de conexión** mostrada antes:

* Username (obligatorio): nombre de la cuenta de Azure Cosmos DB.
* Password (obligatorio): contraseña de la cuenta de Azure Cosmos DB.
* Host (obligatorio): FQDN de la cuenta de Azure Cosmos DB.
* Port (obligatorio): 10255.
* Database (opcional): base de datos que usa la conexión. Si no se proporciona ninguna base de datos, la base de datos predeterminada es "test".
* ssl=true (obligatorio)

Por ejemplo, considere la cuenta que aparece en la hoja **Cadena de conexión**. Una cadena de conexión válida es:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [usar MongoChef](mongodb-mongochef.md) con una API de Azure Cosmos DB para una cuenta de MongoDB.
* Explore [ejemplos](mongodb-samples.md) de la API de Azure Cosmos DB para MongoDB.
