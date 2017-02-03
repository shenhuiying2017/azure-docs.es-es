---
title: "Información general sobre servidores lógicos de Azure SQL Database | Microsoft Docs"
description: "En esta página se incluyen consideraciones e instrucciones para trabajar con servidores lógicos de SQL Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 17e2830dceeaa313dd0fd7d406bf68a75b6f900e


---
# <a name="azure-sql-database-logical-servers"></a>Servidores lógicos de Azure SQL Database

En este tema se incluyen consideraciones e instrucciones para trabajar con servidores lógicos de Azure SQL. Para obtener más información sobre las bases de datos de Azure SQL, consulte el artículo sobre las [bases de datos de SQL Database](sql-database-overview.md).

## <a name="what-is-an-azure-sql-database-logical-server"></a>¿Qué es un servidor lógico de Azure SQL Database?
Un servidor lógico de Azure SQL Database actúa como el punto central de administración de varias bases de datos. En SQL Database, un servidor es una construcción lógica distinta de una instancia de SQL Server, con la que puede estar familiarizado en el mundo de implementaciones locales. En concreto, el servicio SQL Database no ofrece ninguna garantía con respecto a la ubicación de las bases de datos con respecto a sus servidores lógicos y no expone ningún acceso de nivel de instancia ni características. Para obtener más información sobre los servidores lógicos de Azure SQL, consulte el [artículo al respecto](sql-database-server-overview.md). 

Un servidor lógico de Azure Database:

- Se crea en una suscripción de Azure, pero puede transferirse a otra suscripción con los recursos que contiene.
- Es el recurso primario para bases de datos, grupos elásticos y almacenamientos de datos.
- Proporciona un espacio de nombres para bases de datos, grupos elásticos y almacenamientos de datos.
- Es un contenedor lógico con semántica de duración segura. Si se elimina un servidor, se eliminan las bases de datos independientes, los grupos elásticos y los almacenes de datos.
- Participa en el control de acceso basado en rol (RBAC) de Azure. Las bases de datos y los grupos elásticos dentro de un servidor heredan los derechos de acceso del servidor.
- Es un elemento de orden superior de la identidad de las bases de datos y los grupos elásticos con fines de administración de recursos de Azure (vea el esquema de direcciones URL para bases de datos y grupos).
- Coloca recursos en una región.
- Proporciona un punto de conexión para el acceso a la base de datos (<serverName>. database.windows.net).
- Proporciona acceso a los metadatos de recursos contenidos a través de las DMV conectándose a una base de datos maestra. 
- Proporciona el ámbito de las directivas de administración que se aplican a sus bases de datos: inicios de sesión, firewall, auditoría, detección de amenazas, etc. 
- Está restringido por una cuota dentro de la suscripción primaria (seis servidores por suscripción. [Consulte el artículo sobre los límites de la suscripción aquí](../azure-subscription-service-limits.md)).
- Proporciona el ámbito de la cuota de la base de datos y la cuota de DTU para los recursos que contiene (por ejemplo, DTU 45000 en V12).
- Es el ámbito de control de versiones para funciones que se habilitan en los recursos contenidos (la última versión es la V12).
- Los inicios de sesión de la entidad de seguridad en el nivel de servidor pueden administrar todas las bases de datos en un servidor.
- Puede contener inicios de sesión similares a los de las instancias de SQL Server en sus instalaciones que tienen acceso a una o más bases de datos en el servidor y puede ser concesionario de derechos administrativos limitados. Para obtener más información, consulte el artículo sobre [inicios de sesión](sql-database-manage-logins.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>¿Cómo conectarse a un servidor lógico de Azure SQL Database y autenticarse en él?

- **Autenticación y autorización**: Azure SQL Database admite la autenticación de SQL y la de Azure Active Directory (con ciertas limitaciones: consulte el artículo sobre la [conexión con SQL Database con la autenticación de Azure Active Directory](sql-database-aad-authentication.md) para conocer los detalles a este respecto). Puede conectarse a bases de datos de Azure SQL Database y autenticarse en ellas a través de la base de datos maestra del servidor o efectuando ambas acciones directamente con una base de datos de usuario. Para obtener más información, consulte el artículo sobre la [administración de bases de datos e inicios de sesión en Azure SQL Database](sql-database-manage-logins.md). La autenticación de Windows no es compatible. 
- **TDS**: Microsoft Azure SQL Database admite la versión 7.3 (o posterior) del cliente del protocolo de flujo TDS.
- **TCP/IP**: se permiten únicamente las conexiones TCP/IP.
- **Firewall de SQL Database**: para mantener sus datos protegidos, el firewall de SQL Database impide todo acceso al servidor de bases de datos o a estas últimas mientras no se especifiquen qué equipos cuentan con permiso. Consulte el artículo sobre [firewalls](sql-database-firewall-configure.md).

## <a name="what-collations-are-supported"></a>¿Qué intercalaciones son compatibles?

La intercalación de bases de datos predeterminada que utiliza Microsoft Azure SQL Database (incluida la base de datos maestra) es **SQL_LATIN1_GENERAL_CP1_CI_AS**, donde **LATIN1_GENERAL** corresponde a inglés (Estados Unidos); **CP1** es la página de códigos 1252; **CI** indica que no se distingue mayúsculas de minúsculas y **AS** especifica que se tienen en cuenta los acentos. No se recomienda modificar la intercalación de bases de datos de la versión 12 después de su creación. Para obtener más información acerca de las intercalaciones, consulte [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>¿Cuáles son los requisitos de nomenclaturas de los objetos de base de datos?

Los nombres de todos los objetos nuevos deben cumplir las reglas de SQL Server para los identificadores. Para obtener más información, consulte [Identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported"></a>¿Qué características se admiten?

Para obtener información sobre las características admitidas, consulte el [artículo que trata sobre dicho tema](sql-database-features.md). Consulte también [Azure SQL Database Transact-SQL differences](sql-database-transact-sql-information.md) (Diferencias de Transact-SQL de Azure SQL Database) para entender mejor los motivos de la falta de compatibilidad con ciertos tipos de características.

## <a name="how-do-i-manage-a-logical-server"></a>¿Cómo se puede administrar un servidor lógico?

Puede administrar servidores lógicos de Azure SQL Database mediante varios métodos:
- [Portal de Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre el servicio Azure SQL Database, consulte [este artículo](sql-database-technical-overview.md).
- Para conocer las características admitidas, vea el [artículo que trata sobre dicho tema](sql-database-features.md).
- Para acceder a información general acerca de las bases de datos de Azure SQL Database, lea [este resumen sobre el particular](sql-database-overview.md).
- Para conocer los detalles sobre la compatibilidad y las diferencias de Transact-SQL, consulte [Azure SQL Database Transact-SQL differences](sql-database-transact-sql-information.md) (Diferencias de Transact-SQL de Azure SQL Database).
- En el siguiente vínculo, encontrará información sobre las cuotas y limitaciones aplicables a recursos específicos en función de su **nivel de servicio**. Para obtener una descripción general de los niveles de servicio, consulte [Niveles de servicio de la Base de datos SQL](sql-database-service-tiers.md).
- Para obtener información general sobre la seguridad, consulte [Introducción a la seguridad de Azure SQL Database](sql-database-security-overview.md).
- Para obtener información sobre la disponibilidad de controladores y la compatibilidad con Base de datos SQL, consulte [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md).




<!--HONumber=Dec16_HO4-->


