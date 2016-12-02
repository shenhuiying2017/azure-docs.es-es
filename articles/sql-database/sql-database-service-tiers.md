---
title: 'Rendimiento de SQL Database: niveles de servicio | Microsoft Docs'
description: Compare los niveles de servicio de SQL Database.
keywords: opciones de base de datos, rendimiento de la base de datos
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/15/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 7bbdbe345bd468c01e2a790610bcf6c063c11f9b


---
# <a name="sql-database-service-tiers-for-single-databases-and-elastic-database-pools"></a>Niveles de servicio de SQL Database para bases de datos únicas y grupos de bases de datos elásticas
[Azure SQL Database](sql-database-technical-overview.md) ofrece tres niveles de servicio con varios niveles de rendimiento para controlar varias cargas de trabajo. Los niveles de rendimiento más altos proporcionan un conjunto de recursos cada vez mayor, diseñado para proporcionar un mayor rendimiento. Puede cambiar los niveles de servicio y de rendimiento dinámicamente. Para más información, consulte [Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL](sql-database-scale-up.md) .

Puede administrar cada base de datos en su propio [nivel de servicio](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) con su propio nivel de rendimiento. También puede administrar varias bases de datos en un [grupo de bases de datos elásticas](sql-database-service-tiers.md#elastic-database-pool-service-tiers-and-performance-in-edtus) con un conjunto compartido de recursos. Los recursos disponibles para las bases de datos únicas se expresan como unidades de transacción de base de datos (DTU) y, para los grupos de bases de datos elásticas, como DTU elásticas o eDTU. Para más información sobre DTU y eDTU, consulte [Qué es una DTU](sql-database-what-is-a-dtu.md). 

En ambos casos, los niveles de servicio son: **Básico**, **Estándar** y **Premium**. 

## <a name="service-tiers"></a>Niveles de servicio
Los niveles de servicio Básico, Estándar y Premium tienen un Acuerdo de Nivel de Servicio de tiempo de actividad del 99,99 % y ofrecen un rendimiento predecible, opciones de continuidad empresarial flexibles, características de seguridad y facturación por hora. En la tabla siguiente se proporcionan ejemplos de los niveles más adecuados para las diferentes cargas de trabajo de las aplicaciones.

| Nivel de servicio | Carga de trabajo objetivo |
| :--- | --- |
| **Básico** |Ideal para bases de datos pequeñas, que suelen admitir una sola operación activa en un momento dado. Algunos ejemplos son las bases de datos utilizadas para desarrollo o prueba, o las aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| **Estándar** |La mejor opción para la mayoría de aplicaciones en la nube; admite varias consultas simultáneas. Algunos ejemplos son las aplicaciones web o los grupos de trabajo. |
| **Premium** |Diseñado para grandes volúmenes de transacciones, admite muchos usuarios simultáneos y requiere el máximo nivel de funcionalidades de continuidad empresarial. Algunos ejemplos son las bases de datos que admiten aplicaciones críticas. |

> [!NOTE]
> Se han retirado las versiones Web y Business Edition. Si planea seguir utilizando las versiones Web y Business Edition, lea las [P+F de Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/) .
> 
> 

## <a name="single-database-service-tiers-and-performance-levels"></a>Niveles de servicio de la Base de datos única y niveles de rendimiento
Para las bases de datos únicas, hay varios niveles de rendimiento dentro de cada nivel de servicio. Tiene la flexibilidad de elegir el nivel más adecuado a las exigencias de su carga de trabajo. Si necesita escalar o reducir verticalmente, puede cambiar de forma rápida el nivel de rendimiento de la base de datos. Para más información, consulte [Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL](sql-database-scale-up.md) .

Las características de rendimiento enumeradas se aplican a las bases de datos creadas con [SQL Database V12](sql-database-technical-overview.md). Independientemente de la cantidad de bases de datos hospedadas, la base de datos seguirá recibiendo un conjunto de recursos garantizado, y las características de rendimiento previstas de la base de datos no se verán afectadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para ver una explicación detallada de todas las demás filas de esta tabla de niveles de servicio, consulte [Límites y capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 
> 

## <a name="elastic-database-pool-service-tiers-and-performance-in-edtus"></a>Niveles de servicio del grupo de bases de datos elásticas y rendimiento en las eDTU
También puede crear varias bases de datos dentro de un [grupo de bases de datos elásticas](sql-database-elastic-pool.md). Todas las bases de datos de un grupo de bases de datos elásticas comparten un conjunto común de recursos. Las características de rendimiento se miden por *Unidades de transacción de bases de datos elásticas* (eDTU). Al igual que con las bases de datos únicas, los grupos tienen tres niveles de servicio: **Básico**, **Estándar** y **Premium**. En el caso de los grupos, estos tres niveles también definen los límites de rendimiento global y varias características.

Los grupos permiten que las bases de datos compartan y consuman recursos de DTU sin necesidad de asignar un nivel de rendimiento específico a cada una. Por ejemplo, una base de datos de un grupo Estándar puede usar desde 0 eDTU hasta el número máximo de eDTU de base de datos que estableció al configurar el grupo. Los grupos permiten que varias bases de datos con diferentes cargas de trabajo usen de forma eficaz los recursos de eDTU disponibles para todo el grupo. Consulte [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md) para más detalles.

En la tabla siguiente se describen las características de los grupos de bases de datos elásticas de nivel Básico, Estándar y Premium.

[!INCLUDE [SQL DB service tiers table for elastic database pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Además, todas las bases de datos de un grupo se ajustan a las características de base de datos única de ese nivel. Por ejemplo, el grupo Básico tiene un límite máximo de entre 4800 y 28 800 sesiones por grupo, mientras que una base de datos de un grupo Básico tiene un límite de 300 sesiones.

## <a name="choosing-a-service-tier"></a>Selección de un nivel de servicio
Para decidir el nivel de servicio, empiece por determinar si la base de datos será única o formará parte de un grupo de bases de datos elásticas. 

### <a name="choosing-a-service-tier-for-a-single-database"></a>Selección de un nivel de servicio para una base de datos única
Para decidir el nivel de servicio para una base de datos única, empiece por determinar las características de la base de datos que necesita para elegir la edición de SQL Database:

* Tamaño de la base de datos (2 GB como máximo en el nivel Básico, 250 GB como máximo para el nivel Estándar y de 500 GB a 1 TB como máximo para el Premium, según el nivel de rendimiento)
* Período de retención de la copia de seguridad de la base de datos (7 días para el nivel Básico, 35 días para el Estándar y 35 días para el Premium)

Cuando haya determinado la edición de SQL Database, estará listo para determinar el nivel de rendimiento de la base de datos (el número de DTU). Este valor se puede estimar y, después, [escalar o reducir verticalmente de manera dinámica](sql-database-scale-up.md) en función de su experiencia real. También puede utilizar la [calculadora de DTU](http://dtucalculator.azurewebsites.net/) para hacer una estimación del número aproximado de DTU necesario. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Selección de un nivel de servicio para un grupo de bases de datos elásticas
Para decidir el nivel de servicio para un grupo de bases de datos elásticas, empiece por determinar las características de las bases de datos necesarias.

* Tamaño de la base de datos (2 GB para el nivel Básico, 250 GB para el Estándar y 500 GB para el Premium)
* Período de retención de la copia de seguridad de la base de datos (7 días para el nivel Básico, 35 días para el Estándar y 35 días para el Premium)
* Número de bases de datos por grupo (400 en el nivel Básico, 400 en el Estándar y 50 en el Premium)
* Capacidad máxima de almacenamiento por grupo (117 GB en el nivel Básico, 1200 en el Estándar y 750 en el Premium)

Una vez determinado el nivel de servicio para el grupo, estará preparado para determinar su nivel de rendimiento (eDTU). Este valor se puede estimar y, después, [escalar o reducir verticalmente de manera dinámica](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) en función de su experiencia real. También puede utilizar la [calculadora de DTU](http://dtucalculator.azurewebsites.net/) para hacer una estimación del número aproximado de DTU necesario para cada base de datos dentro de un grupo, lo que le ayudará a establecer su límite superior.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre los precios de estos niveles, consulte [Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
* Conozca los detalles sobre los [grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md) y las [consideraciones de precio y rendimiento para grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md).
* Aprenda a [supervisar, administrar y cambiar el tamaño de grupos de bases de datos elásticas](sql-database-elastic-pool-manage-portal.md) y [supervisar el rendimiento de bases de datos únicas](sql-database-single-database-monitor.md).
* Ahora que conoce los niveles de SQL Database, pruébelos con una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) y aprenda a [crear su primera instancia de SQL Database](sql-database-get-started.md).

## <a name="additional-resources"></a>Recursos adicionales
* [Modelos de diseño para las aplicaciones SaaS multiinquilino mediante Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Curso de vídeo de la Academia virtual de Microsoft sobre las funcionalidades de las bases de datos elásticas en Azure SQL Database](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)




<!--HONumber=Nov16_HO4-->


