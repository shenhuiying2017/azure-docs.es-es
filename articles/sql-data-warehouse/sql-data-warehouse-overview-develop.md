---
title: "Diseño de decisiones y técnicas de codificación para el desarrollo de SQL Data Warehouse | Microsoft Docs"
description: "Conceptos de desarrollo, decisiones de diseño, recomendaciones y técnicas de codificación para el Almacenamiento de datos SQL."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 996e3afc-c21c-4e21-b9df-997f953f6dfd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d1c2255264a4240eecc51199050f8406d141ace2


---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisiones de diseño y técnicas de codificación para el Almacenamiento de datos SQL
Eche un vistazo a  estos artículos de desarrollo para comprender mejor las decisiones de diseño clave, las recomendaciones y las técnicas de codificación para el Almacenamiento de datos SQL.

## <a name="key-design-decisions"></a>Decisiones de diseño clave
En los artículos siguientes se abordan algunos de los conceptos y las decisiones de diseño clave que es preciso comprender para el desarrollo de un almacenamiento de datos distribuido con Almacenamiento de datos SQL:

* [conexiones][conexiones]
* [simultaneidad][simultaneidad]
* [transacciones][transacciones]
* [esquemas definidos por el usuario][esquemas definidos por el usuario]
* [Distribución de tablas][Distribución de tablas]
* [índices de tablas][índices de tablas]
* [particiones de tablas][particiones de tablas]
* [CTAS][CTAS]
* [estadísticas][estadísticas]

## <a name="development-recommendations-and-coding-techniques"></a>Recomendaciones de desarrollo y técnicas de codificación
En estos artículos se abordan técnicas de codificación, sugerencias y recomendaciones específicas para el desarrollo de su Almacenamiento de datos SQL:

* [procedimientos almacenados][procedimientos almacenados]
* [etiquetas][etiquetas]
* [vistas][vistas]
* [tablas temporales][tablas temporales]
* [SQL dinámico][SQL dinámico]
* [bucle][bucle]
* [opciones de agrupar por][opciones de agrupar por]
* [asignación de variables][asignación de variables]

## <a name="next-steps"></a>Pasos siguientes
Una vez leídos los artículos sobre desarrollo, eche un vistazo a la página [Referencia de Transact-SQL][Referencia de Transact-SQL] para más detalles sobre la sintaxis admitida en SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[simultaneidad]: ./sql-data-warehouse-develop-concurrency.md
[conexiones]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL dinámico]: ./sql-data-warehouse-develop-dynamic-sql.md
[opciones de agrupar por]: ./sql-data-warehouse-develop-group-by-options.md
[etiquetas]: ./sql-data-warehouse-develop-label.md
[bucle]: ./sql-data-warehouse-develop-loops.md
[estadísticas]: ./sql-data-warehouse-tables-statistics.md
[procedimientos almacenados]: ./sql-data-warehouse-develop-stored-procedures.md
[Distribución de tablas]: ./sql-data-warehouse-tables-distribute.md
[índices de tablas]: ./sql-data-warehouse-tables-index.md
[particiones de tablas]: ./sql-data-warehouse-tables-partition.md
[tablas temporales]: ./sql-data-warehouse-tables-temporary.md
[transacciones]: ./sql-data-warehouse-develop-transactions.md
[esquemas definidos por el usuario]: ./sql-data-warehouse-develop-user-defined-schemas.md
[asignación de variables]: ./sql-data-warehouse-develop-variable-assignment.md
[vistas]: ./sql-data-warehouse-develop-views.md
[Referencia de Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[cambio de nombre de objetos]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


