<properties
   pageTitle="Administración de estadísticas de tablas en Almacenamiento de datos SQL | Microsoft Azure"
   description="Introducción a las estadísticas de tablas en Almacenamiento de datos SQL de Azure."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Administración de estadísticas en tablas en Almacenamiento de datos SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Tipo de datos][]
- [Distribución][]
- [Índice][]
- [Partition][]
- [Estadísticas][]
- [Temporal][]

Cuanto más sepa Almacenamiento de datos SQL acerca de los datos, más rápido podrá ejecutar consultas en ellos. La manera en que se informa a Almacenamiento de datos SQL acerca de los datos es mediante la recopilación de estadísticas sobre los datos. La obtención de estadísticas sobre los datos es una de las tareas más importantes que se pueden realizar para optimizar las consultas. Las estadísticas ayudan a Almacenamiento de datos SQL a crear el plan óptimo para las consultas. Esto se debe a que el optimizador de consultas de Almacenamiento de datos SQL se basa en costos. Es decir, compara el costo de varios planes de consulta y elige el de menor costo, que también debe ser el que se ejecutará más rápidamente.

Se pueden crear estadísticas de una sola columna, de varias columnas o de un índice de una tabla. Las estadísticas se almacenan en un histograma que captura el intervalo y la selectividad de valores. Esto es de especial interés cuando el optimizador debe evaluar las cláusulas JOIN, GROUP BY, HAVING y WHERE en una consulta. Por ejemplo, si el optimizador estima que la fecha de filtro de la consulta devolverá una fila, puede elegir un plan completamente diferente del que elegiría si estima que la fecha seleccionada devolverá un millón de filas. Aunque la creación de estadísticas es muy importante, no lo es menos que las estadísticas reflejen *con precisión* el estado actual de la tabla. La existencia de las estadísticas actualizadas garantiza que el optimizador selecciona un buen plan. Los planes que crea el optimizador son igual de buenos que las estadísticas de los datos.

En la actualidad, el proceso de creación y actualización de estadísticas es un proceso manual, pero muy fácil. Esto es diferente de lo que sucede en SQL Server, que crea y actualiza automáticamente estadísticas de columnas únicas e índices. Mediante la información que proporciona a continuación, se puede automatizar en gran medida la administración de las estadísticas de los datos.

## Introducción a las estadísticas

 Crear estadísticas de muestra en cada columna es una forma sencilla de empezar a trabajar con las estadísticas. Dado que también es muy importante que las estadísticas estén actualizadas, un enfoque conservador puede ser actualizar las estadísticas diariamente o después de cada carga. Existen inconvenientes entre el rendimiento y el costo de crear y actualizar las estadísticas. Si cree que tarda demasiado en realizar el mantenimiento de todas las estadísticas, puede intentar ser más selectivo acerca de las columnas con estadísticas o las que necesitan actualizarse con frecuencia. Por ejemplo, puede preferir actualizar las columnas de fecha diariamente, ya que se pueden agregar nuevos valores, en lugar de después de cada carga. Una vez más, la mayor ventaja se logrará si se tienen estadísticas de las columnas implicadas las cláusulas JOINs, GROUP BY, HAVING y WHERE. Si tiene una tabla con muchas columnas que solo se utilizan en la cláusula SELECT, es posible que las estadísticas de estas columnas no sirvan de ayuda, mientras que realizar ciertos esfuerzos para identificar solo las columnas en que las estadísticas servirán de ayuda puede reducir el tiempo necesario para mantener las estadísticas.

## Estadísticas de varias columnas

Además de crear estadísticas de columnas individuales, es posible que las consultas se beneficien de las estadísticas de varias columnas. Las estadísticas de varias columnas son las estadísticas creadas en una lista de columnas. Incluyen las estadísticas de columna única en la primera columna de la lista, además de cierta información de correlación entre las columnas denominada densidades. Por ejemplo, si tiene una tabla que se une a otro en dos columnas, es posible que Almacenamiento de datos SQL pueda optimizar mejor el plan si conoce la relación entre las dos columnas. Las estadísticas de varias columnas pueden mejorar el rendimiento de las consultas en algunas operaciones, como las cláusulas compuestas JOINs y GROUP BY.

## Actualización de estadísticas

La actualización de estadísticas es una parte importante de la rutina de administración de base de datos. Cuando se cambia la distribución de datos en la base de datos, las estadísticas deben actualizarse. Las estadísticas obsoletas provocarán a rendimiento de las consultas que no llega a ser óptimo.

Uno de los procedimientos recomendados es la actualización diaria de estadísticas en columnas de fecha al agregarse nuevas fechas. Cada fila nueva de tiempo se carga en el almacenamiento de datos, se agregan nuevas fechas de carga o de transacción. Estas cambian la distribución de datos y hacen que las estadísticas se queden obsoletas. Por el contrario, es posible que las estadísticas de una columna de país en una tabla de clientes nunca deban actualizarse, pues la distribución de valores no suele cambiar. Suponiendo que la distribución es constante entre los clientes, agregar nuevas filas a la variación de tabla no va a cambiar la distribución de datos. Sin embargo, si su almacenamiento de datos solo contiene un país y trae datos de un nuevo país, dando esto lugar al almacenamiento de datos de varios países, definitivamente debe actualizar estadísticas en la columna de país.

Una de las primeras preguntas que se deben formular para la solución de problemas de una consulta es "¿Están actualizadas las estadísticas?".

No se trata de una pregunta que se pueda responder por la antigüedad de los datos. Un objeto de estadísticas actualizadas podría ser muy antiguo si no ha habido ningún cambio material en los datos subyacentes. Si el número de filas ha cambiado significativamente o hay un cambio material en la distribución de los valores de una columna determinada, ha llegado el momento de actualizar las estadísticas.

Como referencia, **SQL Server** (no Almacenamiento de datos SQL) actualiza automáticamente las estadísticas para estas situaciones:

- Si tiene cero filas en la tabla, al agregar una filas, las estadísticas se actualizarán automáticamente
- Al agregar más de 500 filas a una tabla comenzando con menos de 500 filas (p. ej., al principio tiene 499 y después agrega 500 filas a un total de 999 filas), obtendrá una actualización automática
- Una vez que haya más de 500 filas, deberá agregar 500 filas adicionales + el 20% del tamaño de la tabla antes de ver una actualización automática en las estadísticas

Al no haber DMV para determinar si los datos de la tabla han cambiado desde la última actualización de estadísticas, estar al tanto de la antigüedad de estas puede proporcionarle una visión parcial. Puede usar la siguiente consulta para determinar la última vez que se actualizaron las estadísticas en cada tabla.

> [AZURE.NOTE] Recuerde que, si hay un cambio material en la distribución de valores para una columna determinada, deberá actualizar estadísticas independientemente de la última vez que se actualizaron.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Las columnas de fecha en un almacenamiento de datos, por ejemplo, normalmente necesitan frecuentes actualizaciones de estadísticas. Cada fila nueva de tiempo se carga en el almacenamiento de datos, se agregan nuevas fechas de carga o de transacción. Estas cambian la distribución de datos y hacen que las estadísticas se queden obsoletas. Por el contrario, las estadísticas en una columna de género en una tabla de clientes nunca necesita actualizarse. Suponiendo que la distribución es constante entre los clientes, agregar nuevas filas a la variación de tabla no va a cambiar la distribución de datos. Sin embargo, si el almacén de datos contiene solo un sexo y un nuevo requisito da como resultado varios sexos, definitivamente necesitará actualizar las estadísticas en la columna de sexo.

Para obtener más información, consulte [Estadísticas][] en MSDN.

## Implementación de administración de estadísticas

A menudo resulta conveniente extender el proceso de carga de datos para garantizar que las estadísticas están actualizadas al final de la carga. La carga de datos se produce cuando las tablas cambian su tamaño con mayor frecuencia o la distribución de los valores. Por consiguiente, se trata de un lugar lógico para implementar algunos procesos de administración.

A continuación se proporcionan algunos principios fundamentales para actualizar las estadísticas durante el proceso de carga:

- Asegúrese de que cada tabla cargada tiene al menos un objeto de estadísticas actualizado. Esto actualiza la información de tamaño de tablas (recuento de filas y recuento de páginas) como parte de la actualización de estadísticas.
- Céntrese en las columnas que participan en las cláusulas JOIN, GROUP BY, ORDER BY y DISTINCT.
- Considere actualizar con más frecuencia las columnas mediante "clave ascendente", como las fechas de transacción, ya que estos valores no se incluirán en el histograma de estadísticas.
- Considere la posibilidad de actualizar columnas de distribución estática con menor frecuencia.
- Recuerde que cada objeto de estadística se actualiza en serie. Implementar solo `UPDATE STATISTICS <TABLE_NAME>` puede no ser recomendable, especialmente para las tablas amplias con muchos objetos de estadísticas.

> [AZURE.NOTE] Para obtener más detalles sobre [clave ascendente], consulte las notas del producto sobre modelos de estimación de cardinalidad para SQL Server 2014.

Para obtener más información, consulte [estimación de cardinalidad][] en MSDN.

## Ejemplos: crear estadísticas

Estos ejemplos muestran cómo utilizar diversas opciones de creación de estadísticas. Las opciones que utiliza para cada columna dependen de las características de los datos y cómo se utilizará la columna en las consultas.

### R: Crear estadísticas de columna única con las opciones predeterminadas

Para crear estadísticas de una columna, basta con proporcionar un nombre para el objeto de estadística y el nombre de la columna.

Esta sintaxis utiliza todas las opciones predeterminadas. De forma predeterminada, el Almacenamiento de datos SQL ofrece un ejemplo del 20 % de la tabla cuando crea estadísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por ejemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### B. Crear estadísticas de columna única mediante el examen de todas las filas

La velocidad de muestreo predeterminada del 20 % es suficiente para la mayoría de las situaciones. Sin embargo, puede ajustar la velocidad de muestreo.

Para probar la tabla completa, utilice esta sintaxis:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por ejemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### C. Crear estadísticas de columna única especificando el tamaño de muestra

Como alternativa, se puede especificar el tamaño de muestra en forma de porcentaje:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### D. Crear estadísticas de columna única solo en algunas filas

Otra alternativa sería crear estadísticas en una parte de las filas de la tabla. A esto se le denomina una estadística filtrada.

Por ejemplo, podría utilizar las estadísticas filtradas si desea consultar una partición específica de una tabla grande con particiones. Al crear estadísticas basadas únicamente en los valores de partición, mejorará la precisión de las estadísticas y, por tanto, también el rendimiento de consulta.

En este ejemplo se crean estadísticas sobre un intervalo de valores. Los valores pueden definirse fácilmente para que coincidan con el intervalo de valores de una partición.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE] Para que el optimizador de consultas considere utilizar estadísticas filtradas al elegir el plan de consulta distribuida, la consulta debe adecuarse a la definición del objeto de estadísticas. Usando el ejemplo anterior, la consulta es cuándo la cláusula necesita especificar valores col1 entre 2000101 y 20001231.

### E. Crear estadísticas de columna única con todas las opciones

Por supuesto, puede combinar las opciones juntas. En el ejemplo siguiente se crea un objeto de estadísticas filtradas con un tamaño personalizado de ejemplo:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obtener la referencia completa, consulte [CREATE STATISTICS][] en MSDN.

### F. Crear estadísticas de varias columnas

Para crear estadísticas de varias columnas, simplemente use los ejemplos anteriores, pero especifique más columnas.

> [AZURE.NOTE] El histograma, que se utiliza para calcular el número de filas en el resultado de la consulta, solo está disponible para la primera columna de la definición del objeto de estadísticas.

En este ejemplo, el histograma se encuentra en *product\_category*. Las estadísticas entre columnas se calculan en *product\_category* y *product\_sub\_c\\ategory*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Dado que no hay una correlación entre *product\_category* y *product\_sub\_category*, una estadística de varias columna puede ser útil si se tiene acceso a estas columnas al mismo tiempo.

### G. Crear estadísticas de todas las columnas de una tabla

Una forma de crear estadísticas consiste en emitir comandos CREATE STATISTICS después de crear la tabla.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### H. Utilizar un procedimiento almacenado para crear estadísticas de todas las columnas de una base de datos

El Almacenamiento de datos SQL no tiene un procedimiento almacenado del sistema equivalente a [] [sp\_create\_stats] en SQL Server. Este procedimiento almacenado crea un objeto de estadísticas de columna única en todas las columnas de la base de datos que ya no tienen estadísticas.

Esto le ayudará a empezar a trabajar con el diseño de la base de datos. Puede adaptarlo a sus necesidades.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
	DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN	sys.[external_tables] e	ON	e.[object_id]		= t.[object_id]
WHERE       l.[object_id] IS NULL
AND			e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para crear estadísticas de todas las columnas de la tabla con este procedimiento, simplemente llame al procedimiento.

```sql
prc_sqldw_create_stats;
```

## Ejemplos: actualizar estadísticas

Para actualizar estadísticas, puede:

1. Actualizar un objeto de estadísticas. Especificar el nombre del objeto de estadísticas que desea actualizar.
2. Actualizar todos los objetos de estadísticas de una tabla. Especificar el nombre de la tabla en lugar de un objeto de estadísticas específico.


### R: Actualizar un objeto de estadísticas específico ###
Para actualizar un objeto de estadísticas específico, use la siguiente sintaxis:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por ejemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Al actualizar los objetos de estadísticas específicos, puede minimizar el tiempo y los recursos necesarios para administrar las estadísticas. Sin embargo, esto requiere un gran esfuerzo para elegir los objetos de estadísticas que se recomienda actualizar.


### B. Actualizar todas las estadísticas de una tabla ###
Se muestra un método sencillo para actualizar todos los objetos de estadísticas de una tabla.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por ejemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

Esta instrucción es fácil de usar. Solo tiene que recordar que esto actualiza todas las estadísticas de la tabla y, por tanto, puede realizar más trabajo del necesario. Si el rendimiento no es un problema, sin duda es la forma más fácil y más completa de garantizar que las estadísticas están actualizadas.

> [AZURE.NOTE] Al actualizar todas las estadísticas de una tabla, el Almacenamiento de datos SQL realiza un análisis para crear muestras de la tabla para cada estadística. Si la tabla es grande y tiene muchas columnas y estadísticas, puede resultar más eficaz actualizar las estadísticas individualmente en función de las necesidades.

Para ver una implementación de un procedimiento `UPDATE STATISTICS`, consulte el artículo sobre [tablas temporales][Temporary]. El método de implementación difiere ligeramente del procedimiento `CREATE STATISTICS` anterior, pero el resultado final es el mismo.

Para obtener la sintaxis completa, consulte [Update Statistics][] en MSDN.

## Metadatos de las estadísticas
Hay varias funciones y vistas del sistema que puede usar para encontrar información sobre las estadísticas. Por ejemplo, puede ver si un objeto de estadísticas podría estar obsoleto mediante la función stats-date para consultar la fecha en que las estadísticas se crearon o actualizaron por última vez.

### Vistas de catálogo para las estadísticas
Estas vistas del sistema proporcionan información acerca de las estadísticas:

| Datos del catálogo | Descripción |
| :----------- | :---------- |
| [sys.columns][] | Una fila por cada columna. |
| [sys.objects][] | Una fila por cada objeto de la base de datos. | |
| [sys.schemas][] | Una fila por cada esquema de la base de datos. | |
| [sys.stats][] | Una fila por cada objeto de estadísticas. |
| [sys.stats\_columns][] | Una fila por cada columna del objeto de estadísticas. Vínculos a la sys.columns. |
| [sys.tables][] | Una fila por cada tabla (incluye tablas externas). |
| [sys.table\_types][] | Una fila por cada tipo de datos. |


### Funciones del sistema para las estadísticas
Estas funciones del sistema son útiles para trabajar con las estadísticas:

| Función del sistema | Descripción |
| :-------------- | :---------- |
| [STATS\_DATE][] | Fecha en que se actualizó por última vez el objeto de estadísticas. |
| [DBCC SHOW\_STATISTICS][] | Proporciona información resumida de nivel y detallada acerca de la distribución de valores según lo entiende el objeto de estadísticas. |

### Combinar funciones y columnas de estadísticas en una vista

Esta vista agrupa columnas relacionadas con las estadísticas y resulta de la función [STATS\_DATE()][].

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## Ejemplos de DBCC SHOW\_STATISTICS()

DBCC SHOW\_STATISTICS() muestra los datos contenidos en un objeto de estadísticas. Estos datos se presentan en tres partes.

1. Encabezado
2. Vector de densidad
3. Histograma

Los metadatos de encabezado sobre las estadísticas. El histograma muestra la distribución de valores en la primera columna de clave del objeto de estadísticas. El vector de densidad mide la correlación entre las columnas. SQLDW calcula las estimaciones de cardinalidad con cualquiera de los datos en el objeto de estadísticas.

### Mostrar el encabezado, la densidad y el histograma

Este ejemplo muestra las tres partes de un objeto de estadísticas.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por ejemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### Mostrar una o varias partes de DBCC SHOW\_STATISTICS();

Si sólo está interesado en ver partes específicas, use la cláusula `WITH` y especifique qué partes desea ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por ejemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## Diferencias de DBCC SHOW\_STATISTICS()
DBCC SHOW\_STATISTICS() se implementa de forma más estricta en el Almacenamiento de datos SQL en comparación con SQL Server.

1. No se admiten características no documentadas.
- No se puede usar Stats\_stream.
- No se pueden combinar resultados de subconjuntos específicos de datos de estadísticas, como por ejemplo (STAT\_HEADER JOIN DENSITY\_VECTOR).
2. No se puede establecer NO\_INFOMSGS para la supresión de mensajes.
3. No se pueden usar corchetes alrededor de los nombres de las estadísticas.
4. No se pueden usar nombres de columna para identificar objetos de estadísticas.
5. No se admite el error personalizado 2767.

## Pasos siguientes

Para obtener más información, consulte [DBCC SHOW\_STATISTICS][] en MSDN. Para más información, consulte los artículos sobre [información general de tablas][Overview], [tipos de datos de tabla][Data Types], [distribución de una tabla][Distribute], [indexación de una tabla][Index], [creación de particiones en una tabla][Partition] y [tablas temporales][Temporary]. Para más información sobre los procedimientos recomendados, consulte [Procedimientos recomendados para Almacenamiento de datos SQL de Azure][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Información general]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Tipo de datos]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribución]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporal]: ./sql-data-warehouse-tables-temporary.md
[Procedimientos recomendados para Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->  
[estimación de cardinalidad]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW\_STATISTICS]: https://msdn.microsoft.com/library/ms174384.aspx
[Estadísticas]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS\_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats\_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table\_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  

<!---HONumber=AcomDC_0706_2016-->