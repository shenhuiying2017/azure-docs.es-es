---
title: "Exploración de tutoriales de Azure SQL Database | Microsoft Docs"
description: "Información sobre las funcionalidades y características de Base de datos SQL"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 04c0fd7f-d260-4e43-a4f0-41cdcd5e3786
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2f24c1c43300a3c5035fbb4e90c3d13f73c1bc74
ms.openlocfilehash: 9d9243d0385b24e8794dc2ac69978b41cdca8331


---
# <a name="explore-azure-sql-database-tutorials"></a>Exploración de tutoriales de Base de datos SQL de Azure
En los siguientes vínculos obtendrá información general de cada área de características enumerada y un tutorial de inicio rápido para cada área. Para ver tutoriales enfocados a soluciones que demuestran el uso de Base de datos SQL en una solución completa basada en escenarios del mundo real, consulte [Exploración de tutoriales de soluciones de Base de datos SQL de Azure](sql-database-solution-quick-starts.md).

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>Creación de servidores, bases de datos y reglas de firewall de nivel de servidor
En los siguientes tutoriales, creará servidores, bases de datos y reglas de firewall de nivel de servidor, y aprenderá a conectarse y consultar servidores y bases de datos.

| Tutorial | Descripción |
| --- | --- | 
| [Introducción a los servidores, las bases de datos y las reglas de firewall de Azure SQL Database mediante Azure Portal y SQL Server Management Studio](sql-database-get-started.md) | En este tutorial, usará Azure Portal y SQL Server Management Studio para crear un servidor lógico de Azure SQL Database, ver sus propiedades, crear un firewall de nivel de servidor, conectarse a la base de datos maestra, crear una base de datos de ejemplo, ver sus propiedades, conectarse a la base de datos de ejemplo y crear una base de datos en blanco. |
| [Introducción a los servidores, las bases de datos y las reglas de firewall de Azure SQL Database mediante Azure PowerShell](sql-database-get-started-powershell.md) | En este tutorial, usará PowerShell para crear un servidor lógico de Azure SQL Database, ver sus propiedades, crear un firewall de nivel de servidor, conectarse a la base de datos maestra, crear una base de datos de ejemplo, ver sus propiedades, conectarse a la base de datos de ejemplo y crear una base de datos en blanco. |
| [Uso de C# para crear una instancia de SQL Database con la biblioteca de SQL Database para .NET](sql-database-get-started-csharp.md)| En este tutorial, usará C# para crear un servidor de SQL Database, una regla de firewall y una base de datos SQL. También creará una aplicación de Active Directory (AD) y la entidad de servicio necesaria para autenticar la aplicación en C#. |
|  | |

## <a name="backup-and-recovery"></a>Copia de seguridad y recuperación
En el siguiente tutorial, restaurará una base de datos a un momento dado, configurará la retención de copias de seguridad a largo plazo y restaurará una base de datos de retención en el almacén de Azure Recovery Services. 

| Tutorial | Descripción |
| --- | --- | 
| [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación](sql-database-get-started-backup-recovery.md)| En este tutorial, usará Azure Portal para restaurar una base de datos a un momento dado, configurar la retención de copias de seguridad a largo plazo y restaurar una base de datos de retención en el almacén de Azure Recovery Services. |
|  | |

## <a name="elastic-pools"></a>Grupos elásticos
En los tutoriales siguientes, aprenderá sobre el uso de [grupos elásticos](sql-database-elastic-pool.md) para administrar los objetivos de rendimiento de varias bases de datos que tienen patrones de uso muy diferentes e imprevisibles.

| Tutorial | Description |
| --- | --- | 
| [Creación de un grupo elástico](sql-database-elastic-pool-create-portal.md) |En este tutorial, aprenderá a crear un grupo escalable de bases de datos SQL de Azure. |
| [Supervisión de una base de datos elástica](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) |En este tutorial, aprenderá a supervisar una base de datos elástica individual en caso de un posible problema. |
| [Adición de una alerta a un grupo de recursos](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) |En este tutorial, aprenderá a agregar reglas a los recursos que envían correos electrónicos a personas o cadenas de alerta a puntos de conexión de URL cuando el recurso alcanza un umbral de uso que establezca. |
| [Movimiento de una base de datos a un grupo elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) |En este tutorial, aprenderá a mover una base de datos a un grupo elástico. |
| [Movimiento de una base de datos fuera de un grupo elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) |En este tutorial, aprenderá a mover una base de datos fuera de un grupo elástico. |
| [Cambio de la configuración de rendimiento de un grupo](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) |En este tutorial, aprenderá a ajustar los límites de almacenamiento y rendimiento para un grupo. |
|  | |

## <a name="elastic-database-jobs"></a>Trabajos de base de datos elástica
En los tutoriales siguientes, aprenderá acerca del uso de [trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

| Tutorial | Description |
| --- | --- | 
| [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md) |En este tutorial, aprenderá a utilizar las funcionalidades de las herramientas de bases de datos elásticas mediante una aplicación de partición sencilla. |
| [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md) |En este tutorial aprenderá a crear y administrar trabajos que administran un grupo de bases de datos relacionadas. |
|  | |

## <a name="elastic-queries"></a>Consultas elásticas
En los tutoriales siguientes, aprenderá a ejecutar [consultas elásticas](sql-database-elastic-query-overview.md).

| Tutorial | Description |
| --- | --- | 
| [Introducción a consultas elásticas para particionamiento horizontal](sql-database-elastic-query-getting-started.md) |En este tutorial, aprenderá a crear informes a partir de todas las bases de datos en una base de datos con particionamiento horizontal mediante una [consulta elástica](sql-database-elastic-query-overview.md) |
| [Introducción a las consultas entre bases de datos (particiones verticales) (versión preliminar)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) |En este tutorial, aprenderá a crear informes a partir de todas las bases de datos en bases de datos con particionamiento vertical mediante una [consulta elástica](sql-database-elastic-query-overview.md) |
| [Convert existing databases to use elastic database tools (Conversión de bases de datos existentes para usar herramientas para bases de datos elásticas)](sql-database-elastic-convert-to-use-elastic-tools.md) |En este tutorial, aprenderá a escalar horizontalmente una Base de datos SQL de Azure. |
|  | |

## <a name="performance-optimization"></a>Optimización del rendimiento
En los tutoriales siguientes, aprenderá a optimizar el [rendimiento de bases de datos independientes](sql-database-performance-guidance.md). Para optimizar el rendimiento de varias bases de datos, consulte [Grupos elásticos](#elastic-pools).

| Tutorial | Description |
| --- | --- | 
| [Cambio del nivel de servicio y del nivel de rendimiento de su base de datos](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) |En este tutorial, aprenderá a escalar verticalmente o a reducir verticalmente el rendimiento de una Base de datos SQL de Azure con niveles de servicio. |
| [SQL Database Performance Insight (Información de rendimiento de Base de datos SQL)](sql-database-performance.md#performance-overview) |En este tutorial, aprenderá a abrir y usar la información de rendimiento de consultas del asesor de Base de datos SQL. |
| [Asesor de Base de datos SQL](sql-database-advisor.md) |En este tutorial, aprenderá a ver y aplicar las recomendaciones de rendimiento del asesor de Base de datos SQL. |
| [Revisión de las consultas que más CPU consumen](sql-database-query-performance.md#review-top-cpu-consuming-queries) |En este tutorial, aprenderá a usar la información de rendimiento de consultas del asesor de Base de datos SQL para revisar las consultas que más CPU consumen. |
| [Visualización de los detalles de las consultas individuales](sql-database-query-performance.md#viewing-individual-query-details) |En este tutorial, aprenderá a usar la información de rendimiento de consultas del asesor de Base de datos SQL para ver los detalles de rendimiento de consultas individuales. |
|  | |

## <a name="sql-database-migration-and-archive"></a>Archivado y migración de SQL Database
En los tutoriales siguientes, aprenderá a [migrar una base de datos SQL Server existente a Base de datos SQL de Azure](sql-database-cloud-migrate.md).

| Tutorial | Description |
| --- | --- | 
| [Detección de problemas de compatibilidad con SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |En este tutorial, aprenderá a usar SQL Server Data Tools para Visual Studio para determinar la compatibilidad con Base de datos SQL de Azure. |
| [Solución de problemas de compatibilidad con SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |En este tutorial, aprenderá a usar SQL Server Data Tools para Visual Studio para solucionar problemas de compatibilidad con Base de datos SQL de Azure. |
| [Determinar la compatibilidad de la Base de datos SQL mediante SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) |En este tutorial, aprenderá a usar la utilidad de línea de comandos SQLPackage.exe para determinar la compatibilidad con Base de datos SQL de Azure. |
| [Determinar la compatibilidad de la Base de datos SQL mediante SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |En este tutorial, aprenderá a usar SQL Server Management Studio para determinar la compatibilidad con Base de datos SQL de Azure. |
| [Migrar la base de datos de SQL Server a Base de datos SQL con el Asistente para implementar base de datos en Base de datos de Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) |En este tutorial, aprenderá a migrar una base de datos de SQL Server compatible con Base de datos SQL de Azure mediante el Asistente para implementar bases de datos de Microsoft Azure en SQL Server Management Studio. |
| [Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) |En este tutorial, aprenderá a exportar una base de datos de SQL Server compatible a un archivo BACPAC mediante el Asistente para exportar aplicaciones de capas de datos en SQL Server Management Studio. |
| [Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) |En este tutorial, aprenderá a exportar una base de datos de SQL Server compatible con un archivo BACPAC mediante la utilidad de línea de comandos SQLPackage.exe. |
| [Importar desde BACPAC a la Base de datos SQL con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) |En este tutorial, aprenderá a importar una base de datos a Base de datos SQL de Azure desde un archivo BACPAC mediante el Asistente para exportar aplicaciones de capas de datos en SQL Server Management Studio. |
| [Importar a la Base de datos SQL desde un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) |En este tutorial, aprenderá a importar una base de datos a Base de datos SQL de Azure desde un archivo BACPAC mediante la utilidad de línea de comandos SQLPackage.exe. |
| [Importar un archivo BACPAC para crear una base de datos SQL de Azure](sql-database-import.md) |En este tutorial, aprenderá a importar una base de datos a Base de datos SQL de Azure desde un archivo BACPAC almacenado en un blob de Azure mediante el Portal de Azure. |
| [Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell](sql-database-import-powershell.md) |En este tutorial, aprenderá a importar una base de datos a Base de datos SQL de Azure desde un archivo BACPAC mediante PowerShell. |
| [Archivar una base de datos de SQL Azure en un archivo BACPAC mediante el Portal de Azure](sql-database-export.md#export-your-database) |En este tutorial, aprenderá a archivar una Base de datos SQL de Azure en un archivo BACPAC mediante el Portal de Azure. |
| [Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell](sql-database-export-powershell.md) |En este tutorial, aprenderá a archivar una base de datos SQL de Azure en un archivo BACPAC mediante el Portal de Azure. |
| [Copiar una base de datos SQL de Azure](sql-database-copy.md) |En este tutorial, aprenderá a copiar una Base de datos SQL de Azure mediante el Portal de Azure. |
| [Copia de una Base de datos SQL de Azure con PowerShell](sql-database-copy-powershell.md) |En este tutorial, aprenderá a copiar una Base de datos SQL de Azure mediante PowerShell. |
| [Copia de una Base de datos SQL de Azure con Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) |En este tutorial, aprenderá a copiar una Base de datos SQL de Azure mediante Transact-SQL. |
|  | |

## <a name="develop"></a>Desarrollo
En los tutoriales siguientes, aprenderá sobre el [desarrollo de SQL Database](sql-database-develop-overview.md) y el uso de [bibliotecas de conectividad](sql-database-libraries.md).

| Tutorial | Description |
| --- | --- | 
| [Conexión a Base de datos SQL mediante .NET (C#)](sql-database-develop-dotnet-simple.md) |En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante C#. |
| [Conexión a la base de datos SQL mediante Java con JDBC en Windows](sql-database-develop-java-simple.md) |En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante Java. |
| [Conexión a Base de datos SQL mediante Node.js](sql-database-develop-nodejs-simple.md) |En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante Node.js. |
| [Conexión a la base de datos SQL mediante PHP en Windows](sql-database-develop-php-simple.md) |En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante PHP. |
| [Conexión a Base de datos SQL mediante Python](sql-database-develop-python-simple.md) |En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante Python. |
| [Conexión a Base de datos SQL mediante Ruby](sql-database-develop-ruby-simple.md) |En este tutorial, aprenderá a conectar una Base de datos SQL de Azure mediante Ruby. |
|  | |

## <a name="database-authentication-and-authorization"></a>Autenticación y autorización de base de datos
En los tutoriales siguientes, aprenderá a [crear y administrar inicios de sesión y usuarios](sql-database-manage-logins.md).

| Tutorial | Descripción |
| --- | --- | 
| [Uso de la autenticación de SQL Server] |En este tutorial, aprenderá a usar SQL Server Management Studio para trabajar con la autenticación, los inicios de sesión, los usuarios y roles de base de datos de SQL Server que conceden acceso y permisos a los servidores y bases de datos de Azure SQL Database. |
|  | |

## <a name="secure-and-protect-data"></a>Protección de los datos
En los tutoriales siguientes, aprenderá a [proteger los datos de Base de datos SQL de Azure](sql-database-security-overview.md).

| Tutorial | Description |
| --- | --- | 
| [Introducción a Detección de amenazas de Base de datos SQL](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) |En este tutorial, aprenderá a configurar la detección de amenazas en el Portal de Azure para la base de datos. |
| [Always Encrypted: protección de datos confidenciales en Base de datos SQL de Azure con cifrado de datos y almacenamiento de las claves de cifrado en el Almacén de claves de Azure ](sql-database-always-encrypted-azure-key-vault.md) |En este tutorial, utilizará el asistente de Always Encrypted para proteger datos confidenciales en una Base de datos SQL de Azure. |
| [Cifrado de datos transparente con Base de datos SQL de Azure](https://msdn.microsoft.com/library/dn948096.aspx) |En este tutorial, aprenderá a usar el cifrado de datos transparente para proteger la información confidencial. |
| [Cifrar una columna de datos](https://msdn.microsoft.com/library/ms179331.aspx) |En este tutorial, aprenderá a cifrar una columna de datos mediante Transact-SQL. |
| [Introducción al enmascaramiento de datos dinámicos de bases de datos SQL (Portal de Azure)](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) |En este tutorial, aprenderá a configurar el enmascaramiento de datos dinámicos para la Base de datos SQL de Azure. |
|  | |

## <a name="business-continuity"></a>Continuidad del negocio
En los tutoriales siguientes, aprenderá a usar [la restauración geográfica y la replicación geográfica activa](sql-database-business-continuity.md) para recuperarse de errores relativos a la continuidad del negocio y al escalado horizontal de las consultas.

| Tutorial | Description |
| --- | --- | 
| [Restauración de una base de datos SQL de Azure a un momento dado anterior con el Portal de Azure](sql-database-point-in-time-restore.md) |En este tutorial, aprenderá a restaurar la base de datos a un momento anterior mediante el Portal de Azure. |
| [Restauración de una Base de datos SQL de Azure a un momento dado anterior con PowerShell](sql-database-point-in-time-restore.md) |En este tutorial, aprenderá a restaurar la base de datos a un momento anterior mediante PowerShell. |
| [Restaurar una base de datos SQL de Azure eliminada con el Portal de Azure](sql-database-restore-deleted-database-portal.md) |En este tutorial, aprenderá a restaurar una base de datos eliminada mediante el Portal de Azure. |
| [Restore a deleted Azure SQL Database using PowerShell (Restauración de una Base de datos SQL de Azure eliminada mediante PowerShell)](sql-database-restore-deleted-database-powershell.md) |En este tutorial, aprenderá a restaurar una base de datos eliminada mediante PowerShell. |
| [Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-portal.md) |En este tutorial, aprenderá a configurar la replicación geográfica activa mediante el Portal de Azure. |
| [Configuración de la replicación geográfica para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-powershell.md) |En este tutorial, aprenderá a configurar la replicación geográfica activa mediante PowerShell. |
| [Configuración de la replicación geográfica para una base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md) |En este tutorial, aprenderá a configurar la replicación geográfica activa mediante Transact-SQL. |
| [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-failover-portal.md) |En este tutorial, aprenderá a conmutar por error en una réplica secundaria de replicación geográfica mediante el Portal de Azure. |
| [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-failover-powershell.md) |En este tutorial, aprenderá a conmutar por error en una réplica secundaria de replicación geográfica mediante PowerShell. |
| [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) |En este tutorial, aprenderá a conmutar por error en una réplica secundaria de replicación geográfica mediante Transact-SQL. |
|  | |

## <a name="data-sync"></a>Sincronización de datos
En este tutorial, aprenderá sobre la [sincronización de datos](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Tutorial | Description |
| --- | --- | 
| [Introducción a SQL Data Sync de Azure (vista previa)](sql-database-get-started-sql-data-sync.md) |En este tutorial aprenderá los fundamentos del uso de SQL Data Sync de Azure mediante el Portal de Azure clásico. |
|  | |

## <a name="next-steps"></a>Pasos siguientes
[Exploración de tutoriales de soluciones de Base de datos SQL de Azure](sql-database-solution-quick-starts.md)



<!--HONumber=Jan17_HO3-->


