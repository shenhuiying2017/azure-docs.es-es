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
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 91942517a481a252d631af15e532389256b64108


---
# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Descripción de las unidades de transacción de bases de datos (DTU) y de las unidades de transacción de bases de datos elásticas (eDTU)
Este artículo describe las unidades de transacción de bases de datos (DTU) y las unidades de transacción de bases de datos elásticas (eDTU) y qué sucede cuando se alcanza el número máximo de DTU o eDTU.  

## <a name="what-are-database-transaction-units-dtus"></a>Qué son las unidades de transacción de base de datos
Una DTU es una unidad de medida de los recursos que se garantiza que estarán disponibles para una instancia independiente de Azure SQL Database en un nivel de rendimiento específico dentro de un [nivel de servicio de base de datos independiente](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels). Una DTU es una medida que combina CPU, memoria, datos de E/S y E/S de registro de transacciones en una relación determinada por una carga de trabajo de pruebas comparativas de OLTP diseñada para ser la normal entre las cargas de trabajo OLTP reales. Duplicar el número de DTU aumentando el nivel de rendimiento de una base de datos equivale a duplicar el conjunto de recursos disponibles para esa base de datos. Por ejemplo, una base de datos Premium P11 con 1750 DTU proporciona una potencia de proceso de DTU 350 veces mayor que una base de datos básica con 5 DTU. Para comprender la metodología detrás de la carga de trabajo de pruebas comparativas de OLTP utilizada para determinar la combinación de DTU, consulte [Información general sobre la prueba comparativa SQL Database](sql-database-benchmark-overview.md).

![Introducción a SQL Database: DTU de bases de datos únicas por nivel](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Puede [cambiar los niveles de servicio](sql-database-scale-up.md) en cualquier momento con un tiempo de inactividad mínimo para la aplicación (normalmente una media de menos de cuatro segundos). Para muchas empresas y aplicaciones, poder crear bases de datos y aumentar o reducir el rendimiento de una base de datos única a petición es suficiente, especialmente si los patrones de uso son relativamente predecibles. Pero si dichos patrones son impredecibles, pueden dificultar la administración de los costos y del modelo de negocio. Para este escenario, se usará un grupo elástico con un número determinado de eDTU.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Qué son las unidades de transacción de base de datos elástica (eDTU)
Una eDTU es una unidad de medida del conjunto de recursos (DTU) que se pueden compartir entre un conjunto de bases de datos de un servidor de Azure SQL denominado [grupo elástico](sql-database-elastic-pool.png). Los grupos elásticos proporcionan una solución sencilla y rentable para administrar los objetivos de rendimiento de varias bases de datos que tienen patrones de uso muy diferentes e imprevisibles. Consulte [los grupos elásticos y los niveles de servicio](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) para más información.

![Introducción a SQL Database: eDTU por servicio y nivel](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A un grupo se le asigna un número fijo de eDTU, por un precio fijo. Dentro del grupo, a las bases de datos individuales se les proporciona la flexibilidad de escalarse automáticamente dentro de unos parámetros establecidos. Con cargas elevadas, una base de datos puede consumir más eDTU para satisfacer la demanda. Las bases de datos con cargas ligeras consumen menos y las bases de datos sin carga no consumen ninguna eDTU. El aprovisionamiento de recursos para el grupo entero en lugar de para bases de datos únicas simplifica las tareas de administración. Además, cuenta con un presupuesto predecible para el grupo.

Se pueden agregar eDTU adicionales a un grupo existente sin que la base de datos experimente tiempo de inactividad o su rendimiento se vea afectado en el grupo elástico. De manera similar, si ya no se necesitan eDTU adicionales, se pueden quitar de un grupo existente en cualquier momento dado. Puede agregar o quitar bases de datos al grupo. Si una base de datos infrautiliza recursos de forma predecible, sáquela del grupo.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>¿Cómo se puede determinar el número de DTU necesarias para la carga de trabajo?
Si desea migrar una carga de trabajo de máquina virtual existente local o de SQL Server en Azure SQL Database, puede usar la [calculadora de DTU](http://dtucalculator.azurewebsites.net/) para hacer una estimación del número aproximado de DTU que se necesitan. Para una carga de trabajo existente de Azure SQL Database, puede usar [la información de rendimiento de consultas de SQL Database](sql-database-query-performance.md) para comprender el consumo de recursos de la base de datos (DTU) y obtener información más detallada sobre cómo optimizar la carga de trabajo. También puede utilizar la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) para obtener la información de consumo de recursos para la última hora. Como alternativa, la vista de catálogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) también se puede consultar para obtener los mismos datos para los últimos 14 días, aunque con una fidelidad inferior media de cinco minutos.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>¿Cómo se puede saber si podría beneficiarme de un grupo elástico de recursos?
Los grupos son apropiados para un amplio número de bases de datos con patrones de utilización específicos. Para una base de datos determinada, este patrón está caracterizado por una utilización media baja con picos de utilización relativamente poco frecuentes. Base de datos SQL evalúa automáticamente el historial de uso de recursos de bases de datos en un servidor de Base de datos SQL existente y recomienda la configuración de grupo apropiada en el Portal de Azure. Consulte [¿Cuándo se debe utilizar un grupo de bases de datos elásticas?](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>¿Qué ocurre cuando se alcanza el número máximo de DTU?
Los niveles de rendimiento se calibran y rigen para proporcionar los recursos necesarios para ejecutar la carga de trabajo de la base de datos hasta los límites máximos permitidos para el nivel de rendimiento o de servicio seleccionado. Si la carga de trabajo alcanza los límites en uno de los límites de CPU/datos, registro de E/S o E/S, seguirá recibiendo los recursos en el nivel máximo permitido, pero es probable que perciba un aumento de las latencias en las consultas. Alcanzar uno de estos límites no provocará errores, sino una ralentización de la carga de trabajo, a menos que la ralentización sea tan severa que las consultas empiecen a agotar el tiempo de espera. Si alcanza el límite máximo permitido de sesiones y solicitudes de usuario simultáneas (subprocesos de trabajo), se producirán errores explícitos. Consulte [Límites de recursos de Azure SQL Database](sql-database-resource-limits.md) para obtener información sobre el límite de otros recursos distintos de la CPU, memoria, E/S de datos y registro de transacciones de E/S.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Nivel de servicio](sql-database-service-tiers.md) para obtener información sobre las DTU y las eDTU disponibles para las bases de datos independientes y para los grupos elásticos.
* Consulte [Límites de recursos de Azure SQL Database](sql-database-resource-limits.md) para obtener información sobre el límite de otros recursos distintos de la CPU, memoria, E/S de datos y registro de transacciones de E/S.
* Consulte [Información de rendimiento de consultas de SQL Database](sql-database-query-performance.md) para comprender el consumo (DTU).
* Para comprender la metodología detrás de la carga de trabajo de pruebas comparativas de OLTP utilizada para determinar la combinación de DTU, consulte [Información general sobre la prueba comparativa SQL Database](sql-database-benchmark-overview.md) .




<!--HONumber=Nov16_HO2-->


