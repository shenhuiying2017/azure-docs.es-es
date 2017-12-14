---
title: "Migración de una base de datos de SQL Server a Azure SQL Database | Microsoft Docs"
description: "Obtenga más información acerca de la migración de una base de datos de SQL Server a Azure SQL Database en la nube."
keywords: "migración de base de datos, migración de base de datos de sql server, herramientas de migración de bases de datos, migración de la base de datos, migrar base de datos sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 11/07/2017
ms.author: carlrab
ms.openlocfilehash: 8a31ed948fe9387720db61018e0edded530cd900
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migración de una base de datos de SQL Server a una instancia de SQL Database en la nube
En este artículo se describen los dos principales métodos para migrar una base de datos de SQL Server 2005 o posterior a Azure SQL Database. El primer método es más sencillo, pero durante la migración se requiere un determinado tiempo de inactividad, que puede ser considerable. El segundo método es más complejo, pero reduce en gran medida el tiempo de inactividad durante la migración.

En ambos casos, debe asegurarse de que la base de datos de origen sea compatible con Azure SQL Database mediante el [Asistente para migración de datos (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Con SQL Database V12, nos estamos acercando a una situación de [paridad de características](sql-database-features.md) con SQL Server, aparte de los problemas relacionados con las operaciones entre bases de datos y de nivel de servidor. Es necesario efectuar tareas de reingeniería en las bases de datos y aplicaciones basadas en [funciones incompatibles o parcialmente compatibles](sql-database-transact-sql-information.md) para [solucionar estas incompatibilidades](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) antes de migrar la base de datos de SQL Server.

> [!NOTE]
> Para migrar bases de datos que no sean de SQL Server, incluidos Microsoft Access, Sybase, MySQL Oracle y DB2 a Azure SQL Database, consulte [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/)(Asistente para migración de SQL Server).
> 

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Método 1: Migración con tiempo de inactividad

 Utilice este método si puede permitirse algún tiempo de inactividad o va a realizar una migración de prueba de la base de datos de producción para una migración posterior. Para consultar un tutorial, vea [Migración de una base de datos de SQL Server](sql-database-migrate-your-sql-server-database.md).

En la lista siguiente se describe el flujo de trabajo general para una migración de base de datos de SQL Server con este método.

  ![Diagrama de migración de VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Evalúe la compatibilidad](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) de la base de datos con la versión más reciente de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Prepare todas las correcciones necesarias como scripts de Transact-SQL.
3. Realice una copia coherente desde un punto de vista transaccional de la base de datos de origen que se está migrando y asegúrese de que no se vayan a realizar más cambios en la base de datos de origen (los cambios pueden aplicarse manualmente una vez completada la migración). Existen diferentes modos de poner una base de datos en modo inactivo, desde la deshabilitación de la conectividad de cliente hasta la creación de una [instantánea de base de datos](https://msdn.microsoft.com/library/ms175876.aspx).
4. Implemente los scripts de Transact-SQL para aplicar las correcciones a la copia de la base de datos.
5. [Migre](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) la copia de la base de datos a una nueva base de datos de Azure SQL Database mediante Data Migration Assistant.

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optimización del rendimiento de transferencia de datos durante la migración 

En la lista siguiente se incluyen recomendaciones para mejorar el rendimiento durante el proceso de importación.

* Elija el mejor nivel de servicio y de rendimiento que su presupuesto permita para optimizar el rendimiento de la transferencia. Una vez completada la migración, puede seleccionar una opción inferior para ahorrar dinero. 
* Reduzca la distancia entre su archivo BACPAC y el centro de datos de destino.
* Deshabilite las estadísticas automáticas durante la migración.
* Cree particiones de tablas e índices.
* Quite las vistas indexadas y vuelva a crearlas cuando se haya completado el proceso.
* Transfiera los datos históricos que se consultan con poca frecuencia a otra base de datos y migre estos datos históricos a una instancia de Azure SQL Database distinta. A continuación, podrá consultar estos datos históricos mediante [consultas elásticas](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optimización del rendimiento una vez completada la migración

[Actualice las estadísticas](https://msdn.microsoft.com/library/ms187348.aspx) con un examen completo una vez completada la migración.

## <a name="method-2-use-transactional-replication"></a>Método 2: Uso de la replicación transaccional

Si no se puede permitir quitar la base de datos de SQL Server de producción mientras se lleva a cabo la migración, puede usar la replicación transaccional de SQL Server como solución de migración. Para usar este método, la base de datos de origen debe cumplir los [requisitos para la replicación transaccional](https://msdn.microsoft.com/library/mt589530.aspx) y ser compatible con Azure SQL Database. Para más información acerca de la replicación de SQL con AlwaysOn, consulte [Configurar la replicación para grupos de disponibilidad AlwaysOn (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Para utilizar esta solución, configurará su instancia de Azure SQL Database como suscriptor de la instancia de SQL Server que quiere migrar. El distribuidor de la replicación transaccional sincroniza los datos de la base de datos de modo que estén sincronizados (el publicador) mientras se siguen produciendo nuevas transacciones. 

Con la replicación transaccional, todos los cambios en los datos o el esquema se muestran en Azure SQL Database. Cuando finalice la sincronización y esté listo para realizar la migración, cambie la cadena de conexión de las aplicaciones para que apunten a la instancia de Azure SQL Database. Cuando la replicación transaccional recupera todos los cambios pendientes en la base de datos de origen y todas las aplicaciones apuntan a Azure DB, puede desinstalar la replicación transaccional. Azure SQL Database es ahora su sistema de producción.

 ![Diagrama de SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> También puede usar la replicación transaccional para migrar un subconjunto de la base de datos de origen. La publicación que se replica en Azure SQL Database puede limitarse a un subconjunto de las tablas de la base de datos que se replica. Para cada tabla que se replica, puede limitar los datos a un subconjunto de filas o un subconjunto de columnas.
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migración a SQL Database con el flujo de trabajo de Replicación transaccional

> [!IMPORTANT]
> Use siempre la versión más reciente de SQL Server Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y SQL Database. Las versiones anteriores de SQL Server Management Studio no pueden configurar la instancia de SQL Database como suscriptor. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

1. Configuración de la distribución
   -  [Uso de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Uso de Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Creación de publicación
   -  [Uso de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Uso de Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Creación de suscripción
   -  [Uso de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Uso de Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

### <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Algunas sugerencias y diferencias para migrar a SQL Database

1. Uso de un distribuidor local 
   - Esto afecta al rendimiento del servidor. 
   - Si el impacto en el rendimiento no es aceptable, puede usar otro servidor, pero hará más compleja la administración.
2. Cuando seleccione una carpeta de instantáneas, asegúrese de que la carpeta que seleccione sea lo suficientemente grande como para contener un BCP de cada tabla que desea replicar. 
3. La creación de instantáneas bloquea las tablas asociadas hasta que se completa, por lo que debe programarse correctamente. 
4. Azure SQL Database solo admite las suscripciones de inserción. Solo puede agregar suscriptores de la base de datos de origen.

## <a name="resolving-database-migration-compatibility-issues"></a>Solución de problemas de compatibilidad de migración de bases de datos
Hay una gran variedad de problemas de compatibilidad que pueden surgir, en función de la versión de SQL Server de la base de datos de origen y de la complejidad de la base de datos que se migra. Las versiones anteriores de SQL Server tienen más problemas de compatibilidad. Use los siguientes recursos, además de una búsqueda específica en Internet mediante el motor de búsqueda de opciones:

* [Características de Base de datos de SQL Server no admitidas en Azure SQL Database](sql-database-transact-sql-information.md)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Funcionalidad del motor de base de datos no incluida en SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Además de realizar búsquedas en Internet y utilizar estos recursos, acuda a los [foros de la comunidad de MSDN de SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) o a [StackOverflow](http://stackoverflow.com/).

## <a name="next-steps"></a>Pasos siguientes
* Use el script del blog de ingenieros de EMEA de Azure SQL para [supervisar el uso de tempdb durante la migración](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
* Use el script del blog de ingenieros de EMEA de Azure SQL para [supervisar el espacio de registro de transacciones de la base de datos mientras se está produciendo la migración](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
* Consulte cómo [migrar de SQL Server a Azure SQL Database con archivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) en el blog de Customer Advisory Team de SQL Server.
* Para obtener información sobre cómo trabajar con la hora UTC después de la migración, consulte cómo [modificar la zona horaria predeterminada para su zona horaria local](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
* Para obtener información acerca de cómo cambiar el idioma predeterminado de una base de datos después de la migración, consulte [cómo cambiar el idioma predeterminado de Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).


