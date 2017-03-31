---
title: 'Rendimiento de SQL Database: niveles de servicio | Microsoft Docs'
description: Compare los niveles de servicio de SQL Database.
keywords: opciones de base de datos, rendimiento de la base de datos
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ab637f9910523cc8d8967dd1507dbcfad9f7ae88
ms.lasthandoff: 03/28/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opciones y rendimiento de SQL Database: descripción de lo que está disponible en cada nivel de servicio

[Azure SQL Database](sql-database-technical-overview.md) ofrece cuatro niveles de servicio: **Básico**, **Estándar**, **Premium** y **Premium RS**. Cada nivel de servicio tiene varios niveles de rendimiento para controlar las diferentes cargas de trabajo. Unos niveles de rendimiento más altos ofrecen recursos adicionales diseñados para proporcionar un mayor rendimiento. Puede cambiar los niveles de servicio y de rendimiento dinámicamente sin tiempo de inactividad. Los niveles de servicio Básico, Estándar y Premium tienen un Acuerdo de Nivel de Servicio de tiempo de actividad del 99,99 %, opciones de continuidad empresarial flexibles, características de seguridad y facturación por hora. El nivel Premium RS proporciona los mismos niveles de rendimiento, características de seguridad y de continuidad empresarial que el nivel Premium aunque con un Acuerdo de Nivel de Servicio reducido.

> [!IMPORTANT]
> Las bases de datos del nivel Premium RS se ejecutan con un número menor de copias redundantes en comparación con las bases de datos Premium o Estándar. Por ello, si se produce un error del servicio, deberá recuperar la base de datos a partir de una copia de seguridad con un retardo de hasta 5 minutos.
>

Puede crear bases de datos únicas con recursos dedicados en un nivel de servicio de un [nivel de rendimiento](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) específico. También puede crear bases de datos en un [grupo elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus), en el que los recursos se comparten entre varias bases de datos. Los recursos disponibles para las bases de datos únicas se expresan como unidades de transacción de base de datos (DTU) y, para los grupos de bases de datos elásticas, como unidades de transacción de bases de datos elásticas (eDTU). Para más información sobre DTU y eDTU, consulte el artículo de [descripción de las DTU](sql-database-what-is-a-dtu.md). 

## <a name="choosing-a-service-tier"></a>Selección de un nivel de servicio
En la tabla siguiente se proporcionan ejemplos de los niveles más adecuados para las diferentes cargas de trabajo de las aplicaciones.

| Nivel de servicio | Carga de trabajo objetivo |
| :--- | --- |
| **Básico** | Ideal para bases de datos pequeñas, que suelen admitir una sola operación activa en un momento dado. Algunos ejemplos son las bases de datos utilizadas para desarrollo o prueba, o las aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| **Standard** |La mejor opción para las aplicaciones en la nube con pocos o medios requisitos de rendimiento de E/S; admite varias consultas simultáneas. Algunos ejemplos son las aplicaciones web o los grupos de trabajo. |
| **Premium** | Diseñado para un alto volumen transaccional con altos requisitos de rendimiento de E/S; admite muchos usuarios simultáneos. Algunos ejemplos son las bases de datos que admiten aplicaciones críticas. |
| **Premium RS** | Diseñado para cargas de trabajo intensivas de E/S pero que no requieren las mayores garantías de disponibilidad. Los ejemplos incluyen las pruebas en cargas de trabajo de alto rendimiento, o en una carga de trabajo analítico en el que la base de datos no es el sistema de registro. |
|||

En primer lugar, decida si desea ejecutar una base de datos única con una cantidad de recursos dedicados definida o si desea compartir un grupo de recursos entre un grupo de bases de datos. Revise las [consideraciones de grupo elástico](sql-database-elastic-pool-guidance.md). Para decidir el nivel de servicio, determine las características mínimas de la base de datos que necesita:

| **Características del nivel de servicio** | **Básico** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamaño máximo individual de la base de datos | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| Almacenamiento total máximo en un grupo elástico | 117 GB | 1200 GB | 750 GB | 750 GB |
| Cantidad máxima de bases de datos por grupo | 400  | 400 | 50 | 50 |
| Período de retención de copias de seguridad de base de datos | 7 días | 35 días | 35 días | 35 días |
||||||

> [!IMPORTANT]
> Los clientes que usan los niveles de rendimiento P11 y P15 pueden usar hasta 4 TB de almacenamiento incluido sin cargo adicional. Esta opción de 4 TB se encuentra actualmente en versión preliminar pública en las siguientes regiones: Este de EE. UU. 2, oeste de EE. UU., Europa Occidental, Asia Suroriental, Japón Oriental, Este de Australia, centro de Canadá y Canadá Oriental. Para las limitaciones actuales, consulte las [limitaciones actuales de 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

Cuando haya determinado el nivel de servicio mínimo, estará listo para determinar el nivel de rendimiento de la base de datos (el número de DTU). Con frecuencia, los niveles de rendimiento S2 y S3 estándar son un buen punto de partida. Para las bases de datos con los requisitos de CPU o E/S altos, los niveles de rendimiento Premium son el punto de partida adecuado. El nivel Premium ofrece más CPU y comienza en 10 veces más E/S que el nivel de rendimiento estándar más alto.

## <a name="single-database-service-tiers-and-performance-levels"></a>Niveles de servicio de la Base de datos única y niveles de rendimiento
Para las bases de datos únicas, hay varios niveles de rendimiento dentro de cada nivel de servicio. Tiene la flexibilidad de elegir el nivel que mejor satisfaga las demandas de cargas de trabajo mediante [Azure Portal](sql-database-manage-single-databases-portal.md), [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md), [Transact-SQL](sql-database-manage-single-databases-tsql.md), C# y la API de REST. 

Independientemente de la cantidad de bases de datos hospedadas, la base de datos seguirá recibiendo un conjunto de recursos garantizado, y las características de rendimiento previstas de la base de datos no se verán afectadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para ver una explicación detallada de todas las demás filas de esta tabla de niveles de servicio, consulte [Límites y capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Escalado y reducción vertical de una base de datos única

Después de elegir inicialmente un nivel de rendimiento, puede escalar o reducir verticalmente una base de datos única de forma dinámica, en función de la experiencia real. Si necesita escalar vertical u horizontalmente, puede cambiar fácilmente los niveles de la base de datos con la utilización de [Azure Portal](sql-database-manage-single-databases-portal.md), [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md), [Transact-SQL](sql-database-manage-single-databases-tsql.md), C# y la API de REST. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Al cambiar el nivel de servicio o de rendimiento de una base de datos, se crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, se cambian las conexiones a la réplica. Durante este proceso, no se pierde ningún dato; sin embargo, durante el breve momento en que se cambie a la réplica, las conexiones a la base de datos estarán deshabilitadas, por tanto, es posible que se reviertan algunas transacciones en curso. Este intervalo varía, pero de media dura menos de 4 segundos, y en más del 99 % de los casos es inferior a 30 segundos. Si el número de transacciones en curso es elevado mientras las conexiones están deshabilitadas, este intervalo puede ser superior.  

La duración de todo el proceso de escalado vertical depende del nivel de servicio y del tamaño de la base de datos antes y después del cambio. Por ejemplo, el cambio de una base de datos de 250 GB dentro de un nivel de servicio Estándar, o bien desde o hacia este, se completará en 6 horas. Para una base de datos del mismo tamaño que cambie los niveles de rendimiento del nivel de servicio Premium, se completará en unas 3 horas.

* Para degradar una base de datos, esta no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino. 
* Al actualizar una base de datos con la opción [Replicación geográfica](sql-database-geo-replication-portal.md) habilitada, primero es preciso actualizar sus bases de datos secundarias en el nivel de rendimiento deseado antes de actualizar la principal.
* Al realizar una degradación desde un nivel de servicio Premium, primero es preciso finalizar todas las relaciones de Replicación geográfica. Puede seguir los pasos que se describen en el tema [Recuperación de una base de datos SQL de Azure tras una interrupción](sql-database-disaster-recovery.md) para detener el proceso de replicación entre la base de datos principal y las bases de datos secundarias activas.
* Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si cambia a un nivel inferior, puede perder la capacidad de restaurar a un momento dado o tener un período de retención de copias de seguridad más breve. Para obtener más información, consulte [Copia de seguridad y restauración de Base de datos SQL de Azure](sql-database-business-continuity.md).
* Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.

> [!IMPORTANT]
> Para obtener instrucciones detalladas, consulte [Administración de bases de datos únicas con Azure Portal](sql-database-manage-single-databases-portal.md), [Administración de bases de datos únicas con Powershell](scripts/sql-database-monitor-and-scale-database-powershell.md) o [Administración de bases de datos únicas con Transact-SQL](sql-database-manage-single-databases-tsql.md).
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Niveles de servicio de grupos elásticos y rendimiento en las eDTU

Los grupos permiten que las bases de datos compartan y consuman recursos de eDTU sin necesidad de asignar un nivel de rendimiento específico a cada una. Por ejemplo, una base de datos única de un grupo Estándar puede usar desde 0 eDTU al número máximo de eDTU de base de datos que estableció al configurar el grupo. Los grupos permiten que varias bases de datos con diferentes cargas de trabajo usen de forma eficaz los recursos de eDTU disponibles para todo el grupo. Consulte el artículo sobre las [consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md) para más información.

En la tabla siguiente se describen las características de los niveles de servicio de los grupos.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Además, todas las bases de datos de un grupo se ajustan a las características de base de datos única de ese nivel. Por ejemplo, el grupo Básico tiene un límite máximo de entre 4800 y 28 800 sesiones por grupo, mientras que una base de datos de un grupo Básico tiene un límite de 300 sesiones.

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>Escalado y reducción vertical de un grupo elástico

Después de elegir inicialmente un nivel de rendimiento, puede escalar o reducir verticalmente el grupo elástico de forma dinámica, en función de la experiencia real. 

* El cambio del número mínimo de eDTU por base de datos o del máximo de eDTU por base de datos suele completarse en cinco minutos o menos.
* El tiempo para cambiar el tamaño del grupo (eDTU) depende el tamaño combinado de todas las bases de datos del grupo. Los cambios tienen un duración media de 90 minutos o menos por cada 100 GB. Por ejemplo, si el espacio total que usan todas las bases de datos del grupo es de 200 GB, la latencia esperada para cambiar las eDTU de grupo por grupo será de 3 horas o menos.

Para obtener instrucciones detalladas, consulte [Administración de un grupo elástico en Azure Portal](sql-database-elastic-pool-manage-portal.md), [Administración de un grupo elástico con Powershell](scripts/sql-database-monitor-and-scale-pool-powershell.md), [Administración de un grupo elástico con Transact-SQL](sql-database-elastic-pool-manage-tsql.md), o [Administración de un grupo elástico con C#](sql-database-elastic-pool-manage-csharp.md).

## <a name="creating-or-upgrading-to-4tb"></a>Creación o actualización a la opción de 4 TB

En las siguientes secciones se describen los detalles de implementación para la opción de 4 TB.

### <a name="creating-in-the-azure-portal"></a>Creación en Azure Portal

Cuando crea un nivel de rendimiento P11 o P15, la opción de almacenamiento predeterminado de 1TB está preseleccionada. Para las bases de datos ubicadas en alguna de las regiones admitidas, puede aumentar la capacidad de almacenamiento máxima hasta 4 TB. En el resto de regiones, el control deslizante de almacenamiento no se puede modificar. El precio no cambia cuando se selecciona la opción de 4 TB de almacenamiento incluido.

### <a name="creating-using-powershell-or-transact-sql"></a>Creación con PowerShell o Transact-SQL

Al crear una base de datos con un nivel de rendimiento P11 o P15, puede establecer el valor de tamaño máximo en 1 TB (valor predeterminado) o en 4 TB. Los valores "1024 GB" y "4096 GB" también se aceptan. Si elige la opción de tamaño máximo de 4 TB, se producirá un error en el comando de creación que indica que la base de datos se ha aprovisionado en una región no admitida.

### <a name="upgrading-to-4tb"></a>Actualización a la opción de 4 TB 

Para las bases de datos con niveles de rendimiento P11 y P15 ubicadas en alguna de las regiones admitidas, puede aumentar la capacidad de almacenamiento máxima hasta 4 TB. Esto se puede hacer en Azure Portal, en PowerShell o con Transact-SQL. En el ejemplo siguiente se muestra cómo se cambia el tamaño máximo mediante el comando ALTER DATABASE:

```ALTER DATABASE <DatabaseName> MODIFY (MAXSIZE = 4096 GB);
```

Upgrading an existing P11 or P15 database can only be performed by a server-level principal login or by members of the dbmanager database role. 
If executed in a supported region the configuration will be updated immediately. This can be checked using the [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) or by inspecting the database size in the Azure portal. The database will remain online during the upgrade process. However, you will not be able to utilize the full 4 TB of storage until the actual database files have been upgraded to the new maxsize. The length of time required depends upon on the size of the database being upgraded.  

### Error messages
When creating or upgrading an P11/P15 database in an unsupported region, the create or upgrade operation will fail with the following error message: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East.**

## Current limitations of P11 and P15 databases with 4 TB maxsize

- When creating or updating a P11 or P15 database, you can only chose between 1 TB and 4 TB maxsize. Intermediate storage sizes are not currently supported.
- The 4 TB database maxsize cannot be changed to 1 TB even if the actual storage used is below 1 TB. Thus, you cannot downgrade a P11-4TB/P15-4TB to a P11-1TB/P15-1TB or a lower performance tier (e.g., to P1-P6) until we are providing additional storage options for the rest of the performance tiers. This restriction also applies to the restore and copy scenarios including point-in-time, geo-restore, long-term-backup-retention, and database copy. Once a database is configured with the 4 TB option, all restore operations of this database must be into a P11/P15 with 4 TB maxsize.
- For Active Geo-Replication scenarios:
   - Setting up a geo-replication relationship: If the primary database is P11 or P15, the secondary(ies) must also be P11 or P15; lower performance tiers will be rejected as secondaries since they are not capable of supporting 4 TB.
   - Upgrading the primary database in a geo-replication relationship: Changing the maxsize to 4 TB on a primary database will trigger the same change on the secondary database. Both upgrades must be successful for the change on the primary to take effect. Region limitations for the 4TB option apply (see above). If the secondary is in a region that does not support 4 TB, the primary will not be upgraded.
- Using the Import/Export service for loading P11-4TB/P15-4TB databases is not supported. Use SqlPackage.exe to [import](sql-database-import-sqlpackage.md) and [export](sql-database-export-sqlpackage.md) data.

## Next steps

* Learn the details of [elastic pools](sql-database-elastic-pool-guidance.md) and [price and performance considerations for elastic pools](sql-database-elastic-pool-guidance.md).
* Learn how to [Monitor, manage, and resize elastic pools](sql-database-elastic-pool-manage-portal.md) and [Monitor the performance of single databases](sql-database-single-database-monitor.md).
* Now that you know about the SQL Database tiers, try them out with a [free account](https://azure.microsoft.com/pricing/free-trial/) and learn [how to create your first SQL database](sql-database-get-started.md).
* For migration scenarios, use the [DTU Calculator](http://dtucalculator.azurewebsites.net/) to approximate the number of DTUs needed. 


