---
title: "¿Qué son las bases de datos de Azure SQL Database? | Microsoft Docs"
description: "Este artículo ofrece información general sobre las bases de datos de Azure SQL Database."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 06639080dec485395d6821c371a4f792c7c02845
ms.openlocfilehash: 5e3ac3a08ea3e5c72082dcc7faa209d848acf2fe


---
# <a name="azure-sql-database-overview"></a>Información general sobre Azure SQL Database
En este tema, se ofrece información general sobre las bases de datos de Azure SQL Database. Para obtener información sobre los servidores lógicos de Azure SQL, consulte el [artículo que trata sobre ellos](sql-database-server-overview.md).

## <a name="what-is-azure-sql-database"></a>¿Qué es Azure SQL Database?
Cada base de datos de Azure SQL Database está asociada a un servidor lógico. La base de datos puede revestir cualquiera de las siguientes formas:

- Una base de datos única con su [propio conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU).
- Parte de un [grupo elástico](sql-database-elastic-pool.md) que [comparte un conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU).
- Parte de un [conjunto escalado horizontalmente de bases de datos particionadas](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), que pueden ser simples o agrupadas.
- Parte de un conjunto de bases de datos que participa en un [modelo de diseño de SaaS multiinquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md) y cuyas bases de datos pueden ser simples o agrupadas (o ambas). 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>¿Cómo conectarse a una base de datos de Azure SQL Database y autenticarse en ella?

- **Autenticación y autorización**: Azure SQL Database admite la autenticación de SQL y la de Azure Active Directory (con ciertas limitaciones: consulte el artículo sobre la [conexión con SQL Database con la autenticación de Azure Active Directory](sql-database-aad-authentication.md) para conocer los detalles a este respecto). Puede conectarse a bases de datos de Azure SQL Database y autenticarse en ellas a través de la base de datos maestra del servidor o efectuando ambas acciones directamente con una base de datos de usuario. Para obtener más información, consulte el artículo sobre la [administración de bases de datos e inicios de sesión en Azure SQL Database](sql-database-manage-logins.md). La autenticación de Windows no es compatible. 
- **TDS**: Microsoft Azure SQL Database admite la versión 7.3 (o posterior) del cliente del protocolo de flujo TDS.
- **TCP/IP**: se permiten únicamente las conexiones TCP/IP.
- **Firewall de SQL Database**: para mantener sus datos protegidos, el firewall de SQL Database impide todo acceso al servidor de bases de datos o a estas últimas mientras no se especifiquen qué equipos cuentan con permiso. Consulte el artículo sobre [firewalls](sql-database-firewall-configure.md).

## <a name="what-collations-are-supported"></a>¿Qué intercalaciones son compatibles?
La intercalación de bases de datos predeterminada que utiliza Microsoft Azure SQL Database es **SQL_LATIN1_GENERAL_CP1_CI_AS**, donde **LATIN1_GENERAL** corresponde a inglés (Estados Unidos); **CP1** es la página de códigos 1252; **CI** indica que no se distingue mayúsculas de minúsculas y **AS** especifica que se tienen en cuenta los acentos. No se puede modificar la intercalación de bases de datos de V12. Para obtener más información acerca de cómo establecer la intercalación, consulte [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>¿Cuáles son los requisitos de nomenclaturas de los objetos de base de datos?

Los nombres de todos los objetos nuevos deben cumplir las reglas de SQL Server para los identificadores. Para obtener más información, consulte [Identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported-by-azure-sql-databases"></a>¿Qué características admiten las bases de datos de Azure SQL Database?

Para obtener información sobre las características admitidas, consulte el [artículo que trata sobre dicho tema](sql-database-features.md). Consulte también [Azure SQL Database Transact-SQL differences](sql-database-transact-sql-information.md) (Diferencias de Transact-SQL de Azure SQL Database) para entender mejor los motivos de la falta de compatibilidad con ciertos tipos de características.

## <a name="how-do-i-manage-an-azure-sql-database"></a>¿Cómo se puede administrar una base de datos de Azure SQL Database?

Puede administrar servidores lógicos de Azure SQL Database mediante varios métodos:
- [Portal de Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [Transact-SQL](sql-database-manage-azure-ssms.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre el servicio Azure SQL Database, consulte [este artículo](sql-database-technical-overview.md).
- Para conocer las características admitidas, vea el [artículo que trata sobre dicho tema](sql-database-features.md).
- Para acceder a información general sobre los servidores lógicos de Azure SQL Database, lea [este resumen sobre el particular](sql-database-server-overview.md).
- Para conocer los detalles sobre la compatibilidad y las diferencias de Transact-SQL, consulte [Azure SQL Database Transact-SQL differences](sql-database-transact-sql-information.md) (Diferencias de Transact-SQL de Azure SQL Database).
- En el siguiente vínculo, encontrará información sobre las cuotas y limitaciones aplicables a recursos específicos en función de su **nivel de servicio**. Para obtener una descripción general de los niveles de servicio, consulte [Niveles de servicio de la Base de datos SQL](sql-database-service-tiers.md).
- Para obtener información general sobre la seguridad, consulte [Introducción a la seguridad de Azure SQL Database](sql-database-security-overview.md).
- Para obtener información sobre la disponibilidad de controladores y la compatibilidad con Base de datos SQL, consulte [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md).




<!--HONumber=Jan17_HO4-->


