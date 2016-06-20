<properties
   pageTitle="Exploración de tutoriales de Base de datos SQL de Azure | Microsoft Azure"
   description="Información sobre las funcionalidades y características de Base de datos SQL"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/01/2016"
   ms.author="carlrab"/>
   
# Exploración de tutoriales de Base de datos SQL de Azure

En los siguientes vínculos obtendrá información general de cada área de características enumerada y un tutorial de inicio rápido para cada área. Para ver tutoriales enfocados a soluciones que demuestran el uso de Base de datos SQL en una solución completa basada en escenarios del mundo real, consulte [Exploración de tutoriales de soluciones de Base de datos SQL de Azure](sql-database-solution-quick-starts.md).

## Uso de SQL Server Management Studio

En los tutoriales siguientes, aprenderá a usar SQL Server Management Studio para administrar y consultar Base de datos SQL de Azure.

| Tutorial | Descripción |
|---|---|---|
| [Conexión a Base de datos SQL de Azure mediante un inicio de sesión de entidad de seguridad de nivel de servidor](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| En este tutorial, aprenderá a conectarse a Base de datos SQL de Azure mediante un inicio de sesión de entidad de seguridad de nivel de servidor.|
| [Conexión a Base de datos SQL de Azure como usuario](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | En este tutorial, aprenderá a conectarse a Base de datos SQL de Azure mediante una cuenta de usuario de nivel de base de datos.|
||||

## Grupos elásticos

En los tutoriales siguientes, aprenderá sobre el uso de [grupos elásticos](sql-database-elastic-pool.md) para administrar los objetivos de rendimiento de varias bases de datos que tienen patrones de uso muy diferentes e imprevisibles.

| Tutorial | Descripción |
|---|---|---|
| [Creación de un grupo elástico](sql-database-elastic-pool-create-portal.md) | En este tutorial, aprenderá a crear un grupo escalable de bases de datos SQL de Azure. |
| [Supervisión de una base de datos elástica](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | En este tutorial, aprenderá a supervisar una base de datos elástica individual en caso de un posible problema. |
| [Adición de una alerta a un grupo de recursos](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | En este tutorial, aprenderá a agregar reglas a los recursos que envían correos electrónicos a personas o cadenas de alerta a puntos de conexión de URL cuando el recurso alcanza un umbral de uso que establezca. |
| [Movimiento de una base de datos a un grupo elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | En este tutorial, aprenderá a mover una base de datos a un grupo elástico. |
| [Movimiento de una base de datos fuera de un grupo elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | En este tutorial, aprenderá a mover una base de datos fuera de un grupo elástico. |
| [Cambio de la configuración de rendimiento de un grupo](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | En este tutorial, aprenderá a ajustar los límites de almacenamiento y rendimiento para un grupo. |
||||

## Trabajos de base de datos elástica

En los tutoriales siguientes, aprenderá acerca del uso de [trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

| Tutorial | Descripción |
|---|---|---|
| [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md) | En este tutorial, aprenderá a utilizar las funcionalidades de las herramientas de bases de datos elásticas mediante una aplicación de partición sencilla. |
| [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md) | En este tutorial aprenderá a crear y administrar trabajos que administran un grupo de bases de datos relacionadas. | 
||||

## Consultas elásticas

En los tutoriales siguientes, aprenderá a ejecutar [consultas elásticas](sql-database-elastic-query-overview.md).

| Tutorial | Descripción |
|---|---|---|
| [Introducción a consultas elásticas para particionamiento horizontal](sql-database-elastic-query-getting-started.md) | En este tutorial, aprenderá a crear informes a partir de todas las bases de datos en una base de datos con particionamiento horizontal mediante una [consulta elástica](sql-database-elastic-query-overview.md). |
| [Introducción a las consultas entre bases de datos (particiones verticales) (versión preliminar)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | En este tutorial, aprenderá a crear informes a partir de todas las bases de datos en bases de datos con particionamiento vertical mediante una [consulta elástica](sql-database-elastic-query-overview.md). |
| [Convert existing databases to use elastic database tools (Conversión de bases de datos existentes para usar herramientas para bases de datos elásticas)](sql-database-elastic-convert-to-use-elastic-tools.md)| En este tutorial, aprenderá a escalar horizontalmente una Base de datos SQL de Azure. |
||||

## Optimización del rendimiento

En los tutoriales siguientes, aprenderá a optimizar el [rendimiento de bases de datos únicas](sql-database-performance-guidance.md). Para optimizar el rendimiento de varias bases de datos, consulte [Grupos elásticos](#elastic-pools).

| Tutorial | Descripción |
|---|---|---|
| [Cambio del nivel de servicio y del nivel de rendimiento de su base de datos](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | En este tutorial, aprenderá a escalar verticalmente o a reducir verticalmente el rendimiento de una Base de datos SQL de Azure con niveles de servicio. |
| [SQL Database Performance Insight (Información de rendimiento de Base de datos SQL)](sql-database-performance.md#performance-overview) | En este tutorial, aprenderá a abrir y usar la información de rendimiento de consultas del asesor de Base de datos SQL.|
| [Asesor de Base de datos SQL](sql-database-index-advisor.md#viewing-recommendations) | En este tutorial, aprenderá a ver y aplicar las recomendaciones de rendimiento del asesor de Base de datos SQL. |
| [Revisión de las consultas que más CPU consumen](sql-database-query-performance.md#review-top-cpu-consuming-queries)| En este tutorial, aprenderá a usar la información de rendimiento de consultas del asesor de Base de datos SQL para revisar las consultas que más CPU consumen.|
| [Visualización de los detalles de las consultas individuales](sql-database-query-performance.md#viewing-individual-query-details)| En este tutorial, aprenderá a usar la información de rendimiento de consultas del asesor de Base de datos SQL para ver los detalles de rendimiento de consultas individuales.|
||||

## Archivado y migración de Base de datos SQL 

En los tutoriales siguientes, aprenderá a [migrar una base de datos SQL Server existente a Base de datos SQL de Azure](sql-database-cloud-migrate.md).

| Tutorial | Descripción |
|---|---|---|
| [Detección de problemas de compatibilidad con SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | En este tutorial, aprenderá a usar SQL Server Data Tools para Visual Studio para determinar la compatibilidad con Base de datos SQL de Azure. |
| [Solución de problemas de compatibilidad con SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | En este tutorial, aprenderá a usar SQL Server Data Tools para Visual Studio para solucionar problemas de compatibilidad con Base de datos SQL de Azure. |
| [Determinar la compatibilidad de la Base de datos SQL mediante SqlPackage.exe](ql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | En este tutorial, aprenderá a usar la utilidad de línea de comandos SQLPackage.exe para determinar la compatibilidad con Base de datos SQL de Azure.|
| [Determinar la compatibilidad de la Base de datos SQL mediante SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |En este tutorial, aprenderá a usar SQL Server Management Studio para determinar la compatibilidad con Base de datos SQL de Azure.|
| [Migrar la base de datos de SQL Server a Base de datos SQL con el Asistente para implementar base de datos en Base de datos de Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | En este tutorial, aprenderá a migrar una base de datos de SQL Server compatible con Base de datos SQL de Azure mediante el Asistente para implementar bases de datos de Microsoft Azure en SQL Server Management Studio.
| [Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | En este tutorial, aprenderá a exportar una base de datos de SQL Server compatible a un archivo BACPAC mediante el Asistente para exportar aplicaciones de capas de datos en SQL Server Management Studio.|
| [Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | En este tutorial, aprenderá a exportar una base de datos de SQL Server compatible con un archivo BACPAC mediante la utilidad de línea de comandos SQLPackage.exe.|
| [Importar desde BACPAC a la Base de datos SQL con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | En este tutorial, aprenderá a importar una base de datos a Base de datos SQL de Azure desde un archivo BACPAC mediante el Asistente para exportar aplicaciones de capas de datos en SQL Server Management Studio. |
| [Importar a la Base de datos SQL desde un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | En este tutorial, aprenderá a importar una base de datos a Base de datos SQL de Azure desde un archivo BACPAC mediante la utilidad de línea de comandos SQLPackage.exe. |
| [Importar un archivo BACPAC para crear una base de datos SQL de Azure](sql-database-import.md) | En este tutorial, aprenderá a importar una base de datos a Base de datos SQL de Azure desde un archivo BACPAC almacenado en un blob de Azure mediante el Portal de Azure.|
| [Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell](sql-database-import-powershell.md) | En este tutorial, aprenderá a importar una base de datos a Base de datos SQL de Azure desde un archivo BACPAC mediante PowerShell.|
| [Archivar una base de datos de SQL Azure en un archivo BACPAC mediante el Portal de Azure](sql-database-export.md#export-your-database) | En este tutorial, aprenderá a archivar una Base de datos SQL de Azure en un archivo BACPAC mediante el Portal de Azure. |
| [Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell](sql-database-export-powershell.md) | En este tutorial, aprenderá a archivar una base de datos SQL de Azure en un archivo BACPAC mediante el Portal de Azure. |
| [Copiar una base de datos SQL de Azure](sql-database-copy.md#copy-your-sql-database) | En este tutorial, aprenderá a copiar una Base de datos SQL de Azure mediante el Portal de Azure. |
| [Copia de una Base de datos SQL de Azure con PowerShell](sql-database-copy-powershell#copy-your-sql-database) | En este tutorial, aprenderá a copiar una Base de datos SQL de Azure mediante PowerShell. |
| [Copia de una Base de datos SQL de Azure con Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | En este tutorial, aprenderá a copiar una Base de datos SQL de Azure mediante Transact-SQL. |
||||

##Desarrollo

En los tutoriales siguientes, aprenderá sobre la [conectividad de cliente a Base de datos SQL de Azure](sql-database-connect-central-recommendations.md) y el uso de [bibliotecas de conectividad](sql-database-libraries.md).

| Tutorial | Descripción |
|---|---|---|
| [Conexión a Base de datos SQL mediante .NET (C#)](sql-database-develop-dotnet-simple.md) | En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante C#. |
| [Conexión a la base de datos SQL mediante Java con JDBC en Windows](sql-database-develop-java-simple.md) | En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante Java. |
| [Conexión a Base de datos SQL mediante Node.js](sql-database-develop-nodejs-simple.md) | En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante Node.js. |
| [Conexión a la base de datos SQL mediante PHP en Windows](sql-database-develop-php-simple.md) | En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante PHP. |
| [Conexión a Base de datos SQL mediante Python](sql-database-develop-python-simple.md) | En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante Python. |
| [Conexión a Base de datos SQL mediante Ruby](sql-database-develop-ruby-simple.md) | En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante Ruby. |
||||
 
## Acceso a la base de datos

En los tutoriales siguientes, aprenderá a [crear y administrar inicios de sesión y usuarios](sql-database-manage-logins.md).

| Tutorial | Descripción |
|---|---|---|
| [Configuración del firewall en la Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md) | En este tutorial, aprenderá a configurar un firewall de nivel de servidor de Base de datos SQL mediante el Portal de Azure. |
| [Configuración del firewall en la Base de datos SQL de Azure mediante TSQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | En este tutorial, aprenderá a crear una regla de firewall de nivel de base de datos mediante Transact-SQL.|
| [Configuración del firewall en la Base de datos SQL de Azure mediante TSQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | En este tutorial, aprenderá a administrar un firewall de nivel de servidor de Base de datos SQL de Azure mediante Transact-SQL.|
| [Configuración del firewall en la Base de datos SQL de Azure mediante PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | En este tutorial, aprenderá a administrar un firewall de nivel de servidor de Base de datos SQL de Azure mediante PowerShell.|
| [Configuración del firewall en la Base de datos SQL de Azure mediante la API de REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | En este tutorial, aprenderá a administrar un firewall de nivel de servidor de Base de datos SQL de Azure mediante la API de REST.|
| [Conexión a Base de datos SQL de Azure mediante un inicio de sesión de entidad de seguridad de nivel de servidor](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| En este tutorial, aprenderá a conectarse a Base de datos SQL de Azure mediante un inicio de sesión de entidad de seguridad de nivel de servidor.|
| [Conceder acceso de base de datos a un inicio de sesión] (sql-database-manage-logins.md#granting-database-access-to-a-login() | En este tutorial, aprenderá a conceder acceso de base de datos a un inicio de sesión de nivel de servidor.|
| [Creación de un nuevo usuario de base de datos con SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | En este tutorial, aprenderá a crear un nuevo usuario de base de datos en una base de datos existente con SSMS.|
| [Concesión de permisos de db\_owner de usuario de base de datos nueva](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | En este tutorial, aprenderá a conceder a usuario de una base de datos existente permisos de db\_owner.|
| [Conexión a Base de datos SQL de Azure como usuario](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | En este tutorial, aprenderá a conectarse a Base de datos SQL de Azure mediante una cuenta de usuario de nivel de base de datos.|
||||


## Seguridad de los datos

En los tutoriales siguientes, aprenderá a [proteger los datos de Base de datos SQL de Azure](sql-database-security.md).

| Tutorial | Descripción |
|---|---|---|
| [Introducción a Detección de amenazas de Base de datos SQL](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | En este tutorial, aprenderá a configurar la detección de amenazas en el Portal de Azure para la base de datos.|
| [Always Encrypted: protección de datos confidenciales en Base de datos SQL de Azure con cifrado de datos y almacenamiento de las claves de cifrado en el Almacén de claves de Azure](sql-database-always-encrypted-azure-key-vault.md) | En este tutorial, utilizará el asistente de Always Encrypted para proteger datos confidenciales en una Base de datos SQL de Azure.|
| [Cifrado de datos transparente con Base de datos SQL de Azure](https://msdn.microsoft.com/library/dn948096.aspx)| En este tutorial, aprenderá a usar el cifrado de datos transparente para proteger la información confidencial.|
| [Cifrar una columna de datos](https://msdn.microsoft.com/library/ms179331.aspx)| En este tutorial, aprenderá a cifrar una columna de datos mediante Transact-SQL.|
| [Introducción al enmascaramiento de datos dinámicos de bases de datos SQL (Portal de Azure)](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) | En este tutorial, aprenderá a configurar el enmascaramiento de datos dinámicos para la Base de datos SQL de Azure. |
||||

## Continuidad del negocio y escalado horizontal de consultas

En los tutoriales siguientes, aprenderá a usar [la restauración geográfica y la replicación geográfica activa](sql-database-business-continuity.md) para recuperarse de errores relativos a la continuidad del negocio y al escalado horizontal de las consultas.

| Tutorial | Descripción |
|---|---|---|
| [Restauración de una base de datos SQL de Azure a un momento dado anterior con el Portal de Azure](sql-database-point-in-time-restore-portal.md)| En este tutorial, aprenderá a restaurar la base de datos a un momento anterior mediante el Portal de Azure.|
| [Restauración de una Base de datos SQL de Azure a un momento dado anterior con PowerShell](sql-database-point-in-time-restore-powershell.md) | En este tutorial, aprenderá a restaurar la base de datos a un momento anterior mediante PowerShell.|
| [Restaurar una base de datos SQL de Azure eliminada con el Portal de Azure](sql-database-restore-deleted-database-portal.md) | En este tutorial, aprenderá a restaurar una base de datos eliminada mediante el Portal de Azure.|
| [Restore a deleted Azure SQL Database using PowerShell (Restauración de una Base de datos SQL de Azure eliminada mediante PowerShell)](sql-database-restore-deleted-database-powershell.md) | En este tutorial, aprenderá a restaurar una base de datos eliminada mediante PowerShell.|
| [Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-portal.md)| En este tutorial, aprenderá a configurar la replicación geográfica activa mediante el Portal de Azure.|
| [Configuración de la replicación geográfica para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-powershell.md)| En este tutorial, aprenderá a configurar la replicación geográfica activa mediante PowerShell.|
| [Configuración de la replicación geográfica para una base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md)| En este tutorial, aprenderá a configurar la replicación geográfica activa mediante Transact-SQL.|
| [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-failover-portal.md) | En este tutorial, aprenderá a conmutar por error en una réplica secundaria de replicación geográfica mediante el Portal de Azure.|
| [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-failover-powershell.md) | En este tutorial, aprenderá a conmutar por error en una réplica secundaria de replicación geográfica mediante PowerShell.|
| [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | En este tutorial, aprenderá a conmutar por error en una réplica secundaria de replicación geográfica mediante Transact-SQL.|
||||

## Sincronización de datos

En este tutorial, aprenderá sobre la [sincronización de datos](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Tutorial | Descripción |
|---|---|---| 
| [Introducción a SQL Data Sync de Azure (vista previa)](sql-database-get-started-sql-data-sync.md) | En este tutorial aprenderá los fundamentos del uso de SQL Data Sync de Azure mediante el Portal de Azure clásico. |
||||

## Pasos siguientes

[Exploración de tutoriales de soluciones de Base de datos SQL de Azure](sql-database-solution-quick-starts.md)

<!---HONumber=AcomDC_0608_2016-->