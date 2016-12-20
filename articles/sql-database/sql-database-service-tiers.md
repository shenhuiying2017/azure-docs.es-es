---
title: 'Rendimiento de SQL Database: niveles de servicio | Microsoft Docs'
description: Compare los niveles de servicio de SQL Database.
keywords: opciones de base de datos, rendimiento de la base de datos
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/09/2016
ms.author: carlrab; janeng
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: ed598cfdaf5bebc1cf92894ba1f6c79f268ef3c3


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opciones y rendimiento de SQL Database: descripción de lo que está disponible en cada nivel de servicio
[Azure SQL Database](sql-database-technical-overview.md) ofrece tres niveles de servicio: **Básico**, **Estándar** y **Premium**, con varios niveles de rendimiento para controlar diferentes cargas de trabajo. Niveles de rendimiento más altos proporcionan recursos diseñados para proporcionar un mayor rendimiento. Puede cambiar los [niveles de servicio y de rendimiento dinámicamente](sql-database-scale-up.md) sin tiempo de inactividad. Los niveles de servicio Básico, Estándar y Premium tienen un Acuerdo de Nivel de Servicio de tiempo de actividad del 99,99 %, opciones de continuidad empresarial flexibles, características de seguridad y facturación por hora. 

Puede crear bases de datos independientes con recursos dedicados en el [nivel de rendimiento](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) seleccionado. También puede administrar varias bases de datos en un [grupo elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus), en el que los recursos se comparten en las bases de datos. Los recursos disponibles para las bases de datos independientes se expresan en términos de unidades de transacción de base de datos (DTU) y para los grupos elásticos en términos de DTU elásticas (eDTU). Para más información sobre DTU y eDTU, consulte el artículo de [descripción de las DTU](sql-database-what-is-a-dtu.md). 

En ambos casos, los niveles de servicio son: **Básico**, **Estándar** y **Premium**. 

## <a name="choosing-a-service-tier"></a>Selección de un nivel de servicio
En la tabla siguiente se proporcionan ejemplos de los niveles más adecuados para las diferentes cargas de trabajo de las aplicaciones.

| Nivel de servicio | Carga de trabajo objetivo |
| :--- | --- |
| **Básico** | Ideal para bases de datos pequeñas, que suelen admitir una sola operación activa en un momento dado. Algunos ejemplos son las bases de datos utilizadas para desarrollo o prueba, o las aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| **Standard** |La mejor opción para las aplicaciones en la nube con pocos o medios requisitos de rendimiento de E/S; admite varias consultas simultáneas. Algunos ejemplos son las aplicaciones web o los grupos de trabajo. |
| **Premium** | Diseñado para un alto volumen transaccional con altos requisitos de rendimiento de E/S; admite muchos usuarios simultáneos. Algunos ejemplos son las bases de datos que admiten aplicaciones críticas. |

En primer lugar, decida si desea ejecutar una base de datos independiente o agrupar las bases de datos que compartan recursos. Revise las [consideraciones de grupo elástico](sql-database-elastic-pool-guidance.md). Para decidir el nivel de servicio, determine las características mínimas de la base de datos que necesita:

* Tamaño máximo de bases de datos individuales (2 GB como máximo para el nivel Básico, 250 GB como máximo para el nivel Estándar y de 500 GB a 1 TB como máximo para el Premium en los niveles de rendimiento altos)
* Almacenamiento total máximo en el caso de un grupo elástico (117 GB para Básico, 1200 para Estándar y 750 para Premium)
* Número máximo de bases de datos por grupo (400 para Básico, 400 para Estándar y 50 para Premium)
* Período de retención de la copia de seguridad de la base de datos (7 días para Básico y 35 días para Estándar y Premium)

Cuando haya determinado el nivel de servicio mínimo, estará listo para determinar el nivel de rendimiento de la base de datos (el número de DTU). En muchos casos, los niveles de rendimiento estándar S2 y S3 son un buen punto de partida. Para las bases de datos con los requisitos de CPU o E/S altos, los niveles de rendimiento Premium son el punto de partida adecuado. El nivel Premium ofrece más CPU y comienza en 10 veces más E/S que el nivel de rendimiento estándar más alto.

Después de elegir inicialmente un nivel de rendimiento, puede escalar o reducir verticalmente la [base de datos individual](sql-database-scale-up.md) o su [grupo elástico](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) de forma dinámica, en función de la experiencia real. Para escenarios de migración, también puede utilizar la [calculadora de DTU](http://dtucalculator.azurewebsites.net/) para calcular el número aproximado de DTU necesarias. 

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Niveles de servicio de bases de datos independientes y niveles de rendimiento
Para las bases de datos independientes, hay varios niveles de rendimiento dentro de cada nivel de servicio. Tiene la flexibilidad de elegir el nivel más adecuado a las exigencias de su carga de trabajo. Si necesita escalar o reducir verticalmente, puede cambiar fácilmente los niveles de la base de datos. Para más información, consulte [Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL](sql-database-scale-up.md) .

Las características de rendimiento enumeradas se aplican a las bases de datos creadas con [SQL Database V12](sql-database-v12-whats-new.md). Independientemente de la cantidad de bases de datos hospedadas, la base de datos seguirá recibiendo un conjunto de recursos garantizado, y las características de rendimiento previstas de la base de datos no se verán afectadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para ver una explicación detallada de todas las demás filas de esta tabla de niveles de servicio, consulte [Límites y capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Niveles de servicio de grupos elásticos y rendimiento en las eDTU

Los grupos permiten que las bases de datos compartan y consuman recursos de eDTU sin necesidad de asignar un nivel de rendimiento específico a cada una. Por ejemplo, una base de datos independiente de un grupo Estándar puede usar desde ninguna eDTU hasta el número máximo de eDTU de base de datos que haya establecido al configurar el grupo. Los grupos permiten que varias bases de datos con diferentes cargas de trabajo usen de forma eficaz los recursos de eDTU disponibles para todo el grupo. Consulte el artículo sobre las [consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md) para más información.

En la tabla siguiente se describen las características de los niveles de servicio de los grupos.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Todas las bases de datos de un grupo también se adhieren a las características de las bases de datos independientes de ese nivel. Por ejemplo, el grupo Básico tiene un límite máximo de entre 4800 y 28 800 sesiones por grupo, mientras que una base de datos de un grupo Básico tiene un límite de 300 sesiones.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda los detalles sobre los [grupos elásticos](sql-database-elastic-pool-guidance.md) y las [consideraciones de precio y rendimiento para estos](sql-database-elastic-pool-guidance.md).
* Aprenda cómo [supervisar, administrar y cambiar el tamaño de los grupos elásticos](sql-database-elastic-pool-manage-portal.md) y [supervisar el rendimiento de bases de datos independientes](sql-database-single-database-monitor.md).
* Ahora que conoce los niveles de SQL Database, pruébelos con una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) y aprenda a [crear su primera instancia de SQL Database](sql-database-get-started.md).




<!--HONumber=Dec16_HO2-->


