<properties
	pageTitle="¿Qué es un grupo de bases de datos elásticas de Azure? | Microsoft Azure"
	description="Administre cientos o miles de bases de datos mediante un grupo. Un precio para un conjunto de unidades de rendimiento se puede distribuir por el grupo. Agregue o quite bases de datos a voluntad."
	keywords="base de datos elástica, bases de datos SQL"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/12/2016"
	ms.author="CarlRabeler"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# ¿Qué es un grupo de bases de datos elásticas de Azure?

Los grupos elásticos proporcionan una solución sencilla y rentable para administrar los objetivos de rendimiento de varias bases de datos que tienen patrones de uso muy diferentes e imprevisibles.

> [AZURE.NOTE] Los grupos elásticos están disponibles con carácter general (GA) en todas las regiones de Azure excepto centro-norte de EE. UU. y oeste de la India, en donde actualmente se encuentran en versión preliminar. La disponibilidad general de los grupos elásticos en estas regiones se proporcionarán tan pronto como sea posible. Además, los grupos elásticos no admiten actualmente las bases de datos mediante [OLTP en memoria o análisis en memoria](sql-database-in-memory.md).

## Cómo funciona

Un modelo de aplicación SaaS común es el modelo de base de datos de inquilino único: cada cliente tiene su propia base de datos. Cada cliente (base de datos) tiene requisitos impredecibles de recursos de memoria, E/S y CPU. Con estos valores máximos y mínimos de demanda, ¿cómo se pueden asignar recursos de manera eficaz y rentable? Normalmente había dos opciones: (1) aprovisionar en exceso los recursos según el uso máximo y pagar de más o (2) aprovisionar de forma predeterminada para ahorrar costos, en detrimento del rendimiento y la satisfacción del cliente durante las horas de actividad máxima. Los grupos de bases de datos elásticas solucionan este problema asegurándose de que las bases de datos obtengan precisamente los recursos de rendimiento que necesitan y en el momento en que los necesitan. Proporcionan un mecanismo de asignación de recursos simples dentro de un presupuesto predecible. Para más información sobre los patrones de diseño de las aplicaciones SaaS que usan grupos elásticos, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Diseño de patrones para aplicaciones SaaS multiempresa con Base de datos SQL de Azure).

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

En Base de datos SQL, la medida relativa de la capacidad de una base de datos para administrar demandas de recursos se expresa en unidades de transacción de base de datos (DTU) para bases de datos únicas y DTU elásticas (eDTU) para grupos de bases de datos elásticas. Consulte [¿Qué es Base de datos SQL? Introducción a Base de datos SQL](sql-database-technical-overview.md#understand-dtus) para más información sobre las DTU y las eDTU.

A un grupo se le asigna un número fijo de eDTU, por un precio fijo. Dentro del grupo, a las bases de datos individuales se les proporciona la flexibilidad de escalarse automáticamente dentro de unos parámetros establecidos. Con cargas elevadas, una base de datos puede consumir más eDTU para satisfacer la demanda. Las bases de datos con cargas ligeras consumen menos y las bases de datos sin carga no consumen ninguna eDTU. El aprovisionamiento de recursos para el grupo entero en lugar de para bases de datos únicas simplifica las tareas de administración. Además, cuenta con un presupuesto predecible para el grupo.

Se pueden agregar eDTU adicionales a un grupo existente sin que la base de datos experimente tiempo de inactividad o su rendimiento se vea afectado en el grupo elástico. De manera similar, si ya no se necesitan eDTU adicionales, se pueden quitar de un grupo existente en cualquier momento dado.

Y puede agregar bases de datos al grupo o quitar bases de datos del grupo. Si una base de datos infrautiliza recursos de forma predecible, sáquela del grupo.

## ¿Qué bases de datos se incluyen en un grupo?

![Bases de datos SQL que comparten elementos eDTU en un grupo de bases de datos elásticas.][1]

Las bases de datos que son buenos candidatos para grupos de bases de datos elásticas tienen normalmente períodos de actividad y otros períodos de inactividad. En el ejemplo anterior, puede ver la actividad de una base de datos única, cuatro bases de datos y, por último, un grupo de bases de datos elásticas con 20 bases de datos. Las bases de datos con actividad variable con el tiempo son excelentes candidatos para grupos elásticos porque no están activas al mismo tiempo y pueden compartir eDTU. No todas las bases de datos se ajustan a este patrón. Las bases de datos que tienen una demanda de recursos más constante son las más idóneas para los niveles de servicio Basic, Standard y Premium, donde los recursos se asignan por separado.

[Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md).


> [AZURE.NOTE] Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12.

## Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas y bases de datos elásticas

[AZURE.INCLUDE [Tabla de niveles de servicio de Base de datos SQL para bases de datos elásticas](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Si se usan todas las unidades DTU de un grupo elástico, cada una de las bases de datos del grupo recibe una misma cantidad de recursos para procesar consultas. El servicio Base de datos SQL proporciona ecuanimidad de uso compartido de recursos entre bases de datos garantizando los mismos segmentos de tiempo de proceso. La ecuanimidad de uso compartido de recursos del grupo elástico es adicional a cualquier cantidad de recursos garantizados de otro modo a cada base de datos cuando el número mínimo de DTU por base de datos se establece en un valor distinto de cero.

## Propiedades del grupo de bases de datos elásticas

### Límites de grupos elásticos

| Propiedad | Description |
| :-- | :-- |
| Nivel de servicio | Basic, Standard o Premium. El nivel de servicio determina el intervalo en los límites de almacenamiento y rendimiento que se pueden configurar, así como las opciones de continuidad del negocio disponibles. Cada base de datos dentro de un grupo tiene el mismo nivel de servicio que el grupo. "Nivel de servicio" también se conoce como "edición". |
| eDTU por grupo | Número máximo de eDTU que pueden compartir las bases de datos del grupo. El número total de eDTU que usan las bases de datos del grupo no puede superar este límite en el mismo punto en el tiempo. |
| Almacenamiento máximo por grupo (GB) | Cantidad máxima de almacenamiento en GB que pueden compartir las bases de datos del grupo. El almacenamiento total que usan las bases de datos del grupo no puede superar este límite. Este límite viene determinado por las eDTU por grupo. Si este límite se rebasa, todas las bases de datos pasarán a ser de solo lectura. |
| Cantidad máxima de bases de datos por grupo | Cantidad máxima de bases de datos permitidas por grupo. |
| Cantidad máxima de trabajos simultáneos por grupo | Cantidad máxima de trabajos simultáneos (solicitudes) disponibles de todas las bases de datos del grupo. |
| Cantidad máxima de inicios de sesión simultáneos por grupo | Cantidad máxima de inicios de sesión simultáneos de todas las bases de datos del grupo. |
| Cantidad máxima de sesiones simultáneas por grupo | Cantidad máxima de sesiones disponibles de todas las bases de datos del grupo. |


### Límites de bases de datos elásticas

| Propiedad | Description |
| :-- | :-- |
| Cantidad máxima de eDTU por base de datos | Cantidad máxima de eDTU que puede utilizar cualquier base de datos del grupo, si está disponible según el uso que hacen otras bases de datos del grupo. La cantidad máxima de eDTU por base de datos no garantiza la disponibilidad de recursos. Se trata de una configuración global que se aplica a todas las bases de datos del grupo. Establezca una cantidad máxima de eDTU por base de datos lo suficientemente alta como para gestionar los picos de uso de la base de datos. Se admite cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de uso no tienen lugar en todas las bases de datos a la vez. Por ejemplo, supongamos que el pico de uso de cada base de datos es 20 eDTU y solo tiene lugar en el 20 % de las 100 bases de datos del grupo a la vez. Si la cantidad máxima de eDTU por base de datos se establece en 20, puede asignar al grupo una cantidad 5 veces mayor y establecer las eDTU por grupo en 400. |
| Cantidad mínima de eDTU por base de datos | Cantidad mínima de eDTU que se garantiza en cualquier base de datos del grupo. Se trata de una configuración global que se aplica a todas las bases de datos del grupo. La cantidad mínima de eDTU por base de datos se puede establecer en 0, y también se trata del valor predeterminado. Esta propiedad se establece en cualquier valor entre 0 y el uso medio de eDTU por base de datos. El resultado de multiplicar la cantidad de bases de datos del grupo y la cantidad mínima de eDTU por base de datos no puede superar el número de eDTU por grupo. Por ejemplo, si un grupo tiene 20 bases de datos y la cantidad mínima de eDTU por base de datos establecida es 10, el número de eDTU por grupo debe ser de, al menos, 200. |
| Almacenamiento máximo por base de datos (GB) | Almacenamiento máximo de una base de datos de un grupo. Las bases de datos elásticas comparten almacenamiento de grupo, por lo que el almacenamiento de base de datos se limita a la menor cantidad de almacenamiento de grupos restante y al almacenamiento máximo por base de datos.|


## Trabajos de base de datos elástica

Con un grupo, las tareas de administración se simplifican al ejecutarse los scripts en **[trabajos elásticos](sql-database-elastic-jobs-overview.md)**. Un trabajo de base de datos elástica elimina la mayoría de las tediosas tareas asociadas con un gran número de bases de datos. Para comenzar, consulte [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md).

Para más información sobre otras herramientas, consulte [Elastic database tools learning map](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) (Mapa de aprendizaje de herramientas de bases de datos elásticas).

## Características de continuidad del negocio para bases de datos de un grupo

Las bases de datos elásticas suelen ser compatibles con las mismas [características de continuidad del negocio](sql-database-business-continuity.md) que encontrará en las bases de datos únicas de los servidores V12.


### Restauración a un momento dado

La característica de restauración a un momento dado utiliza copias de seguridad automáticas de bases de datos para restaurar una base de datos de un grupo a un momento específico. Consulte [Restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore).

### Restauración geográfica

La restauración geográfica proporciona la opción de recuperación predeterminada cuando una base de datos no está disponible debido a una incidencia en la región en la que se hospeda la base de datos. Consulte [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](sql-database-disaster-recovery.md).

### Replicación geográfica activa

Para las aplicaciones que tienen requisitos de recuperación más exigentes que lo que puede ofrecer la restauración geográfica, configure la replicación geográfica activa mediante el [Portal de Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) o [Transact-SQL](sql-database-geo-replication-transact-sql.md).


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0817_2016-->