<properties
   pageTitle="Administrar la asimetría de datos de tablas distribuidas en el almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Busque y corrija la asimetría de datos cuando haya filas distribuidas de forma desigual entre todas las distribuciones de una tabla con distribución hash en el Almacenamiento de datos SQL de Azure." 
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
   ms.date="04/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Administrar la asimetría de datos de tablas distribuidas en el almacenamiento de datos SQL de Azure
En este tutorial se aborda la asimetría de datos en las tablas con distribución hash y se ofrecen sugerencias para solucionar el problema.

Cuando los datos de una tabla se distribuyen mediante el método de distribución hash, puede ocurrir que algunas distribuciones sean asimétricas por tener más datos que otras. Una asimetría de datos excesiva puede afectar al rendimiento de las consultas, ya que el resultado final de una consulta distribuida no estará listo hasta que finalice la distribución de ejecución más larga. Según cuál sea el grado de asimetría, es posible que tenga que solucionarla.

En este tutorial, aprenderá lo siguiente:

- Usar metadatos para saber qué tablas tienen asimetría de datos
- Obtener sugerencias para saber cuándo resolver asimetrías de datos
- Volver a crear la tabla para resolver asimetrías de datos

## DBCC PDW\_SHOWSPACEUSED

Un método de identificación del sesgo de datos consiste en usar [DBCC PDW\_SHOWSPACEUSED()][]

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Esta es una forma rápida y sencilla de ver el número de filas de tabla que se almacena en cada una de las 60 distribuciones de la base de datos. Recuerde que para obtener el máximo rendimiento equilibrado, las filas de la tabla distribuida se deben repartir uniformemente entre todas las distribuciones.

Sin embargo, si consulta las vistas de administración dinámica de Almacenamiento de datos SQL de Azure, puede realizar un análisis más detallado. El resto de este artículo le muestra cómo hacerlo.

## Paso 1: Crear una vista que detecte la asimetría de datos

Cree esta vista para identificar las tablas con asimetría de datos.

```sql
CREATE VIEW dbo.vDistributionSkew
AS
WITH base
AS
(
SELECT 
	SUBSTRING(@@version,34,4)															AS  [build_number]
,	GETDATE()																			AS  [execution_time]
,	DB_NAME()																			AS  [database_name]
,	s.name																				AS  [schema_name]
,	t.name																				AS  [table_name]
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)												AS  [two_part_name]
,	nt.[name]																			AS  [node_table_name]
,	ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))					AS  [node_table_name_seq]
,	tp.[distribution_policy_desc]														AS  [distribution_policy_name]
,	nt.[distribution_id]																AS  [distribution_id]
,	nt.[pdw_node_id]																	AS  [pdw_node_id]
,	pn.[type]																			AS	[pdw_node_type]
,	pn.[name]																			AS	[pdw_node_name]
,	nps.[partition_number]																AS	[partition_nmbr]
,	nps.[reserved_page_count]															AS	[reserved_space_page_count]
,	nps.[reserved_page_count] - nps.[used_page_count]									AS	[unused_space_page_count]
,	nps.[in_row_data_page_count] 
	+ nps.[row_overflow_used_page_count] + nps.[lob_used_page_count]					AS  [data_space_page_count]
,	nps.[reserved_page_count] 
	- (nps.[reserved_page_count] - nps.[used_page_count]) 
	- ([in_row_data_page_count]+[row_overflow_used_page_count]+[lob_used_page_count])	AS  [index_space_page_count]
,	nps.[row_count]																		AS  [row_count]
from sys.schemas s
join sys.tables t								ON	s.[schema_id]			= t.[schema_id]
join sys.pdw_table_distribution_properties	tp	ON	t.[object_id]			= tp.[object_id]
join sys.pdw_table_mappings tm					ON	t.[object_id]			= tm.[object_id]
join sys.pdw_nodes_tables nt					ON	tm.[physical_name]		= nt.[name]
join sys.dm_pdw_nodes pn 						ON  nt.[pdw_node_id]		= pn.[pdw_node_id]
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.[object_id]			= nps.[object_id]
												AND nt.[pdw_node_id]		= nps.[pdw_node_id]
												AND nt.[distribution_id]	= nps.[distribution_id]
)
, size
AS
(
SELECT	[build_number]
,		[execution_time]
,		[database_name]
,		[schema_name]
,		[table_name]
,		[two_part_name]
,		[node_table_name]
,		[node_table_name_seq]
,		[distribution_policy_name]
,		[distribution_id]
,		[pdw_node_id]
,		[pdw_node_type]
,		[pdw_node_name]
,		[partition_nmbr]
,		[reserved_space_page_count]
,		[unused_space_page_count]
,		[data_space_page_count]
,		[index_space_page_count]
,		[row_count]
,		([reserved_space_page_count] * 8.0)				AS [reserved_space_KB]
,		([reserved_space_page_count] * 8.0)/1000		AS [reserved_space_MB]
,		([reserved_space_page_count] * 8.0)/1000000		AS [reserved_space_GB]
,		([reserved_space_page_count] * 8.0)/1000000000	AS [reserved_space_TB]
,		([unused_space_page_count]   * 8.0)				AS [unused_space_KB]
,		([unused_space_page_count]   * 8.0)/1000		AS [unused_space_MB]
,		([unused_space_page_count]   * 8.0)/1000000		AS [unused_space_GB]
,		([unused_space_page_count]   * 8.0)/1000000000	AS [unused_space_TB]
,		([data_space_page_count]     * 8.0)				AS [data_space_KB]
,		([data_space_page_count]     * 8.0)/1000		AS [data_space_MB]
,		([data_space_page_count]     * 8.0)/1000000		AS [data_space_GB]
,		([data_space_page_count]     * 8.0)/1000000000	AS [data_space_TB]
,		([index_space_page_count]	 * 8.0)				AS [index_space_KB]
,		([index_space_page_count]	 * 8.0)/1000		AS [index_space_MB]
,		([index_space_page_count]	 * 8.0)/1000000		AS [index_space_GB]
,		([index_space_page_count]	 * 8.0)/1000000000	AS [index_space_TB]
FROM	base
)
SELECT	* 
FROM	size
;
```

## Paso 2: Consultar la vista

Tras crear la vista, ejecute esta consulta de ejemplo para saber qué tablas tienen asimetría de datos.

```sql
SELECT	[two_part_name]
,		[distribution_id]
,		[row_count]
,		[reserved_space_GB]
,		[unused_space_GB]
,		[data_space_GB]
,		[index_space_GB]
FROM	[dbo].[vDistributionSkew]
WHERE	[table_name] = 'FactInternetSales'
ORDER BY [row_count] DESC
```

>[AZURE.NOTE] No se deberían sesgar las tablas ROUND\_ROBIN distribuidas. Los datos se distribuyen uniformemente entre los nodos de forma predeterminada.

## Paso 3: Decidir si resolver la asimetría de datos

Para decidir si merece la pena resolver la asimetría de datos en una tabla, conviene tener el mayor conocimiento posible sobre los volúmenes de datos y consultas en la carga de trabajo.

Lo importante al distribuir datos es hallar el equilibrio perfecto entre minimizar la asimetría de datos y reducir el movimiento de datos. Ambas tareas pueden ser opuestas, de forma que, a veces, preferirá mantener la asimetría datos para reducir el movimiento de datos. Por ejemplo, cuando la columna de distribución es frecuentemente la columna compartida en las combinaciones y agregaciones, reducirá el movimiento de datos. La ventaja que reporta reducir el movimiento de datos al mínimo podría compensar el impacto de tener asimetría de datos.

## Paso 4: Resolver la asimetría de datos

Existen dos posibles formas de resolver la asimetría de datos: Recurra a una de ellas si ha decidido que debe solucionar la asimetría.

### Método 1: Volver a crear la tabla con una columna de distribución diferente

La forma habitual para resolver la asimetría de datos consiste en volver a crear la tabla con una columna de distribución diferente. Para obtener instrucciones sobre cómo seleccionar una columna de distribución hash, consulte este artículo sobre [distribución hash][]. En este ejemplo se usa [CTAS][] para volver a crear una tabla con una columna de distribución diferente.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### Método 2: Volver a crear la tabla mediante una distribución ROUND ROBIN

En este ejemplo la tabla se vuelve a crear mediante ROUND-ROBIN en lugar de con una distribución HASH. Este cambio generará una distribución uniforme de los datos. Sin embargo, suele aumentar el movimiento de datos en las consultas.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## Pasos siguientes
Para obtener más detalles sobre la distribución de tablas, consulte los siguientes artículos:

* [Diseño de tablas][]
* [Distribución hash][]

<!--Image references-->

<!--Article references-->
[Diseño de tablas]: sql-data-warehouse-develop-table-design.md
[distribución hash]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->
[DBCC PDW\_SHOWSPACEUSED()]: https://msdn.microsoft.com/es-ES/library/mt204028.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->