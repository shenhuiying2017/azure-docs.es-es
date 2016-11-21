# Información general

## [¿Qué es SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md)
## [Carga de trabajo del almacenamiento de datos](sql-data-warehouse-overview-workload.md)
## [Datos distribuidos](sql-data-warehouse-distributed-data.md)

# Introducción

## Creación de Almacenamiento de datos SQL
### [Azure Portal](sql-data-warehouse-get-started-provision.md)
### [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)
## [procedimientos recomendados](sql-data-warehouse-best-practices.md)
## [Administración](sql-data-warehouse-overview-manage.md)
## [Obtención de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md)

# Procedimientos

## Creación de copias de seguridad y restauración de datos

### [Introducción a la copia de seguridad](sql-data-warehouse-backups.md)
### [Introducción a la restauración](sql-data-warehouse-restore-database-overview.md)
#### [Azure Portal](sql-data-warehouse-restore-database-portal.md)
#### [PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## Conectar

### [Información general](sql-data-warehouse-connect-overview.md)
### [Cadenas de conexión](sql-data-warehouse-connection-strings.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Instalación de Visual Studio](sql-data-warehouse-install-visual-studio.md)

## Desarrollo

### [Información general](sql-data-warehouse-overview-develop.md)

### Tablas

#### [Información general](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Tipos de datos](sql-data-warehouse-tables-data-types.md)
#### [Tablas distribuidas](sql-data-warehouse-tables-distribute.md)
#### [Índices](sql-data-warehouse-tables-index.md)
#### [Particiones](sql-data-warehouse-tables-partition.md)
#### [Estadísticas](sql-data-warehouse-tables-statistics.md)
#### [Temporal](sql-data-warehouse-tables-temporary.md)

### Consultas

#### [SQL dinámico](sql-data-warehouse-develop-dynamic-sql.md)
#### [Opciones de Agrupar por](sql-data-warehouse-develop-group-by-options.md)
#### [Etiquetas](sql-data-warehouse-develop-label.md)

### Elementos del lenguaje T-SQL

#### [Bucles](sql-data-warehouse-develop-loops.md)
#### [Procedimientos almacenados](sql-data-warehouse-develop-stored-procedures.md)
#### [Transacciones](sql-data-warehouse-develop-transactions.md)
#### [Procedimientos recomendados relacionados con las transacciones](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Esquemas definidos por el usuario](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Asignación de variables](sql-data-warehouse-develop-variable-assignment.md)
#### [Vistas](sql-data-warehouse-develop-views.md)

## Integrate

### [Información general](sql-data-warehouse-overview-integrate.md)
### [Factoría de datos](sql-data-warehouse-integrate-azure-data-factory.md)
### [Aprendizaje automático](sql-data-warehouse-integrate-azure-machine-learning.md)
### [Tutorial sobre Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Visualización de Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)

## Carga

### [Información general](sql-data-warehouse-overview-load.md)
### [Datos de ejemplo](sql-data-warehouse-load-sample-databases.md)
### [AZCopy](sql-data-warehouse-load-from-sql-server-with-azcopy.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [BCP desde SQL Server](sql-data-warehouse-load-from-sql-server-with-bcp.md)
### [Factoría de datos](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
### [Data Factory desde Blob Storage](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
### [Guía de PolyBase](sql-data-warehouse-load-polybase-guide.md)
### [PolyBase desde Blob Storage](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [PolyBase desde SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [Redgate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## Migrar

### [Información general](sql-data-warehouse-overview-migrate.md)
### [Utilidad de migración](sql-data-warehouse-migrate-migration-utility.md)
### [Migración del esquema](sql-data-warehouse-migrate-schema.md)
### [Migración del código](sql-data-warehouse-migrate-code.md)
### [Migración de los datos](sql-data-warehouse-migrate-data.md)
### [Migración a Premium Storage](sql-data-warehouse-migrate-to-premium-storage.md)

## Pausa y escalado

### [Información general](sql-data-warehouse-manage-compute-overview.md)
### [Azure Portal](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [API DE REST](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)

## Rendimiento

### [Información general](sql-data-warehouse-overview-manage-user-queries.md)
### [Compresión de almacén de columnas](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Consultas](sql-data-warehouse-manage-monitor.md)
### [Carga de trabajo](sql-data-warehouse-develop-concurrency.md)
### [Solución de problemas](sql-data-warehouse-troubleshoot.md)

## Seguridad

### [Información general](sql-data-warehouse-overview-manage-security.md)
### [Auditoría](sql-data-warehouse-auditing-overview.md)
### [Auditoría para clientes de nivel inferior](sql-data-warehouse-auditing-downlevel-clients.md)
### [Autenticación](sql-data-warehouse-authentication.md)
### [Cifrado](sql-data-warehouse-encryption-tde.md)
### [Cifrado con T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Detección de amenazas](sql-data-warehouse-security-threat-detection.md)

# Referencia

## [Límites de capacidad](sql-data-warehouse-service-capacity-limits.md)
## [Elementos del lenguaje T-SQL](sql-data-warehouse-reference-tsql-language-elements.md)
## [Instrucciones de T-SQL](sql-data-warehouse-reference-tsql-statements.md)
## [Vistas del sistema T-SQL](sql-data-warehouse-reference-tsql-system-views.md)
## [Cmdlets de PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# resources
## [Precios](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Foro](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Asociados
### [Business intelligence](sql-data-warehouse-partner-business-intelligence.md)
### [Integración de datos](sql-data-warehouse-partner-data-integration.md)
### [Administración de datos](sql-data-warehouse-partner-data-management.md)


<!--HONumber=Nov16_HO3-->


