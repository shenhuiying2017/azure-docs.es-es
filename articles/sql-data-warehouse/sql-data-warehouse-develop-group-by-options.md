---
title: Uso de las opciones de Group by en Azure SQL Data Warehouse | Microsoft Docs
description: Sugerencias para implementar opciones de Group by en Azure SQL Data Warehouse para el desarrollo de soluciones.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0548983df23b158385783ac777b23268b5ac7d01
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526053"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Opciones de Group by en SQL Data Warehouse
Sugerencias para implementar opciones de Group by en Azure SQL Data Warehouse para el desarrollo de soluciones.

## <a name="what-does-group-by-do"></a>¿Qué hace GROUP BY?

La cláusula [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) de T-SQL se usa para agregar datos a un conjunto de filas de resumen. GROUP BY tiene algunas opciones que no admite SQL Data Warehouse. Estas opciones tienen soluciones alternativas.

Estas opciones son:

* GROUP BY con ROLLUP
* GROUPING SETS
* GROUP BY con CUBE

## <a name="rollup-and-grouping-sets-options"></a>Opciones Rollup y Grouping sets
Aquí, la opción más sencilla consiste en usar UNION ALL en su lugar para realizar la acumulación en lugar de depender de la sintaxis explícita. El resultado es exactamente el mismo

En el siguiente ejemplo se utiliza la instrucción GROUP BY con la opción de ROLLUP:
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

Al utilizar ROLLUP, en el ejemplo anterior se solicitan las agregaciones siguientes:

* País y región
* País
* Total general

Para reemplazar ROLLUP y obtener los mismos resultados, puede utilizar UNION ALL y especificar explícitamente las agregaciones necesarias:

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

Para reemplazar GROUPING SETS, se aplica el principio de ejemplo. Basta con crear secciones UNION ALL para los niveles de agregación que desea ver.

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

A continuación se muestran los resultados de CTAS:

![Agrupar por cubo](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

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

Por último, se pueden devolver los resultados simplemente leyendo en la tabla temporal #Results.

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Al dividir el código en secciones y generar una construcción de bucle, el código resulta más fácil de administrar y mantener.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo](sql-data-warehouse-overview-develop.md).

