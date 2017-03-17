---
title: "Información general de las características de Azure SQL Database | Microsoft Docs"
description: "En esta página se proporciona información general sobre las bases de datos y los servidores lógicos de Azure SQL Database y, además, se incluye una matriz de compatibilidad de características con vínculos para cada característica enumerada."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: e052109ff10f21c3a737f336479fe43fb54e500e
ms.lasthandoff: 03/14/2017


---
# <a name="azure-sql-database-features"></a>Características de Azure SQL Database
En este tema se proporciona información general sobre las bases de datos y los servidores lógicos de Azure SQL Database y, además, se incluye una matriz de compatibilidad de características con vínculos para cada característica enumerada. 

## <a name="what-is-an-azure-sql-database-logical-server"></a>¿Qué es un servidor lógico de Azure SQL Database?
Un servidor lógico de Azure SQL Database actúa como el punto central de administración de varias bases de datos. En SQL Database, un servidor es una construcción lógica distinta de una instancia de SQL Server, con la que puede estar familiarizado en el mundo de implementaciones locales. En concreto, el servicio SQL Database no ofrece ninguna garantía con respecto a la ubicación de las bases de datos con respecto a sus servidores lógicos y no expone ningún acceso de nivel de instancia ni características. Para obtener más información sobre los servidores lógicos de Azure SQL, consulte el [artículo al respecto](sql-database-server-overview.md). 

## <a name="what-is-an-azure-sql-database"></a>¿Qué son las bases de datos de Azure SQL Database?
Cada base de datos de Azure SQL Database está asociada a un servidor lógico. La base de datos puede revestir cualquiera de las siguientes formas:

- Una base de datos única con su [propio conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU).
- Parte de un [grupo de bases de datos](sql-database-elastic-pool.md) que [comparte un conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU).
- Parte de un [conjunto escalado horizontalmente de bases de datos particionadas](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), que pueden ser simples o agrupadas.
- Parte de un conjunto de bases de datos que participan en un [modelo de diseño de SaaS multiinquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md) y cuyas bases de datos pueden ser simples o agrupadas (o ambas). 

Para obtener más información sobre las bases de datos de Azure SQL, consulte el [artículo al respecto](sql-database-overview.md).

## <a name="what-features-are-supported"></a>¿Qué características se admiten?

En las tablas siguientes se enumeran las características principales de Azure SQL Database y SQL Server, se especifica su compatibilidad y se proporciona un vínculo para más información sobre la característica en cada plataforma. En el caso de las características de Transact-SQL, visite el vínculo de la tabla de la categoría de la característica. Consulte también [Azure SQL Database Transact-SQL differences](sql-database-transact-sql-information.md) (Diferencias de Transact-SQL de Azure SQL Database) para entender mejor los motivos de la falta de compatibilidad con ciertos tipos de características.

Seguimos agregando características a la V12. Por lo tanto, le recomendamos que visite nuestra página web Actualizaciones del servicio para Azure y que use los filtros:

* Filtrado a [Servicio de Base de datos SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado a Disponibilidad general [Anuncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para características de Base de datos SQL.

> [!TIP]
> Para comprobar la compatibilidad con Azure SQL Database, consulte [Migración de una base de datos de SQL Server a Azure](sql-database-cloud-migrate.md).
>

| **Característica** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| Replicación geográfica activa | No compatible; consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). | [Compatible](sql-database-geo-replication-overview.md)
| Always Encrypted | [Compatible](https://msdn.microsoft.com/library/mt163865.aspx) | [Compatible](sql-database-always-encrypted.md) |
| Grupos de disponibilidad AlwaysOn | [Compatible](https://msdn.microsoft.com/library/hh510230.aspx) | No compatible; consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md). |
| Adjuntar una base de datos | [Compatible](https://msdn.microsoft.com/library/ms190209.aspx) | No compatible |
| Roles de la aplicación | [Compatible](https://msdn.microsoft.com/library/ms190998.aspx) | [Compatible](https://msdn.microsoft.com/library/ms190998.aspx) |
| Escalado automático | No compatible | [Compatible](sql-database-service-tiers.md) |
| Azure Active Directory | No compatible | [Compatible](sql-database-aad-authentication.md) |
| Factoría de datos de Azure | [Compatible](../data-factory/data-factory-introduction.md) | [Compatible](../data-factory/data-factory-introduction.md) |
| Auditoría | [Compatible](https://msdn.microsoft.com/library/cc280386.aspx) | [Compatible](sql-database-auditing.md) |
| Archivo BACPAC (exportar) | [Compatible](https://msdn.microsoft.com/library/hh213241.aspx) | [Compatible](sql-database-export.md) |
| Archivo BACPAC (importar) | [Compatible](https://msdn.microsoft.com/library/hh710052.aspx) | [Compatible](sql-database-import-portal.md) |
| Instrucciones BACKUP y RESTORE | [Compatible](https://msdn.microsoft.com/library/ff848768.aspx) | No compatible |
| Funciones integradas | [Compatible](https://msdn.microsoft.com/library/ms174318.aspx) | [La mayoría](https://msdn.microsoft.com/library/ms174318.aspx) |
| Captura de datos modificados | [Compatible](https://msdn.microsoft.com/library/cc645937.aspx) | No compatible |
| Seguimiento de cambios | [Compatible](https://msdn.microsoft.com/library/bb933875.aspx) | [Compatible](https://msdn.microsoft.com/library/bb933875.aspx) |
| Instrucciones de intercalación | [Compatible](https://msdn.microsoft.com/library/ff848763.aspx) | [Compatible](https://msdn.microsoft.com/library/ff848763.aspx) |
| Índices de almacén de columnas | [Compatible](https://msdn.microsoft.com/library/gg492088.aspx) | [Solo en Premium Edition](https://msdn.microsoft.com/library/gg492088.aspx) |
| Common Language Runtime (CLR) | [Compatible](https://msdn.microsoft.com/library/ms131102.aspx) | No compatible |
| Bases de datos independientes | [Compatible](https://msdn.microsoft.com/library/ff929071.aspx) | Característica integrada |
| Usuarios contenidos | [Compatible](https://msdn.microsoft.com/library/ff929188.aspx) | [Compatible](sql-database-manage-logins.md#non-administrator-users) |
| Palabras clave del lenguaje de control de flujo | [Compatible](https://msdn.microsoft.com/library/ms174290.aspx) | [Compatible](https://msdn.microsoft.com/library/ms174290.aspx) |
| Consultas entre bases de datos | [Compatible](https://msdn.microsoft.com/library/dn584627.aspx) | [Consultas elásticas](sql-database-elastic-query-overview.md) |
| Cursores | [Compatible](https://msdn.microsoft.com/library/ms181441.aspx) | [Compatible](https://msdn.microsoft.com/library/ms181441.aspx) | 
| Compresión de datos | [Compatible](https://msdn.microsoft.com/library/cc280449.aspx) | [Compatible](https://msdn.microsoft.com/library/cc280449.aspx) |
| Copias de seguridad de bases de datos | [Expuestas para los usuarios](https://msdn.microsoft.com/library/ms187048.aspx) | [Característica integrada](sql-database-automated-backups.md) |
| Correo electrónico de base de datos | [Compatible](https://msdn.microsoft.com/library/ms189635.aspx) | No compatible |
| Creación de reflejo de la base de datos | [Compatible](https://msdn.microsoft.com/library/ms189852.aspx) | No compatible |
| Opciones de configuración de la base de datos | [Compatible](https://msdn.microsoft.com/library/mt629158.aspx) | [Compatible](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [Compatible](https://msdn.microsoft.com/library/ff877925.aspx) | No compatible |
| Instantáneas de base de datos | [Compatible](https://msdn.microsoft.com/library/ms175158.aspx) | No compatible |
| Tipos de datos | [Compatible](https://msdn.microsoft.com/library/ms187752.aspx) | [Compatible](https://msdn.microsoft.com/library/ms187752.aspx) |  
| Instrucciones DBCC | [Todo](https://msdn.microsoft.com/library/ms188796.aspx) | [Algunas](https://msdn.microsoft.com/library/ms188796.aspx) |
| Instrucciones DDL | [Compatible](https://msdn.microsoft.com/library/ff848799.aspx) | [La mayoría](https://msdn.microsoft.com/library/ff848799.aspx)
| Desencadenadores DDL | [Compatible](https://msdn.microsoft.com/library/ms175941.aspx) | [Solo en la base de datos](https://msdn.microsoft.com/library/ms175941.aspx) |
| Transacciones distribuidas | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | Solo en escenarios limitados dentro de SQL Database |
| Instrucciones DML | [Compatible](https://msdn.microsoft.com/library/ff848766.aspx) | [La mayoría](https://msdn.microsoft.com/library/ff848766.aspx) |
| Desencadenadores DML | [Compatible](https://msdn.microsoft.com/library/ms178110.aspx) | [Compatible](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMV | [Todo](https://msdn.microsoft.com/library/ms188754.aspx) | [Algunas](https://msdn.microsoft.com/library/ms188754.aspx) |
| grupos elásticos | No compatible | [Compatible](sql-database-elastic-pool.md) |
| Trabajos elásticos | No compatible; consulte [Agente SQL Server](https://msdn.microsoft.com/library/ms189237.aspx). | [Compatible](sql-database-elastic-jobs-getting-started.md) | 
| Consultas elásticas | No compatible; consulte [Consultas entre bases de datos](https://msdn.microsoft.com/library/dn584627.aspx). | [Compatible](sql-database-elastic-query-overview.md) |
| Notificaciones de eventos | [Compatible](https://msdn.microsoft.com/library/ms186376.aspx) | [Compatible](sql-database-insights-alerts-portal.md) |
| Expresiones | [Compatible](https://msdn.microsoft.com/library/ms190286.aspx) | [Compatible](https://msdn.microsoft.com/library/ms190286.aspx) |
| Eventos extendidos | [Compatible](https://msdn.microsoft.com/library/bb630282.aspx) | [Algunos](sql-database-xevent-db-diff-from-svr.md) |
| Procedimientos almacenados extendidos | [Compatible](https://msdn.microsoft.com/library/ms164627.aspx) | No compatible |
| Grupos de archivos | [Compatible](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [Solo principal](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| Secuencia de archivos | [Compatible](https://msdn.microsoft.com/library/gg471497.aspx) | No compatible |
| Búsqueda de texto completo | [Compatible](https://msdn.microsoft.com/library/ms142571.aspx) | [No se admiten separados de palabras de terceros](https://msdn.microsoft.com/library/ms142571.aspx). |
| Funciones | [Compatible](https://msdn.microsoft.com/library/ms174318.aspx) | [La mayoría](https://msdn.microsoft.com/library/ms174318.aspx) |
| Optimización en memoria | [Compatible](https://msdn.microsoft.com/library/dn133186.aspx) | [Solo en Premium Edition](https://msdn.microsoft.com/library/dn133186.aspx) |
| Trabajos | [Agente SQL Server](https://msdn.microsoft.com/library/ms189237.aspx) | [Compatible](sql-database-elastic-jobs-getting-started.md) |
| Compatibilidad con datos JSON | [Compatible](https://msdn.microsoft.com/library/dn921897.aspx) | [Compatible](sql-database-json-features.md) |
| Elementos de lenguaje | [Compatible](https://msdn.microsoft.com/library/ff848807.aspx) | [La mayoría](https://msdn.microsoft.com/library/ff848807.aspx) |  
| Servidores vinculados | [Compatible](https://msdn.microsoft.com/library/ms188279.aspx) | No compatible; consulte el artículo sobre [consultas elásticas](sql-database-elastic-query-horizontal-partitioning.md). |
| Trasvase de registros | [Compatible](https://msdn.microsoft.com/library/ms187103.aspx) | No compatible; consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md). |
| Comandos de administración | [Compatible](https://msdn.microsoft.com/library/ms190286.aspx)| [No compatible](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [Compatible](https://msdn.microsoft.com/library/ff487003.aspx) | No compatible |
| Registro mínimo durante la importación masiva | [Compatible](https://msdn.microsoft.com/library/ms190422.aspx) | No compatible |
| Modificación de datos del sistema | [Compatible](https://msdn.microsoft.com/library/ms178028.aspx) | No compatible |
| Operaciones de índice en línea | [Compatible](https://msdn.microsoft.com/library/ms177442.aspx) | [Tamaño de las transacciones limitado por nivel de servicio](https://msdn.microsoft.com/library/ms177442.aspx) |
| Operadores | [Compatible](https://msdn.microsoft.com/library/ms174986.aspx) | [La mayoría](https://msdn.microsoft.com/library/ms174986.aspx) |
| Restauración de base de datos a un momento dado | [Compatible](https://msdn.microsoft.com/library/ms179451.aspx) | [Compatible](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [Compatible](https://msdn.microsoft.com/library/mt143171.aspx) | [No compatible]
| Administración basada en directivas | [Compatible](https://msdn.microsoft.com/library/bb510667.aspx) | No compatible |
| Predicados | [Compatible](https://msdn.microsoft.com/library/ms189523.aspx) | [La mayoría](https://msdn.microsoft.com/library/ms189523.aspx)
| R Services | [Compatible](https://msdn.microsoft.com/library/mt604845.aspx)
| Regulador de recursos | [Compatible](https://msdn.microsoft.com/library/bb933866.aspx) | No compatible |
| Restaurar la base de datos a partir de una copia de seguridad | [Compatible](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [Solo desde copias de seguridad integradas](sql-database-recovery-using-backups.md) |
| Seguridad de nivel de fila | [Compatible](https://msdn.microsoft.com/library/dn765131.aspx) | [Compatible](https://msdn.microsoft.com/library/dn765131.aspx) |
| Instrucciones de seguridad | [Compatible](https://msdn.microsoft.com/library/ff848791.aspx) | [Algunas](https://msdn.microsoft.com/library/ff848791.aspx) |
| Búsqueda semántica | [Compatible](https://msdn.microsoft.com/library/gg492075.aspx) | No compatible |
| Números de secuencia | [Compatible](https://msdn.microsoft.com/library/ff878058.aspx) | [Compatible](https://msdn.microsoft.com/library/ff878058.aspx) |
| Service Broker | [Compatible](https://msdn.microsoft.com/library/bb522893.aspx) | No compatible |
| Opciones de configuración del servidor | [Compatible](https://msdn.microsoft.com/library/ms189631.aspx) | No compatible; consulte el artículo sobre las [opciones de configuración de la base de datos](https://msdn.microsoft.com/library/mt629158.aspx). |
| Instrucciones SET | [Compatible](https://msdn.microsoft.com/library/ms190356.aspx) | [La mayoría](https://msdn.microsoft.com/library/ms190356.aspx) 
| Espacial | [Compatible](https://msdn.microsoft.com/library/bb933790.aspx) | [Compatible](https://msdn.microsoft.com/library/bb933790.aspx) |
| Agente SQL Server | [Compatible](https://msdn.microsoft.com/library/ms189237.aspx) | No compatible; consulte el artículo sobre [trabajos elásticos](sql-database-elastic-jobs-getting-started.md). |
| SQL Server Analysis Services (SSAS) | [Compatible](https://msdn.microsoft.com/library/bb522607.aspx) | No compatible; consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/). |
| SQL Server Integration Services (SSIS) | [Compatible](https://msdn.microsoft.com/library/ms141026.aspx) | No compatible; consulte [Data Factory de Azure](https://azure.microsoft.com/services/data-factory/). |
| SQL Server PowerShell | [Compatible](https://msdn.microsoft.com/library/hh245198.aspx) | [Compatible](https://msdn.microsoft.com/library/hh245198.aspx) |
| Analizador de SQL Server | [Compatible](https://msdn.microsoft.com/library/ms181091.aspx) | No compatible; consulte el artículo sobre los [eventos extendidos](https://msdn.microsoft.com/library/ms181091.aspx). |
| Replicación de SQL Server | [Compatible](https://msdn.microsoft.com/library/ms151198.aspx) | [Solo los suscriptores de replicación transaccional y replicación de instantáneas](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Compatible](https://msdn.microsoft.com/library/ms159106.aspx) | No compatible |
| Procedimientos almacenados | [Compatible](https://msdn.microsoft.com/library/ms190782.aspx) | [Compatible](https://msdn.microsoft.com/library/ms190782.aspx) |
| Funciones almacenadas del sistema | [Compatible](https://msdn.microsoft.com/library/ff848780.aspx) | [Algunos](https://msdn.microsoft.com/library/ff848780.aspx) |
| Procedimientos almacenados del sistema | [Compatible](https://msdn.microsoft.com/library/ms187961.aspx) | [Algunos](https://msdn.microsoft.com/library/ms187961.aspx) |
| Tablas del sistema | [Compatible](https://msdn.microsoft.com/library/ms179932.aspx) | [Algunas](https://msdn.microsoft.com/library/ms179932.aspx) |
| Vistas de sistema | [Compatible](https://msdn.microsoft.com/library/ms177862.aspx) | [Algunas](https://msdn.microsoft.com/library/ms177862.aspx)
| Partición de tabla | [Compatible](https://msdn.microsoft.com/library/ms190787.aspx) | [Solo el grupo de archivos principal](https://msdn.microsoft.com/library/ms190787.aspx) |
| Tablas temporales | [Locales y globales](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [Solo locales](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| Tablas temporales | [Compatible](https://msdn.microsoft.com/library/dn935015.aspx) | [Compatible](sql-database-temporal-tables.md) |
| Instrucciones de transacciones | [Compatible](https://msdn.microsoft.com/library/ms174377.aspx) | [Compatible](https://msdn.microsoft.com/library/ms174377.aspx) |
| Variables | [Compatible](https://msdn.microsoft.com/library/ff848809.aspx) | | [Compatible](https://msdn.microsoft.com/library/ff848809.aspx) | 
| Cifrado de datos transparente (TDE)  | [Compatible](https://msdn.microsoft.com/library/bb934049.aspx) | [Compatible](https://msdn.microsoft.com/dn948096.aspx) |
| Clústeres de conmutación por error de Windows Server | [Compatible](https://msdn.microsoft.com/library/hh270278.aspx) | No compatible; consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md). |
| Índices XML | [Compatible](http://msdn.microsoft.com/library/bb934097.aspx) | [Compatible](http://msdn.microsoft.com/library/bb934097.aspx) |
| Instrucciones XML | [Compatible](https://msdn.microsoft.com/library/ff848798.aspx) | [Compatible](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el servicio Azure SQL Database, consulte el artículo que explica [en qué consiste SQL Database](sql-database-technical-overview.md).
- Para acceder a información general sobre los servidores lógicos de Azure SQL Database, lea [este resumen sobre el particular](sql-database-server-overview.md).
- Para acceder a información general acerca de las bases de datos de Azure SQL Database, lea [este resumen sobre el particular](sql-database-overview.md).
- Para conocer los detalles sobre la compatibilidad y las diferencias de Transact-SQL, consulte [Azure SQL Database Transact-SQL differences](sql-database-transact-sql-information.md) (Diferencias de Transact-SQL de Azure SQL Database).
- En el siguiente vínculo, encontrará información sobre las cuotas y limitaciones aplicables a recursos específicos en función de su **nivel de servicio**. Para obtener una descripción general de los niveles de servicio, consulte [Niveles de servicio de la Base de datos SQL](sql-database-service-tiers.md).
- Para obtener información general sobre la seguridad, consulte [Introducción a la seguridad de Azure SQL Database](sql-database-security-overview.md).
- Para obtener información sobre la disponibilidad de controladores y la compatibilidad con Base de datos SQL, consulte [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md).

