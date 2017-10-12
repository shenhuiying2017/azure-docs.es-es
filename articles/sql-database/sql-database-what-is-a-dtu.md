---
title: "SQL Database: ¿Qué es una DTU? | Microsoft Docs"
description: "Comprender lo que es una unidad de transacción de Azure SQL Database."
keywords: opciones de base de datos, rendimiento de la base de datos
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 04/14/2017
ms.author: carlrab
ms.openlocfilehash: da3399b9c6642435dc7b40ed1c843217c984d15e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Unidades de transacción de bases de datos (DTU) y unidades de transacción de bases de datos elásticas (eDTU)
Este artículo describe las unidades de transacción de bases de datos (DTU) y las unidades de transacción de bases de datos elásticas (eDTU) y qué sucede cuando se alcanza el número máximo de DTU o eDTU.  

## <a name="what-are-database-transaction-units-dtus"></a>¿Qué son las unidades de transacción de base de datos?
Para una sola base de datos de Azure SQL en un nivel de rendimiento específico dentro de un [nivel de servicio](sql-database-single-database-resources.md), Microsoft garantiza un cierto nivel de recursos para esa base de datos (independiente de cualquier otra base de datos en la nube de Azure) y asegura ofrecer un nivel de rendimiento predecible. Esta cantidad de recursos se calcula como un número de unidades de transacción de base de datos o DTU y es una medida combinada de CPU, memoria y E/S (E/S de datos y de registro de transacciones). La relación entre estos recursos se determinó originalmente mediante una [carga de trabajo OLTP de prueba comparativa](sql-database-benchmark-overview.md) diseñada para representar las típicas cargas de trabajo OLTP reales. Cuando la carga de trabajo supera la cantidad de cualquiera de estos recursos, se limita el rendimiento, con lo que se obtiene un rendimiento y tiempos de espera más lentos. Los recursos utilizados por la carga de trabajo no afectan a los recursos disponibles para otras bases de datos SQL en la nube de Azure, y los recursos utilizados por otras cargas de trabajo no afectan a los recursos disponibles para la base de datos SQL.

![rectángulo de selección](./media/sql-database-what-is-a-dtu/bounding-box.png)

Las DTU son especialmente útiles para comprender la cantidad relativa de recursos entre bases de datos de Azure SQL Database en diferentes niveles de rendimiento y niveles de servicio. Por ejemplo, duplicar el número de DTU aumentando el nivel de rendimiento de una base de datos equivale a duplicar el conjunto de recursos disponibles para esa base de datos. Por ejemplo, una base de datos Premium P11 con 1750 DTU proporciona una potencia de proceso de DTU 350 veces mayor que una base de datos básica con 5 DTU.  

Para obtener información más detallada sobre el consumo de recursos (DTU) de la carga de trabajo, use [Información de rendimiento de consultas de Azure SQL Database](sql-database-query-performance.md) para:

- Identificar las consultas principales por CPU, duración y recuento de ejecuciones, que se pueden ajustar para mejorar el rendimiento. Por ejemplo, una consulta de uso intensivo de E/S puede beneficiarse del uso de [técnicas de optimización en memoria](sql-database-in-memory.md) para hacer un mejor uso de la memoria disponible en un cierto nivel de rendimiento y de nivel de servicio.
- Profundizar en los detalles de una consulta, ver su texto e historial de uso de recursos.
- Tener acceso a recomendaciones de ajuste del rendimiento que muestran las acciones realizadas por el [SQL Database Advisor](sql-database-advisor.md).

Puede [cambiar los niveles de servicio](sql-database-service-tiers.md) en cualquier momento con un tiempo de inactividad mínimo para la aplicación (normalmente una media de menos de cuatro segundos). Para muchas empresas y aplicaciones, poder crear bases de datos y aumentar o reducir el rendimiento a petición es suficiente, especialmente si los patrones de uso son relativamente predecibles. Pero si dichos patrones son impredecibles, pueden dificultar la administración de los costos y del modelo de negocio. Para este escenario, se usa un grupo elástico con un determinado número de eDTU que se comparten entre varias bases de datos del grupo.

![Introducción a SQL Database: DTU de bases de datos únicas por nivel](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>¿Qué son las unidades de transacción de base de datos elásticas (eDTU)?
En lugar de proporcionar un conjunto exclusivo de recursos (DTU) a una SQL Database que está siempre disponible sin tener en cuenta si no necesita, puede colocar las bases de datos en un [grupo elástico](sql-database-elastic-pool.md) en un servidor de SQL Database que comparte un grupo de recursos entre esas bases de datos. Los recursos compartidos de un grupo elástico medido por unidades de transacción de bases de datos elásticas o eDTU. Los grupos elásticos proporcionan una solución sencilla y rentable para administrar los objetivos de rendimiento de varias bases de datos que tienen patrones de uso muy diferentes e imprevisibles. En un grupo elástico, puede garantizar que ninguna base de datos usa todos los recursos del grupo y, al mismo tiempo, que siempre está disponible una cantidad mínima de recursos para una base de datos de un grupo elástico. 

![Introducción a SQL Database: eDTU por servicio y nivel](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A un grupo se le asigna un número fijo de eDTU, por un precio fijo. Dentro del grupo elástico, a las bases de datos individuales se les proporciona la flexibilidad de escalarse automáticamente dentro de los límites configurados. Cuando se produce una sobrecarga, una base de datos puede consumir más eDTU para satisfacer la demanda, mientras que las bases de datos con poca carga consumen menos, hasta el punto de que las bases de datos sin carga no consumen eDTU. Mediante el aprovisionamiento de recursos para todo el grupo, en lugar de por base de datos, se simplifican las tareas de administración y el presupuesto del grupo es predecible.

Se pueden agregar eDTU adicionales a un grupo existente sin que la base de datos experimente tiempo de inactividad ni que las bases de datos del grupo resulten afectadas. De igual forma, si las eDTU adicionales dejan de necesitarse, se pueden quitar de cualquier grupo existente en cualquier momento. Puede agregar o quitar bases de datos del grupo, o limitar la cantidad de eDTU que una base de datos puede utilizar cuando está sobrecargada para reservar eDTU para otras bases de datos. Si una base de datos infrautiliza recursos de forma predecible, es posible sacarla del grupo y configurarla como una base de datos única con la cantidad predecible de recursos que requiere.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>¿Cómo se puede determinar el número de DTU necesarias para la carga de trabajo?
Si desea migrar una carga de trabajo de máquina virtual existente local o de SQL Server en Azure SQL Database, puede usar la [calculadora de DTU](http://dtucalculator.azurewebsites.net/) para hacer una estimación del número aproximado de DTU que se necesitan. Para una carga de trabajo existente de Azure SQL Database, puede usar [la información de rendimiento de consultas de SQL Database](sql-database-query-performance.md) para comprender el consumo de recursos de la base de datos (DTU) y obtener información más detallada sobre cómo optimizar la carga de trabajo. También puede utilizar la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) para obtener la información de consumo de recursos para la última hora. Como alternativa, la vista de catálogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) también se puede consultar para obtener los mismos datos para los últimos 14 días, aunque con una fidelidad inferior media de cinco minutos.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>¿Cómo se puede saber si podría beneficiarme de un grupo elástico de recursos?
Los grupos son apropiados para un amplio número de bases de datos con patrones de utilización específicos. Para una base de datos determinada, este patrón está caracterizado por una utilización media baja con picos de utilización relativamente poco frecuentes. Base de datos SQL evalúa automáticamente el historial de uso de recursos de bases de datos en un servidor de Base de datos SQL existente y recomienda la configuración de grupo apropiada en el Portal de Azure. Para más información, consulte [¿Cuándo se debe utilizar un grupo elástico?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>¿Qué ocurre cuando se alcanza el número máximo de DTU?
Los niveles de rendimiento se calibran y rigen para proporcionar los recursos necesarios para ejecutar la carga de trabajo de la base de datos hasta los límites máximos permitidos para el nivel de rendimiento o de servicio seleccionado. Si la carga de trabajo alcanza los límites en uno de los límites de CPU/datos, registro de E/S o E/S, seguirá recibiendo los recursos en el nivel máximo permitido, pero es probable que perciba un aumento de las latencias en las consultas. Alcanzar uno de estos límites no provocará errores, sino una ralentización de la carga de trabajo, a menos que la ralentización sea tan severa que las consultas empiecen a agotar el tiempo de espera. Si alcanza el límite máximo permitido de sesiones y solicitudes de usuario simultáneas (subprocesos de trabajo), se producirán errores explícitos. Consulte [Límites de recursos de Azure SQL Database]( sql-database-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) para obtener información sobre el límite de otros recursos distintos de la CPU, memoria, E/S de datos y registro de transacciones de E/S.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Nivel de servicio](sql-database-service-tiers.md) para obtener información sobre las DTU y eDTU disponibles para las bases de datos únicas y para los grupos elásticos, así como los límites de recursos distintos de la CPU, la memoria, la E/S de datos y la E/S de registro de transacciones .
* Consulte [Información de rendimiento de consultas de SQL Database](sql-database-query-performance.md) para comprender el consumo (DTU).
* Para comprender la metodología detrás de la carga de trabajo de pruebas comparativas de OLTP utilizada para determinar la combinación de DTU, consulte [Información general sobre la prueba comparativa SQL Database](sql-database-benchmark-overview.md) .
