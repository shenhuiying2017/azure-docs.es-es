---
title: Aprovechamiento de bucles T-SQL en Azure SQL Data Warehouse | Microsoft Docs
description: Sugerencias para los bucles de Transact-SQL en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 40a872ff310f48bfd543ac184fe7301b85b50258
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="loops-in-sql-data-warehouse"></a>Bucles en Almacenamiento de datos SQL
SQL Data Warehouse admite el bucle [WHILE][WHILE] para ejecutar bloques de instrucciones de forma repetida. Esta acción continuará siempre y cuando las condiciones especificadas se cumplan o hasta que el código termine específicamente el bucle con la palabra clave `BREAK` . Los bucles son especialmente útiles para reemplazar los cursores definidos en código SQL. Afortunadamente, casi todos los cursores que están escritos en código SQL son de la variedad avance rápido y solo lectura. Por lo tanto, los bucles [WHILE] son una buena alternativa si se encuentra con que tiene que reemplazar uno.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Aprovechamiento de bucles y sustitución de cursores en Almacenamiento de datos SQL
Sin embargo, antes de profundizar en el tema, debe hacerse la siguiente pregunta: "¿Se pudo escribir de nuevo este cursor para usar operaciones basadas en conjunto?". En muchos casos, la respuesta será afirmativa y este suele ser el mejor enfoque. Una operación basada en conjunto a menudo se realiza bastante más rápido que un enfoque iterativo, fila a fila.

Los cursores de avance rápido y solo lectura se pueden reemplazar fácilmente por una construcción de bucle. A continuación se muestra un ejemplo sencillo: Este código de ejemplo actualiza las estadísticas de todas las tablas de la base de datos. Mediante la iteración a través de las tablas del bucle, podemos ejecutar cada comando en una secuencia.

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


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Pasos siguientes
Para más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
