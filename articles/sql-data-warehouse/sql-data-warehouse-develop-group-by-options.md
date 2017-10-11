---
title: Opciones de Group by en SQL Data Warehouse | Microsoft Docs
description: Sugerencias para implementar opciones de Group by en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f95a1e43-768f-4b7b-8a10-8a0509d0c871
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: da71cb834c13da5d0f5690f471efc6c696163f30
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Opciones de Group by en Almacenamiento de datos SQL
La cláusula [GROUP BY][GROUP BY] se usa para agregar datos a un conjunto de filas de resumen. También cuenta con algunas opciones que amplían su funcionalidad y que es necesario solucionar ya que no son directamente compatibles con Almacenamiento de datos SQL de Azure.

Estas opciones son:

* GROUP BY con ROLLUP
* GROUPING SETS
* GROUP BY con CUBE

## <a name="rollup-and-grouping-sets-options"></a>Opciones Rollup y Grouping sets
Aquí, la opción más sencilla consiste en usar `UNION ALL` en su lugar para realizar la acumulación en lugar de depender de la sintaxis explícita. El resultado es exactamente el mismo

A continuación se muestra un ejemplo de una instrucción Group by mediante el uso de la opción `ROLLUP` :

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Mediante el uso de ROLLUP hemos solicitado las agregaciones siguientes:

* País y región
* País
* Total general

Para reemplazar esta operación, deberá usar `UNION ALL`y especificar las agregaciones necesarias explícitamente para devolver los mismos resultados:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Para GROUPING SETS, todo lo que tenemos que hacer es adoptar la misma entidad de seguridad pero solo crear secciones UNION ALL para los niveles de agregación que queramos ver.

## <a name="cube-options"></a>Opciones de Cube
Es posible crear una cláusula GROUP BY WITH CUBE con el método UNION ALL. El problema es que el código puede volverse rápidamente complicado y difícil de manejar. Para mitigar esta situación, puede usar este enfoque más avanzado.

Vamos a usar el ejemplo anterior.

El primer paso es definir el 'cubo' que define todos los niveles de agregación que se van a crear. Es importante tomar nota de la cláusula CROSS JOIN de las dos tablas derivadas. De esta forma se nos generan todos los niveles. El resto del código está realmente ahí para dar formato.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Los resultados de CTAS pueden verse a continuación:

![][1]

El segundo paso es especificar una tabla de destino para almacenar los resultados temporales:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

El tercer paso es recorrer el cubo de columnas al realizar la agregación. La consulta se ejecuta una vez para cada fila de la tabla temporal #Cube y almacena los resultados en la tabla temporal #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Por último, podemos devolver los resultados simplemente leyendo en la tabla temporal #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Al dividir el código en secciones y generar una construcción de bucle, el código resulta más fácil de administrar y mantener.

## <a name="next-steps"></a>Pasos siguientes
Para más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][development overview].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GROUP BY]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
