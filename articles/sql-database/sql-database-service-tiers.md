<properties
	pageTitle="Rendimiento de la Base de datos SQL y opciones: niveles de servicio | Microsoft Azure"
	description="Compare las características de rendimiento y de continuidad del negocio de Base de datos SQL de los niveles de servicio para encontrar el equilibrio adecuado entre costo y funcionalidad a medida que escalan."
	keywords="opciones de base de datos, rendimiento de la base de datos"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="07/05/2016"
	ms.author="carlrab"/>

# Opciones y rendimiento de Base de datos SQL: comprender lo que está disponible en cada nivel de servicio

[Base de datos SQL de Azure](sql-database-technical-overview.md) tiene varios niveles de servicio para administrar las diferentes cargas de trabajo. Puede [cambiar los niveles de servicio](sql-database-scale-up.md) en cualquier momento con un tiempo de inactividad mínimo para la aplicación (normalmente una media de menos de 4 segundos). También, puede [crear una base de datos única](sql-database-get-started.md) con características y precios definidos. o bien puede administrar varias bases de datos mediante la [creación de un grupo de bases de datos elásticas](sql-database-elastic-pool-create-portal.md). En ambos casos, los niveles son: **Basic**, **Standard** y **Premium**. Las opciones de base de datos en estos niveles son similares para bases de datos únicas y para grupos elásticos, pero hay consideraciones adicionales para los grupos elásticos. Este artículo le proporciona información detallada de los niveles de servicio para bases de datos únicas y bases de datos elásticas.

## Niveles de servicio y opciones de la base de datos
Los niveles de servicio Basic, Standard y Premium tienen un SLAde tiempo de actividad del 99,99% y ofrecen un rendimiento predecible, opciones de continuidad del negocio flexibles, características de seguridad y facturación por hora. La tabla siguiente proporciona ejemplos de los niveles más adecuados para las cargas de trabajo de las diferentes aplicaciones.

| Nivel de servicio | Cargas de trabajo de destino |
|---|---|
| **Básica** | Ideal para bases de datos pequeñas, que suelen admitir una sola operación activa en un momento dado. Entre los ejemplos se incluyen las bases de datos utilizadas para desarrollo, pruebas o aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| **Standard** | La opción predilecta para la mayoría de aplicaciones en la nube, admite varias consultas simultáneas. Entre los ejemplos se incluyen aplicaciones web o para grupos de trabajo. |
| **Premium** | Diseñado para altos volúmenes de transacciones, admite un gran número de usuarios simultáneos y requiere el máximo nivel de funcionalidades de continuidad empresarial. Entre los ejemplos se incluyen bases de datos que admiten aplicaciones críticas. |

>[AZURE.NOTE] Se han retirado las versiones Web y Business Edition Si quiere seguir utilizando las versiones Web y Business Edition, lea las [P+F de Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/).

## Niveles de servicio de la Base de datos única y niveles de rendimiento
Para bases de datos únicas, hay varios niveles de rendimiento dentro de cada nivel de servicio. Tiene la flexibilidad de elegir el nivel que mejor se adapte a las exigencias de la carga de trabajo. Si necesita escalar o reducir verticalmente, puede cambiar fácilmente los niveles de la base de datos. Para obtener información más detallada, consulte [Modificación de niveles de servicio y de rendimiento de la base de datos](sql-database-scale-up.md).

Las características de rendimiento que se enumeran aquí se aplican a las bases de datos creadas con la [Base de datos SQL V12](sql-database-v12-whats-new.md). En aquellas situaciones donde el hardware subyacente de Azure hospeda varias bases de datos, la base de datos seguirá recibiendo un conjunto garantizado de recursos y las características de rendimiento esperado de la base de datos no se ven afectadas.

[AZURE.INCLUDE [Tabla de niveles de servicio de datos de la Base de datos SQL](../../includes/sql-database-service-tiers-table.md)]

Para comprender mejor las DTU, consulte la [sección DTU](#understanding-dtus) de este tema.

>[AZURE.NOTE] Para obtener una explicación detallada de todas las demás filas de esta tabla de niveles de servicio, consulte [Límites y capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## Niveles de servicio del grupo elástico y rendimiento en las eDTU
Además de crear y escalar una base de datos única, también es posible administrar varias bases de datos dentro de un [grupo elástico](sql-database-elastic-pool.md). Todas las bases de datos de un grupo elástico comparten un conjunto común de recursos. Las características de rendimiento se miden por *Unidades de transmisión de bases de datos elásticas* (eDTU). Al igual que con las bases de datos únicas, los grupos tienen tres niveles de servicio: **Básico**, **Estándar** y **Premium**. En el caso de los grupos, estos tres niveles también definen los límites de rendimiento global y varias características.

Los grupos permiten que estas bases de datos compartan y consuman recursos de DTU sin que sea preciso de asignar un nivel de rendimiento específico a las bases de datos del grupo. Por ejemplo, una base de datos única de un grupo Estándar puede usar desde 0 eDTU al número máximo de eDTU de base de datos que estableció al configurar el grupo. Esto permite que varias bases de datos con diferentes cargas de trabajo usen de forma eficaz los recursos de eDTU disponibles para todo el grupo. Consulte el artículo sobre [consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md) para más información.

En la tabla siguiente se describen las características de los niveles de servicio del grupo.

[AZURE.INCLUDE [Tabla de niveles de servicio de Base de datos SQL para bases de datos elásticas](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Todas las bases de datos de un grupo también se ajustan a las características de base de datos única de ese nivel. Por ejemplo, en el grupo Básico, el límite máximo de sesiones por grupo varía entre 4800 y 28 800, mientras que una base de datos específica de ese grupo tiene un límite de 300 sesiones (el límite de una base de datos única de tipo Básico, tal y como se indicó en la sección anterior).

## Descripción de las DTU

[AZURE.INCLUDE [Descripción de DTU de base de datos SQL](../../includes/sql-database-understanding-dtus.md)]

## Pasos siguientes
- Para obtener más información sobre los precios de estos niveles, consulte [Precios de Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Conozca los detalles sobre los [grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md) y las [consideraciones de precio y rendimiento para grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md).
- Aprenda cómo [supervisar, administrar y cambiar el tamaño de los grupos de bases de datos elásticas](sql-database-elastic-pool-manage-portal.md) y [supervisar el rendimiento de bases de datos únicas](sql-database-single-database-monitor.md).
- Ahora que conoce los niveles de Base de datos SQL, pruébelos con una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) y aprenda a [crear su primera base de datos SQL](sql-database-get-started.md).

## Recursos adicionales

Para más información sobre los patrones comunes de la arquitectura de datos de aplicaciones de base de datos de software como servicio (SaaS) multiinquilino, consulte [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!----HONumber=AcomDC_0706_2016-->