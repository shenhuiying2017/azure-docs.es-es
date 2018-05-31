---
title: 'SQL Database: ¿Qué es una DTU? | Microsoft Docs'
description: Comprender lo que es una unidad de transacción de Azure SQL Database.
keywords: opciones de base de datos, rendimiento de la base de datos
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 3fb0d0f73f475e246580f7ea6e4aea60b069c230
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894014"
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Unidades de transacción de bases de datos (DTU) y unidades de transacción de bases de datos elásticas (eDTU)
Este artículo describe las unidades de transacción de bases de datos (DTU) y las unidades de transacción de bases de datos elásticas (eDTU) y qué sucede cuando se alcanza el número máximo de DTU o eDTU. Para obtener información de precios, consulte [Precio de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="what-are-database-transaction-units-dtus"></a>¿Qué son las unidades de transacción de base de datos?
Para una sola base de datos de Azure SQL en un nivel de rendimiento específico dentro de un [nivel de servicio](sql-database-single-database-resources.md), Microsoft garantiza un determinado nivel de recursos para esa base de datos (independiente de cualquier otra base de datos en la nube de Azure), de forma que ofrece un nivel de rendimiento predecible. La cantidad de recursos se calcula como un número de unidades de transacción de base de datos o DTU y es una medida combinada de recursos de proceso, almacenamiento y E/S. La relación entre estos recursos se determinó originalmente mediante una [carga de trabajo OLTP de prueba comparativa](sql-database-benchmark-overview.md) diseñada para representar las típicas cargas de trabajo OLTP reales. Cuando la carga de trabajo supera la cantidad de cualquiera de estos recursos, se limita el rendimiento, con lo que se obtiene un rendimiento y tiempos de espera más lentos. Los recursos usados por la carga de trabajo no afectan a los recursos disponibles para otras bases de datos SQL en la nube de Azure, y los recursos usados por otras cargas de trabajo no afectan a los recursos disponibles para la base de datos SQL.

![rectángulo de selección](./media/sql-database-what-is-a-dtu/bounding-box.png)

Las DTU son especialmente útiles para comprender la cantidad relativa de recursos entre bases de datos de Azure SQL Database en diferentes niveles de rendimiento y niveles de servicio. Por ejemplo, duplicar el número de DTU al aumentar el nivel de rendimiento de una base de datos equivale a duplicar el conjunto de recursos disponibles para esa base de datos. Por ejemplo, una base de datos Premium P11 con 1750 DTU proporciona una potencia de proceso de DTU 350 veces mayor que una base de datos básica con 5 DTU.  

Para obtener información más detallada sobre el consumo de recursos (DTU) de la carga de trabajo, use [Información de rendimiento de consultas de Azure SQL Database](sql-database-query-performance.md) para:

- Identificar las consultas principales por CPU, duración y recuento de ejecuciones, que se pueden ajustar para mejorar el rendimiento. Por ejemplo, una consulta de uso intensivo de E/S puede beneficiarse del uso de [técnicas de optimización en memoria](sql-database-in-memory.md) para hacer un mejor uso de la memoria disponible en un cierto nivel de rendimiento y de nivel de servicio.
- Profundizar en los detalles de una consulta, ver su texto e historial de uso de recursos.
- Tener acceso a recomendaciones de ajuste del rendimiento que muestran las acciones realizadas por el [SQL Database Advisor](sql-database-advisor.md).

Puede [cambiar los niveles de servicio de DTU](sql-database-service-tiers-dtu.md) en cualquier momento con un tiempo de inactividad mínimo para la aplicación (normalmente una media de menos de cuatro segundos). Para muchas empresas y aplicaciones, poder crear bases de datos y aumentar o reducir el rendimiento a petición es suficiente, especialmente si los patrones de uso son relativamente predecibles. Pero si dichos patrones son impredecibles, pueden dificultar la administración de los costos y del modelo de negocio. Para este escenario, se usa un grupo elástico con un determinado número de eDTU que se comparten entre varias bases de datos del grupo.

![Introducción a SQL Database: DTU de bases de datos únicas por nivel](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>¿Qué son las unidades de transacción de base de datos elásticas (eDTU)?
En lugar de proporcionar un conjunto exclusivo de recursos (DTU) que puede que no siempre sea necesario para una SQL Database que está siempre disponible, puede colocar las bases de datos en un [grupo elástico](sql-database-elastic-pool.md) en un servidor de SQL Database que comparte un grupo de recursos entre esas bases de datos. Los recursos compartidos de un grupo elástico se miden mediante unidades de transacción de bases de datos elásticas o eDTU. Los grupos elásticos proporcionan una solución sencilla y rentable para administrar los objetivos de rendimiento de varias bases de datos que tienen patrones de uso muy diferentes e imprevisibles. Un grupo elástico garantiza que una base de datos del grupo no puede consumir los recursos, mientras que asegura que cada base de datos del grupo tenga disponible siempre una cantidad mínima de recursos necesarios. 

![Introducción a SQL Database: eDTU por servicio y nivel](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A un grupo se le asigna un número fijo de eDTU, por un precio fijo. Dentro del grupo elástico, a las bases de datos individuales se les proporciona la flexibilidad de escalarse automáticamente dentro de los límites configurados. Una base de datos con cargas más elevadas consumirá más eDTU para satisfacer la demanda. Las bases de datos con cargas más bajas consumirán menos eDTU. Las bases de datos sin cargas no consumirán ningún eDTU. Mediante el aprovisionamiento de recursos para todo el grupo, en lugar de por base de datos, se simplifican las tareas de administración, de forma que se proporciona un presupuesto del grupo predecible.

Se pueden agregar eDTU adicionales a un grupo existente sin que la base de datos experimente tiempo de inactividad ni que las bases de datos del grupo resulten afectadas. De igual forma, si las eDTU adicionales dejan de necesitarse, se pueden quitar de cualquier grupo existente en cualquier momento. Puede agregar o quitar bases de datos del grupo, o limitar la cantidad de eDTU que puede usar una base de datos cuando está sobrecargada para reservar eDTU para otras bases de datos. Si una base de datos infrautiliza recursos de forma predecible, es posible sacarla del grupo y configurarla como una base de datos única con una cantidad predecible de recursos necesarios.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>¿Cómo se puede determinar el número de DTU necesarias para la carga de trabajo?
Si desea migrar una carga de trabajo de máquina virtual existente local o de SQL Server en Azure SQL Database, puede usar la [calculadora de DTU](http://dtucalculator.azurewebsites.net/) para hacer una estimación del número aproximado de DTU que se necesitan. Para una carga de trabajo existente de Azure SQL Database, puede usar la [información de rendimiento de consultas de SQL Database](sql-database-query-performance.md) para comprender el consumo de recursos de la base de datos (DTU) y obtener información más detallada para optimizar la carga de trabajo. También puede usar la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) para ver el consumo de recursos de la última hora. Como alternativa, la vista de catálogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) muestra el consumo de recursos de los últimos 14 días, aunque con una fidelidad inferior media de cinco minutos.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>¿Cómo se puede saber si podría beneficiarme de un grupo elástico de recursos?
Los grupos son apropiados para un amplio número de bases de datos con patrones de utilización específicos. Para una base de datos determinada, este patrón está caracterizado por una utilización media baja con picos de utilización relativamente poco frecuentes. SQL Database evalúa automáticamente el historial de uso de recursos de bases de datos en un servidor de SQL Database existente y recomienda la configuración de grupo apropiada en Azure Portal. Para más información, consulte [¿Cuándo se debe utilizar un grupo elástico?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>¿Qué ocurre cuando se alcanza el número máximo de DTU?
Los niveles de rendimiento se calibran y rigen para proporcionar los recursos necesarios para ejecutar la carga de trabajo de la base de datos hasta el máximo permitido para el nivel de rendimiento o de servicio seleccionado. Si la carga de trabajo alcanza uno de los límites de CPU, E/S de datos o E/S de registro, seguirá recibiendo el nivel máximo de recursos permitido, pero es probable que perciba latencias de consultas aumentadas. Alcanzar uno de estos límites no provocará errores, sino una ralentización de la carga de trabajo, a menos que la ralentización sea tan severa que las consultas empiecen a agotar el tiempo de espera. Si alcanza las sesiones y solicitudes de usuario simultáneas máximas permitidas (subprocesos de trabajo), verá errores explícitos. Consulte [Límites de recursos de Azure SQL Database]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) para obtener información sobre los límites de recursos no relacionados con la CPU, la memoria, la E/S de datos o la E/S del registro de transacciones.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [DTU-based purchasing model](sql-database-service-tiers-dtu.md) (Modelo de compra basado en DTU) para obtener información sobre las DTU y eDTU disponibles para las bases de datos únicas y para los grupos elásticos, así como los límites de recursos distintos de la CPU, la memoria, la E/S de datos y la E/S de registro de transacciones.
* Consulte [vCore-based purchasing model (preview)](sql-database-service-tiers-vcore.md) [Modelo de compra basado en núcleos virtuales (versión preliminar)] para obtener información sobre la asignación de recursos basados en núcleos virtuales y los niveles de servicio. 
* Consulte [Información de rendimiento de consultas de SQL Database](sql-database-query-performance.md) para comprender el consumo (DTU).
* Para comprender la metodología detrás de la carga de trabajo de pruebas comparativas de OLTP utilizada para determinar la combinación de DTU, consulte [Información general sobre la prueba comparativa SQL Database](sql-database-benchmark-overview.md) .
