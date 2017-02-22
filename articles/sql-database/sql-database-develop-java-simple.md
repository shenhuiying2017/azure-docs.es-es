---
title: "Conexión a SQL Database mediante Java con JDBC en Windows | Microsoft Docs"
description: "Este tema muestra un ejemplo de código Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo usa JDBC y se ejecuta en un equipo cliente de Windows."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: genemi
ms.assetid: 08fc49b1-cd48-4dcc-a293-ff22a4d2d62c
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: aafc89ca958e4bdc2ba52bf1a7d379e3a4cddf1c


---
# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Conexión a la base de datos SQL mediante Java con JDBC en Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tema presenta un ejemplo de código de Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo de Java se basa en la versión 1.8 del kit de desarrollo de Java (JDK). El ejemplo se conecta a una base de datos SQL de Azure mediante el controlador JDBC.

## <a name="step-1--configure-development-environment"></a>Paso 1: Configuración del entorno de desarrollo
[Configure development environment for Java development](https://docs.microsoft.com/sql/connect/jdbc/step-1-configure-development-environment-for-java-development/)

## <a name="step-2-create-a-sql-database"></a>Paso 2: Creación de una base de datos SQL
Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos.  

## <a name="step-3-get-connection-string"></a>Paso 3: Obtención de la cadena de conexión
[!INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [!NOTE]
> Si usa el controlador JDBC de JTDS, tiene que agregar "ssl=require" a la dirección URL de la cadena de conexión y establecer la siguiente opción para JVM "-Djsse.enableCBCProtection=false". Esta opción de JVM deshabilita una revisión para una vulnerabilidad de seguridad; por tanto, asegúrese de entender qué riesgo implica antes de establecer esta opción.
> 
> 

## <a name="step-4-run-sample-code"></a>Paso 4: ejecución de código de muestra
* [Proof of Concept connecting to SQL using Java](https://docs.microsoft.com/sql/connect/jdbc/step-3-proof-of-concept-connecting-to-sql-using-java/)

## <a name="next-steps"></a>Pasos siguientes
* Visite el [Centro para desarrolladores de Java](/develop/java/).
* Consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md)
* Puede encontrar más información en [Microsoft JDBC Driver para SQL Server](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/)

## <a name="additional-resources"></a>Recursos adicionales
* [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Descubra todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


