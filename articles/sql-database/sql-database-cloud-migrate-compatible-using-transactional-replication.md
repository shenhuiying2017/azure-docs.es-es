---
title: "Migración a SQL Database mediante la replicación transaccional | Microsoft Docs"
description: "Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, replicación transaccional"
services: sql-database
documentationcenter: 
author: jognanay
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 12/09/2016
ms.author: carlrab; jognanay;
translationtype: Human Translation
ms.sourcegitcommit: 8baeadbf7ef24e492c4115745c0d384e4f526188
ms.openlocfilehash: 8380925a56d39bd53fe737bed539b862cc835fad


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

## <a name="how-transactional-replication-works"></a>Uso de la replicación transaccional

La replicación transaccional implica tres componentes principales. Estos componentes son el publicador, el distribuidor y el suscriptor. Estos componentes llevan a cabo la replicación en conjunto. El distribuidor tiene la responsabilidad de controlar los procesos que mueven los datos entre los servidores. Cuando configure la distribución, SQL creará una base de datos de distribución. Cada publicador debe estar vinculado a una base de datos de distribución. La base de datos de distribución contiene los metadatos de cada publicación asociada y los datos sobre el progreso de cada replicación. En el caso de la replicación transaccional, contendrá todas las transacciones que se deben ejecutar en el suscriptor.

El publicador es la base de datos donde se originan todos los datos de la migración. Dentro del publicador puede haber muchas publicaciones. Estas publicaciones contienen artículos que se asignan a todas las tablas y datos que se deben replicar. Según cómo defina la publicación y los artículos, puede replicar parte o la totalidad de la base de datos. 

En la replicación, el suscriptor es el servidor que recibe todos los datos y transacciones desde la publicación. Cada publicación puede tener muchas replicaciones.

## <a name="transactional-replication-requirements"></a>Requisitos de la replicación transaccional
[Vaya a este vínculo para ver una lista actualizada de los requisitos.](https://msdn.microsoft.com/en-US/library/mt589530.aspx)
> [!IMPORTANT]
> Use siempre la versión más reciente de SQL Server Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. Las versiones anteriores de SQL Server Management Studio no pueden configurar la instancia de Base de datos SQL como suscriptor. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migración a SQL Database con el flujo de trabajo de Replicación transaccional

1. Configuración de la distribución
   -  [Uso de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Uso de Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Creación de publicación
   -  [Uso de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Uso de Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Creación de suscripción
   -  [Uso de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Uso de Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

## <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Algunas sugerencias y diferencias para migrar a SQL Database

1. Uso de un distribuidor local 
   - Esto afectará el rendimiento del servidor. 
   - Si el impacto en el rendimiento no es aceptable puede usar otro servidor, pero hará más compleja la administración.
2. Cuando seleccione una carpeta de instantáneas, asegúrese de que la carpeta que seleccione sea lo suficientemente grande como para contener un BCP de cada tabla que desea replicar. 
3. Tenga en cuenta que la creación de instantáneas bloqueará las tablas asociadas hasta que finalice; recuerde esto cuando programe la instantánea. 
4. Azure SQL Database solo admite las suscripciones de inserción
   - Solo puede agregar suscripciones del lado de la base de datos local.

## <a name="next-steps"></a>Pasos siguientes
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>Recursos adicionales
* Para más información sobre la replicación transaccional, consulte [Replicación transaccional](https://msdn.microsoft.com/library/mt589530.aspx).
* Para información sobre el proceso de migración general y sus opciones, consulte [Migración de una base de datos de SQL Server a SQL Database en la nube](sql-database-cloud-migrate.md).



<!--HONumber=Dec16_HO2-->


