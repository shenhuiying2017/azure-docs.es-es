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
ms.custom: features
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a1ede93b5aacf0d8a5bcf83f208f72be328ee72f
ms.lasthandoff: 04/15/2017


---
# <a name="azure-sql-database-features"></a>Características de Azure SQL Database

En las tablas siguientes se enumeran las características principales de Azure SQL Database y las características equivalentes de SQL Server, y se proporciona información sobre si se admite cada característica en particular y un vínculo para más información acerca de la característica en cada plataforma. Para consultar las diferencias de Transact-SQL que hay que tener en cuenta al migrar a una solución de base de datos diferente, consulte [Resolución de diferencias de Transact-SQL durante la migración a SQL Database](sql-database-transact-sql-information.md).

Se continúan agregando características a Azure SQL Database. Por lo tanto, le recomendamos que visite nuestra página web Actualizaciones del servicio para Azure y que use los filtros:

* Filtrado a [Servicio de Base de datos SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado a Disponibilidad general [Anuncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para características de Base de datos SQL.

| **Característica** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| Replicación geográfica activa | No admitida; consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). | [Compatible](sql-database-geo-replication-overview.md)
| Always Encrypted | [Compatible](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Admitida; consulte [Almacén de certificados](sql-database-always-encrypted.md) y [Almacén de claves](sql-database-always-encrypted-azure-key-vault.md).|
| Grupos de disponibilidad AlwaysOn | [Compatible](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | No compatible; consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md). |
| Adjuntar una base de datos | [Compatible](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | No compatible |
| Roles de la aplicación | [Compatible](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [Compatible](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| Escalado automático | No compatible | Admitida; consulte [Niveles de servicio](sql-database-service-tiers.md). |
| Azure Active Directory | No compatible | [Compatible](sql-database-aad-authentication.md) |
| Factoría de datos de Azure | [Compatible](../data-factory/data-factory-introduction.md) | [Compatible](../data-factory/data-factory-introduction.md) |
| Auditoría | [Compatible](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Compatible](sql-database-auditing.md) |
| Archivo BACPAC (exportar) | [Compatible](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [Compatible](sql-database-export.md) |
| Archivo BACPAC (importar) | [Compatible](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [Compatible](sql-database-import.md) |
| COPIA DE SEGURIDAD | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | No compatible |
| Funciones integradas | [Compatible](https://docs.microsoft.com/sql/t-sql/functions/functions) | La mayoría; consulte [funciones individuales] (https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Captura de datos modificados | [Compatible](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | No compatible |
| Seguimiento de cambios | [Compatible](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [Compatible](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| Instrucciones de intercalación | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/collations) | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| Índices de almacén de columnas | [Compatible](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Solo en Premium Edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| Common Language Runtime (CLR) | [Compatible](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | No compatible |
| Bases de datos independientes | [Compatible](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [Compatible](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| Usuarios contenidos | [Compatible](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [Compatible](sql-database-manage-logins.md#non-administrator-users) |
| Palabras clave del lenguaje de control de flujo | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| Consultas entre bases de datos | [Compatible](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Algunas; consulte [Consultas elásticas](sql-database-elastic-query-overview.md). |
| Cursores | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| Compresión de datos | [Compatible](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [Compatible](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| Copias de seguridad de bases de datos | [Usuario administrado](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [Administrado por el servicio SQL Database](sql-database-automated-backups.md). |
| Correo electrónico de base de datos | [Compatible](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | No compatible |
| Creación de reflejo de la base de datos | [Compatible](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | No compatible |
| Configuración de la base de datos | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [Compatible](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | No compatible |
| Instantáneas de base de datos | [Compatible](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | No compatible |
| Tipos de datos | [Compatible](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [Compatible](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| Instrucciones DBCC | [Compatible](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | La mayoría; consulte [Instrucciones individuales](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql). |
| Instrucciones DDL | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/statements) | La mayoría; consulte [Instrucciones individuales](https://docs.microsoft.com/sql/t-sql/statements/statements).
| Desencadenadores DDL | [Compatible](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [Solo en la base de datos](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| Transacciones distribuidas | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Solo en escenarios limitados dentro de SQL Database |
| Instrucciones DML | [Compatible](https://docs.microsoft.com/sql/t-sql/queries/queries) | La mayoría; consulte [instrucciones individuales] (https://docs.microsoft.com/sql/t-sql/queries/queries). |
| Desencadenadores DML | [Compatible](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [Compatible](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMV | [Todo](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Algunas; consulte [DMV individuales](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). |
| Grupos elásticos | No compatible | [Compatible](sql-database-elastic-pool.md) |
| Trabajos elásticos | No compatible; consulte [Agente SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent). | [Compatible](sql-database-elastic-jobs-getting-started.md) | 
| Consultas elásticas | No compatible; consulte [Consultas entre bases de datos](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries). | [Compatible](sql-database-elastic-query-overview.md) |
| Notificaciones de eventos | [Compatible](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [Compatible](sql-database-insights-alerts-portal.md) |
| Expresiones | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| Eventos extendidos | [Compatible](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Algunas; consulte [Eventos individuales](sql-database-xevent-db-diff-from-svr.md). |
| Procedimientos almacenados extendidos | [Compatible](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | No compatible |
| Archivos y grupos de archivos | [Compatible](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [Solo principal](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| Secuencia de archivos | [Compatible](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | No compatible |
| Búsqueda de texto completo | [Compatible](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [No se admiten separadores de palabras de terceros](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). |
| Funciones | [Compatible](https://docs.microsoft.com/sql/t-sql/functions/functions) | La mayoría; consulte [Funciones individuales](https://docs.microsoft.com/sql/t-sql/functions/functions). |
| Optimización en memoria | [Compatible](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Solo en Premium Edition](sql-database-in-memory.md) |
| Trabajos | Consulte [Agente SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent). | Consulte [Trabajos elásticos](sql-database-elastic-jobs-getting-started.md). |
| Compatibilidad con datos JSON | [Compatible](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Compatible](sql-database-json-features.md) |
| Elementos de lenguaje | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | La mayoría; consulte [Elementos individuales](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql). |  
| Servidores vinculados | [Compatible](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | No compatible; consulte el artículo sobre [consultas elásticas](sql-database-elastic-query-horizontal-partitioning.md). |
| Trasvase de registros | [Compatible](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | No compatible; consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md). |
| Master Data Services (MDS) | [Compatible](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | No compatible |
| Registro mínimo durante la importación masiva | [Compatible](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | No compatible |
| Modificación de datos del sistema | [Compatible](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | No compatible |
| Operaciones de índice en línea | [Compatible](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [Admitida; tamaño de las transacciones limitado por nivel de servicio](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| Operadores | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | La mayoría; consulte [Operadores individuales](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql). |
| Restauración de base de datos a un momento dado | [Compatible](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [Compatible](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [Compatible](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | No compatible
| Administración basada en directivas | [Compatible](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | No compatible |
| Predicados | [Compatible](https://docs.microsoft.com/sql/t-sql/queries/predicates) | La mayoría; consulte [Predicados individuales](https://docs.microsoft.com/sql/t-sql/queries/predicates).
| R Services | [Compatible](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| Regulador de recursos | [Compatible](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | No compatible |
| Instrucciones RESTORE | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | No compatible | 
| Restaurar la base de datos a partir de una copia de seguridad | [Compatible](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [Solo desde copias de seguridad integradas](sql-database-recovery-using-backups.md) |
| Seguridad de nivel de fila | [Compatible](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [Compatible](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| Búsqueda semántica | [Compatible](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | No compatible |
| Números de secuencia | [Compatible](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [Compatible](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Service Broker | [Compatible](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | No compatible |
| Valores de configuración del servidor | [Compatible](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | No compatible; consulte el artículo sobre las [opciones de configuración de la base de datos](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql). |
| Instrucciones SET | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | La mayoría; consulte [Instrucciones individuales](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql). 
| Espacial | [Compatible](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [Compatible](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| Agente SQL Server | [Compatible](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | No compatible; consulte el artículo sobre [trabajos elásticos](sql-database-elastic-jobs-getting-started.md). |
| SQL Server Analysis Services (SSAS) | [Compatible](https://docs.microsoft.com/sql/analysis-services/analysis-services) | No compatible; consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/). |
| SQL Server Integration Services (SSIS) | [Compatible](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | No compatible; consulte [Data Factory de Azure](https://azure.microsoft.com/services/data-factory/). |
| SQL Server PowerShell | [Compatible](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [Compatible](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| Analizador de SQL Server | [Compatible](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | No compatible; consulte el artículo sobre los [eventos extendidos](sql-database-xevent-db-diff-from-svr.md). |
| Replicación de SQL Server | [Compatible](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Solo los suscriptores de replicación transaccional y replicación de instantáneas](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Compatible](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | No compatible |
| Procedimientos almacenados | [Compatible](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [Compatible](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| Funciones almacenadas del sistema | [Compatible](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Algunas; consulte [Funciones individuales](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql). |
| Procedimientos almacenados del sistema | [Compatible](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Algunas; consulte [Procedimiento almacenado individual](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql). |
| Tablas del sistema | [Compatible](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Algunas; consulte [Tabla individual](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql). |
| Vistas de catálogo del sistema | [Compatible](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Algunas; consulte [Vistas individuales](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql).
| Partición de tabla | [Compatible](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Admitida; [solo el grupo de archivos principal](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes). |
| Tablas temporales | [Locales y globales](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [Solo locales](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| Tablas temporales | [Compatible](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Compatible](sql-database-temporal-tables.md) |
| Transacciones | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| Variables | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [Compatible](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| Cifrado de datos transparente (TDE)  | [Compatible](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [Compatible](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Clústeres de conmutación por error de Windows Server | [Compatible](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | No compatible; consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md). |
| Índices XML | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [Compatible](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el servicio Azure SQL Database, consulte el artículo que explica [en qué consiste SQL Database](sql-database-technical-overview.md).
- Para conocer detalles de la compatibilidad y las diferencias de Transact-SQL, consulte [Resolución de diferencias de Transact-SQL durante la migración a SQL Database](sql-database-transact-sql-information.md).

