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
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 60bcd02d24e2084b9020ce56ef6a9f8268c6b1b5
ms.openlocfilehash: 23d4c00f9e4c719e2124ad3c8733269712a796f0

        
---
 
# <a name="explore-azure-sql-database-tutorials"></a>Exploración de tutoriales de Base de datos SQL de Azure
Los vínculos de las tablas siguientes le permitirán ver información general de cada área de características enumerada y un tutorial de inicio rápido para cada área. Para ver tutoriales enfocados a soluciones que demuestran el uso de Base de datos SQL en una solución completa basada en escenarios del mundo real, consulte [Exploración de tutoriales de soluciones de Base de datos SQL de Azure](sql-database-solution-quick-starts.md).

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>Creación de servidores, bases de datos y reglas de firewall de nivel de servidor
En los siguientes tutoriales, creará servidores, bases de datos y reglas de firewall de nivel de servidor, y aprenderá a conectarse y consultar servidores y bases de datos.

| Tutorial | Descripción |
| --- | --- | 
| [Tutorial de inicio rápido: su primera base de datos de Azure SQL Database](sql-database-get-started.md) | Cuando termine este tutorial de inicio rápido, tendrá una base de datos de ejemplo y una base de datos vacía en ejecución en un grupo de recursos de Azure y estarán conectadas a un servidor lógico. También tendrá dos reglas de firewall de nivel de servidor configuradas para que la entidad de seguridad de nivel de servidor pueda iniciar sesión en el servidor desde dos direcciones IP especificadas. Por último, aprenderá a consultar una base de datos en Azure Portal y cómo conectarse y consultar con SQL Server Management Studio. |
| [Tutorial: aprovisionamiento y acceso de una instancia de Azure SQL Database mediante PowerShell](sql-database-get-started-powershell.md) | Cuando termine este tutorial, tendrá una base de datos de ejemplo y una base de datos vacía en ejecución en un grupo de recursos de Azure y se conectan a un servidor lógico. También tendrá una regla de firewall de nivel de servidor configurada para que la entidad de seguridad de nivel de servidor pueda iniciar sesión en el servidor desde una dirección IP especificada (o intervalo de direcciones IP). |
| [Uso de C# para crear una instancia de SQL Database con la biblioteca de SQL Database para .NET](sql-database-get-started-csharp.md)| En este tutorial, usará C# para crear un servidor de SQL Database, una regla de firewall y una base de datos SQL. También creará una aplicación de Active Directory (AD) y la entidad de servicio necesaria para autenticar la aplicación en C#. |
|  | |

## <a name="backups-long-term-retention-and-database-recovery"></a>Copias de seguridad, retención a largo plazo y recuperación de bases de datos
En los siguientes tutoriales, obtendrá información sobre cómo usar las [copias de seguridad de bases de datos](sql-database-automated-backups.md), la [retención de copias de seguridad a largo plazo](sql-database-long-term-retention.md) y la [recuperación de bases de datos mediante copias de seguridad](sql-database-recovery-using-backups.md).

| Tutorial | Descripción |
| --- | --- | 
| [Copia de seguridad y restauración mediante Azure Portal](sql-database-get-started-backup-recovery.md) | En este tutorial, obtendrá información sobre cómo usar Azure Portal para ver copias de seguridad, realizar una recuperación a un momento dado, configurar la retención de copias de seguridad a largo plazo y recuperar desde una copia de seguridad en el almacén de Azure Recovery Services
| [Copia de seguridad y restauración mediante PowerShell](sql-database-get-started-backup-recovery-powershell.md) | En este tutorial, obtendrá información sobre cómo usar PowerShell para ver copias de seguridad, realizar una recuperación a un momento dado, configurar la retención de copias de seguridad a largo plazo y recuperar desde una copia de seguridad en el almacén de Azure Recovery Services
|  | |

## <a name="sharded-databases"></a>Bases de datos con particiones
En los tutoriales siguientes, obtendrá información sobre cómo [escalar horizontalmente las bases de datos mediante Shard Map Manager](sql-database-elastic-scale-shard-map-management.md).

| Tutorial | Descripción |
| --- | --- | 
| [Creación de una aplicación con particiones](sql-database-elastic-scale-get-started.md) |En este tutorial, aprenderá a utilizar las funcionalidades de las herramientas de bases de datos elásticas mediante una aplicación de partición sencilla. |
| [Implemente un servicio de división y combinación](sql-database-elastic-scale-configure-deploy-split-and-merge.md) |En este tutorial, obtendrá información sobre cómo mover datos entre bases de datos con particiones. |
|  | |

## <a name="elastic-database-jobs"></a>Trabajos de base de datos elástica
En los tutoriales siguientes, obtendrá información sobre cómo usar los [trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

| Tutorial | Descripción |
| --- | --- | 
| [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md) |En este tutorial, obtendrá información sobre cómo crear y administrar los trabajos que administran un grupo de bases de datos relacionadas. |
|  | |

## <a name="elastic-queries"></a>Consultas elásticas
En los tutoriales siguientes, obtendrá información sobre cómo ejecutar las [consultas elásticas](sql-database-elastic-query-overview.md).

| Tutorial | Descripción |
| --- | --- | 
| [Cree consultas elásticas)](sql-database-elastic-query-getting-started-vertical.md) | En este tutorial, obtendrá información sobre cómo ejecutar las consultas T-SQL que abarcan varias bases de datos mediante un solo punto de conexión |
| [Informes de bases de datos escaladas horizontalmente en la nube](sql-database-elastic-query-getting-started.md) |En este tutorial, obtendrá información sobre cómo crear informes a partir de todas las bases de datos de una base de datos con particionamiento horizontal (particionada) |
| [Consulta de bases de datos en la nube con esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md) | En este tutorial, obtendrá información sobre cómo ejecutar las consultas T-SQL que abarcan varias bases de datos con esquemas distintos |
| [Informes de bases de datos escaladas horizontalmente en la nube](sql-database-elastic-query-horizontal-partitioning.md) |En este tutorial, obtendrá información sobre cómo crear informes que abarcan todas las bases de datos en una base de datos particionada. |
|  | |

## <a name="database-authentication-and-authorization"></a>Autenticación y autorización de base de datos
En los tutoriales siguientes, obtendrá información sobre cómo [crear y administrar inicios de sesión y usuarios](sql-database-manage-logins.md).

| Tutorial | Descripción |
| --- | --- | --- |
| [Autenticación y autorización de SQL](sql-database-control-access-sql-authentication-get-started.md) | En este tutorial, aprenderá a crear inicios de sesión y usuarios con la autenticación de SQL Server, a agregarlos a roles y a concederles permisos. |
| [Autenticación y autorización en Azure AD](sql-database-control-access-aad-authentication-get-started.md) | En este tutorial, aprenderá a crear inicios de sesión y usuarios con la autenticación de Azure Active Directory. |
|  | |

## <a name="secure-and-protect-data"></a>Protección de los datos
En los tutoriales siguientes, obtendrá información sobre cómo [proteger los datos de Azure SQL Database](sql-database-security-overview.md).

| Tutorial | Descripción |
| --- | --- | 
| [Protección de datos confidenciales con Always Encrypted ](sql-database-always-encrypted-azure-key-vault.md) |En este tutorial, usará el asistente de Always Encrypted para proteger datos confidenciales en una instancia de Azure SQL Database. |
|  | |

## <a name="develop"></a>Desarrollo
En los tutoriales siguientes, obtendrá información sobre el desarrollo de aplicaciones y bases de datos.

| Tutorial | Descripción |
| --- | --- | 
| [Creación de un informe con Excel](sql-database-connect-excel.md) |En este tutorial, obtendrá información sobre cómo conectar Excel a una instancia de SQL Database en la nube para que pueda importar datos y crear tablas y gráficos basados en los valores de la base de datos. |
| [Compilación de una aplicación con SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) |En este tutorial, obtendrá información sobre cómo compilar una aplicación con SQL Server |
| [Uso de Entity Framework con herramientas elásticas](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) |En este tutorial, obtendrá información sobre los cambios que es necesario realizar en una aplicación de Entity Framework para su integración con las herramientas de Elastic Database. |
| [Adopción de OLTP en memoria](sql-database-in-memory-oltp-migration.md) | En este tutorial, puede obtener información sobre cómo usar [OLTP en memoria](sql-database-in-memory.md) para mejorar el rendimiento del procesamiento de transacciones. |
|  | |

## <a name="data-sync"></a>Sincronización de datos
En este tutorial, obtendrá información sobre [Data Sync](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Tutorial | Description |
| --- | --- | 
| [Introducción a SQL Data Sync de Azure (vista previa)](sql-database-get-started-sql-data-sync.md) |En este tutorial aprenderá los fundamentos del uso de SQL Data Sync de Azure mediante el Portal de Azure clásico. |
|  | |

## <a name="next-steps"></a>Pasos siguientes
[Exploración de tutoriales de soluciones de Base de datos SQL de Azure](sql-database-solution-quick-starts.md)



<!--HONumber=Feb17_HO2-->


