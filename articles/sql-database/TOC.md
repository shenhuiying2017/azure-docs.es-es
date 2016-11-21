# Información general
## [¿Qué es Base de datos SQL?](sql-database-technical-overview.md)

## Características
### [Niveles de servicio](sql-database-service-tiers.md)
### [Unidades de transacción de base de datos](sql-database-what-is-a-dtu.md)
### [Introducción al banco de pruebas de DTU](sql-database-benchmark-overview.md)
### [Herramientas de administración](sql-database-manage-overview.md)
## Consideraciones y limitaciones
### [SQL Database frente a SQL en una máquina virtual](sql-database-paas-vs-sql-server-iaas.md)
### [Diferencias de T-SQL](sql-database-transact-sql-information.md)
### [Límites de recursos](sql-database-resource-limits.md)
### [Limitaciones generales](sql-database-general-limitations.md)
### [Directrices de seguridad](sql-database-security-guidelines.md)

## Ventajas
### [Aprende y se adapta](sql-database-learn-and-adapt.md)
### [Escalado sobre la marcha](sql-database-scale-on-the-fly.md)
### [Crea aplicaciones multiinquilino](sql-database-build-multi-tenant-apps.md)
### [Asegura y protege](sql-database-helps-secures-and-protects.md)
### [Trabaja en su entorno](sql-database-works-in-your-environment.md)

## Escenarios

### Servidores, grupos, bases de datos y firewalls
#### [Cuándo se utiliza un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md)
#### [Grupos de bases de datos elásticas](sql-database-elastic-pool.md)
#### [Automation](sql-database-manage-automation.md)

### Bases de datos de escalado horizontal
#### [Información general](sql-database-elastic-scale-introduction.md)
#### [Creación de bases de datos escalables en la nube](sql-database-elastic-database-client-library.md)
#### [Trabajos entre bases de datos](sql-database-elastic-jobs-overview.md)
#### [Glosario de las herramientas de bases de datos elásticas](sql-database-elastic-scale-glossary.md)
#### [Preguntas más frecuentes](sql-database-elastic-scale-faq.md)

### Acceso y permisos
#### [Información general](sql-database-security.md)
#### [Azure Security Center para Azure SQL Database](../security-center/security-center-sql-database.md?toc=%2fazure%2fsql-database%2ftoc.json)
#### [Centro de seguridad de SQL](https://msdn.microsoft.com/library/azure/bb510589)

### Continuidad del negocio
#### [Información general](sql-database-business-continuity.md)
#### [Copias de seguridad de bases de datos](sql-database-automated-backups.md)
#### [Recuperación de bases de datos a partir de copias de seguridad](sql-database-recovery-using-backups.md)
#### [Requisitos de autenticación para la recuperación ante desastres](sql-database-geo-replication-security-config.md)
#### [Escenarios de diseño de la continuidad empresarial](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estrategias de recuperación ante desastres con grupos elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Actualizaciones graduales](sql-database-manage-application-rolling-upgrade.md)

### [Desarrollo de bases de datos](sql-database-develop-overview.md)
### [Migración de bases de datos de SQL Server](sql-database-cloud-migrate.md)

## Implementaciones del cliente
### [Software Daxko/CSI](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)


# Primeros pasos
## [Inicios rápidos de soluciones](sql-database-solution-quick-starts.md)
## Creación de una Base de datos SQL
### [Azure Portal](sql-database-get-started.md)
### [C#](sql-database-get-started-csharp.md)
### [PowerShell](sql-database-get-started-powershell.md)
## Creación y administración de bases de datos escaladas horizontalmente
### [Escalado elástico](sql-database-elastic-scale-get-started.md)
### [Trabajos elásticos](sql-database-elastic-jobs-getting-started.md)
### [Informes entre bases de datos](sql-database-elastic-query-getting-started.md)
### [Consultas entre bases de datos](sql-database-elastic-query-getting-started-vertical.md)
## Supervisión y ajuste de bases de datos
### [Introducción a SQL Database Advisor](sql-database-advisor.md)
### [Detalles de la carga de trabajo en Azure Portal](sql-database-performance.md)
## [Creación y administración de acceso y permisos](sql-database-get-started-security.md)
## [Optimización en memoria](sql-database-in-memory.md)
## [Sincronización de datos](sql-database-get-started-sql-data-sync.md)
## [Preguntas más frecuentes sobre la Base de datos SQL](sql-database-faq.md)

# Procedimientos

## Creación y administración
### Servidores y bases de datos
#### [Bases de datos únicas](sql-database-manage-portal.md)
#### [Azure Portal](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Grupos de bases de datos elásticas
#### [Azure Portal](sql-database-elastic-pool-create-portal.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
### Bases de datos con particiones
#### [Uso de Shard Map Manager](sql-database-elastic-scale-shard-map-management.md)
#### [Configuración de seguridad de combinación y división](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [Trabajo con Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Uso de Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)
#### [Seguridad de nivel de fila multiinquilino](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Administración de credenciales](sql-database-elastic-scale-manage-credentials.md)
#### [Implemente un servicio de división y combinación](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Incorporación de una partición](sql-database-elastic-scale-add-a-shard.md)
#### [Uso de la clase RecoveryManager para solucionar problemas de mapas de particiones](sql-database-elastic-database-recovery-manager.md)
###  Autenticación
#### [Autenticación de Azure AD](sql-database-aad-authentication.md)
#### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### Reglas de firewall
#### [Azure Portal](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [API DE REST](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Trabajos
#### [Instalación del servicio](sql-database-elastic-jobs-service-installation.md)
#### [Azure Portal](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Actualización de la biblioteca de cliente](sql-database-elastic-scale-upgrade-client-library.md)
### [Inicios de sesión](sql-database-manage-logins.md)

## Desarrollo
### [Información general](https://msdn.microsoft.com/library/mt763826.aspx)
### [Bibliotecas de conectividad](sql-database-libraries.md)
### Escenarios
#### [Aplicaciones SaaS multiinquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Datos de JSON](sql-database-json-features.md)
#### [Tablas temporales](sql-database-temporal-tables.md)
#### [Directivas de retención](sql-database-temporal-tables-retention-policy.md)
### Conexión de una aplicación
#### [.NET](sql-database-develop-dotnet-simple.md)
#### [Java](sql-database-develop-java-simple.md)
#### [Node.js](sql-database-develop-nodejs-simple.md)
#### [PHP](sql-database-develop-php-simple.md)
#### [Python](sql-database-develop-python-simple.md)
#### [Ruby](sql-database-develop-ruby-simple.md)
#### [Excel](sql-database-connect-excel.md)
#### [Visual Studio](sql-database-connect-query.md)

### [Creación de bases de datos](sql-database-get-started-powershell.md)
### Administración de grupos elásticos
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [Obtención de los valores necesarios para autenticar una aplicación](sql-database-client-id-keys.md)
### [Uso de puertos más allá de 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
### [Trabajo con mensajes de error](sql-database-develop-error-messages.md)
### [Uso de lotes](sql-database-use-batching-to-improve-performance.md)

### Referencia
#### [.NET (conceptos)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [.NET (referencia de la API)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### [Entity Framework](https://www.nuget.org/packages/EntityFramework/)
#### [Azure SDK (descarga)](https://www.visualstudio.com/vs/azure-tools/)
#### [Azure SDK (documentación)](../dotnet-sdk.md)
#### [Cmdlets de administración de servicios de PowerShell](https://msdn.microsoft.com/library/azure/dn546723.aspx)
#### REST
##### [REST (Resource Management)](https://msdn.microsoft.com/library/azure/mt420159)
##### [REST (Service Management)](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## Detectar amenazas
### [Detección de amenazas](sql-database-threat-detection-get-started.md)
### [Firewall](sql-database-firewall-configure.md)

## Cifrado de datos
### [Introducción a Always Encrypted](sql-database-always-encrypted.md)
### [Always-encrypted Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
### [Cifrado de datos transparente](https://msdn.microsoft.com/library/azure/dn948096)
### [Cifrado de columnas](https://msdn.microsoft.com/library/azure/ms179331)

## Enmascaramiento de datos
### [Enmascaramiento de datos dinámicos](sql-database-dynamic-data-masking-get-started-portal.md)
### [Clientes de nivel inferior](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)

## Migrar
### Determinación de compatibilidad
#### [Utilidad de paquetes de SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Solución de problemas de compatibilidad
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Uso del Asistente para la migración de SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [Uso de la replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [Migración de bases de datos escaladas horizontalmente existentes para escalado horizontal](sql-database-elastic-convert-to-use-elastic-tools.md)

## Supervisión y ajuste
### [Bases de datos únicas](sql-database-performance-guidance.md)
### [Información de rendimiento de consultas](sql-database-query-performance.md)
### [Asesor de Base de datos SQL](sql-database-advisor-portal.md)
### [Rendimiento de base de datos](sql-database-single-database-monitor.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [Niveles de compatibilidad](sql-database-compatibility-level-query-performance-130.md)
### [Auditoría de eventos](sql-database-auditing-get-started.md)
### [Contadores de rendimiento para Shard Map Manager](sql-database-elastic-database-perf-counters.md)
### [Sugerencias de optimización del rendimiento](sql-database-troubleshoot-performance.md)
### Cambio de niveles de servicio y de rendimiento
#### [Azure Portal](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### OLTP en memoria
#### [Adopción de OLTP en memoria](sql-database-in-memory-oltp-migration.md)
#### [Supervisión del almacenamiento OLTP en memoria](sql-database-in-memory-oltp-monitoring.md)
### Almacén de consultas
#### [Supervisión del rendimiento mediante el Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Escenarios de uso del Almacén de consultas](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Funcionamiento del Almacén de consultas](sql-database-operate-query-store.md)
### Eventos extendidos
#### [Eventos extendidos](sql-database-xevent-db-diff-from-svr.md)
#### [Código de destino del archivo de eventos](sql-database-xevent-code-event-file.md)
#### [Código de destino del búfer en anillo](sql-database-xevent-code-ring-buffer.md)

## Mover datos
### [Copia de SQL Database](sql-database-copy.md)
#### [Azure Portal](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Exportación de base de datos a un archivo BACPAC
#### [Azure Portal](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Utilidad de paquetes de SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importación de base de datos desde un archivo BACPAC
#### [Azure Portal](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Utilidad de paquetes de SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Carga desde archivo CSV mediante BCP](sql-database-load-from-csv-with-bcp.md)
### [Moving data between scaled-out cloud databases (Mover datos entre bases de datos en la nube escaladas horizontalmente)](sql-database-elastic-scale-overview-split-and-merge.md)

## Consultar
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md)
### Consultas entre bases de datos
#### [Información general](sql-database-elastic-query-overview.md)
#### [Consultas entre bases de datos con distintos esquemas](sql-database-elastic-query-vertical-partitioning.md)
#### [Informes entre bases de datos](sql-database-elastic-query-horizontal-partitioning.md)
#### [Transacciones distribuidas en bases de datos en la nube](sql-database-elastic-transactions-overview.md)

## Restauración
### Restaurar la base de datos eliminada
#### [Azure Portal](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### Restauración a un momento dado
#### [Azure Portal](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### Restauración geográfica
#### [Azure Portal](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Tabla única](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [Recuperación de una interrupción del centro de datos](sql-database-disaster-recovery.md)
### [Obtención de detalles de recuperación ante desastres](sql-database-disaster-recovery-drills.md)

## Replicar
### [Introducción a la replicación geográfica activa](sql-database-geo-replication-overview.md)
### Configuración
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### Conmutación por error
#### [Azure Portal](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## Solución de problemas
### [Problemas de conexión](sql-database-troubleshoot-common-connection-issues.md)
### [Error de conexión transitorio](sql-database-troubleshoot-connection.md)
### [Diagnóstico y prevención](sql-database-connectivity-issues.md)
### [Permisos](sql-database-troubleshoot-permissions.md)
### [Movimiento de bases de datos](sql-database-troubleshoot-moving-data.md)

# Referencia
## [PowerShell](/powershell/azureps-cmdlets-docs)
## [PowerShell clásico](/powershell/servicemanagement/)
## [Java](/java/api/)
## [.NET](/dotnet/api/)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [Cmdlets de Azure SQL Database](/powershell/resourcemanager/AzureRM.Sql/v1.0.12/AzureRM.Sql)
## [Cmdlets de SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
## [REST](/rest/api/sql/)

## Biblioteca de administración de SQL Database
### [Referencia de biblioteca de administración de SQL Database](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [Obtención del paquete de la biblioteca de administración de SQL Database](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Controladores de SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# Recursos
## [Precios](https://azure.microsoft.com/pricing/details/sql-database/)
## [Foro de MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?service=sql-database)
## [Herramientas de SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)




<!--HONumber=Nov16_HO3-->


