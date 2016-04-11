<properties
   pageTitle="Tablas temporales en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para usar las tablas temporales en el Almacenamiento de datos SQL Azure para desarrollar soluciones."
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
   ms.date="03/23/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Tablas temporales en el Almacenamiento de datos SQL
Las tablas temporales son muy útiles al procesar datos, especialmente durante la transformación donde los resultados intermedios son transitorios. Las tablas temporales de Almacenamiento de datos SQL existen en el nivel de sesión. Sin embargo, se definen como tablas temporales locales pero, a diferencia de las tablas de SQL Server, se puede tener acceso a ellas desde cualquier lugar dentro de la sesión.

Este artículo contiene directrices esenciales para el uso de tablas temporales y resalta los principios de las tablas temporales de nivel de sesión. Esta información le ayudará a modularizar el código. La modularidad de código es importante para facilitar el mantenimiento y la reutilización del código.

## Creación de tablas temporales
Es muy sencillo crear una tabla temporal. Lo único que debe hacer es usar el prefijo # antes del nombre de la tabla, como en el ejemplo siguiente:

```sql
CREATE TABLE #stats_ddl
(
	[schema_name]			NVARCHAR(128) NOT NULL
,	[table_name]            NVARCHAR(128) NOT NULL
,	[stats_name]            NVARCHAR(128) NOT NULL
,	[stats_is_filtered]     BIT           NOT NULL
,	[seq_nmbr]              BIGINT        NOT NULL
,	[two_part_name]         NVARCHAR(260) NOT NULL
,	[three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
```

También se pueden crear tablas temporales mediante `CTAS` siguiendo exactamente el mismo método.

```sql
CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS` es un comando muy eficaz y ofrece un uso muy eficiente del espacio del registro de transacciones.


## Eliminación de tablas temporales

Para garantizar que las instrucciones `CREATE TABLE` sean correctas, es importante asegurarse de que la tabla no existe en la sesión. Para ello puede llevar a cabo una sencilla comprobación de existencia previa usando el patrón siguiente:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END
```

> [AZURE.NOTE] Por coherencia de la codificación, se recomienda usar este patrón tanto para las tablas como para las tablas temporales.

También es buena idea usar `DROP TABLE` para quitar las tablas temporales cuando haya acabado con ellas en el código.

```sql
DROP TABLE #stats_ddl
```

En el desarrollo de procedimientos almacenados es bastante habitual que los comandos de eliminación se empaqueten juntos al final de un procedimiento para garantizar que estos objetos se limpian.

## Modularización de código

Puede aprovechar el hecho de que las tablas temporales se pueden ver en cualquier parte en una sesión de usuario para modularizar el código de la aplicación.

Vamos a exponer un ejemplo práctico.

El siguiente procedimiento almacenado combina los ejemplos mencionados anteriormente. El código puede usarse para generar el archivo DDL necesario para actualizar las estadísticas de todas las columnas de la base de datos:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
	,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

En esta fase no se ha producido ninguna acción en la tabla. El procedimiento simplemente ha generado el archivo DDL necesario para actualizar las estadísticas y ha almacenado ese código en una tabla temporal.

Sin embargo, tenga en cuenta que el procedimiento almacenado no incluye un comando `DROP TABLE` al final. Sin embargo, incluimos una comprobación de existencia previa en el procedimiento almacenado para que el código sea sólido y repetible. Así nos aseguramos de que `CTAS` no producirá ningún error como consecuencia de un objeto duplicado en la sesión.

Ahora viene la parte interesante.

En el Almacenamiento de datos SQL es posible usar la tabla temporal fuera del procedimiento que la creó. Esto es diferente de SQL Server. De hecho se puede utilizar la tabla temporal **en cualquier parte** dentro de la sesión.

Esto puede generar código más modular y fácil de administrar. Observe el ejemplo siguiente:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

El código resultante es mucho más compacto.

En algunos casos, las funciones insertadas y de múltiples instrucciones también pueden reemplazarse con esta técnica.

> [AZURE.NOTE] También puede extender esta solución. Si solo quería actualizar una única tabla, por ejemplo, lo único que tiene que hacer es filtrar la tabla #stats\_ddl.

## Limitaciones de tablas temporales
El Almacenamiento de datos SQL impone algunas limitaciones al implementar las tablas temporales.

Las principales limitaciones son:

- No se admiten tablas temporales globales.
- No se pueden crear vistas en las tablas temporales.

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->