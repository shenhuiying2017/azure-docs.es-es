<properties
   pageTitle="Migración a Base de datos SQL mediante la replicación transaccional | Microsoft Azure"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, replicación transaccional"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Migración de una base de datos SQL Server a Base de datos SQL de Azure mediante la replicación transaccional

> [AZURE.SELECTOR]
- [Asistente para migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportación a un archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importación desde un archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

En este artículo, aprenderá a migrar una base de datos SQL Server compatible a Base de datos SQL Azure con un tiempo de inactividad mínimo mediante la replicación transaccional de SQL Server.

## Descripción de la arquitectura de replicación transaccional

Si no se puede permitir quitar la base de datos de SQL Server de producción mientras se lleva a cabo la migración, puede usar la replicación transaccional de SQL Server como solución de migración. Para utilizar esta solución, configurará su instancia de Base de datos SQL de Azure como suscriptor de la instancia de SQL Server local que quiere migrar. El distribuidor de la replicación transaccional local sincronizará los datos de la base de datos local de modo que estén sincronizados (el publicador) mientras se siguen produciendo nuevas transacciones.

También puede usar la replicación transaccional para migrar un subconjunto de la base de datos local. La publicación que se replica en Base de datos SQL de Azure puede limitarse a un subconjunto de las tablas de la base de datos que se replica. Además, para cada tabla que se replica, puede limitar los datos a un subconjunto de filas o un subconjunto de columnas.

Con la replicación transaccional, todos los cambios en los datos o el esquema que se producen entre el momento en que se inicia la migración y el momento en que finaliza aparecerán en Base de datos SQL de Azure.

Cuando finalice la sincronización y esté listo para realizar la migración, solo debe cambiar la cadena de conexión de las aplicaciones para que apunten a la instancia de Base de datos SQL de Azure en lugar de a la base de datos local. Cuando la replicación transaccional recupera todos los cambios pendientes en la base de datos local y todas las aplicaciones apuntan a la base de datos de Azure, ya se puede desinstalar con seguridad la replicación y dejar Base de datos SQL de Azure como sistema de producción.

 ![Diagrama de SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## Requisitos de la replicación transaccional

La replicación transaccional es una tecnología integrada con SQL Server desde SQL Server 6.5. Es una tecnología muy madura y comprobada, que conocen la mayoría de los administradores de base de datos y con la que tienen experiencia. Ahora, con [SQL Server 2016](https://www.microsoft.com/es-ES/cloud-platform/sql-server), Base de datos SQL de Azure se puede configurar como un [suscriptor de replicación transaccional](https://msdn.microsoft.com/library/mt589530.aspx) para la publicación local. La experiencia que obtiene configurándolo desde Management Studio es exactamente la misma que si configura un suscriptor de replicación transaccional en un servidor local. Se admite la compatibilidad con este escenario cuando el publicador y el distribuidor es al menos de una de las siguientes versiones de SQL Server:

 - SQL Server 2016 y versiones posteriores
 - SQL Server 2014 SP1 CU3 y versiones posteriores
 - SQL Server 2014 RTM CU10 y versiones posteriores
 - SQL Server 2012 SP2 CU8 y versiones posteriores
 - SQL Server 2013 SP3 cuando se publique


> [AZURE.IMPORTANT] Debe usar siempre la versión más reciente de SQL Server Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. Las versiones anteriores de SQL Server Management Studio no podrán configurar la instancia de Base de datos SQL como suscriptor. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## Pasos siguientes

- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/es-ES/cloud-platform/sql-server)

## Recursos adicionales

- [Replicación transaccional](https://msdn.microsoft.com/library/mt589530.aspx)
- [Base de datos SQL V12](sql-database-v12-whats-new.md)
- [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
- [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0803_2016-->