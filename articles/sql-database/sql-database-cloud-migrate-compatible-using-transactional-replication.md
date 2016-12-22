---
title: "Migración a SQL Database mediante la replicación transaccional | Microsoft Docs"
description: "Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, replicación transaccional"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 891c10b2f8e560a3c97f93198c742f657a92e927


---
# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Migración de una base de datos SQL Server a Base de datos SQL de Azure mediante la replicación transaccional
> [!div class="op_single_selector"]
> * [Asistente para migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exportación a un archivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importación desde un archivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

En este artículo, aprenderá a migrar una base de datos SQL Server compatible a Base de datos SQL Azure con un tiempo de inactividad mínimo mediante la replicación transaccional de SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Descripción de la arquitectura de replicación transaccional
Si no se puede permitir quitar la base de datos de SQL Server de producción mientras se lleva a cabo la migración, puede usar la replicación transaccional de SQL Server como solución de migración. Para utilizar esta solución, configurará su instancia de Base de datos SQL de Azure como suscriptor de la instancia de SQL Server local que quiere migrar. El distribuidor de la replicación transaccional local sincroniza los datos de la base de datos local de modo que estén sincronizados (el publicador) mientras se siguen produciendo nuevas transacciones. 

También puede usar la replicación transaccional para migrar un subconjunto de la base de datos local. La publicación que se replica en Base de datos SQL de Azure puede limitarse a un subconjunto de las tablas de la base de datos que se replica. Para cada tabla que se replica, puede limitar los datos a un subconjunto de filas o un subconjunto de columnas.

Con la replicación transaccional, todos los cambios en los datos o el esquema se muestran en la Base de datos SQL de Azure. Cuando finalice la sincronización y esté listo para realizar la migración, cambie la cadena de conexión de las aplicaciones para que apunten a la instancia de Base de datos SQL de Azure. Cuando la replicación transaccional recupera todos los cambios pendientes en la base de datos local y todas las aplicaciones apuntan a Base de datos de Azure, puede desinstalar la replicación transaccional. Base de datos SQL de Azure es ahora su sistema de producción.

 ![Diagrama de SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>Requisitos de la replicación transaccional
La replicación transaccional es una tecnología integrada con SQL Server desde SQL Server 6.5. Es una tecnología madura y comprobada, que conocen la mayoría de los administradores de base de datos y con la que tienen experiencia. Ahora, con [SQL Server 2016](https://www.microsoft.com/sql-server/sql-server-2016), Azure SQL Database se puede configurar como un [suscriptor de replicación transaccional](https://msdn.microsoft.com/library/mt589530.aspx) para la publicación local. La experiencia que obtiene configurándolo desde Management Studio es la misma que si configura un suscriptor de replicación transaccional en un servidor local. Se admite la compatibilidad con este escenario cuando el publicador y el distribuidor tienen al menos de una de las siguientes versiones de SQL Server:

* SQL Server 2016 y versiones posteriores 
* SQL Server 2014 SP1 CU3 y versiones posteriores
* SQL Server 2014 RTM CU10 y versiones posteriores
* SQL Server 2012 SP2 CU8 y versiones posteriores
* SQL Server 2012 SP3 y versiones posteriores

> [!IMPORTANT]
> Use siempre la versión más reciente de SQL Server Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. Las versiones anteriores de SQL Server Management Studio no pueden configurar la instancia de Base de datos SQL como suscriptor. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>Recursos adicionales
* [Replicación transaccional](https://msdn.microsoft.com/library/mt589530.aspx)
* [Características de SQL Database](sql-database-features.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


