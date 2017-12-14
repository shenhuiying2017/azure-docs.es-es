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
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/25/2017
ms.author: carlrab
ms.openlocfilehash: a15c23683a3334328720e8ae4369b2495f9613de
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-sql-database-features"></a>Características de Azure SQL Database

Azure SQL Database comparte un código base común con SQL Server y, en el nivel de base de datos, admite la mayoría de las mismas características. Las principales diferencias de características entre Azure SQL Database y SQL Server se encuentran en el nivel de instancia. 

Se continúan agregando características a Azure SQL Database. Por lo tanto, le recomendamos que visite nuestra página web Actualizaciones del servicio para Azure y que use los filtros:

* Filtrado a [Servicio de SQL Database](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado a Disponibilidad general [Anuncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para características de SQL Database.

## <a name="sql-server-and-sql-database-feature-support"></a>Compatibilidad con las características de SQL Server y SQL Database

En las tablas siguientes se enumeran las características principales de SQL Server y se proporciona información sobre si se admite cada característica en concreto, así como un vínculo para obtener más información sobre dicha característica. Para consultar las diferencias de Transact-SQL que hay que tener en cuenta al migrar a una solución de base de datos diferente, consulte [Resolución de diferencias de Transact-SQL durante la migración a SQL Database](sql-database-transact-sql-information.md).


| **Característica de SQL Server** | **Compatible con Azure SQL Database** | 
| --- | --- |  
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Sí; consulte los artículos sobre el [almacén de certificados](sql-database-always-encrypted.md) y el [almacén de claves](sql-database-always-encrypted-azure-key-vault.md).|
| [Grupos de disponibilidad AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Cada base de datos incluye alta disponibilidad. La recuperación ante desastres se explica en [Información general sobre continuidad empresarial con Azure SQL Database](sql-database-geo-replication-overview.md) |
| [Conectar una base de datos](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | No |
| [Roles de la aplicación](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Sí |
|[Auditoría](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Sí](sql-database-auditing.md)|
| [Ajuste automático](/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sí](sql-database-automatic-tuning.md)|
| [Archivo BACPAC (exportar)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sí; consulte el artículo sobre [exportación de SQL Database](sql-database-export.md). |
| [Archivo BACPAC (importar)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sí; consulte el artículo sobre [importación de SQL Database](sql-database-import.md). |
| [Comando BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | No; consulte el artículo sobre [copias de seguridad automatizadas](sql-database-automated-backups.md). |
| [Funciones integradas](https://docs.microsoft.com/sql/t-sql/functions/functions) | La mayoría; consulte las funciones. |
| [Captura de datos modificados](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | No |
| [Seguimiento de cambios](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Sí |
| [Instrucciones de intercalación](https://docs.microsoft.com/sql/t-sql/statements/collations) | Sí |
| [Índices de almacén de columnas](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Sí; [solo en Premium Edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview). |
| [Common Language Runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | No |
| [Bases de datos independientes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Sí |
| [Usuarios contenidos](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Sí |
| [Palabras clave del lenguaje de control de flujo](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Sí |
| [Consultas entre bases de datos](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Algunas; consulte [Consultas elásticas](sql-database-elastic-query-overview.md). |
| [Cursores](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Sí | 
| [Compresión de datos](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Sí |
| [Correo electrónico de base de datos](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | No |
| [Creación de reflejo de la base de datos](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | No |
| [Configuración de la base de datos](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Sí |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | No |
| [Instantáneas de base de datos](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | No |
| [Tipos de datos](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Sí |  
| [Instrucciones DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | La mayoría; consulte el artículo sobre instrucciones. |
| [Instrucciones DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Sí |
| [Desencadenadores DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Solo base de datos |
| [Transacciones distribuidas: MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | No; consulte el artículo sobre [transacciones elásticas](sql-database-elastic-transactions-overview.md). |
| [Instrucciones DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Sí |
| [Desencadenadores DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | La mayoría; consulte el artículo sobre instrucciones. | 
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Algunas; consulte el artículo sobre DMV. |
|[Enmascaramiento de datos dinámicos](/sql/relational-databases/security/dynamic-data-masking)|[Sí](sql-database-dynamic-data-masking-get-started.md)|
| [Notificaciones de eventos](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | No; consulte el artículo sobre [alertas](sql-database-insights-alerts-portal.md). |
| [Expresiones](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Sí |
| [Eventos extendidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Algunas; consulte [Eventos extendidos en SQL Database](sql-database-xevent-db-diff-from-svr.md) |
| [Procedimientos almacenados extendidos](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | No |
| [Archivos y grupos de archivos](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Solo el grupo de archivos principal. |
| [Secuencia de archivos](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | No |
| [Búsqueda de texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | No se admiten separadores de palabras de terceros. |
| [Funciones](https://docs.microsoft.com/sql/t-sql/functions/functions) | La mayoría; consulte las funciones. |
| [Procesamiento de Graph](/sql/relational-databases/graphs/sql-graph-overview) | Sí |
| [Optimización en memoria](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Sí; [solo en Premium Edition](sql-database-in-memory.md). |
| [Compatibilidad con datos JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | Sí |
| [Elementos de lenguaje](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | La mayoría; consulte el artículo sobre elementos. |  
| [Servidores vinculados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | No; consulte el artículo sobre [consulta elástica](sql-database-elastic-query-horizontal-partitioning.md). |
| [Trasvase de registros](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Cada base de datos incluye alta disponibilidad. La recuperación ante desastres se explica en [Información general sobre continuidad empresarial con Azure SQL Database](sql-database-geo-replication-overview.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | No |
| [Registro mínimo durante la importación en bloque](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | No |
| [Modificación de datos del sistema](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | No |
| [Operaciones de índice en línea](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Sí |
| [Operadores](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | La mayoría; consulte el artículo sobre operadores. |
| [Restauración de base de datos a un momento dado](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Sí; consulte el artículo sobre [recuperación SQL Database](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | No |
| [Administración basada en directivas](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | No |
| [Predicados](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Sí |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Versión preliminar; consulte el artículo [What's new in machine learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services) (Novedades del aprendizaje automático).  |
| [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | No |
| [Instrucciones RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | No | 
| [Restaurar la base de datos a partir de una copia de seguridad](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Solo a partir de copias de seguridad integradas; consulte el artículo sobre [recuperación de SQL Database](sql-database-recovery-using-backups.md) |
| [Seguridad de nivel de fila](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Sí |
| [Búsqueda semántica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | No |
| [Número de secuencias](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Sí |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | No |
| [Valores de configuración del servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | No |
| [Instrucciones SET](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | La mayoría; consulte el artículo sobre instrucciones. 
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Sí |
| [Agente SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | No - Consulte [Trabajos elásticos](sql-database-elastic-jobs-getting-started.md). |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | No; consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Auditoría de SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | No; consulte el artículo sobre [auditoría de SQL Database](sql-database-auditing.md). |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Sí: vea [Levantar y mover las cargas de trabajo de SQL Server Integration Services a la nube](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Sí |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | No; consulte el artículo sobre [eventos extendidos](sql-database-xevent-db-diff-from-svr.md). |
| [Replicación de SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Solo los suscriptores de replicación transaccional y replicación de instantáneas](sql-database-cloud-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | No |
| [Procedimientos almacenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Sí |
| [Funciones almacenadas del sistema](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Algunas; consulte el artículo sobre funciones. |
| [Procedimientos almacenados del sistema](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Algunas; consulte el artículo sobre procedimientos almacenados. |
| [Tablas del sistema](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Algunas; consulte el artículo sobre tablas. |
| [Vistas de catálogo del sistema](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Algunas; consulte el artículo sobre vistas. |
| [Partición de tabla](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Sí; solo el grupo de archivos principal. |
| [Tablas temporales](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | Solo las tablas temporales globales locales y de ámbito de base de datos. |
| [Tablas temporales](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | Sí |
| [Variables](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Sí | 
| [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Sí |
| [Agrupación en clústeres de conmutación por error de Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Cada base de datos incluye alta disponibilidad. La recuperación ante desastres se explica en [Información general sobre continuidad empresarial con Azure SQL Database](sql-database-geo-replication-overview.md) |
| [Índices XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Sí |

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el servicio Azure SQL Database, consulte el artículo que explica [en qué consiste SQL Database](sql-database-technical-overview.md).
- Para conocer detalles de la compatibilidad y las diferencias de Transact-SQL, consulte [Resolución de diferencias de Transact-SQL durante la migración a SQL Database](sql-database-transact-sql-information.md).
