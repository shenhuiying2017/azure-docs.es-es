<properties
    pageTitle="Opciones y rendimiento de SQL Database: niveles de servicio | Microsoft Azure"
    description="Compare el rendimiento y las características de continuidad empresarial de SQL Database de los niveles de servicio para encontrar el equilibrio adecuado entre costo y funcionalidad a medida que se escalan."
    keywords="opciones de base de datos, rendimiento de la base de datos"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>


# <a name="sql-database-options-and-performance:-understand-what's-available-in-each-service-tier"></a>Opciones y rendimiento de SQL Database: descripción de lo que está disponible en cada nivel de servicio

[Azure SQL Database](sql-database-technical-overview.md) ofrece tres niveles de servicio con varios niveles de rendimiento para controlar varias cargas de trabajo. Cada nivel de rendimiento proporciona un conjunto de recursos cada vez mayor, diseñado para proporcionar un mayor rendimiento. Puede administrar cada base de datos en su propio [nivel de servicio](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) con su propio nivel de rendimiento. También puede administrar varias bases de datos en un [grupo elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) con un conjunto compartido de recursos. Los recursos disponibles para las bases de datos independientes se expresan en términos de unidades de transacción de base de datos (DTU) y para los grupos elásticos en términos de DTU elásticas o eDTU. Para más información sobre DTU y eDTU, consulte [Qué es una DTU](sql-database-what-is-a-dtu.md). 

En ambos casos, los niveles de servicio son: **Básico**, **Estándar** y **Premium**. En estos niveles, las opciones de las bases de datos son similares para las bases de datos independientes y para los grupos elásticos, pero hay consideraciones adicionales para los grupos elásticos. Este artículo proporciona información detallada sobre los niveles de servicio para las bases de datos independientes y los grupos elásticos.

## <a name="service-tiers-and-database-options"></a>Niveles de servicio y opciones de las bases de datos
Los niveles de servicio Básico, Estándar y Premium tienen un Acuerdo de Nivel de Servicio de tiempo de actividad del 99,99 % y ofrecen un rendimiento predecible, opciones de continuidad empresarial flexibles, características de seguridad y facturación por hora. En la tabla siguiente se proporcionan ejemplos de los niveles más adecuados para las diferentes cargas de trabajo de las aplicaciones.

| Nivel de servicio | Carga de trabajo objetivo |
|---|---|
| **Básico** | Ideal para bases de datos pequeñas, que suelen admitir una sola operación activa en un momento dado. Algunos ejemplos son las bases de datos utilizadas para desarrollo o prueba, o las aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| **Estándar** | La mejor opción para la mayoría de aplicaciones en la nube; admite varias consultas simultáneas. Algunos ejemplos son las aplicaciones web o los grupos de trabajo. |
| **Premium** | Diseñado para grandes volúmenes de transacciones, admite muchos usuarios simultáneos y requiere el máximo nivel de funcionalidades de continuidad empresarial. Algunos ejemplos son las bases de datos que admiten aplicaciones críticas. |

>[AZURE.NOTE] Se han retirado las versiones Web y Business Edition. Si planea seguir utilizando las versiones Web y Business Edition, lea las [P+F de Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/) .

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Niveles de servicio de bases de datos independientes y niveles de rendimiento
Para las bases de datos independientes, hay varios niveles de rendimiento dentro de cada nivel de servicio. Tiene la flexibilidad de elegir el nivel más adecuado a las exigencias de su carga de trabajo. Si necesita escalar o reducir verticalmente, puede cambiar fácilmente los niveles de la base de datos. Para más información, consulte [Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL](sql-database-scale-up.md) .

Las características de rendimiento enumeradas se aplican a las bases de datos creadas con [SQL Database V12](sql-database-v12-whats-new.md). Independientemente de la cantidad de bases de datos hospedadas, la base de datos seguirá recibiendo un conjunto de recursos garantizado, y las características de rendimiento previstas de la base de datos no se verán afectadas.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] Para ver una explicación detallada de todas las demás filas de esta tabla de niveles de servicio, consulte [Límites y capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Niveles de servicio de grupos elásticos y rendimiento en las eDTU
Además de crear y escalar una base de datos independiente, también es posible administrar varias bases de datos dentro de un [grupo elástico](sql-database-elastic-pool.md). Todas las bases de datos de un grupo elástico comparten un conjunto de recursos común. Las características de rendimiento se miden por *Unidades de transacción de bases de datos elásticas* (eDTU). Al igual que con las bases de datos independientes, los grupos tienen tres niveles de servicio: **Básico**, **Estándar** y **Premium**. En el caso de los grupos, estos tres niveles también definen los límites de rendimiento global y varias características.

Los grupos permiten que las bases de datos compartan y consuman recursos de DTU sin necesidad de asignar un nivel de rendimiento específico a cada una. Por ejemplo, una base de datos independiente de un grupo Estándar puede usar desde ninguna eDTU hasta el número máximo de eDTU de base de datos que haya establecido al configurar el grupo. Los grupos permiten que varias bases de datos con diferentes cargas de trabajo usen de forma eficaz los recursos de eDTU disponibles para todo el grupo. Consulte el artículo sobre las [consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md) para más información.

En la tabla siguiente se describen las características de los niveles de servicio de los grupos.

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Todas las bases de datos de un grupo también se adhieren a las características de las bases de datos independientes de ese nivel. Por ejemplo, el grupo Básico tiene un límite máximo de entre 4800 y 28 800 sesiones por grupo, mientras que una base de datos de un grupo Básico tiene un límite de 300 sesiones.

## <a name="choosing-a-service-tier"></a>Selección de un nivel de servicio

Para decidir el nivel de servicio, empiece por determinar si la base de datos será independiente o formará parte de un grupo elástico. 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>Selección de un nivel de servicio para una base de datos independiente

Para decidir el nivel de servicio para una base de datos independiente, empiece por determinar las características de la base de datos que necesita para elegir la edición de SQL Database:

- Tamaño de la base de datos (2 GB como máximo en el nivel Básico, 250 GB como máximo para el nivel Estándar y de 500 GB a 1 TB como máximo para el Premium, según el nivel de rendimiento)
- Período de retención de la copia de seguridad de la base de datos (7 días para el nivel Básico, 35 días para el Estándar y 35 días para el Premium)

Cuando haya determinado la edición de SQL Database, estará listo para determinar el nivel de rendimiento de la base de datos (el número de DTU). Este valor se puede estimar y, después, [escalar o reducir verticalmente de manera dinámica](sql-database-scale-up.md) en función de su experiencia real. También puede utilizar la [calculadora de DTU](http://dtucalculator.azurewebsites.net/) para hacer una estimación del número aproximado de DTU necesario. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool."></a>Selección de un nivel de servicio para un grupo de bases de datos elásticas

Para decidir el nivel de servicio para un grupo de bases de datos elásticas, empiece por determinar las características de las bases de datos necesarias.

- Tamaño de la base de datos (2 GB para el nivel Básico, 250 GB para el Estándar y 500 GB para el Premium)
- Período de retención de la copia de seguridad de la base de datos (7 días para el nivel Básico, 35 días para el Estándar y 35 días para el Premium)
- Número de bases de datos por grupo (400 en el nivel Básico, 400 en el Estándar y 50 en el Premium)
- Capacidad máxima de almacenamiento por grupo (117 GB en el nivel Básico, 1200 en el Estándar y 750 en el Premium)

Una vez determinado el nivel de servicio para el grupo, estará preparado para determinar su nivel de rendimiento (eDTU). Este valor se puede estimar y, después, [escalar o reducir verticalmente de manera dinámica](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) en función de su experiencia real. También puede utilizar la [calculadora de DTU](http://dtucalculator.azurewebsites.net/) para hacer una estimación del número aproximado de DTU necesario para cada base de datos dentro de un grupo, lo que le ayudará a establecer su límite superior.

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre los precios de estos niveles, consulte [Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Aprenda los detalles sobre los [grupos elásticos](sql-database-elastic-pool-guidance.md) y las [consideraciones de precio y rendimiento para estos](sql-database-elastic-pool-guidance.md).
- Aprenda cómo [supervisar, administrar y cambiar el tamaño de los grupos elásticos](sql-database-elastic-pool-manage-portal.md) y [supervisar el rendimiento de bases de datos independientes](sql-database-single-database-monitor.md).
- Ahora que conoce los niveles de SQL Database, pruébelos con una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) y aprenda a [crear su primera instancia de SQL Database](sql-database-get-started.md).

## <a name="additional-resources"></a>Recursos adicionales

- [Modelos de diseño para las aplicaciones SaaS multiinquilino mediante Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
- [Curso de vídeo de la Academia virtual de Microsoft sobre las funcionalidades de las bases de datos elásticas en Azure SQL Database](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)



<!--HONumber=Oct16_HO2-->


