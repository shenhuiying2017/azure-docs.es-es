# Información general

## [¿Qué es SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md)
## [Carga de trabajo del almacenamiento de datos](sql-data-warehouse-overview-workload.md)
## [Datos distribuidos](sql-data-warehouse-distributed-data.md)
## [Preguntas más frecuentes](sql-data-warehouse-overview-faq.md)

# Introducción

## [Tutorial para principiantes](sql-data-warehouse-get-started-tutorial.md)
## [Tutorial de consultas elásticas](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
## [procedimientos recomendados](sql-data-warehouse-best-practices.md)
## [Administración](sql-data-warehouse-overview-manage.md)



# Procedimientos

## Copia de seguridad y restauración

### [Introducción a la copia de seguridad](sql-data-warehouse-backups.md)
### [Introducción a la restauración](sql-data-warehouse-restore-database-overview.md)
#### [Portal de Azure](sql-data-warehouse-restore-database-portal.md)
#### [Azure PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## Conectar

### [Información general](sql-data-warehouse-connect-overview.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Instalación de Visual Studio](sql-data-warehouse-install-visual-studio.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [Cadenas de conexión](sql-data-warehouse-connection-strings.md)

## Crear
### [Portal de Azure](sql-data-warehouse-get-started-provision.md)
### [Azure PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)

## Desarrollo

### [Información general](sql-data-warehouse-overview-develop.md)

### Tablas

#### [Información general](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Tipos de datos](sql-data-warehouse-tables-data-types.md)
#### [Tablas distribuidas](sql-data-warehouse-tables-distribute.md)
#### [Índices](sql-data-warehouse-tables-index.md)
#### [Identidad](sql-data-warehouse-tables-identity.md)
#### [Particiones](sql-data-warehouse-tables-partition.md)
#### [Tablas replicadas](design-guidance-for-replicated-tables.md)
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

### [Consulta elástica con SQL Database y SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md)

## Carga

### Conceptos
#### [Información general](sql-data-warehouse-overview-load.md)
#### [Guía de PolyBase](sql-data-warehouse-load-polybase-guide.md)

### Tutoriales
#### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)

### Guías de procedimientos
#### [Datos de ejemplo](sql-data-warehouse-load-sample-databases.md)
#### [Almacén de Azure Data Lake](sql-data-warehouse-load-from-azure-data-lake-store.md)
#### [BCP](sql-data-warehouse-load-with-bcp.md)
#### [Factoría de datos](sql-data-warehouse-load-with-data-factory.md)
#### [PolyBase desde Blob Storage](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
#### [PolyBase desde SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
#### [Redgate](sql-data-warehouse-load-with-redgate.md)
#### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## Migrar

### [Información general](sql-data-warehouse-overview-migrate.md)
### [Utilidad de migración](sql-data-warehouse-migrate-migration-utility.md)
### [Migración del esquema](sql-data-warehouse-migrate-schema.md)
### [Migración del código](sql-data-warehouse-migrate-code.md)
### [Migración de los datos](sql-data-warehouse-migrate-data.md)
### [Migración a Premium Storage](sql-data-warehouse-migrate-to-premium-storage.md)

## Administración de procesos

### [Información general](sql-data-warehouse-manage-compute-overview.md)
### [Azure Portal](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [API de REST](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)

## Rendimiento

### [Información general](sql-data-warehouse-overview-manage-user-queries.md)
### [Compresión de almacén de columnas](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Supervisión](sql-data-warehouse-manage-monitor.md)
### [Carga de trabajo](sql-data-warehouse-develop-concurrency.md)

## Seguridad

### [Información general](sql-data-warehouse-overview-manage-security.md)
### [Auditoría](sql-data-warehouse-auditing-overview.md)
### [Auditoría para clientes de nivel inferior](sql-data-warehouse-auditing-downlevel-clients.md)
### [Autenticación](sql-data-warehouse-authentication.md)
### [Cifrado](sql-data-warehouse-encryption-tde.md)
### [Cifrado con T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Detección de amenazas](sql-data-warehouse-security-threat-detection.md)

## Solución de problemas
### [Solución de problemas](sql-data-warehouse-troubleshoot.md)

# Referencia

## [Límites de capacidad](sql-data-warehouse-service-capacity-limits.md)
## T-SQL
### [Referencia completa](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Elementos del lenguaje SQL DW](sql-data-warehouse-reference-tsql-language-elements.md)
### [Instrucciones de SQL DW](sql-data-warehouse-reference-tsql-statements.md)
## [Vistas de sistema](sql-data-warehouse-reference-tsql-system-views.md)
## [Cmdlets de PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=databases)
## [Foro](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Precios](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Solicitud de función](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Asociados
### [Business intelligence](sql-data-warehouse-partner-business-intelligence.md)
### [Integración de datos](sql-data-warehouse-partner-data-integration.md)
### [Administración de datos](sql-data-warehouse-partner-data-management.md)
