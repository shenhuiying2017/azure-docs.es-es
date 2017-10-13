---
title: "Migración del código SQL a SQL Data Warehouse | Microsoft Docs"
description: "Sugerencias para migrar el código SQL a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 19c252a3-0e41-4eec-9d3e-09a68c7e7add
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/23/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: c6e6b890f5e2d0e31b10bbb6803adad02bf60248
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migración del código SQL a Almacenamiento de datos SQL
En este artículo se explican los cambios de código que probablemente tenga que realizar al migrar el código desde otra base de datos a SQL Data Warehouse. Algunas características de Almacenamiento de datos SQL pueden mejorar significativamente el rendimiento, ya que están diseñadas para trabajar directamente en un modo distribuido. Sin embargo, para mantener el rendimiento y la escala, también hay algunas características que no están disponibles.

## <a name="common-t-sql-limitations"></a>Limitaciones comunes de T-SQL
En la lista siguiente se resumen las características más comunes que SQL Data Warehouse no admite. Los vínculos le llevan a soluciones alternativas para la característica no admitida:

* [ANSI JOINS en UPDATE][ANSI joins on updates]
* [ANSI JOINS en DELETE][ANSI joins on deletes]
* [Instrucción MERGE][merge statement]
* combinaciones entre bases de datos
* [Cursores][cursors]
* [INSERT..EXEC][INSERT..EXEC]
* cláusula OUTPUT
* funciones insertadas definidas por el usuario
* funciones de múltiples instrucciones
* [expresiones de tabla comunes](#Common-table-expressions)
* [expresiones de tabla común recursivas (CTE)](#Recursive-common-table-expressions-(CTE)
* procedimientos y funciones CLR
* función $partition
* variables de tabla
* parámetros de valor de tabla
* transacciones distribuidas
* trabajo con COMMIT/ROLLBACK
* SAVE TRANSACTION
* contextos de ejecución (EXECUTE AS)
* [cláusula GROUP BY con opciones ROLLUP/CUBE/GROUPING SETS][group by clause with rollup / cube / grouping sets options]
* [anidación de niveles más allá de 8][nesting levels beyond 8]
* [actualización a través de vistas][updating through views]
* [uso de SELECT para la asignación de variables][use of select for variable assignment]
* [No escriba ningún tipo de datos MAX en cadenas de SQL dinámico][no MAX data type for dynamic SQL strings]

Afortunadamente, la mayoría de estas limitaciones se puede solucionar. Los artículos de desarrollo correspondientes antes mencionados incluyen explicaciones.

## <a name="supported-cte-features"></a>Características admitidas de las CTE
Las expresiones de tabla común (CTE) se admiten parcialmente en Almacenamiento de datos SQL.  Actualmente se admiten las siguientes características de CTE:

* Se puede especificar una CTE en una instrucción SELECT.
* Se puede especificar una CTE en una instrucción CREATE VIEW.
* Se puede especificar una CTE en una instrucción CREATE TABLE AS SELECT (CTAS) .
* Se puede especificar una CTE en una instrucción CREATE REMOTE TABLE AS SELECT (CRTAS).
* Se puede especificar una CTE en una instrucción CREATE EXTERNAL TABLE AS SELECT (CETAS).
* Se puede hacer referencia a una tabla remota desde una CTE.
* Se puede hacer referencia a una tabla externa desde una CTE.
* Se pueden incluir varias definiciones de consulta CTE en una CTE.

## <a name="cte-limitations"></a>Limitaciones de las CTE
Estas son algunas de las limitaciones de las expresiones de tabla comunes en Almacenamiento de datos SQL:

* Una CTE debe ir seguida de una sola instrucción SELECT. No se admiten las instrucciones INSERT, UPDATE, DELETE y MERGE.
* No se admite una expresión de tabla común que incluya referencias a sí misma (una expresión de tabla común recursiva). Consulte la sección siguiente.
* No se permite especificar más de una cláusula WITH en una CTE. Por ejemplo, si una definición de consulta CTE contiene una subconsulta, esta no puede contener una cláusula WITH anidada que defina otra CTE.
* No se puede usar una cláusula ORDER BY en las definiciones de consulta CTE, excepto cuando se especifica una cláusula TOP.
* Cuando se usa una CTE en una instrucción que forma parte de un lote, la instrucción antes de ella debe ir seguida por un punto y coma.
* Cuando se usa en instrucciones preparadas por sp_prepare, las CTE comportarán del mismo modo que otras instrucciones SELECT en PDW. Sin embargo, si las CTE se usan como parte de las CETAS preparadas por sp_prepare, el comportamiento puede diferir de SQL Server y de otras instrucciones PDW debido a la manera en que se implementa el enlace para sp_prepare. Si SELECT que hace referencia a la CTE está usando una columna incorrecta que no existe en la CTE, sp_prepare pasará sin detectar el error, pero el error se generará durante sp_execute en su lugar.

## <a name="recursive-ctes"></a>CTE recursivas
Las CTE recursivas no se admiten en Almacenamiento de datos SQL.  La migración de CTE recursivas puede ser bastante compleja y el mejor proceso es dividirla en varios pasos. Normalmente puede usar un bucle y rellenar una tabla temporal conforme se recorren en iteración las consultas provisionales recursivas. Cuando se rellene la tabla temporal, puede devolver los datos como un conjunto único de resultados. Se ha usado un enfoque similar para resolver `GROUP BY WITH CUBE` en el artículo [cláusula GROUP BY con opciones ROLLUP/CUBE/GROUPING SETS][group by clause with rollup / cube / grouping sets options].

## <a name="unsupported-system-functions"></a>Funciones de sistema no compatibles
También hay algunas funciones del sistema que no son compatibles. Algunas de las principales que normalmente se usan en almacenamiento de datos son:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Algunos de estos problemas se pueden solucionar.

## <a name="rowcount-workaround"></a>Solución alternativa para @@ROWCOUNT
Con el fin de evitar la falta de compatibilidad para @@ROWCOUNT, cree un procedimiento almacenado que recupere el último número de filas de sys.dm_pdw_request_steps y, a continuación, ejecute `EXEC LastRowCount` después de una instrucción DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Pasos siguientes
Para ver una lista completa de todas las instrucciones de T-SQL admitidas, vea [Temas de Transact-SQL][Transact-SQL topics].

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
