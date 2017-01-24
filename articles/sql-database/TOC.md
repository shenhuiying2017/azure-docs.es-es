# Información general
## [¿Qué es SQL Database?](sql-database-technical-overview.md)
### [Niveles de servicio](sql-database-service-tiers.md)
### [Unidades de transacción de base de datos](sql-database-what-is-a-dtu.md)
### [Introducción al banco de pruebas de DTU](sql-database-benchmark-overview.md)
### [Límites de recursos](sql-database-resource-limits.md)
### [Características](sql-database-features.md)
### [Preguntas más frecuentes sobre SQL Database](sql-database-faq.md)
## Ventajas
### [Aprende y se adapta](sql-database-learn-and-adapt.md)
### [Escalado sobre la marcha](sql-database-scale-on-the-fly.md)
### [Crea aplicaciones multiinquilino](sql-database-build-multi-tenant-apps.md)
### [Asegura y protege](sql-database-helps-secures-and-protects.md)
### [Trabaja en su entorno](sql-database-works-in-your-environment.md)
## Comparaciones
### [SQL Database frente a SQL en una máquina virtual](sql-database-paas-vs-sql-server-iaas.md)
### [Diferencias de T-SQL](sql-database-transact-sql-information.md)
### [SQL frente a NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [Herramienta de SQL Database](sql-database-manage-overview.md)
## [Tutoriales de SQL Database](sql-database-explore-tutorials.md)
## [Inicios rápidos de soluciones](sql-database-solution-quick-starts.md)
## Seguridad
### [Azure Security Center para Azure SQL Database](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL Security Center](https://msdn.microsoft.com/library/azure/bb510589)

# Introducción
## Bases de datos y servidores
### Bases de datos únicas
#### Aprender
##### [Tutorial de Azure Portal: cree una instancia de Azure SQL Database mediante Azure Portal](sql-database-get-started.md)
#### Sí
##### [Tutorial de Azure Portal: cree una instancia de Azure SQL Database mediante Azure Portal](sql-database-get-started.md)
##### [Tutorial de Azure Portal: cree una instancia de Azure SQL Database mediante PowerShell](sql-database-get-started-powershell.md)
##### [Tutorial de Azure Portal: cree una instancia de Azure SQL Database mediante C#](sql-database-get-started-csharp.md)
### Grupos de bases de datos elásticas
#### Aprender
##### [Grupos de bases de datos elásticas](sql-database-elastic-pool.md)
##### [Cuándo se utiliza un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md)
##### [Precios de grupo elásticos](sql-database-elastic-pool-price.md)
#### Sí
##### [Creación mediante Azure Portal](sql-database-elastic-pool-create-portal.md)
##### [Creación mediante PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Creación mediante C#](sql-database-elastic-pool-create-csharp.md)
### Escalado horizontal
#### Aprender
##### [Escalado horizontal](sql-database-elastic-scale-introduction.md)
##### [Escalado elástico](sql-database-elastic-scale-get-started.md)
##### [Creación de bases de datos escalables en la nube](sql-database-elastic-database-client-library.md)
##### [Trabajos entre bases de datos](sql-database-elastic-jobs-overview.md)
##### [Preguntas más frecuentes sobre herramientas elásticas](sql-database-elastic-scale-faq.md)
##### [Glosario de las herramientas de bases de datos elásticas](sql-database-elastic-scale-glossary.md)
##### [Moving data between scaled-out cloud databases (Mover datos entre bases de datos en la nube escaladas horizontalmente)](sql-database-elastic-scale-overview-split-and-merge.md)
#### Sí
##### [Trabajos elásticos](sql-database-elastic-jobs-getting-started.md)
##### [Informes entre bases de datos](sql-database-elastic-query-getting-started.md)
##### [Consultas entre bases de datos](sql-database-elastic-query-getting-started-vertical.md)
##### [Desinstalación de trabajos elásticos](sql-database-elastic-jobs-uninstall.md)
## Migración y movimiento de datos
### Aprender
#### [Migración de una base de datos](sql-database-cloud-migrate.md)
#### [Replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Sincronización de datos](sql-database-get-started-sql-data-sync.md)
#### [Copia de SQL Database](sql-database-copy.md)
### Sí
#### Determinación de compatibilidad de bases de datos
##### [Utilidad de paquetes de SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### Solución de problemas de compatibilidad de bases de datos
##### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [Asistente para la migración de SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### Copia de una base de datos
##### [Copia mediante Azure Portal](sql-database-copy-portal.md)
##### [Copia mediante PowerShell](sql-database-copy-powershell.md)
##### [Copia mediante T-SQL](sql-database-copy-transact-sql.md)
#### Exportación de base de datos a un archivo BACPAC
##### [Azure Portal](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [Utilidad de paquetes de SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### Importación de base de datos desde un archivo BACPAC
##### [Azure Portal](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [Utilidad de paquetes de SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Carga desde archivo CSV mediante BCP](sql-database-load-from-csv-with-bcp.md)
## Autenticación y concesión de acceso
### Aprender
#### [Información general](sql-database-security.md)
#### [Directrices de seguridad](sql-database-security-guidelines.md)
#### [Administración de inicios de sesión](sql-database-manage-logins.md)
### Sí
### [Tutorial: autenticación y acceso a SQL](sql-database-get-started-security.md)
## Protección de los datos
### Aprender
#### Auditoría
##### [Auditoría](sql-database-auditing-get-started.md)
##### [Compatibilidad con clientes de nivel inferior y cambios de punto de conexión IP para auditoría](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Detección de amenazas](sql-database-threat-detection-get-started.md)
#### Cifrado de datos
##### [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
##### [Almacén de certificados de Windows](sql-database-always-encrypted.md)
##### [Cifrado de datos transparente](https://msdn.microsoft.com/library/azure/dn948096)
##### [Cifrado de columnas](https://msdn.microsoft.com/library/azure/ms179331)
#### Enmascaramiento de datos
##### Enmascaramiento de datos dinámicos
###### [Azure Portal](sql-database-dynamic-data-masking-get-started.md)
### Sí
#### [Enmascaramiento dinámico de datos mediante Azure Portal](sql-database-dynamic-data-masking-get-started.md)
## Continuidad del negocio
### Aprender
#### [Información general](sql-database-business-continuity.md)
#### [Copias de seguridad de bases de datos](sql-database-automated-backups.md)
#### [Retención a largo plazo](sql-database-long-term-retention.md)
#### [Recuperación de bases de datos a partir de copias de seguridad](sql-database-recovery-using-backups.md)
#### [Requisitos de autenticación para la recuperación ante desastres](sql-database-geo-replication-security-config.md)
#### [Escenarios de diseño de la continuidad empresarial](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estrategias de recuperación ante desastres con grupos elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Actualizaciones graduales](sql-database-manage-application-rolling-upgrade.md)
### Sí
#### Restaurar la base de datos eliminada
##### [Azure Portal](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### Restauración a un momento dado
##### [Azure Portal](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### Bases de datos de restauración geográfica
##### [Azure Portal](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Tabla única](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Recuperación de una interrupción del centro de datos](sql-database-disaster-recovery.md)
#### [Obtención de detalles de recuperación ante desastres](sql-database-disaster-recovery-drills.md)
### Replicación de bases de datos
#### [Introducción a la replicación geográfica activa](sql-database-geo-replication-overview.md)
#### Configuración de replicación geográfica activa
##### [Azure Portal](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### Conmutación por error con replicación geográfica activa
##### [Azure Portal](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Desarrollo de aplicaciones
### Aprender
#### [Introducción al desarrollo de aplicaciones de bases de datos](sql-database-develop-overview.md)
#### [Bibliotecas de conectividad](sql-database-libraries.md)
#### [Aplicaciones SaaS multiinquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md)
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
### Implementaciones por parte del cliente de software como servicio
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
#### [En memoria](sql-database-in-memory.md)
### Sí
#### [Desarrollo de SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
## Supervisión y optimización
### Aprender
#### [Bases de datos únicas](sql-database-single-database-monitor.md)
#### [Introducción a SQL Database Advisor](sql-database-advisor.md)
#### [Guía de base de datos única](sql-database-performance-guidance.md)
#### [Detalles de la carga de trabajo en Azure Portal](sql-database-performance.md)
#### [Uso de lotes](sql-database-use-batching-to-improve-performance.md)
## SQL Database V11
### [Retirada de las versiones Web y Business](sql-database-web-business-sunset-faq.md)
### [Service Tier Advisor](sql-database-service-tier-advisor.md)
### [Herramienta de evaluación de grupo elástico](sql-database-elastic-pool-database-assessment-powershell.md)
### [Actualización a V12](sql-database-v12-plan-prepare-upgrade.md)
#### [Actualización mediante Azure Portal](sql-database-upgrade-server-portal.md)
#### [Actualización mediante PowerShell](sql-database-upgrade-server-powershell.md)
#### [Recomendaciones sobre el plan de tarifa](sql-database-service-tier-advisor.md)

# Procedimientos
## Creación y administración
### Servidores y bases de datos
#### [Bases de datos únicas](sql-database-manage-portal.md)
#### [Azure Portal](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Grupos de bases de datos elásticas
#### [Creación mediante Azure Portal](sql-database-elastic-pool-create-portal.md)
#### [Creación mediante PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [Creación mediante C#](sql-database-elastic-pool-create-csharp.md)
#### [Administración mediante Azure Portal](sql-database-elastic-pool-manage-portal.md)
#### [Administración mediante PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [Administración mediante C#](sql-database-elastic-pool-manage-csharp.md)
#### [Administración mediante T-SQL](sql-database-elastic-pool-manage-tsql.md)
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
#### [API de REST](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Trabajos y automatización
#### [Instalación del servicio](sql-database-elastic-jobs-service-installation.md)
#### [Creación y administración de trabajos elásticos en Azure Portal](sql-database-elastic-jobs-create-and-manage.md)
#### [Creación y administración de trabajos elásticos mediante PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Actualización de la biblioteca de cliente](sql-database-elastic-scale-upgrade-client-library.md)
#### [Administración de bases de datos SQL mediante el servicio Azure Automation](sql-database-manage-automation.md)
### [Firewall](sql-database-firewall-configure.md)
## Cifrado de datos
### [Introducción a Always Encrypted](sql-database-always-encrypted.md)
### [Cifrado de datos transparente](https://msdn.microsoft.com/library/azure/dn948096)
### [Cifrado de columnas](https://msdn.microsoft.com/library/azure/ms179331)
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
### [Información de rendimiento de consultas](sql-database-query-performance.md)
### [Asesor de SQL Database](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [Niveles de compatibilidad](sql-database-compatibility-level-query-performance-130.md)
### [Contadores de rendimiento para Shard Map Manager](sql-database-elastic-database-perf-counters.md)
### [Sugerencias de optimización del rendimiento](sql-database-troubleshoot-performance.md)
### Cambio de niveles de servicio y de rendimiento
#### [Uso de Azure Portal](sql-database-scale-up.md)
#### [Uso de PowerShell](sql-database-scale-up-powershell.md)
### [Creación de alertas](sql-database-insights-alerts-portal.md)
### In-Memory OLTP
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
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (base de datos elástica)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](https:dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [REST](/rest/api/sql/)

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


<!--HONumber=Dec16_HO2-->


