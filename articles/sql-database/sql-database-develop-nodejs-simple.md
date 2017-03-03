---
title: "Conexión a SQL Database mediante Node.js | Microsoft Docs"
description: "Este tema muestra un ejemplo de código Node.js que puede usar para conectarse a la base de datos SQL de Azure."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: 6c3f6bc00d147bc498a859560ffe4719ecf888bf
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-nodejs"></a>Conexión a Base de datos SQL mediante Node.js
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

En este tema se muestra cómo conectarse a una base de datos SQL de Azure y consultarla mediante Node.js. Puede ejecutar esta muestra desde las plataformas Windows, Ubuntu Linux o Mac.

## <a name="step-1-configure-development-environment"></a>Paso 1: Configuración del entorno de desarrollo
[Consulte el artículo Prerequisites for using the tedious Node.js Driver for SQL Server (Requisitos previos para usar el controlador tedioso de Node.js para SQL Server).](https://docs.microsoft.com/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/)

## <a name="step-2-create-a-sql-database"></a>Paso 2: Creación de una base de datos SQL
Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo.  Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Paso 3: Obtención de detalles de la conexión
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Paso 4: ejecución de código de muestra
[Proof of Concept connecting to SQL using Node.js](https://docs.microsoft.com/sql/connect/node-js/step-3-proof-of-concept-connecting-to-sql-using-node-js/)

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md)
* Puede encontrar más información en [Microsoft Node.js Driver para SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)

## <a name="additional-resources"></a>Recursos adicionales
* [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Descubra todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/)


