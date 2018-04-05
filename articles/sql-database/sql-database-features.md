---
title: Comparación de características de Azure SQL Database | Microsoft Docs
description: En este artículo se comparan las características de Azure SQL Database e Instancias administradas entre sí y con SQL Server.
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: article
ms.date: 03/21/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 842c65987dc97872751071dbcf138794a14940e2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Comparación de funciones: Azure SQL Database frente a SQL Server 

Azure SQL Database comparte una base de código común con SQL Server. Las características de SQL Server que Azure SQL Database admite dependen del tipo de base de datos SQL de Azure creado por el usuario. Con Azure SQL Database, puede crear una base de datos como parte de una [instancia administrada](sql-database-managed-instance.md) (actualmente en versión preliminar pública) o puede crear una base de datos que sea una base de datos única o una base de datos que forme parte de un grupo elástico. 

Microsoft continúa agregando características a Azure SQL Database. Visite la página web Actualizaciones de servicios de Azure para las actualizaciones más recientes mediante estos filtros:

* Filtrado a [Servicio de SQL Database](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado a Disponibilidad general [Anuncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para características de SQL Database.

## <a name="sql-server-and-sql-database-feature-support"></a>Compatibilidad con las características de SQL Server y SQL Database

En las tablas siguientes se enumeran las características principales de SQL Server y se proporciona información sobre si la característica se admite parcial o totalmente, así como un vínculo a más información sobre dicha característica. 

| **Característica de SQL** | **Compatible con Azure SQL Database** | **Instancia administrada (versión preliminar)** |
| --- | --- | --- |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Sí; consulte los artículos sobre el [almacén de certificados](sql-database-always-encrypted.md) y el [almacén de claves](sql-database-always-encrypted-azure-key-vault.md). | Sí; consulte los artículos sobre el [almacén de certificados](sql-database-always-encrypted.md) y el [almacén de claves](sql-database-always-encrypted-azure-key-vault.md). |
| [Grupos de disponibilidad AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Cada base de datos incluye [alta disponibilidad](sql-database-high-availability.md). La recuperación ante desastres se explica en [Información general sobre continuidad empresarial con Azure SQL Database](sql-database-business-continuity.md) | Cada base de datos incluye [alta disponibilidad](sql-database-high-availability.md). La recuperación ante desastres se explica en [Información general sobre continuidad empresarial con Azure SQL Database](sql-database-business-continuity.md) |
| [Conectar una base de datos](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Sin  | Sin  |
| [Roles de la aplicación](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Sí | Sí |
|[Auditoría](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Sí](sql-database-auditing.md)| [Sí](sql-database-managed-instance-auditing.md) |
| [Copias de seguridad automáticas](sql-database-automated-backups.md) | Sí | Sí |
| [Ajuste automático (forzar el plan)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sí](sql-database-automatic-tuning.md)| [Sí](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Ajuste automático (índices)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sí](sql-database-automatic-tuning.md)| Sin  |
| [Archivo BACPAC (exportar)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sí; consulte el artículo sobre [exportación de SQL Database](sql-database-export.md). | Sí |
| [Archivo BACPAC (importar)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sí; consulte el artículo sobre [importación de SQL Database](sql-database-import.md). | Sí |
| [Comando BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | No, solo copias de seguridad automáticas iniciadas por el sistema; consulte [Copias de seguridad automáticas](sql-database-automated-backups.md) | Copias de seguridad automatizadas iniciadas por el sistema y copias de seguridad de solo copia iniciadas por el usuario; consulte el artículo sobre las [diferencias de copia de seguridad](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Funciones integradas](https://docs.microsoft.com/sql/t-sql/functions/functions) | La mayoría; consulte las funciones. | S; consulte el artículo sobre las [diferencias de procedimientos almacenados, funciones y desencadenadores](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Captura de datos modificados](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Sin  | Sí |
| [Seguimiento de cambios](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Sí |Sí |
| [Instrucciones de intercalación](https://docs.microsoft.com/sql/t-sql/statements/collations) | Sí | Sí |
| [Índices de almacén de columnas](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Sí: [plan Premium y Estándar (S3 y versiones posteriores)](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Sí |
| [Common Language Runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Sin  | Sí; consulte el artículo sobre las [diferencias de CLR](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Bases de datos independientes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Sí | Sí |
| [Usuarios contenidos](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Sí | Sí |
| [Palabras clave del lenguaje de control de flujo](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Sí | Sí |
| [Consultas entre bases de datos](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | No; consulte el artículo sobre [consultas elásticas](sql-database-elastic-query-overview.md) | Sí, más el artículo sobre [consultas elásticas](sql-database-elastic-query-overview.md) |
| [Transacciones entre bases de datos](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Sin  | Sí |
| [Cursores](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Sí |Sí | 
| [Compresión de datos](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Sí |Sí |
| [Correo electrónico de base de datos](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Sin  | Sí |
| [Data Migration Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Sí | Sí |
| [Creación de reflejo de la base de datos](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Sin  | Sin  |
| [Configuración de la base de datos](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Sí | Sí |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Sin  | Sin  |
| [Instantáneas de base de datos](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Sin  | Sin  |
| [Tipos de datos](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Sí |Sí |
| [Instrucciones DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | La mayoría; consulte el artículo sobre instrucciones. | Sí; consulte el artículo sobre [diferencias de DBCC](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Instrucciones DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | La mayoría; consulte el artículo sobre instrucciones. | Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Desencadenadores DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Solo base de datos |  Sí |
| [Vistas con particiones distribuidas](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Sin  | Sí |
| [Transacciones distribuidas: MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | No; consulte el artículo sobre [transacciones elásticas](sql-database-elastic-transactions-overview.md). |  No; consulte el artículo sobre [transacciones elásticas](sql-database-elastic-transactions-overview.md). |
| [Instrucciones DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Sí | Sí |
| [Desencadenadores DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | La mayoría; consulte el artículo sobre instrucciones. |  Sí |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | La mayoría; consulte el artículo sobre DMV |  Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
|[Enmascaramiento de datos dinámicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Sí](sql-database-dynamic-data-masking-get-started.md)| Sí |
| [Grupos elásticos](sql-database-elastic-pool.md) | Sí | Una sola instancia administrada puede tener varias bases de datos que comparten el mismo grupo de recursos |
| [Notificaciones de eventos](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | No; consulte el artículo sobre [alertas](sql-database-insights-alerts-portal.md). | Sí |
| [Expresiones](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Sí | Sí |
| [Eventos extendidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Algunas; consulte [Eventos extendidos en SQL Database](sql-database-xevent-db-diff-from-svr.md) | S; consulte el artículo sobre [diferencias de eventos extendidos](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Procedimientos almacenados extendidos](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Sin  | Sin  |
[Archivos y grupos de archivos](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Solo el grupo de archivos principal. | Sí |
| [Secuencia de archivos](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Sin  | Sin  |
| [Búsqueda de texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  No se admiten separadores de palabras de terceros. |No se admiten separadores de palabras de terceros. |
| [Funciones](https://docs.microsoft.com/sql/t-sql/functions/functions) | La mayoría; consulte las funciones. | S; consulte el artículo sobre las [diferencias de procedimientos almacenados, funciones y desencadenadores](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Restauración geográfica](sql-database-recovery-using-backups.md#geo-restore) | Sí | No; puede restaurar copias de seguridad completas COPY_ONLY que toma periódicamente; consulte el artículo sobre [diferencias de copias de seguridad](sql-database-managed-instance-transact-sql-information.md#backup) y [diferencias de restauración](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Replicación geográfica](sql-database-geo-replication-overview.md) | Sí | Sin  |
| [Procesamiento de Graph](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Sí | Sí |
| [Optimización en memoria](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Sí; [solo en Premium Edition](sql-database-in-memory.md). | Sin  |
| [Compatibilidad con datos JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | Sí | Sí |
| [Elementos de lenguaje](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | La mayoría; consulte el artículo sobre elementos. |  Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Servidores vinculados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | No; consulte el artículo sobre [consulta elástica](sql-database-elastic-query-horizontal-partitioning.md). | Solo con SQL Server |
| [Trasvase de registros](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Cada base de datos incluye [alta disponibilidad](sql-database-high-availability.md). La recuperación ante desastres se explica en [Información general sobre continuidad empresarial con Azure SQL Database](sql-database-business-continuity.md) |Cada base de datos incluye [alta disponibilidad](sql-database-high-availability.md). La recuperación ante desastres se explica en [Información general sobre continuidad empresarial con Azure SQL Database](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Sin  | Sin  |
| [Registro mínimo durante la importación en bloque](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Sin  | Sin  |
| [Modificación de datos del sistema](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Sin  | Sí |
| [Operaciones de índice en línea](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Sí | Sí |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Sin |Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Sí|Sí|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Sin |Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Sin |Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Sí|Sí|
| [Operadores](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | La mayoría; consulte el artículo sobre operadores. |Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Creación de particiones](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Sí | Sí |
| [Restauración de base de datos a un momento dado](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Sí; consulte el artículo sobre [recuperación SQL Database](sql-database-recovery-using-backups.md#point-in-time-restore) | Sí; consulte el artículo sobre [recuperación SQL Database](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Sin  | Sin  |
| [Administración basada en directivas](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Sin  | Sin  |
| [Predicados](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Sí | Sí |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Versión preliminar; consulte el artículo [What's new in machine learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services) (Novedades del aprendizaje automático).  | Sin  |
| [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Sin  | Sin  |
| [Instrucciones RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Sin  | Sí; consulte el artículo sobre [diferencias de restauración](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Restaurar la base de datos a partir de una copia de seguridad](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Solo a partir de copias de seguridad automatizadas; consulte el artículo sobre [recuperación de SQL Database](sql-database-recovery-using-backups.md) | Desde copias de seguridad automatizadas (consulte el artículo sobre [recuperación de SQL Database](sql-database-recovery-using-backups.md)) y desde copias de seguridad completas (consulte el artículo sobre [diferencias de copia de seguridad](sql-database-managed-instance-transact-sql-information.md#backup)) |
| [Seguridad de nivel de fila](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Sí | Sí |
| [Búsqueda semántica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Sin  | Sin  |
| [Número de secuencias](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Sí | Sí |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Sin  | Sí; consulte el artículo sobre [diferencias de Service Broker](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Valores de configuración del servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Sin  | Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Instrucciones SET](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | La mayoría; consulte el artículo sobre instrucciones. | Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Sí | Sí |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Sí | Sí |
| [SQL Data Sync](sql-database-get-started-sql-data-sync.md) | Sí | Sí |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | Sí | Sí |
| [Agente SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | No - Consulte [Trabajos elásticos](sql-database-elastic-jobs-getting-started.md). | Sí; consulte el artículo sobre [diferencias de Agente SQL Server](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | No; consulte el artículo sobre [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | No; consulte [Administración de Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/). |
| [Auditoría de SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | No; consulte el artículo sobre [auditoría de SQL Database](sql-database-auditing.md). | Sí; consulte el artículo sobre las [diferencias de auditoría](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Sí | Sí |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Parcial; el desarrollo de paquetes en SQL Server Data Tools. | No, no en versión preliminar pública |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Sí | Sí |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Sí | Sí |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | No; consulte el artículo sobre [eventos extendidos](sql-database-xevent-db-diff-from-svr.md). | Sí |
| [Replicación de SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Solo los suscriptores de replicación transaccional y replicación de instantáneas](sql-database-cloud-migrate.md) | Sin  |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | No; [consulte el artículo sobre Power BI](https://docs.microsoft.com/power-bi/) | No; [consulte el artículo sobre Power BI](https://docs.microsoft.com/power-bi/) |
| [procedimientos almacenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Sí | Sí |
| [Funciones almacenadas del sistema](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | La mayoría; consulte las funciones. | S; consulte el artículo sobre las [diferencias de procedimientos almacenados, funciones y desencadenadores](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Procedimientos almacenados del sistema](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Algunas; consulte el artículo sobre procedimientos almacenados. | S; consulte el artículo sobre las [diferencias de procedimientos almacenados, funciones y desencadenadores](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Tablas del sistema](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Algunas; consulte el artículo sobre tablas. | Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Vistas de catálogo del sistema](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Algunas; consulte el artículo sobre vistas. | Sí; consulte el artículo sobre [diferencias de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Tablas temporales](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Tablas temporales globales locales y de ámbito de base de datos | Tablas temporales globales locales y de ámbito de instancia |
| [Tablas temporales](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | Sí | Sí |
|Detección de amenazas|  [Sí](sql-database-threat-detection.md)|[Sí](sql-database-managed-instance-threat-detection.md)|
| [Marcas de seguimiento](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Sin  | Sin  |
| [Variables](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Sí | Sí |
| [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Sí | No, no en versión preliminar pública |
[Red virtual](../virtual-network/virtual-networks-overview.md) | Parcial; consulte el artículo sobre [puntos de conexión de VNET](sql-database-vnet-service-endpoint-rule-overview.md) | Sí; solo el modelo de Resource Manager |
| [Agrupación en clústeres de conmutación por error de Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Cada base de datos incluye [alta disponibilidad](sql-database-high-availability.md). La recuperación ante desastres se explica en [Información general sobre continuidad empresarial con Azure SQL Database](sql-database-business-continuity.md) | Cada base de datos incluye [alta disponibilidad](sql-database-high-availability.md). La recuperación ante desastres se explica en [Información general sobre continuidad empresarial con Azure SQL Database](sql-database-business-continuity.md) |
| [Índices XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Sí | Sí |

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el servicio Azure SQL Database, consulte el artículo que explica [en qué consiste SQL Database](sql-database-technical-overview.md).
- Para más información acerca de la instancia administrada, consulte [What is a Managed Instance?](sql-database-managed-instance.md) (¿Qué es Instancia administrada?).
