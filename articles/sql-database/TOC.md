# Información general
## [¿Qué es Base de datos SQL?](sql-database-technical-overview.md)
## Características
### [Niveles de servicio](sql-database-service-tiers.md)
### [¿Qué es una DTU?](sql-database-what-is-a-dtu.md)
### [Introducción al banco de pruebas de DTU](sql-database-benchmark-overview.md)
### [Firewall de SQL Database y reglas de firewall](sql-database-firewall-configure.md)
### [Herramientas de administración](sql-database-manage-overview.md)
## Consideraciones y limitaciones
### [SQL Database frente a SQL en una máquina virtual](sql-database-paas-vs-sql-server-iaas.md)
### [Diferencias de T-SQL](sql-database-transact-sql-information.md)
### [Límites de recursos](sql-database-resource-limits.md)
### [Limitaciones generales](sql-database-general-limitations.md)
## [Precios](https://azure.microsoft.com/pricing/details/sql-database/)
## [Novedades](https://azure.microsoft.com/updates/?service=sql-database)
## [Preguntas más frecuentes sobre la Base de datos SQL](sql-database-faq.md)

## Ventajas
### [Aprende y se adapta](sql-database-learn-and-adapt.md)
### [Escalado sobre la marcha](sql-database-scale-on-the-fly.md)
### [Crea aplicaciones multiinquilino](sql-database-build-multi-tenant-apps.md)
### [Asegura y protege](sql-database-helps-secures-and-protects.md)
### [Trabaja en su entorno](sql-database-works-in-your-environment.md)

## Escenarios
### Creación y administración de servidores, grupos, bases de datos y firewalls
#### Creación y administración de grupos de bases de datos elásticas
#### [Cuándo se utiliza un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md)
#### [Directrices de seguridad](sql-database-security-guidelines.md)
#### [Grupos de bases de datos elásticas](sql-database-elastic-pool.md)
#### [Automatización](sql-database-manage-automation.md)
#### Modificación de niveles de servicio y de rendimiento
##### [Azure [Portal](sql-database-scale-up.md)
##### [PowerShell](sql-database-scale-up-powershell.md)
### Creación y administración de bases de datos escaladas horizontalmente
#### [Información general](sql-database-elastic-scale-introduction.md)
#### [Creación de bases de datos escalables en la nube](sql-database-elastic-database-client-library.md)
#### [Escalado horizontal con Shard Map Manager](sql-database-elastic-scale-shard-map-management.md)
#### [Migrate existing databases to scale-out (Migrar bases de datos existentes de escalado horizontal)](sql-database-elastic-convert-to-use-elastic-tools.md)
#### [Movimiento de datos entre bases de datos en la nube escaladas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md)
#### [Uso de Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Trabajo con Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Creación de bases de datos escalables en la nube](sql-database-elastic-database-perf-counters.md)
#### Consultas y trabajos entre bases de datos
##### [Consultas entre bases de datos](sql-database-elastic-query-overview.md)
##### [Consultas entre bases de datos con distintos esquemas](sql-database-elastic-query-vertical-partitioning.md)
##### [Informes entre bases de datos](sql-database-elastic-query-horizontal-partitioning.md)
##### [Trabajos entre bases de datos](sql-database-elastic-jobs-overview.md)
##### [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)
##### [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md)
##### [Seguridad de nivel de fila multiinquilino](sql-database-elastic-tools-multi-tenant-row-level-security.md)
##### [Introducción sobre las transacciones de base de datos elástica con Base de datos SQL de Azure](sql-database-elastic-transactions-overview.md)
####[Glosario de las herramientas de bases de datos elásticas](sql-database-elastic-scale-glossary.md)
#### [P+F](sql-database-elastic-scale-faq.md)
### Creación y administración de acceso y permisos
#### [Información general](sql-database-security.md)
#### [Directrices de seguridad](sql-database-security-guidelines.md)
#### [Administración de inicios de sesión](sql-database-manage-logins.md)
#### [Azure Security Center para Azure SQL Database](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
#### [Centro de seguridad de SQL](https://msdn.microsoft.com/library/azure/bb510589)
### [Desarrollo de aplicaciones y bases de datos](sql-database-develop-overview.md)
### Migración de bases de datos
#### [Migración de una base de datos de SQL Server](sql-database-cloud-migrate.md)
### Proporcionar continuidad del negocio
#### [Información general](sql-database-business-continuity.md)
#### [Copias de seguridad de bases de datos](sql-database-automated-backups.md) 
#### [Recuperación de bases de datos a partir de copias de seguridad](sql-database-recovery-using-backups.md)
#### [Recuperación de una interrupción del centro de datos](sql-database-disaster-recovery.md)
#### [Requisitos de autenticación para la recuperación ante desastres](sql-database-geo-replication-security-config.md)
#### [Escenarios de diseño de la continuidad empresarial](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estrategias de recuperación ante desastres con grupos elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Actualizaciones graduales](sql-database-manage-application-rolling-upgrade.md)
### Supervisión y ajuste de bases de datos
#### [Bases de datos únicas](sql-database-single-database-monitor.md)
#### [Guía de base de datos única](sql-database-performance-guidance.md)
#### [Detalles de la carga de trabajo en Azure Portal](sql-database-performance.md)

## Implementaciones del cliente
### [Software Daxko/CSI](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)

# Introducción
## Creación de servidores, grupos, bases de datos y firewalls
### [Portal de Azure](sql-database-get-started.md)
### [PowerShell](sql-database-get-started-powershell.md)
### [C#](sql-database-get-started-csharp.md)
## Datos de consulta
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
## Administración de servidores, grupos, bases de datos y firewalls
### [Portal de Azure](sql-database-manage-portal.md)
### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### [PowerShell](sql-database-manage-powershell.md)
## [Creación y administración de acceso y permisos](sql-database-get-started-security.md)
## Protección de los datos
### [Auditoría](sql-database-auditing-get-started.md)
### [Detección de amenazas](sql-database-threat-detection-get-started.md)
### Enmascaramiento de datos dinámicos
#### [Portal de Azure](sql-database-dynamic-data-masking-get-started-portal.md)
## Creación y administración de bases de datos escaladas horizontalmente
### [Escalado elástico](sql-database-elastic-scale-get-started.md)
### [Trabajos elásticos](sql-database-elastic-jobs-getting-started.md)
### Consultas elásticas
### [Informes entre bases de datos](sql-database-elastic-query-getting-started.md)
### [Consultas entre bases de datos](sql-database-elastic-query-getting-started-vertical.md)
## [Optimización en memoria](sql-database-in-memory.md)
## [Movimiento de bases de datos](sql-database-troubleshoot-moving-data.md)
## [Sincronización de datos](sql-database-get-started-sql-data-sync.md)
##Supervisión y ajuste de bases de datos
### [Introducción a SQL Database Advisor](sql-database-advisor.md)
### [Detalles de la carga de trabajo en Azure Portal](sql-database-performance.md)
## [Inicios rápidos de soluciones](sql-database-solution-quick-starts.md)
# Procedimientos
## [Auditoría](sql-database-auditing-get-started.md)
## Crear
### Grupos de recursos
#### [PowerShell](sql-database-manage-powershell.md#create-a-resource-group)
### Servidores
#### [Portal de Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-server)
### Grupos de bases de datos elásticas
#### [Portal de Azure](sql-database-elastic-pool-create-portal.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
### Bases de datos
#### Bases de datos únicas
##### [Portal de Azure](sql-database-get-started.md)
##### [C#](sql-database-get-started-csharp.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-get-started-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
#### Bases de datos con particiones
##### [Uso de Shard Map Manager](sql-database-elastic-scale-shard-map-management.md)
##### [Configuración de seguridad de combinación y división](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Trabajo con Dapper](sql-database-elastic-scale-working-with-dapper.md)
##### [Uso de Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
##### [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)
##### [Seguridad de nivel de fila multiinquilino](sql-database-elastic-tools-multi-tenant-row-level-security.md)
### Reglas de firewall
#### Server
##### [Portal de Azure](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [API DE REST](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### Base de datos
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Trabajos
#### [Instalación del servicio](sql-database-elastic-jobs-service-installation.md)
#### [Portal de Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
### Inicios de sesión
#### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-logins.md)
## Desarrollo
### [Información general](https://msdn.microsoft.com/library/mt763826.aspx)
### Escenarios
#### [Aplicaciones SaaS multiinquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### Tablas temporales
##### [Tablas temporales](sql-database-temporal-tables.md)
##### [Directivas de retención](sql-database-temporal-tables-retention-policy.md)
#### [Datos de JSON](sql-database-json-features.md)
#### [En memoria](sql-database-in-memory.md)
###Primeros pasos
#### [Bibliotecas de conectividad](sql-database-libraries.md)
#### Conexión de una aplicación
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Conexión a Visual Studio](sql-database-connect-query.md)
### Cómo
#### Creación de servidores
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Creación de grupos elásticos
###### [PowerShell](sql-database-elastic-pool-create-powershell.md)
###### [C#](sql-database-elastic-pool-create-csharp.md)
#### Creación de bases de datos
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Creación de reglas de firewall
###### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
###### [API DE REST](sql-database-configure-firewall-settings-rest.md)
#### Administración de servidores, grupos, bases de datos y firewalls
##### [PowerShell](sql-database-manage-powershell.md)
##### Administración de grupos elásticos
###### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
###### [C#](sql-database-elastic-pool-manage-csharp.md)
##### [Cambio de niveles de servicio y de rendimiento](sql-database-scale-up-powershell.md)
#### Mover datos
##### [Exportación de base de datos a un archivo BACPAC](sql-database-export-powershell.md)
##### [Importación de base de datos desde un archivo BACPAC](sql-database-import-powershell.md)
##### [Copia de una base de datos a otra ubicación de Azure](sql-database-copy-powershell.md)
#### [Obtención de los valores necesarios para autenticar una aplicación](sql-database-client-id-keys.md)
#### [Trabajos elásticos](sql-database-elastic-jobs-powershell.md)
#### Restauración y recuperación de base de datos
##### Restaurar la base de datos eliminada
###### [PowerShell](sql-database-restore-deleted-database-powershell.md)
##### Restauración de base de datos a un momento dado
###### [PowerShell](sql-database-point-in-time-restore-powershell.md)
##### Restauración geográfica
###### [PowerShell](sql-database-geo-restore-powershell.md)
#### Replicación de datos con replicación geográfica activa
##### Configuración
###### [PowerShell](sql-database-geo-replication-powershell.md)
##### Conmutación por error
###### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [Uso de puertos más allá de 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Trabajo con mensajes de error](sql-database-develop-error-messages.md)
#### [Uso de lotes](sql-database-use-batching-to-improve-performance.md)
### Referencia
#### [Transact-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
#### [Proveedor de datos .NET Framework para SQL Server (conceptos)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [Proveedor de datos .NET Framework para SQL Server (referencia de API)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### SQL PowerShell
##### [Cmdlets de Azure SQL Database (Resource Management)](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
##### [Cmdlets de Azure SQL Database (Service Management)](https://msdn.microsoft.com/library/azure/dn546723(v=azure.300\).aspx)
##### [Cmdlets de SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
#### API de REST de SQL Database
##### [API de REST (Resource Management)](https://msdn.microsoft.com/library/azure/mt420159)
##### [API de REST (Service Management)](https://msdn.microsoft.com/library/azure/dn505719.aspx)
#### Biblioteca de administración de SQL Database
##### [Referencia de biblioteca de administración de SQL Database](https://msdn.microsoft.com/library/azure/mt349017.aspx)
##### [Obtención del paquete de la biblioteca de administración de SQL Database](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
#### Entity Framework
##### [Obtención del paquete de Entity Framework](https://www.nuget.org/packages/EntityFramework/)
#### [Controladores de SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
##### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
##### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
##### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
##### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
##### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
##### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
##### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
#### [Azure SDK (descarga)](https://www.visualstudio.com/vs/azure-tools/)
#### [Azure SDK (documentación)](https://azure.microsoft.com/documentation/articles/dotnet-sdk/)
### resources
#### [Herramientas de SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
#### [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
#### [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
#### [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
#### [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
#### [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)
## Eliminar
### Base de datos
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database)
### Server
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database-server)
## Detectar amenazas
### [Detección de amenazas](sql-database-threat-detection-get-started.md)
### [Firewall](sql-database-firewall-configure.md)
## Cifrado de datos
### Always Encrypted
#### [Introducción a Always Encrypted](sql-database-always-encrypted.md)
#### [Azure Key Vault Always Encrypted](sql-database-always-encrypted-azure-key-vault.md)
### [Cifrado de datos transparente](https://msdn.microsoft.com/library/azure/dn948096)
### [Cifrado de columnas](https://msdn.microsoft.com/library/azure/ms179331)
## Administrar
###  Autenticación
#### Autenticación de SQL
#### [Autenticación con Azure Active Directory](sql-database-aad-authentication.md)
#### [Multi-factor Authentication](sql-database-ssms-mfa-authentication.md)
### Servidores
### Grupos elásticos
#### [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### Bases de datos
#### Bases de datos únicas
##### [Portal de Azure](sql-database-manage-portal.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-blank)
#### Cambio de niveles de servicio y de rendimiento
#### [Azure [Portal](sql-database-scale-up.md)
#### [PowerShell](sql-database-manage-powershell.md#change-the-performance-level-of-a-sql-database)
#### Bases de datos con particiones
##### [Migración de bases de datos escaladas horizontalmente existentes para escalado horizontal](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Administración de credenciales](sql-database-elastic-scale-manage-credentials.md)
##### [Moving data between scaled-out cloud databases (Mover datos entre bases de datos en la nube escaladas horizontalmente)](sql-database-elastic-scale-overview-split-and-merge.md)
##### [Implemente un servicio de división y combinación](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
##### [Incorporación de una partición](sql-database-elastic-scale-add-a-shard.md)
##### [Uso de la clase RecoveryManager para solucionar problemas de mapas de particiones](sql-database-elastic-database-recovery-manager.md)
### Reglas de firewall
#### Server
##### [Portal de Azure](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [API DE REST](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### Base de datos
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Trabajos
#### [Instalación del servicio](sql-database-elastic-jobs-service-installation.md)
#### [Portal de Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Actualización de la biblioteca de cliente](sql-database-elastic-scale-upgrade-client-library.md)
### Inicios de sesión
#### [Administración de inicios de sesión](sql-database-manage-logins.md)
## Enmascaramiento de datos
### Enmascaramiento de datos dinámicos
#### [Portal de Azure](sql-database-dynamic-data-masking-get-started-portal.md)
#### [Clientes de nivel inferior](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
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
### Exportación de base de datos a un archivo BACPAC
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Utilidad de paquetes de SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importación de base de datos desde un archivo BACPAC
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Utilidad de paquetes de SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Portal de Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)

## Supervisión y ajuste
### [Bases de datos únicas](sql-database-performance-guidance.md)
### Grupos elásticos
#### [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [Query Performance Insight](sql-database-query-performance.md)
### Asesor de Base de datos SQL
#### [Portal de Azure](sql-database-advisor-portal.md)
### Cambio de niveles de servicio y de rendimiento
#### [Portal de Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### [Sugerencias de optimización del rendimiento](sql-database-troubleshoot-performance.md)
### In-Memory OLTP
#### [Adopción de OLTP en memoria](sql-database-in-memory-oltp-migration.md)
#### [Supervisión del almacenamiento OLTP en memoria](sql-database-in-memory-oltp-monitoring.md)
### Almacén de consultas
#### [Supervisión del rendimiento mediante el Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Escenarios de uso del Almacén de consultas](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Funcionamiento del Almacén de consultas](sql-database-operate-query-store.md)
### [Niveles de compatibilidad](sql-database-compatibility-level-query-performance-130.md)
### [Auditoría de eventos](sql-database-auditing-get-started.md)
### [Creación de bases de datos escalables en la nube](sql-database-elastic-database-perf-counters.md)
### Eventos extendidos
#### [Eventos extendidos](sql-database-xevent-db-diff-from-svr.md)
#### [Código de destino del archivo de eventos](sql-database-xevent-code-event-file.md)
#### [Código de destino del búfer en anillo](sql-database-xevent-code-ring-buffer.md)
### DMV
#### [DMV](sql-database-monitoring-with-dmvs.md)
#### [DMVs](sql-database-manage-azure-ssms#monitor-sql-database-using-dynamic-management-views


## Mover datos
### Copia de SQL Database
#### [Información general](sql-database-copy.md)
#### [Portal de Azure](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Exportación de base de datos a un archivo BACPAC
#### [Portal de Azure](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importación de base de datos desde un archivo BACPAC
#### [Portal de Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
### [Sincronización de datos](sql-database-get-started-sql-data-sync.md)
### [Carga desde archivo CSV mediante BCP](sql-database-load-from-csv-with-bcp.md)

## Consultar
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md)
### Consultas entre bases de datos
#### [Consultas entre bases de datos con distintos esquemas](sql-database-elastic-query-vertical-partitioning.md)
#### [Informes entre bases de datos](sql-database-elastic-query-horizontal-partitioning.md)
#### [Introducción sobre las transacciones de base de datos elástica con Base de datos SQL de Azure](sql-database-elastic-transactions-overview.md)
#### [Actualización de la biblioteca de cliente](sql-database-elastic-scale-upgrade-client-library.md)
#### [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md)

## Restauración
### Restaurar la base de datos eliminada
### [Portal de Azure](sql-database-restore-deleted-database-portal.md)
### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### Restauración a un momento dado
#### [Portal de Azure](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### Restauración geográfica
#### [Portal de Azure](sql-database-geo-restore-portal.md)
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
#### [Portal de Azure](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## Solución de problemas
### Conectividad
#### [Problemas de conexión](sql-database-troubleshoot-common-connection-issues.md)
#### [Error de conexión transitorio](sql-database-troubleshoot-connection.md)
#### [Diagnóstico y prevención](sql-database-connectivity-issues.md)
### [Permisos](sql-database-troubleshoot-permissions.md)

# Referencia
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## SQL PowerShell
### [Cmdlets de Azure SQL Database (Resource Management)](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
### [Cmdlets de SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
## API de REST de SQL Database
### [API de REST (Resource Management)](https://msdn.microsoft.com/library/azure/mt420159)
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

# resources
## [Herramientas de SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)

<!--HONumber=Nov16_HO2-->


