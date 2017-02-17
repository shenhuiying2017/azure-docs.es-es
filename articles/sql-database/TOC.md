# Información general
## [¿Qué es SQL Database?](sql-database-technical-overview.md)
### [Niveles de servicio](sql-database-service-tiers.md)
### [DTU y eDTU](sql-database-what-is-a-dtu.md)
### [Introducción al banco de pruebas de DTU](sql-database-benchmark-overview.md)
### [Límites de recursos](sql-database-resource-limits.md)
### [Características](sql-database-features.md)
### [Preguntas más frecuentes sobre SQL Database](sql-database-faq.md)
## Comparaciones
### [SQL Database frente a SQL en una máquina virtual](sql-database-paas-vs-sql-server-iaas.md)
### [Diferencias de T-SQL](sql-database-transact-sql-information.md)
### [SQL frente a NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [Herramienta de SQL Database](sql-database-manage-overview.md)
## [Tutoriales de SQL Database](sql-database-explore-tutorials.md)
## [Inicios rápidos de soluciones](sql-database-solution-quick-starts.md)
## Seguridad
### [Introducción a la seguridad](sql-database-security-overview.md)
### [Azure Security Center para Azure SQL Database](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL Security Center](https://msdn.microsoft.com/library/azure/bb510589)
# Introducción
## Bases de datos y servidores
### Aprender
#### [Servidores](sql-database-server-overview.md)
#### [Bases de datos únicas](sql-database-overview.md)
#### [Varias bases de datos](sql-database-elastic-scale-introduction.md)
##### Asignación de inquilinos
###### [Biblioteca de cliente elástica](sql-database-elastic-database-client-library.md)
###### [Administrador de mapas de particiones](sql-database-elastic-scale-shard-map-management.md)
###### [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)
###### [Administración de credenciales](sql-database-elastic-scale-manage-credentials.md)
###### [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md)
##### Grupos elásticos (grupos de recursos)
###### [¿Qué es un grupo elástico?](sql-database-elastic-pool.md)
###### [¿Cuándo se utiliza un grupo elástico?](sql-database-elastic-pool-guidance.md)
###### [Precios de grupo elásticos](sql-database-elastic-pool-price.md)
##### Bases de datos con particiones
###### [Glosario sobre herramientas elásticas](sql-database-elastic-scale-glossary.md)
###### [Movimiento de datos entre particiones](sql-database-elastic-scale-overview-split-and-merge.md)
###### [Preguntas más frecuentes sobre herramientas elásticas](sql-database-elastic-scale-faq.md)
##### Consulta elástica (consultas entre bases de datos)
###### [¿Qué es una consulta elástica?](sql-database-elastic-query-overview.md)
##### Transacciones elásticas (transacciones distribuidas)
###### [Transacciones en bases de datos en la nube](sql-database-elastic-transactions-overview.md)
##### Trabajos elásticos (trabajos entre bases de datos)
###### [¿Qué es un trabajo elástico?](sql-database-elastic-jobs-overview.md)
#### [Uso de Azure RemoteApp para conectarse a SQL Database](sql-database-ssms-remoteapp.md)
#### [Administración de bases de datos SQL mediante el servicio Azure Automation](sql-database-manage-automation.md)
### Sí
#### [Creación de una base de datos única mediante Azure Portal](sql-database-get-started.md)
#### [Creación de una base de datos única con PowerShell](sql-database-get-started-powershell.md)
#### [Creación de una base de datos única con C#](sql-database-get-started-csharp.md)
#### [Creación de una aplicación con particiones](sql-database-elastic-scale-get-started.md)
#### [Movimiento de datos entre particiones](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Introducción a los trabajos elásticos](sql-database-elastic-jobs-getting-started.md)
#### [Introducción a las consultas elásticas](sql-database-elastic-query-getting-started-vertical.md)
#### [Creación de informes mediante consultas elásticas](sql-database-elastic-query-getting-started.md)
#### [Consulta de bases de datos con diferentes esquemas](sql-database-elastic-query-vertical-partitioning.md)
#### [Informes de bases de datos escaladas horizontalmente](sql-database-elastic-query-horizontal-partitioning.md)
## Migración y movimiento de datos
### Aprender
#### [Migración de una base de datos](sql-database-cloud-migrate.md)
#### [Replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Sincronización de datos](sql-database-get-started-sql-data-sync.md)
#### [Copia de SQL Database](sql-database-copy.md)
## Reglas de firewall, autenticación y autorización
### Aprender
#### [Control de acceso](sql-database-control-access.md)
#### [Firewall](sql-database-firewall-configure.md)
#### [Administración de inicios de sesión](sql-database-manage-logins.md)
### Sí
#### [Autenticación y autorización de SQL](sql-database-control-access-sql-authentication-get-started.md)
#### [Autenticación y autorización en Azure AD](sql-database-control-access-aad-authentication-get-started.md)
## Protección de los datos
### Aprender
#### Auditoría
##### [Auditoría](sql-database-auditing-get-started.md)
##### [Compatibilidad con clientes de nivel inferior y cambios de punto de conexión IP para auditoría](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Detección de amenazas](sql-database-threat-detection-get-started.md)
#### Cifrado de datos
##### [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
##### [Cifrado de datos transparente](https://msdn.microsoft.com/library/azure/dn948096)
##### [Cifrado de columnas](https://msdn.microsoft.com/library/azure/ms179331)
#### Enmascaramiento de datos
##### Enmascaramiento de datos dinámicos
###### [Azure Portal](sql-database-dynamic-data-masking-get-started.md)
### Sí
#### [Enmascaramiento dinámico de datos mediante Azure Portal](sql-database-dynamic-data-masking-get-started.md)
##### [Always Encrypted mediante el almacén de certificados de Windows](sql-database-always-encrypted.md)
## Continuidad del negocio
### Aprender
#### [Información general](sql-database-business-continuity.md)
#### [Copias de seguridad de bases de datos](sql-database-automated-backups.md)
#### [Retención a largo plazo](sql-database-long-term-retention.md)
#### [Recuperación de bases de datos a partir de copias de seguridad](sql-database-recovery-using-backups.md)
#### [Recuperación de una sola tabla](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Recuperación de una interrupción del centro de datos](sql-database-disaster-recovery.md)
#### [Requisitos de autenticación para la recuperación ante desastres](sql-database-geo-replication-security-config.md)
#### [Escenarios de diseño de la continuidad empresarial](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estrategias de recuperación ante desastres con grupos elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Actualizaciones graduales](sql-database-manage-application-rolling-upgrade.md)
#### [Obtención de detalles de recuperación ante desastres](sql-database-disaster-recovery-drills.md)
#### [Introducción a la replicación geográfica activa](sql-database-geo-replication-overview.md)
### Sí
#### [Azure Portal: copia de seguridad y restauración](sql-database-get-started-backup-recovery.md)
#### [PowerShell: copia de seguridad y restauración](sql-database-get-started-backup-recovery-powershell.md)
## Desarrollo de aplicaciones
### Aprender
#### [Introducción al desarrollo de aplicaciones de bases de datos](sql-database-develop-overview.md)
#### [Bibliotecas de conectividad](sql-database-libraries.md)
#### [Aplicaciones SaaS multiinquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Escalado de aplicaciones SaaS multiinquilino con seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Uso de puertos más allá de 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Obtención de los valores necesarios para autenticar una aplicación](sql-database-client-id-keys.md)
### Sí
#### Conexión de una aplicación
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C y C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Conexión a Visual Studio](sql-database-connect-query.md)
#### [Compilación de una aplicación cliente](https://www.microsoft.com/sql-server/developer-get-started)
#### [Trabajo con mensajes de error](sql-database-develop-error-messages.md)
#### [Uso de Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Uso de biblioteca de cliente con Dapper](sql-database-elastic-scale-working-with-dapper.md)
### Implementaciones del cliente
#### [Software Daxko/CSI](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## Desarrollo de bases de datos
### Aprender
#### Tablas temporales
##### [Tablas temporales](sql-database-temporal-tables.md)
##### [Directivas de retención](sql-database-temporal-tables-retention-policy.md)
#### [Datos de JSON](sql-database-json-features.md)
#### [Optimización en memoria](sql-database-in-memory.md)
### Sí
#### [Desarrollo de SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
#### [Adopción de OLTP en memoria](sql-database-in-memory-oltp-migration.md)
## Supervisión y optimización
### Aprender
#### [Bases de datos únicas](sql-database-single-database-monitor.md)
#### [Introducción a SQL Database Advisor](sql-database-advisor.md)
#### [Guía de base de datos única](sql-database-performance-guidance.md)
#### [Información del rendimiento: Azure Portal](sql-database-performance.md)
#### [Uso de lotes](sql-database-use-batching-to-improve-performance.md)
#### [Eventos extendidos](sql-database-xevent-db-diff-from-svr.md)
## SQL Database V11
### [Retirada de las versiones Web y Business](sql-database-web-business-sunset-faq.md)
### [Service Tier Advisor](sql-database-service-tier-advisor.md)
### [Herramienta de evaluación de grupo elástico](sql-database-elastic-pool-database-assessment-powershell.md)
### [Actualización a V12](sql-database-v12-plan-prepare-upgrade.md)
#### [Actualización mediante Azure Portal](sql-database-upgrade-server-portal.md)
#### [Actualización mediante PowerShell](sql-database-upgrade-server-powershell.md)
# Procedimientos
## Creación y administración
### [Administración de SQL Database mediante Azure Portal](sql-database-manage-portal.md)
### [Administración de SQL Database con PowerShell](sql-database-manage-powershell.md)
### [Administración de SQL Database con SSMS](sql-database-manage-azure-ssms.md)
### Servidores
#### [Creación de servidores](sql-database-create-servers.md)
#### [Visualización o actualización de la configuración de servidor](sql-database-view-update-server-settings.md)
### Bases de datos únicas
#### [Creación de bases de datos únicas](sql-database-create-databases.md)
#### [Visualización o actualización de la configuración de la base de datos](sql-database-view-update-database-settings.md)
### Reglas de firewall
#### [Creación de reglas de firewall mediante Azure Portal](sql-database-configure-firewall-settings.md)
#### [Creación de reglas de firewall mediante PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [Creación de reglas de firewall mediante la API de REST](sql-database-configure-firewall-settings-rest.md)
#### [Creación de reglas de firewall mediante T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Varias bases de datos
#### [Actualización de la biblioteca de cliente en aplicaciones cliente](sql-database-elastic-scale-upgrade-client-library.md)
#### Bases de datos con particiones
##### [Configuración de seguridad](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Incorporación de una partición](sql-database-elastic-scale-add-a-shard.md)
##### [Solución de problemas de asignación de particiones](sql-database-elastic-database-recovery-manager.md)
##### [Migración de bases de datos escaladas horizontalmente y ya existentes, a bases de datos con particiones](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Creación de contadores de rendimiento para Shard Map Manager](sql-database-elastic-database-perf-counters.md)
#### Trabajos elásticos
##### [¿Cómo se instala el servicio de trabajos elásticos?](sql-database-elastic-jobs-service-installation.md)
##### [Creación y administración de trabajos elásticos mediante PowerShell](sql-database-elastic-jobs-powershell.md) 
##### [Creación y administración de trabajos elásticos mediante Azure Portal](sql-database-elastic-jobs-create-and-manage.md)
##### [Desinstalación de trabajos elásticos](sql-database-elastic-jobs-uninstall.md)
#### Grupos elásticos
##### [Creación mediante Azure Portal](sql-database-elastic-pool-create-portal.md)
##### [Creación mediante PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Creación mediante C#](sql-database-elastic-pool-create-csharp.md)
##### [Administración mediante Azure Portal](sql-database-elastic-pool-manage-portal.md)
##### [Administración mediante PowerShell](sql-database-elastic-pool-manage-powershell.md)
##### [Administración mediante C#](sql-database-elastic-pool-manage-csharp.md)
##### [Administración mediante T-SQL](sql-database-elastic-pool-manage-tsql.md)
##  Autenticación y autorización
### [Autenticación de Azure AD](sql-database-aad-authentication.md)
### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
## Cifrado de datos
### [Cifrado de datos transparente](https://msdn.microsoft.com/library/azure/dn948096)
### [Cifrado de columnas](https://msdn.microsoft.com/library/azure/ms179331)
## Migración de bases de datos
### Determinación de compatibilidad
#### [Determinación de la compatibilidad con la utilidad SQL Package](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [Determinación de compatibilidad con SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Solución de problemas de compatibilidad
#### [Solución de problemas de compatibilidad mediante SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [Solución de problemas de compatibilidad mediante SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Solución de problemas de compatibilidad mediante SMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Migración mediante el Asistente para migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## Supervisión y ajuste
### [Información de rendimiento de consultas](sql-database-query-performance.md)
### [Asesor de SQL Database](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [Niveles de compatibilidad](sql-database-compatibility-level-query-performance-130.md)
### [Sugerencias de optimización del rendimiento](sql-database-troubleshoot-performance.md)
### Cambio de niveles de servicio y de rendimiento
#### [Cambio de los niveles de servicio mediante Azure Portal](sql-database-scale-up.md)
#### [Cambio de los niveles de servicio mediante PowerShell](sql-database-scale-up-powershell.md)
### [Creación de alertas](sql-database-insights-alerts-portal.md)
#### [Supervisión del almacenamiento OLTP en memoria](sql-database-in-memory-oltp-monitoring.md)
### Almacén de consultas
#### [Supervisión del rendimiento mediante el Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Escenarios de uso del Almacén de consultas](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Funcionamiento del Almacén de consultas](sql-database-operate-query-store.md)
### Eventos extendidos
#### [Código de destino del archivo de eventos](sql-database-xevent-code-event-file.md)
#### [Código de destino del búfer en anillo](sql-database-xevent-code-ring-buffer.md)
## Mover datos
### Copia de SQL Database
#### [Copia mediante Azure Portal](sql-database-copy-portal.md)
#### [Copia mediante PowerShell](sql-database-copy-powershell.md)
#### [Copia mediante T-SQL](sql-database-copy-transact-sql.md)
### Exportación de base de datos a un archivo BACPAC
#### [Exportación mediante Azure Portal](sql-database-export.md)
#### [Exportación mediante SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Exportación mediante la utilidad SQL Package](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [Exportación mediante PowerShell](sql-database-export-powershell.md)
### Importación de base de datos desde un archivo BACPAC
#### [Importación mediante Azure Portal](sql-database-import.md)
#### [Importación mediante PowerShell](sql-database-import-powershell.md)
#### [Importación mediante SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Importación mediante la utilidad SQL Package](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Carga desde archivo CSV mediante BCP](sql-database-load-from-csv-with-bcp.md)
## Consultar
### [Consulta mediante SSMS](sql-database-connect-query-ssms.md)
## Copia de seguridad y restauración
### Retención de copia de seguridad a largo plazo
#### [Configuración de la retención de copia de seguridad a largo plazo](sql-database-configure-long-term-retention.md)
#### [Visualización de copias de seguridad en un almacén de Recovery Services](sql-database-view-backups-in-vault.md)
#### [Restauración de la retención de copia de seguridad a largo plazo](sql-database-restore-from-long-term-retention.md)
#### [Eliminación de la retención de copia de seguridad a largo plazo](sql-database-long-term-retention-delete.md)
### Restaurar la base de datos eliminada
#### [Restauración de una base de datos eliminada mediante Azure Portal](sql-database-restore-deleted-database-portal.md)
#### [Restauración de bases de datos eliminadas mediante PowerShell](sql-database-restore-deleted-database-powershell.md)
### Restauración a un momento dado
#### [Restauración a un punto en el tiempo](sql-database-point-in-time-restore.md)
#### [Visualización del punto de restauración más antiguo](sql-database-view-oldest-restore-point.md)
### [Restauración a partir de una copia de seguridad con redundancia geográfica](sql-database-geo-restore.md)
## Replicación geográfica activa
### [Configuración mediante Azure Portal](sql-database-geo-replication-portal.md)
### [Configuración mediante PowerShell](sql-database-geo-replication-powershell.md)
### [Configuración mediante T-SQL](sql-database-geo-replication-transact-sql.md)
### [Conmutación por error mediante Azure Portal](sql-database-geo-replication-failover-portal.md)
### [Conmutación por error mediante PowerShell](sql-database-geo-replication-failover-powershell.md)
### [Conmutación por error mediante T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Solución de problemas
### [Problemas de conexión](sql-database-troubleshoot-common-connection-issues.md)
### [Error de conexión transitorio](sql-database-troubleshoot-connection.md)
### [Diagnóstico y prevención](sql-database-connectivity-issues.md)
### [Permisos](sql-database-troubleshoot-permissions.md)
### [Movimiento de bases de datos](sql-database-troubleshoot-moving-data.md)
# Referencia
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (base de datos elástica)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# Temas relacionados
## Biblioteca de administración de SQL Database
### [Obtención del paquete de la biblioteca de administración de SQL Database](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Controladores de SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

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


<!--HONumber=Feb17_HO1-->


