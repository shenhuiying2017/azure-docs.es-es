---
title: Uso de bucles T-SQL en Azure SQL Data Warehouse | Microsoft Docs
description: Sugerencias para el uso de bucles de T-SQL en Azure SQL Data Warehouse para el desarrollo de soluciones.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8d51c8f18d7c00d21fcc057efcda73e2a6b46cc7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Uso de bucles T-SQL en SQL Data Warehouse
Sugerencias para el uso de bucles de T-SQL en Azure SQL Data Warehouse para el desarrollo de soluciones.

## <a name="purpose-of-while-loops"></a>Propósito de los bucles WHILE

SQL Data Warehouse admite el bucle [WHILE](/sql/t-sql/language-elements/while-transact-sql) para ejecutar bloques de instrucciones de forma repetida. El bucle WHILE continúa siempre y cuando las condiciones especificadas se cumplan o hasta que el código termine específicamente el bucle con la palabra clave BREAK. Los bucles son útiles para reemplazar los cursores definidos en código SQL. Afortunadamente, casi todos los cursores que están escritos en código SQL son de la variedad avance rápido y solo lectura. Por lo tanto, los bucles [WHILE] son una buena alternativa para reemplazar los cursores.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Reemplazo de cursores en SQL Data Warehouse
Sin embargo, antes de profundizar en el tema, debe hacerse la siguiente pregunta: "¿Se pudo escribir de nuevo este cursor para usar operaciones basadas en conjunto?". En muchos casos, la respuesta es afirmativa y este suele ser el mejor enfoque. Una operación basada en conjunto a menudo se realiza más rápido que un enfoque iterativo, fila a fila.

Los cursores de avance rápido y solo lectura se pueden reemplazar fácilmente por una construcción de bucle. Lo siguiente es un ejemplo sencillo. Este código de ejemplo actualiza las estadísticas de todas las tablas de la base de datos. Mediante la iteración a través de las tablas del bucle, cada comando se ejecuta en secuencia.

En primer lugar, cree una tabla temporal que contenga un número de fila único usado para identificar las instrucciones individuales:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

En segundo lugar, inicialice las variables necesarias para realizar el bucle:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Ahora, recorra las instrucciones ejecutándolas de una en una:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Finalmente, elimine la tabla temporal creada en el primer paso

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Pasos siguientes
Para más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo](sql-data-warehouse-overview-develop.md).

