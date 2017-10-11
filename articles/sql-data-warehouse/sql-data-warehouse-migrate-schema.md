---
title: "Migración del esquema a SQL Data Warehouse | Microsoft Docs"
description: Sugerencias para migrar el esquema a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: 07ca2321852e276502187e768177e7e82bdfd080
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migración de esquemas a SQL Data Warehouse
Guía para migrar esquemas SQL a SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Planeamiento de la migración del esquema

Cuando planee una migración, consulte la [información general de tablas][table overview] para familiarizarse con las consideraciones sobre el diseño de tabla, como las estadísticas, la distribución, la creación de particiones y la indexación.  También se incluyen algunas [características no admitidas de las tablas][unsupported table features] y las soluciones alternativas a tales carencias.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Uso de esquemas definidos por el usuario para consolidar bases de datos

Probablemente, su carga de trabajo existente tiene más de una base de datos. Por ejemplo, un almacenamiento de datos de SQL Server podría incluir una base de datos provisional, una de almacenamiento de datos y algunas de tipo data mart. En esta topología, cada base de datos se ejecuta como una carga de trabajo independiente con directivas de seguridad distintas.

Por el contrario, el Almacenamiento de datos SQL ejecuta la carga de trabajo completa del almacenamiento de datos dentro de una base de datos. No se permiten las combinaciones entre bases de datos. Por lo tanto, SQL Data Warehouse espera que todas las tablas utilizadas por el almacenamiento de datos se almacenen en una única base de datos.

Se recomienda usar esquemas definidos por el usuario para consolidar la carga de trabajo existente en una sola base de datos. Para ver ejemplos, consulte [Esquemas definidos por el usuario](sql-data-warehouse-develop-user-defined-schemas.md).

## <a name="use-compatible-data-types"></a>Uso de tipos de datos compatibles
Modifique los tipos de datos para que sean compatibles con SQL Data Warehouse. Para ver una lista de tipos de datos admitidos y no admitidos, consulte el artículo sobre [tipos de datos][data types]. En ese tema, se proporcionan soluciones alternativas para los tipos no compatibles. También se proporciona una consulta para identificar tipos existentes que no se admitan en SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimización del tamaño de fila
Para lograr un rendimiento óptimo, minimice la longitud de fila de las tablas. Dado que las longitudes de fila más cortas conducen a un mejor rendimiento, use los tipos de datos más pequeños que funcionen para los datos. 

Para el ancho de fila de tabla, PolyBase tiene un límite de 1 MB.  Si tiene previsto cargar datos en SQL Data Warehouse con PolyBase, actualice las tablas para que el ancho máximo de la fila sea de menos de 1 MB. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Especificación de la opción de distribución
SQL Data Warehouse es un sistema de base de datos distribuida. Cada tabla se distribuye o se replica en todos los nodos de proceso. Hay una opción de tabla que le permite especificar cómo distribuir los datos. Las opciones son round robin, replicada o distribuida por hash. Cada una tiene ventajas y desventajas. Si no se especifica la opción de distribución, SQL Data Warehouse usará round robin como valor predeterminado.

- Round robin es el valor predeterminado. Es la más sencilla de usar y carga los datos lo más rápido posible, pero las combinaciones requerirán que se muevan los datos, lo que reduce el rendimiento de las consultas.
- Replicada almacena una copia de la tabla en cada nodo de proceso. Las tablas replicadas tienen buen rendimiento porque no requieren que se muevan datos para las combinaciones y agregaciones. Sí requieren almacenamiento adicional y, por lo tanto, funcionan mejor con tablas más pequeñas.
- Distribuida por hash distribuye las filas en todos los nodos mediante una función hash. Las tablas distribuidas por hash son la esencia de SQL Data Warehouse, ya que están diseñadas para proporcionar un rendimiento elevado de consultas en tablas grandes. Esta opción requiere cierto planeamiento para seleccionar la mejor columna en la que distribuir los datos. Sin embargo, si no elige la mejor columna la primera vez, se pueden volver a distribuir fácilmente los datos en una columna diferente. 

Para elegir la mejor opción de distribución para cada tabla, consulte [Tablas distribuidas](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>Pasos siguientes
Una vez migrado correctamente el esquema de base de datos al Almacenamiento de datos SQL, continúe con uno de los siguientes artículos:

* [Migración de los datos][Migrate your data]
* [Migración del código][Migrate your code]

Para obtener más información sobre los procedimientos recomendados de SQL Data Warehouse, consulte el artículo sobre [procedimientos recomendados][best practices].

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->
