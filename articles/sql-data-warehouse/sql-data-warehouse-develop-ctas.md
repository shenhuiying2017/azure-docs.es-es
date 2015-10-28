<properties
   pageTitle="Create table as select (CTAS) en Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para la codificación con la instrucción Create table as select (CTAS) en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Create Table As Select (CTAS) en Almacenamiento de datos SQL
Create table as select o CTAS es una de las características más importantes de T-SQL disponibles. Se trata de una operación completamente en paralelo que crea una nueva tabla basada en la salida de una instrucción Select. Si lo prefiere, puede considerarla como una versión supercargada de SELECT... INTO.

CTAS también se puede usar para solucionar una serie de características no compatibles indicadas anteriormente. A menudo, esto puede resultar ser una situación ventajosa para todos dado que no solo el código será compatible sino que con frecuencia se ejecutará más rápido en Almacenamiento de datos SQL. El motivo es su diseño completamente en paralelo.

> [AZURE.NOTE]Intente pensar: "primero CTAS". Si cree que puede resolver un problema mediante CTAS, entonces este puede ser el mejor enfoque, incluso si como consecuencia escribe más datos.

Algunas situaciones que se pueden solucionar con CTAS incluyen:

- SELECT..INTO
- ANSI JOINS en UPDATE 
- ANSI JOINS en DELETE
- Instrucción MERGE

Este documento también incluye algunas prácticas recomendadas para cuando se codifica con CTAS.

## SELECT..INTO
Puede encontrar que SELECT... INTO aparece en varios lugares de la solución.

A continuación se muestra un ejemplo de SELECT.. INTO:

```
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

La conversión de esto a CTAS es bastante directa:

```
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

El uso de CTAS significa que también puede especificar una preferencia de distribución de datos y una tabla de índice opcional.

## Sustitución de una combinación ANSI en instrucciones update

Puede encontrarse con que tiene una actualización compleja que combina más de dos tablas con una sintaxis de combinación ANSI para realizar una operación UPDATE o DELETE.

Imagine que hubiera que actualizar esta tabla:

```
CREATE TABLE [dbo].[AnnualCategorySales]
(	[EnglishProductCategoryName]	NVARCHAR(50)	NOT NULL
,	[CalendarYear]					SMALLINT		NOT NULL
,	[TotalSalesAmount]				MONEY			NOT NULL
)
WITH
(
	DISTRIBUTION = ROUND_ROBIN
)
;
```

La consulta original podría haber tenido esta apariencia:

```
UPDATE	acs
SET		[TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM	[dbo].[AnnualCategorySales] 	AS acs
JOIN	(
		SELECT	[EnglishProductCategoryName]
		,		[CalendarYear]
		,		SUM([SalesAmount])				AS [TotalSalesAmount]
		FROM	[dbo].[FactInternetSales]		AS s
		JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
		JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
		JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
		JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
		WHERE 	[CalendarYear] = 2004
		GROUP BY
				[EnglishProductCategoryName]
		,		[CalendarYear]
		) AS fis
ON	[acs].[EnglishProductCategoryName]	= [fis].[EnglishProductCategoryName]
AND	[acs].[CalendarYear]				= [fis].[CalendarYear]
;
```

Como Almacenamiento de datos SQL no admite combinaciones ANSI, no puede copiar este código sin cambiando ligeramente.

Puede usar una mezcla de un CTAS y una combinación implícita para reemplazar este código:

```
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT	ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)	AS [EnglishProductCategoryName]
,		ISNULL(CAST([CalendarYear] AS SMALLINT),0) 						AS [CalendarYear]
,		ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)						AS [TotalSalesAmount]
FROM	[dbo].[FactInternetSales]		AS s
JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
WHERE 	[CalendarYear] = 2004
GROUP BY
		[EnglishProductCategoryName]
,		[CalendarYear]
;

-- Use an implicit join to perform the update 
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## Sustitución de una combinación ANSI en instrucciones delete
A veces el mejor enfoque para eliminar los datos es el uso de CTAS. En lugar de eliminar los datos, simplemente seleccione los datos que desea conservar. Esto se aplica especialmente a las instrucciones DELETE que usan sintaxis de combinación ANSI dado que no se admite en Almacenamiento de datos SQL.

A continuación se muestra un ejemplo de una instrucción DELETE convertida:

```
CREATE TABLE dbo.DimProduct_upsert
WITH 
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p 
RIGHT JOIN dbo.stg_DimProduct s 
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## Sustitución de instrucciones MERGE
Las instrucciones merge se pueden sustituir, al menos en parte, mediante el uso de CTAS. Puede consolidar `INSERT` y `UPDATE` en una única instrucción. Sería necesario que los registros eliminados se bloquearan en una segunda instrucción.

Un ejemplo de un `UPSERT` está disponible a continuación:

```
CREATE TABLE dbo.[DimProduct_upsert]
WITH 
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS 
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## Recomendación de CTAS: establezca explícitamente el tipo de datos y la nulabilidad de salida

Al migrar código, podría encontrarse ejecutando este tipo modelo de codificación:

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f 
;
```

Instintivamente, puede pensar que debería migrar este código a CTAS y sería correcto. Sin embargo, hay un problema oculto aquí.

El siguiente código NO produce el mismo resultado:

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Observe que la columna "resultado" traslada los valores de tipo y nulabilidad de los datos de la expresión. Esto puede provocar sutiles variaciones en los valores si no tiene cuidado.

Intente lo siguiente como ejemplo:

```
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

El valor almacenado para el resultado es diferente. Como el valor persistente en la columna de resultados se usa en otras expresiones, el error se vuelve más importante incluso.

![][1]

Esto es especialmente importante para las migraciones de datos. Aunque se puede decir que la segunda consulta es más precisa, hay un problema. Los datos serán diferentes en comparación con el sistema de origen y eso conduce a preguntas sobre la integridad de la migración. Éste es uno de esos casos raros en los que la respuesta "incorrecta" es realmente la adecuada.

El motivo de que veamos esta disparidad entre los dos resultados se debe a la conversión implícita de los tipos. En el primer ejemplo, la tabla define la definición de columna. Cuando se inserta la fila, se produce una conversión implícita de los tipos. En el segundo ejemplo, no hay ninguna conversión de tipos implícita ya que la expresión define el tipo de datos de la columna. Observe también que la columna del segundo ejemplo se ha definido como una columna que acepta valores NULL mientras que en el primer ejemplo no. Cuando se creó la tabla en el primer ejemplo, la nulabilidad se definió explícitamente. En el segundo ejemplo, simplemente se dejó con la expresión y, de forma predeterminada, esto dará lugar a una definición de NULL.

Para resolver estos problemas, debe establecer explícitamente la conversión de tipos y la nulabilidad en la parte SELECT de la instrucción CTAS. No se pueden establecer estas propiedades en la parte create table.

En el ejemplo siguiente se muestra cómo corregir el código:

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenga en cuenta lo siguiente: - se podrían haber usado CAST o CONVERT - ISNULL se usa para forzar la nulabilidad no COALESCE - ISNULL es la función más externa - La segunda parte de ISNULL es una constante, es decir, 0

> [AZURE.NOTE]Para que la nulabilidad se establezca correctamente, es fundamental usar ISNULL y no COALESCE. COALESCE no es una función determinista y, por lo tanto, el resultado de la expresión será siempre que acepta valores NULL. ISNULL es diferente. Es determinista. Por lo tanto, cuando la segunda parte de la función ISNULL es una constante o un literal, el valor resultante será NOT NULL.

Esta sugerencia no solo es útil para garantizar la integridad de los cálculos. También es importante para la modificación de particiones de tabla. Imagine que tiene esta tabla definida como hecho:

```
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH 
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
) 
;
```

Sin embargo, el campo de valor es una expresión calculada que no forma parte de los datos de origen.

Para crear el conjunto de datos particionado, podría hacer esto:

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

La consulta se ejecutaría perfectamente. El problema se produce al intentar realizar el cambio de partición. Las definiciones de tabla no coinciden. Para que las definiciones de tabla coincidan, CTAS debe modificarse.

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Por lo tanto, puede ver que la coherencia de los tipos y el mantenimiento de las propiedades de nulabilidad en CTAS es una buena práctica de ingeniería. Ayuda a mantener la integridad de los cálculos y también garantiza que la modificación de particiones es posible.

Consulte MSDN para obtener más información sobre el uso de [CTAS][]. Es una de las instrucciones más importantes de Almacenamiento de datos SQL. Asegúrese de que la comprende perfectamente.

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/es-ES/library/mt204041.aspx

<!--Other Web references-->

<!---HONumber=Oct15_HO3-->