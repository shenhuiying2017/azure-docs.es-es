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
ms.date: 02/09/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 920143756a0e0b369cf839681f9c4308f77a4af0


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opciones y rendimiento de SQL Database: descripción de lo que está disponible en cada nivel de servicio
[Azure SQL Database](sql-database-technical-overview.md) ofrece tres niveles de servicio: **Básico**, **Estándar** y **Premium**, con varios niveles de rendimiento para controlar diferentes cargas de trabajo. Niveles de rendimiento más altos proporcionan recursos diseñados para proporcionar un mayor rendimiento. Puede cambiar los [niveles de servicio y de rendimiento dinámicamente](sql-database-service-tiers.md) sin tiempo de inactividad. Los niveles de servicio Básico, Estándar y Premium tienen un Acuerdo de Nivel de Servicio de tiempo de actividad del 99,99 %, opciones de continuidad empresarial flexibles, características de seguridad y facturación por hora. 

Puede crear bases de datos únicas con recursos dedicados en el [nivel de rendimiento](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) seleccionado. También puede administrar varias bases de datos en un [grupo elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus), en el que los recursos se comparten en las bases de datos. Los recursos disponibles para las bases de datos únicas se expresan en términos de unidades de transacción de base de datos (DTU) y para los grupos elásticos en términos de DTU elásticas (eDTU). Para más información sobre DTU y eDTU, consulte el artículo de [descripción de las DTU](sql-database-what-is-a-dtu.md). 

En ambos casos, los niveles de servicio son: **Básico**, **Estándar** y **Premium**. 

## <a name="choosing-a-service-tier"></a>Selección de un nivel de servicio
En la tabla siguiente se proporcionan ejemplos de los niveles más adecuados para las diferentes cargas de trabajo de las aplicaciones.

| Nivel de servicio | Carga de trabajo objetivo |
| :--- | --- |
| **Básico** | Ideal para bases de datos pequeñas, que suelen admitir una sola operación activa en un momento dado. Algunos ejemplos son las bases de datos utilizadas para desarrollo o prueba, o las aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| **Standard** |La mejor opción para las aplicaciones en la nube con pocos o medios requisitos de rendimiento de E/S; admite varias consultas simultáneas. Algunos ejemplos son las aplicaciones web o los grupos de trabajo. |
| **Premium** | Diseñado para un alto volumen transaccional con altos requisitos de rendimiento de E/S; admite muchos usuarios simultáneos. Algunos ejemplos son las bases de datos que admiten aplicaciones críticas. |

En primer lugar, decida si desea ejecutar una base de datos única o agrupar las bases de datos que compartan recursos. Revise las [consideraciones de grupo elástico](sql-database-elastic-pool-guidance.md). Para decidir el nivel de servicio, determine las características mínimas de la base de datos que necesita:

* Tamaño máximo de bases de datos individuales (2 GB como máximo para el nivel Básico, 250 GB como máximo para el nivel Estándar y de 500 GB a 1 TB como máximo para el Premium en los niveles de rendimiento altos)
* Almacenamiento total máximo en un grupo elástico (117 GB para Básico, 1200 GB para Estándar y 750 GB para Premium)
* Número máximo de bases de datos por grupo (400 para Básico, 400 para Estándar y 50 para Premium)
* Período de retención de la copia de seguridad de la base de datos (7 días para Básico y 35 días para Estándar y Premium)

Cuando haya determinado el nivel de servicio mínimo, estará listo para determinar el nivel de rendimiento de la base de datos (el número de DTU). Con frecuencia, los niveles de rendimiento S2 y S3 estándar son un buen punto de partida. Para las bases de datos con los requisitos de CPU o E/S altos, los niveles de rendimiento Premium son el punto de partida adecuado. El nivel Premium ofrece más CPU y comienza en 10 veces más E/S que el nivel de rendimiento estándar más alto.

## <a name="single-database-service-tiers-and-performance-levels"></a>Niveles de servicio de la Base de datos única y niveles de rendimiento
Para las bases de datos únicas, hay varios niveles de rendimiento dentro de cada nivel de servicio. Tiene la flexibilidad de elegir el nivel más adecuado a las exigencias de su carga de trabajo. Si necesita escalar o reducir verticalmente, puede cambiar fácilmente los niveles de la base de datos. Para más información, consulte [Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL](sql-database-service-tiers.md) .

Independientemente de la cantidad de bases de datos hospedadas, la base de datos seguirá recibiendo un conjunto de recursos garantizado, y las características de rendimiento previstas de la base de datos no se verán afectadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para ver una explicación detallada de todas las demás filas de esta tabla de niveles de servicio, consulte [Límites y capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Escalado y reducción vertical de una base de datos única

Después de elegir inicialmente un nivel de rendimiento, puede escalar o reducir verticalmente una base de datos única de forma dinámica, en función de la experiencia real. 

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
> Para obtener instrucciones detalladas, consulte [Administración de bases de datos únicas con Azure Portal](sql-database-manage-single-databases-portal.md), [Administración de bases de datos únicas con Powershell](sql-database-manage-single-databases-powershell.md) o [Administración de bases de datos únicas con Transact-SQL](sql-database-manage-single-databases-tsql.md).
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

> [!IMPORTANT]
> Para obtener instrucciones detalladas, consulte [Administración de grupos elásticos con Azure Portal](sql-database-elastic-pool-manage-portal.md), [Administración de grupos elásticos con Powershell](sql-database-elastic-pool-manage-powershell.md), [Administración de grupos elásticos con Transact-SQL](sql-database-elastic-pool-manage-tsql.md) o [Administración de grupos elásticos con C#](sql-database-elastic-pool-manage-csharp.md).
>

## <a name="next-steps"></a>Pasos siguientes

* Aprenda los detalles sobre los [grupos elásticos](sql-database-elastic-pool-guidance.md) y las [consideraciones de precio y rendimiento para estos](sql-database-elastic-pool-guidance.md).
* Aprenda cómo [supervisar, administrar y cambiar el tamaño de los grupos de bases de datos elásticas](sql-database-elastic-pool-manage-portal.md) y [supervisar el rendimiento de bases de datos únicas](sql-database-single-database-monitor.md).
* Ahora que conoce los niveles de SQL Database, pruébelos con una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) y aprenda a [crear su primera instancia de SQL Database](sql-database-get-started.md).
* Para escenarios de migración, utilice la [calculadora de DTU](http://dtucalculator.azurewebsites.net/) para calcular el número aproximado de DTU necesarias. 




<!--HONumber=Feb17_HO3-->


