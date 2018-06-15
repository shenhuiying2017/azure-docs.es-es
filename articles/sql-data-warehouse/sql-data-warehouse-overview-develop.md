---
title: Recursos para desarrollar un almacén de datos en Azure | Microsoft Docs
description: Conceptos de desarrollo, decisiones de diseño, recomendaciones y técnicas de codificación para SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: d9a272b2f43e080cd44b7179fe6f9dc55507142b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601811"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisiones de diseño y técnicas de codificación para SQL Data Warehouse
Eche un vistazo a estos artículos de desarrollo para comprender mejor las decisiones de diseño clave, las recomendaciones y las técnicas de codificación para SQL Data Warehouse.

## <a name="key-design-decisions"></a>Decisiones de diseño clave
En los siguientes artículos se resaltan los conceptos y las decisiones de diseño para desarrollar un almacén de datos distribuido mediante SQL Data Warehouse:

* [conexiones][connections]
* [simultaneidad][concurrency]
* [transacciones][transactions]
* [esquemas definidos por el usuario][user-defined schemas]
* [distribución de tablas][table distribution]
* [índices de tabla][table indexes]
* [particiones de tabla][table partitions]
* [CTAS][CTAS]
* [estadísticas][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Recomendaciones de desarrollo y técnicas de codificación
En estos artículos se abordan técnicas de codificación, sugerencias y recomendaciones específicas para el desarrollo de SQL Data Warehouse:

* [procedimientos almacenados][stored procedures]
* [etiquetas][labels]
* [vistas][views]
* [tablas temporales][temporary tables]
* [SQL dinámico][dynamic SQL]
* [bucle][looping]
* [opciones de agrupar por][group by options]
* [asignación de variables][variable assignment]

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información de referencia, consulte las [instrucciones de T-SQL de SQL Data Warehouse](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
