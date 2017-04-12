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
ms.date: 03/06/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 06a67e4d2134baf54e88708d64981b897ecb74a0
ms.lasthandoff: 04/03/2017


---

# <a name="how-elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>Cómo pueden ayudarle los grupos elásticos a administrar y escalar varias instancias de SQL Database

Los grupos elásticos de SQL Database son una solución simple y rentable para la administración y escalado de varias bases de datos que tienen distintas e imprevisibles demandas de uso. Las bases de datos de un grupo elástico se encuentran en un único servidor de Azure SQL Database y comparten un número establecido de recursos a un precio establecido.

Puede crear y administrar un grupo elástico con [Azure Portal](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) y la API de REST. Los recursos de un grupo elástico se miden en [eDTU](sql-database-what-is-a-dtu.md).


> [!NOTE]
> Los grupos elásticos están disponibles con carácter general (GA) en todas las regiones de Azure excepto oeste de la India, donde actualmente se encuentran en versión preliminar.  La disponibilidad general de grupos elásticos en esta región se producirá tan pronto como sea posible.
>
>


## <a name="how-do-elastic-pools-help-manage-database-resources"></a>¿Cómo ayudan los grupos elásticos a administrar los recursos de una base de datos?

Un modelo de aplicación SaaS común es el modelo de base de datos de inquilino único: cada cliente (base de datos) tiene su propia base de datos. Cada cliente tiene requisitos impredecibles de recursos de memoria, E/S y CPU. Con estos valores máximos y mínimos de demanda, ¿cómo se pueden asignar recursos de manera eficaz y rentable? Normalmente había dos opciones: (1) aprovisionar en exceso los recursos según el uso máximo y pagar de más o (2) aprovisionar de forma predeterminada para ahorrar costos, en detrimento del rendimiento y la satisfacción del cliente durante las horas de actividad máxima. Los grupos elásticos solucionan este problema y se aseguran de que las bases de datos obtengan los recursos de rendimiento que necesitan y en el momento en que los necesitan. Proporcionan un mecanismo de asignación de recursos simples dentro de un presupuesto predecible. Para más información sobre los patrones de diseño de las aplicaciones SaaS que usan grupos elásticos, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Diseño de patrones para aplicaciones SaaS multiempresa con Base de datos SQL de Azure).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

En SQL Database, la medida relativa de la capacidad de una base de datos para administrar demandas de recursos se expresa en unidades de transacción de base de datos (DTU) para bases de datos únicas y en unidades de transacción de bases de datos elásticas (eDTU) para bases de datos de grupos elásticos. Consulte el artículo de [introducción a SQL Database](sql-database-technical-overview.md) para obtener más información sobre las DTU y eDTU.

A un grupo elástico se le asigna un número establecido de eDTU, a un precio establecido. Dentro del grupo, a las bases de datos individuales se les proporciona la flexibilidad de escalarse automáticamente dentro de unos parámetros establecidos. Con cargas elevadas, una base de datos puede consumir más eDTU para satisfacer la demanda. Las bases de datos con cargas ligeras consumen menos y las bases de datos sin carga no consumen ninguna eDTU. El aprovisionamiento de recursos para el grupo entero en lugar de para bases de datos únicas simplifica las tareas de administración. Además, cuenta con un presupuesto predecible para el grupo.

Se pueden agregar eDTU adicionales a un grupo existente sin que haya un tiempo de inactividad de la base de datos, excepto en los casos en los que las bases de datos se deben mover para proporcionar los recursos de proceso adicionales para la reserva de nuevas eDTU. De manera similar, si ya no se necesitan eDTU adicionales, se pueden quitar de un grupo existente en cualquier momento dado.

Y puede agregar bases de datos al grupo o quitar bases de datos del grupo. Si una base de datos infrautiliza recursos de forma predecible, sáquela del grupo.

## <a name="which-databases-go-in-a-pool"></a>¿Qué bases de datos se incluyen en un grupo?
![Las bases de datos SQL que comparten eDTU en un grupo elástico.][1]

Las bases de datos que son buenos candidatos para grupos elásticos tienen normalmente períodos de actividad y otros períodos de inactividad. En el ejemplo anterior, puede ver la actividad de una base de datos única, cuatro bases de datos y, por último, un grupo elástico con 20 bases de datos. Las bases de datos con actividad variable con el tiempo son excelentes candidatos para grupos elásticos porque no están activas al mismo tiempo y pueden compartir eDTU. No todas las bases de datos se ajustan a este patrón. Las bases de datos que tienen una demanda de recursos más constante son las más idóneas para los niveles de servicio Basic, Standard y Premium, donde los recursos se asignan por separado.

[Consideraciones de precio y rendimiento para un grupo elástico](sql-database-elastic-pool-guidance.md).

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas

En la tabla siguiente se describen las características de los grupos elásticos de nivel Básico, Estándar y Premium.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Si se usan todas las unidades DTU de un grupo elástico, cada una de las bases de datos del grupo recibe una misma cantidad de recursos para procesar consultas.  El servicio Base de datos SQL proporciona ecuanimidad de uso compartido de recursos entre bases de datos garantizando los mismos segmentos de tiempo de proceso. La ecuanimidad de uso compartido de recursos del grupo elástico es adicional a cualquier cantidad de recursos garantizados de otro modo a cada base de datos cuando el número mínimo de DTU por base de datos se establece en un valor distinto de cero.

## <a name="elastic-pool-properties"></a>Propiedades de los grupos elásticos

En las tablas siguientes se describen los límites de los grupos elásticos y de las bases de datos agrupadas.

### <a name="limits-for-elastic-pools"></a>Límites de grupos elásticos
| Propiedad | Description |
|:--- |:--- |
| Nivel de servicio |Basic, Standard o Premium. El nivel de servicio determina el intervalo en los límites de almacenamiento y rendimiento que se pueden configurar, así como las opciones de continuidad del negocio disponibles. Cada base de datos dentro de un grupo tiene el mismo nivel de servicio que el grupo. "Nivel de servicio" también se conoce como "edición". |
| eDTU por grupo |Número máximo de eDTU que pueden compartir las bases de datos del grupo. El número total de eDTU que usan las bases de datos del grupo no puede superar este límite en el mismo punto en el tiempo. |
| Almacenamiento máximo por grupo (GB) |Cantidad máxima de almacenamiento en GB que pueden compartir las bases de datos del grupo. El almacenamiento total que usan las bases de datos del grupo no puede superar este límite. Este límite viene determinado por las eDTU por grupo. Si este límite se rebasa, todas las bases de datos pasarán a ser de solo lectura. El almacenamiento máximo por grupo hace referencia al almacenamiento máximo de los archivos de datos en el grupo y no incluye el espacio utilizado por los archivos de registro. |
| Cantidad máxima de bases de datos por grupo |Cantidad máxima de bases de datos permitidas por grupo. |
| Cantidad máxima de trabajos simultáneos por grupo |Cantidad máxima de trabajos simultáneos (solicitudes) disponibles de todas las bases de datos del grupo. |
| Cantidad máxima de inicios de sesión simultáneos por grupo |Cantidad máxima de inicios de sesión simultáneos de todas las bases de datos del grupo. |
| Cantidad máxima de sesiones simultáneas por grupo |Cantidad máxima de sesiones disponibles de todas las bases de datos del grupo. |
|||

### <a name="limits-for-pooled-databases"></a>Límites de las bases de datos agrupadas
| Propiedad | Description |
|:--- |:--- |
| Cantidad máxima de eDTU por base de datos |Cantidad máxima de eDTU que puede utilizar cualquier base de datos del grupo, si está disponible según el uso que hacen otras bases de datos del grupo.  La cantidad máxima de eDTU por base de datos no garantiza la disponibilidad de recursos.  Se trata de una configuración global que se aplica a todas las bases de datos del grupo. Establezca una cantidad máxima de eDTU por base de datos lo suficientemente alta como para gestionar los picos de uso de la base de datos. Se admite cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de uso no tienen lugar en todas las bases de datos a la vez. Por ejemplo, supongamos que el pico de uso de cada base de datos es 20 eDTU y solo tiene lugar en el 20 % de las 100 bases de datos del grupo a la vez.  Si la cantidad máxima de eDTU por base de datos se establece en 20, puede asignar al grupo una cantidad 5 veces mayor y establecer las eDTU por grupo en 400. |
| Cantidad mínima de eDTU por base de datos |Cantidad mínima de eDTU que se garantiza en cualquier base de datos del grupo.  Se trata de una configuración global que se aplica a todas las bases de datos del grupo. La cantidad mínima de eDTU por base de datos se puede establecer en 0, y también se trata del valor predeterminado. Esta propiedad se establece en cualquier valor entre 0 y el uso medio de eDTU por base de datos. El resultado de multiplicar la cantidad de bases de datos del grupo y la cantidad mínima de eDTU por base de datos no puede superar el número de eDTU por grupo.  Por ejemplo, si un grupo tiene 20 bases de datos y la cantidad mínima de eDTU por base de datos establecida es 10, el número de eDTU por grupo debe ser de, al menos, 200. |
| Almacenamiento máximo por base de datos (GB) |Almacenamiento máximo de una base de datos de un grupo. Las bases de datos agrupadas comparten almacenamiento de grupo, por lo que el almacenamiento de base de datos se limita a la menor cantidad de almacenamiento de grupos restante y al almacenamiento máximo por base de datos. El almacenamiento máximo por base de datos hace referencia al tamaño máximo de los archivos de datos y no incluye el espacio utilizado por los archivos de registro. |
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
Para las aplicaciones que tienen requisitos de recuperación más exigentes que lo que puede ofrecer la restauración geográfica, configure la replicación geográfica activa mediante [Azure Portal](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) o [Transact-SQL](sql-database-geo-replication-transact-sql.md).

## <a name="next-steps"></a>Pasos siguientes

* Puede crear y administrar un grupo elástico con [Azure Portal](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) y la API de REST.
* Para obtener orientación sobre cuándo usar grupos elásticos, vea la [guía sobre grupos elásticos](sql-database-elastic-pool-guidance.md).
* Para ver un vídeo, vea el [Curso de vídeo de la Academia virtual de Microsoft sobre las funcionalidades de las bases de datos elásticas en Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554).

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

