---
title: "Conexión a SQL Database mediante Ruby | Microsoft Docs"
description: "Muestra de código Ruby que puede ejecutar en Windows para conectarse a Base de datos SQL de Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 02/03/2017
ms.author: andrela
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: cbd13711911b67ace7ef43676b4c52aa93744bcb


---
# <a name="connect-to-sql-database-by-using-ruby"></a>Conexión a Base de datos SQL mediante Ruby
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

En este tema se muestra cómo conectarse a una base de datos SQL de Azure y consultarla mediante Ruby. Puede ejecutar esta muestra desde las plataformas Windows, Ubuntu Linux o Mac.

## <a name="step-1-configure-development-environment"></a>Paso 1: Configuración del entorno de desarrollo
[Requisitos previos para usar el controlador de Ruby TinyTDS para SQL Server](https://docs.microsoft.com/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/)

## <a name="step-2-create-a-sql-database"></a>Paso 2: Creación de una base de datos SQL
Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo.  Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Paso 3: Obtención de detalles de la conexión
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Paso 4: ejecución de código de muestra
[Proof of Concept connecting to SQL using Ruby](https://docs.microsoft.com/sql/connect/ruby/step-3-proof-of-concept-connecting-to-sql-using-ruby/)

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md)
* Puede encontrar más información en [Microsoft Ruby Driver para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)

## <a name="additional-resources"></a>Recursos adicionales
* [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Descubra todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


