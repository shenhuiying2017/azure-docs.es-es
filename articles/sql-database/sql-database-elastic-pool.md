---
title: "¿Qué son los grupos elásticos? Administración de varias instancias de SQL Database: Azure | Microsoft Docs"
description: "Administración y escalado de muchas instancias de SQL Database, cientos y miles, mediante los grupos elásticos. Un precio para los recursos que se puede distribuir cuando sea necesario."
keywords: varias bases de datos, recursos de base de datos, rendimiento de la base de datos
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 05/15/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 448bdbd2ae12d325781587846c812995cae33774
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>Los grupos elásticos pueden ayudarle a administrar y escalar varias instancias de SQL Database

Los grupos elásticos de SQL Database son una solución simple y rentable para la administración y escalado de varias bases de datos que tienen distintas e imprevisibles demandas de uso. Las bases de datos de un grupo elástico se encuentran en un único servidor de Azure SQL Database y comparten un número establecido de recursos [[unidades de transacción de bases de datos elásticas](sql-database-what-is-a-dtu.md) (eDTUs)] a un precio establecido. Los grupos elásticos en Base de datos SQL de Azure permiten a los desarrolladores de SaaS optimizar el rendimiento del precio para un grupo de bases de datos dentro de un presupuesto prescrito a la vez que se ofrece elasticidad de rendimiento para cada base de datos.   

> [!NOTE]
> Los grupos elásticos están disponibles con carácter general (GA) en todas las regiones de Azure excepto oeste de la India, donde actualmente se encuentran en versión preliminar.  La disponibilidad general de grupos elásticos en esta región se producirá tan pronto como sea posible.
>

## <a name="overview-of-elastic-pools"></a>Introducción a los grupos elásticos 

Los desarrolladores de SaaS crean aplicaciones en los niveles superiores de datos de la escala que constan de varias bases de datos. Un patrón de aplicación común es aprovisionar una base de datos única para cada cliente. Sin embargo, cada cliente suele tener patrones de uso variables e impredecibles y resulta difícil predecir los requisitos de recursos de cada usuario de bases de datos individuales. Tradicionalmente, había dos opciones: 

- Aprovisionar recursos en exceso basándose en el uso máximo y pagando de más, o
- Aprovisionar por debajo de lo necesario para ahorrar costos a expensas del rendimiento y satisfacción del cliente durante las horas de máxima actividad. 

Los grupos elásticos solucionan este problema y se aseguran de que las bases de datos obtengan los recursos de rendimiento que necesitan y en el momento en que los necesitan. Proporcionan un mecanismo de asignación de recursos simples dentro de un presupuesto predecible. Para más información sobre los patrones de diseño de las aplicaciones SaaS que usan grupos elásticos, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Diseño de patrones para aplicaciones SaaS multiempresa con Base de datos SQL de Azure).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Los grupos elásticos permiten al desarrollador adquirir [unidades de transacción de base de datos elástica](sql-database-what-is-a-dtu.md) (eDTU) para un grupo compartido entre varias bases de datos con el fin de dar cabida a periodos impredecibles de uso por bases de datos individuales. El requisito de eDTU para un grupo se determina mediante el uso agregado de sus bases de datos. El número de eDTU disponibles para el grupo se controla mediante el presupuesto del desarrollador. El desarrollador simplemente agrega bases de datos al grupo, establece el número mínimo y máximo de eDTU para las bases de datos y luego establece la eDTU del grupo según el presupuesto. Un desarrollador puede usar grupos para aumentar de forma eficiente su servicio a partir de un método Lean Startup hasta un negocio con madurez a una escala cada vez mayor.

Dentro del grupo, a las bases de datos individuales se les proporciona la flexibilidad de escalarse automáticamente dentro de unos parámetros establecidos. Con cargas elevadas, una base de datos puede consumir más eDTU para satisfacer la demanda. Las bases de datos con cargas ligeras consumen menos y las bases de datos sin carga no consumen ninguna eDTU. El aprovisionamiento de recursos para el grupo entero en lugar de para bases de datos únicas simplifica las tareas de administración. Además, cuenta con un presupuesto predecible para el grupo. Se pueden agregar eDTU adicionales a un grupo existente sin que haya un tiempo de inactividad de la base de datos, excepto en los casos en los que las bases de datos se deben mover para proporcionar los recursos de proceso adicionales para la reserva de nuevas eDTU. De manera similar, si ya no se necesitan eDTU adicionales, se pueden quitar de un grupo existente en cualquier momento dado. Y puede agregar bases de datos al grupo o quitar bases de datos del grupo. Si una base de datos infrautiliza recursos de forma predecible, sáquela del grupo.

Puede crear y administrar un grupo elástico con [Azure Portal](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) y la API de REST. 

## <a name="when-to-consider-a-pool"></a>Cuándo considerar un grupo
Los grupos son apropiados para un amplio número de bases de datos con patrones de utilización específicos. Para una base de datos determinada, este patrón está caracterizado por una utilización media baja con picos de utilización relativamente poco frecuentes.

Cuantas más bases de datos pueda agregar a un grupo, mayores ahorros habrá. Según su patrón de uso de la aplicación, es posible ver los ahorros con tan solo dos bases de datos S3.  

En las secciones siguientes, obtendrá ayuda para evaluar si resulta beneficioso que la colección específica de bases de datos se incluya en un grupo. Los ejemplos usan grupos Estándar, pero también se aplican los mismos principios a grupos Básico y Premium.

### <a name="assessing-database-utilization-patterns"></a>Evaluación de los patrones de utilización de base de datos
La siguiente ilustración muestra un ejemplo de una base de datos que está mucho tiempo inactiva, pero que también tiene picos periódicos de actividad. Se trata de un patrón de uso que es apropiado para un grupo:

   ![una base de datos única adecuada para un grupo](./media/sql-database-elastic-pool/one-database.png)

En el período de cinco minutos de la ilustración, DB1 llega a las 90 DTU, pero su uso medio es inferior a cinco DTU. Se requiere un nivel de rendimiento S3 para ejecutar esta carga de trabajo en una base de datos única, pero esto deja a la mayoría de los recursos sin usar durante los períodos de baja actividad.

Con un grupo, estas DTU sin usar pueden compartirse entre varias bases de datos, lo que permite reducir el número total de DTU necesarias y el costo general.

Según el ejemplo anterior, suponga que existen bases de datos adicionales con patrones de utilización similares como DB1. En las siguientes dos figuras, la utilización de cuatro bases de datos y 20 bases de datos se estratifican en el mismo gráfico para mostrar la naturaleza de no solapamiento de su utilización con el paso del tiempo:

   ![cuatro bases de datos con un patrón de uso adecuado para un grupo](./media/sql-database-elastic-pool/four-databases.png)

  ![veinte bases de datos con un patrón de uso adecuado para un grupo](./media/sql-database-elastic-pool/twenty-databases.png)

En la ilustración anterior, el uso total de DTU en las 20 bases de datos está representado por la línea negra. Esto muestra que la utilización de DTU agregada nunca supera las 100 DTU e indica que las 20 bases de datos pueden compartir 100 eDTU en este período. El resultado es una reducción multiplicada por 20 en las DTU y una reducción del precio 13 veces menor en comparación con la colocación de cada base de datos en los niveles de rendimiento S3 para bases de datos únicas.

Este ejemplo es ideal por las siguientes razones:

* Existen grandes diferencias entre la utilización de picos y la utilización media por base de datos.  
* La utilización de picos para cada base de dato se produce en puntos de tiempo distintos.
* Las eDTU se comparten entre varias bases de datos.

El precio de un grupo es una función de las eDTU del grupo. Aunque el precio unitario de una eDTU para un grupo es 1,5 veces mayor que el de una DTU para una base de datos única, **las eDTU de grupo pueden compartirse entre muchas bases de datos, por lo que el número total de eDTU que se necesitan es menor**. Estas distinciones de precio y uso compartido de la eDTU son la base de la posibilidad de ahorro en el precio que pueden proporcionar los grupos.  

Las siguientes reglas generales relacionadas con el recuento de base de datos y la utilización de base de datos ayudan a garantizar que un grupo proporciona costes reducidos en comparación con el uso de niveles de rendimiento para bases de datos únicas.


### <a name="minimum-number-of-databases"></a>Número mínimo de bases de datos
Si la suma de las DTU de los niveles de rendimiento de las bases de datos únicas supera en más de 1,5 al de las eDTU necesarias para el grupo, es más rentable usar un grupo elástico. Para tamaños disponibles, consulte [Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas y bases de datos elásticas](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

***Ejemplo***<br>
: al menos dos bases de datos S3 o 15 bases de datos S0 son necesarias para que un grupo de 100 eDTU sea más rentable que usar niveles de rendimiento para bases de datos únicas.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de bases de datos de picos simultáneamente
Cuando se comparten eDTU, no todas las bases de datos de un grupo pueden usar simultáneamente las eDTU hasta el límite disponible al usar niveles de rendimiento para bases de datos únicas. Cuantas menos bases de datos con un pico simultáneo haya, más bajo puede establecerse el número de eDTU de grupo y más rentable resultará el grupo. En general, no más de los 2/3 (o el 67%) de las bases de datos del grupo deben alcanzar el límite de eDTU establecido como pico de forma simultánea.

***Ejemplo***<br>
: para reducir los costos de tres bases de datos S3 de un grupo de 200 eDTU, como mucho dos de estas bases de datos pueden alcanzar simultáneamente el pico de uso máximo. De lo contrario, si más de dos de estas cuatro bases de datos S3 establecen simultáneamente el pico, tendría que establecerse un tamaño del grupo en más de 200 eDTU. Si el tamaño del grupo se cambia a más de 200 eDTU, será necesario agregar más bases de datos S3 al grupo para que los costos sigan siendo inferiores a los niveles de rendimiento de las bases de datos únicas.

Tenga en cuenta que este ejemplo no tiene en cuenta la utilización de otras bases de datos en el grupo. Si en un momento determinado se están usando todas las bases de datos, menos de los 2/3 (o el 67%) de las bases de datos podrán alcanzar simultáneamente el pico de uso.

### <a name="dtu-utilization-per-database"></a>Utilización de DTU por base de datos
Una gran diferencia entre el pico y la utilización media de una base de datos indica largos períodos de poca utilización y breves períodos de uso intenso. Este patrón de uso es ideal para compartir recursos entre bases de datos. Debe considerarse utilizar una base de datos para un grupo cuando su uso máximo es aproximadamente 1,5 veces mayor que su uso medio.

***Ejemplo***<br>
: una base de datos S3 que establece un pico en 100 DTU y de media usa 67 DTU o menos es una buena candidata para compartir DTU en un grupo. O bien, una base de datos S1 con un pico de hasta 20 DTU y que de media usa 13 DTU o menos es una buena candidata para un grupo.

## <a name="sizing-an-elastic-pool"></a>Cambio del tamaño de un grupo elástico
El mejor tamaño para un grupo depende de las eDTU agregadas y los recursos de almacenamiento necesarios para todas las bases de datos del grupo. Esto implica determinar la cantidad mayor de lo siguiente:

* Número máximo de DTU utilizado por todas las bases de datos en el grupo.
* Número máximo de bytes de almacenamiento utilizado por todas las bases de datos en el grupo.

Para tamaños disponibles, consulte [Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas y bases de datos elásticas](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

Base de datos SQL evalúa automáticamente el historial de uso de recursos de bases de datos en un servidor de Base de datos SQL existente y recomienda la configuración de grupo apropiada en el Portal de Azure. Además de las recomendaciones, una experiencia integrada calcula el uso de eDTU para un grupo personalizado de bases de datos en el servidor. Esto le permite realizar un análisis de hipótesis agregando bases de datos interactivamente al grupo y quitándolas para obtener análisis de uso de recursos y consejos de cambio de tamaño antes de confirmar los cambios. Para ver un procedimiento, consulte [Supervisión y administración de un grupo de bases de datos elásticas con el Portal de Azure](sql-database-elastic-pool-manage-portal.md).

En casos donde no se pueden usar herramientas, las siguientes instrucciones paso a paso pueden ayudarle a estimar si un grupo es más rentable que las bases de datos únicas:

1. Calcule las eDTU necesarias para el grupo de la siguiente forma:

   MAX(<*Número total de bases de datos* X *promedio de uso de DTU por base de datos*>,<br>
   <*Número de bases de datos con picos simultáneos* X *Uso de picos de DTU por base de datos*)
2. Calcule el espacio de almacenamiento necesario para el grupo agregando el número de bytes necesarios para todas las bases de datos del grupo. A continuación, determine el tamaño del grupo de eDTU que proporciona esta cantidad de almacenamiento. Para conocer los límites de almacenamiento de grupo basados en el tamaño de grupo de eDTU, consulte [Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas y bases de datos elásticas](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).
3. Tome el mayor de los cálculos de eDTU de los pasos 1 y 2.
4. Consulte la [página de precios de Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/) y busque el tamaño de grupo de eDTU más pequeño que sea mayor que el cálculo del paso 3.
5. Compare el precio del grupo del paso 5 con el precio de uso de los niveles de rendimiento adecuados para bases de datos únicas.

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas

En las tablas siguientes se describen los límites de recursos de los grupos elásticos.  Tenga en cuenta que los límites de recursos de las bases de datos individuales de los grupos elásticos suelen ser los mismos que para las bases de datos únicas fuera de los grupos basados en DTU y el nivel de servicio.  Por ejemplo, el número máximo de trabajadores simultáneos de una base de datos S2 es de 120 trabajadores.  Por lo tanto, el número máximo de trabajadores simultáneos de una base de datos de un grupo estándar también es de 120 trabajadores si el número máximo de DTU por base de datos del grupo es de 50 DTU (que es equivalente a S2).

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Si se usan todas las unidades DTU de un grupo elástico, cada una de las bases de datos del grupo recibe una misma cantidad de recursos para procesar consultas.  El servicio Base de datos SQL proporciona ecuanimidad de uso compartido de recursos entre bases de datos garantizando los mismos segmentos de tiempo de proceso. La ecuanimidad de uso compartido de recursos del grupo elástico es adicional a cualquier cantidad de recursos garantizados de otro modo a cada base de datos cuando el número mínimo de DTU por base de datos se establece en un valor distinto de cero.

## <a name="database-properties-for-pooled-databases"></a>Propiedades de base de datos para bases de datos agrupadas

En la tabla siguiente se describen las propiedades de las bases de datos agrupadas.

| Propiedad | Description |
|:--- |:--- |
| Cantidad máxima de eDTU por base de datos |Cantidad máxima de eDTU que puede utilizar cualquier base de datos del grupo, si está disponible según el uso que hacen otras bases de datos del grupo.  La cantidad máxima de eDTU por base de datos no garantiza la disponibilidad de recursos.  Se trata de una configuración global que se aplica a todas las bases de datos del grupo. Establezca una cantidad máxima de eDTU por base de datos lo suficientemente alta como para gestionar los picos de uso de la base de datos. Se admite cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de uso no tienen lugar en todas las bases de datos a la vez. Por ejemplo, supongamos que el pico de uso de cada base de datos es 20 eDTU y solo tiene lugar en el 20 % de las 100 bases de datos del grupo a la vez.  Si la cantidad máxima de eDTU por base de datos se establece en 20, puede asignar al grupo una cantidad 5 veces mayor y establecer las eDTU por grupo en 400. |
| Cantidad mínima de eDTU por base de datos |Cantidad mínima de eDTU que se garantiza en cualquier base de datos del grupo.  Se trata de una configuración global que se aplica a todas las bases de datos del grupo. La cantidad mínima de eDTU por base de datos se puede establecer en 0, y también se trata del valor predeterminado. Esta propiedad se establece en cualquier valor entre 0 y el uso medio de eDTU por base de datos. El resultado de multiplicar la cantidad de bases de datos del grupo y la cantidad mínima de eDTU por base de datos no puede superar el número de eDTU por grupo.  Por ejemplo, si un grupo tiene 20 bases de datos y la cantidad mínima de eDTU por base de datos establecida es 10, el número de eDTU por grupo debe ser de, al menos, 200. |
| Almacenamiento máximo de datos por base de datos |Almacenamiento máximo de una base de datos de un grupo. Las bases de datos agrupadas comparten almacenamiento de grupo, por lo que el almacenamiento de base de datos se limita a la menor cantidad de almacenamiento de grupos restante y al almacenamiento máximo por base de datos. El almacenamiento máximo por base de datos hace referencia al tamaño máximo de los archivos de datos y no incluye el espacio utilizado por los archivos de registro. |
|||

## <a name="elastic-jobs"></a>Trabajos elásticos
Con un grupo, las tareas de administración se simplifican al ejecutarse los scripts en **[trabajos elásticos](sql-database-elastic-jobs-overview.md)**. Un trabajo elástico elimina la mayoría de las tediosas tareas asociadas con un gran número de bases de datos. Para comenzar, consulte [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md).

Para más información sobre otras herramientas de bases de datos para trabajar con varias bases de datos, consulte [Escalado horizontal con Azure SQL Database](sql-database-elastic-scale-introduction.md).

## <a name="business-continuity-features-for-databases-in-a-pool"></a>Características de continuidad del negocio para bases de datos de un grupo
Las bases de datos agrupadas suelen ser compatibles con las mismas [características de continuidad empresarial](sql-database-business-continuity.md) que encontrará en las bases de datos únicas.

### <a name="point-in-time-restore"></a>Restauración a un momento dado
La característica de restauración a un momento dado utiliza copias de seguridad automáticas de bases de datos para restaurar una base de datos de un grupo a un momento específico. Consulte [Restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>Restauración geográfica
La restauración geográfica proporciona la opción de recuperación predeterminada cuando una base de datos no está disponible debido a una incidencia en la región en la que se hospeda la base de datos. Consulte [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>Replicación geográfica activa
Si se trata de aplicaciones que tienen unos requisitos de recuperación más exigentes que los que puede ofrecer la restauración geográfica, configure la [Replicación geográfica activa](sql-database-geo-replication-overview.md).

## <a name="next-steps"></a>Pasos siguientes

* Puede crear y administrar un grupo elástico con [Azure Portal](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) y la API de REST.
* Para ver un vídeo, vea el [Curso de vídeo de la Academia virtual de Microsoft sobre las funcionalidades de las bases de datos elásticas en Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554).
* Para más información sobre los patrones de diseño de las aplicaciones SaaS que usan grupos elásticos, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Diseño de patrones para aplicaciones SaaS multiempresa con Base de datos SQL de Azure).

