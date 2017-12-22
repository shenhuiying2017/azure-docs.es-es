---
title: "Distribución de tablas en SQL Data Warehouse | Microsoft Docs"
description: "Introducción a la distribución de tablas en SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 5ed4337f-7262-4ef6-8fd6-1809ce9634fc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 82e17e575cdb227af2fabf94f01e94df22994aac
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribución de tablas en SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Información general][Overview]
> * [Tipos de datos][Data Types]
> * [Distribución][Distribute]
> * [Índice][Index]
> * [Partición][Partition]
> * [Estadísticas][Statistics]
> * [Temporal][Temporary]
>
>

SQL Data Warehouse es un sistema de base de datos distribuidas de procesamiento masivo en paralelo (MPP).  Al dividir los datos y la funcionalidad de procesamiento entre varios nodos, SQL Data Warehouse puede ofrecer una gran escalabilidad, mucha más que cualquier sistema individual.  La decisión de cómo distribuir los datos en SQL Data Warehouse es uno de los factores más importantes para lograr un rendimiento óptimo.   La clave para lograr un rendimiento óptimo es minimizar el movimiento de datos y, a su vez, la clave para minimizar el movimiento de datos es seleccionar la estrategia de distribución adecuada.

## <a name="understanding-data-movement"></a>Descripción del movimiento de datos
En un sistema MPP, los datos de cada tabla se dividen entre varias bases de datos subyacentes.  Las consultas más optimizadas en un sistema MPP se pueden pasar simplemente para su ejecución en las bases de datos distribuidas individuales sin interacción entre las otras bases de datos.  Por ejemplo, supongamos que tiene una base de datos con datos de ventas que contiene dos tablas, ventas y clientes.  Si tiene una consulta que necesite combinar la tabla de ventas con la de clientes y divide ambas tablas por el número de cliente, coloque a cada cliente en una base de datos independiente y, de este modo, se podrán resolver todas las consultas que combinen ventas y clientes dentro de cada base de datos sin el conocimiento de las otras bases de datos.  En cambio, si divide los datos de ventas por número de pedido y los datos de clientes por número de cliente, cualquier base de datos dada no tendrá los datos correspondientes de cada cliente y, por tanto, si desea combinar los datos de ventas con los datos de clientes, necesitará obtener los datos para cada cliente desde las demás bases de datos.  En este segundo ejemplo, el movimiento de datos debe producirse para mover los datos del cliente a los datos de ventas, de modo que se pueden combinar las dos tablas.  

El movimiento de datos no es algo negativo, a veces es necesario para resolver una consulta.  Pero si se puede evitar este paso adicional, evidentemente la consulta se ejecutará más rápido.  El movimiento de los datos surge normalmente cuando se unen tablas o se realizan agregaciones.  Normalmente, tendrá que realizar ambas acciones por lo que, aunque es posible que pueda optimizar un escenario como el de una combinación, necesitará el movimiento de datos para ayudarle con la resolución del otro escenario, el de una agregación.  El truco es averiguar cuál supone menos trabajo.  En la mayoría de los casos, la distribución de tablas de datos de gran tamaño en una columna que normalmente está combinada es el método más eficaz para reducir al máximo el movimiento de datos.  La distribución de datos en columnas de combinación es un método mucho más frecuente para reducir el movimiento de datos que la distribución de datos en columnas que participan en una agregación.

## <a name="select-distribution-method"></a>Selección del método de distribución
En segundo plano, SQL Data Warehouse divide los datos en 60 bases de datos.  Cada base de datos individual se conoce como una **distribución**.  Cuando se cargan datos en cada tabla, SQL Data Warehouse tiene que saber cómo dividirlos entre las 60 distribuciones.  

El método de distribución se define en el nivel de tabla y actualmente hay dos opciones:

1. **Round Robin** , que distribuye los datos de manera uniforme, pero aleatoria.
2. **Distribuido por hash** , que distribuye datos en función de los valores de hash de una sola columna

De manera predeterminada, si no se define un método de distribución de datos, la tabla se distribuirá con el método **Round Robin** .  Sin embargo, a medida que aumente la sofisticación de la implementación, deberá considerar el uso tablas **distribuidas por hash** para minimizar el movimiento de datos, lo que, a su vez, optimizará el rendimiento de las consultas.

### <a name="round-robin-tables"></a>Tablas Round Robin
El método Round Robin de distribución de datos es muy cíclico.  Cuando los datos se cargan, cada fila simplemente se envía a la siguiente distribución.  Este método siempre distribuirá los datos aleatoriamente los datos muy uniformemente entre todas las distribuciones.  Es decir, durante el proceso Round Robin que coloca los datos no se realiza ningún tipo de clasificación.  Una distribución round robin a veces se denomina hash aleatorio por este motivo.  Con una tabla con distribución Round Robin no es preciso conocer los datos.  Por este motivo, las tablas Round Robin suelen ser buenos objetivos para la carga.

De manera predeterminada, si no se selecciona ningún método de distribución, se utilizará Round Robin.  Sin embargo, aunque las tablas Round Robin son fáciles de usar, porque los datos se distribuyen aleatoriamente en el sistema, lo que significa que el sistema no puede garantizar en qué distribución está cada fila.  Como consecuencia, el sistema a veces necesita invocar una operación de movimiento de datos para organizar mejor los datos antes de que pueda resolver una consulta.  Este paso adicional puede ralentizar las consultas.

Considere la opción de usar la distribución round robin para la tabla en los siguientes casos:

* Cuando se empieza como un punto de partida sencillo.
* Si no hay ninguna clave de combinación obvia.
* Si no hay ninguna columna que sea buena candidata para la distribución de la tabla por hash.
* Si la tabla no comparte una clave de combinación común con otras tablas.
* Si la combinación es menos importante que otras combinaciones de la consulta.
* Cuando la tabla es una tabla de almacenamiento provisional

Ambos ejemplos crearán una tabla Round Robin:

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [!NOTE]
> Aunque Round Robin es el tipo de tabla de predeterminado, se recomienda ser explícito en DDL, con el fin de que las intenciones del diseño de la tabla sean claras para otros usuarios.
>
>

### <a name="hash-distributed-tables"></a>Tablas con distribución por hash
El uso de un algoritmo de **distribución por hash** para distribuir las tablas puede mejorar el rendimiento en muchos escenarios, ya que reduce el movimiento de datos en el momento de la consulta.  Las tablas distribuidas por hash son tablas que se dividen entre las bases de datos distribuidas mediante un algoritmo de hash en una sola columna que seleccione.  La columna de distribución es lo que determina la forma en que los datos se dividen entre las bases de datos distribuidas.  La función hash utiliza la columna de distribución para asignar filas a las distribuciones.  El algoritmo de hash y la distribución resultante son deterministas.  Es decir, el mismo valor con el mismo tipo de datos siempre tiene la misma distribución.    

En este ejemplo se creará una tabla distribuida en id.:

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Selección de la columna de distribución
Si elige **distribuir por hash** una tabla, será preciso que seleccione una sola columna de distribución individual.  Al seleccionar una columna de distribución, deben tenerse en cuenta tres factores principales.  

Seleccione una sola columna que:

1. No se actualizará
2. Distribuirá los datos uniformemente, con lo que se evita la asimetría de datos
3. Reducirá el movimiento de datos

### <a name="select-distribution-column-which-will-not-be-updated"></a>Selección de una columna de distribución que no se actualizará
Las columnas de distribución no se pueden actualizar; por lo tanto, seleccione una columna con valores estáticos.  Si una columna necesita actualizarse, por lo general no será una buena candidata para la distribución.  Si en algún caso debe actualizar una columna de distribución, puede hacerlo eliminando la fila y, a continuación, insertando una fila nueva.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Selección de una columna que distribuirá los datos de manera uniforme
Dado que un sistema distribuido funciona con la misma rapidez que lo haga su distribución más lenta, es importante dividir el trabajo uniformemente entre todas las distribuciones, con el fin de lograr una ejecución equilibrada en el sistema.  La forma en que se divide el trabajo en un sistema distribuido se basa en el lugar en que residen los datos de cada distribución.  Esto hace que sea muy importante seleccionar la columna correcta para distribuir los datos, de forma que cada distribución tenga el mismo trabajo y tarde el mismo tiempo en completar su parte del trabajo.  Cuando el trabajo se divide en el sistema, los datos se equilibran en las distribuciones.  Cuando los datos no están equilibrados de manera uniforme, se conoce como **asimetría de datos**.  

Para dividir los datos uniformemente y evitar la asimetría de datos, considere lo siguiente al seleccionar la columna de distribución:

1. Seleccione una columna que contenga un importante número de valores distintos.
2. Evite la distribución de los datos en las columnas con unos pocos valores distintivos.
3. Evite la distribución de los datos en las columnas con una alta frecuencia de valores NULL.
4. Evite la distribución de datos en columnas de fecha.

Dado que se puede aplicar un algoritmo hash a 1 de las 60 distribuciones, para lograr una distribución uniforme deberá seleccionar una columna que sea altamente exclusiva y que contenga más de 60 valores únicos.  Para ilustrarlo, considere el caso en el que una columna solo tenga 40 valores exclusivos.  Si esta columna se seleccionara como clave de distribución, los datos de dicha tabla se colocarían en 40 distribuciones, como máximo, lo que dejaría 20 distribuciones sin datos y sin procesos que realizar.  Por el contrario, las 40 distribuciones restantes tendrían más trabajo que realizar que si los datos se distribuyeran uniformemente por las 60 distribuciones.  Este escenario es un ejemplo de la asimetría de datos.

En el sistema MPP, cada paso de la consulta espera que todas las distribuciones completen su parte del trabajo.  Si una distribución hace más trabajo que las demás, los recursos de las otras distribuciones, en esencia, se desaprovechan al esperar la distribución ocupada.  Si el trabajo no se distribuye de manera uniforme en todas las distribuciones, se denomina una **asimetría de procesamiento**.  La asimetría de procesamiento hará que las consultas se ejecuten más lento que si la carga de trabajo se pudiera distribuir de manera uniforme en las distribuciones.  La asimetría de datos llevará a la asimetría de procesamiento.

Evite la distribución en una columna que acepte muchos valores NULL debido a que todos los valores NULL se colocarán en la misma distribución. Distribuir en una columna de fecha también podría generar una asimetría de procesamiento, porque todos los datos correspondientes a una fecha determinada se colocarán en la misma distribución. Si varios usuarios ejecutan consultas que se filtran en la misma fecha, solo 1 de las 60 distribuciones realizará todo el trabajo, dado que una fecha determinada solo puede encontrarse en una distribución. En este escenario, es probable que las consultas se ejecuten 60 veces más lento que si los datos estuvieran distribuidos de manera uniforme en todas las distribuciones.

Si no existe ninguna columna que sea una buena candidata, considere la posibilidad de utilizar el método de distribución Round Robin.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Selección de una columna de distribución que minimizará el movimiento de datos
Una de las estrategias más importantes para optimizar el rendimiento de SQL Data Warehouse es minimizar el movimiento de datos mediante la selección de la columna de distribución correcta.  El movimiento de los datos surge normalmente cuando se unen tablas o se realizan agregaciones.  Las columnas usadas en las cláusulas `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` y `HAVING` son todas **buenas** candidatas para la distribución por hash.

Por otro lado, las columnas de la cláusula `WHERE`**no** son buenas candidatas de hash porque limitan las distribuciones que participan en la consulta, lo que genera asimetría de procesamiento.  Una columna de fecha es un buen ejemplo de una columna en la que podría resultar atractivo realizar la distribución pero que, a menudo, podría provocar esta asimetría de procesamiento.

Por lo general, si tiene dos tablas de datos de gran tamaño implicadas normalmente en una combinación, obtendrá el mayor rendimiento distribuyendo ambas tablas en una de las columnas de combinación.  Si tiene una tabla que nunca se combina con otra tabla de datos de gran tamaño, examine las columnas que se encuentran con frecuencia en la cláusula `GROUP BY` .

Hay algunos criterios clave que deben cumplirse para evitar el movimiento de datos durante una combinación:

1. Las tablas implicadas en la combinación deben distribuirse por hash en **una** de las columnas que participan en la combinación.
2. Los tipos de datos de las columnas de combinación deben coincidir en ambas tablas.
3. Las columnas deben combinarse con un operador equals.
4. El tipo de combinación no puede ser `CROSS JOIN`.

## <a name="troubleshooting-data-skew"></a>Solución de problemas de asimetría de datos
Cuando los datos de una tabla se distribuyen mediante el método de distribución hash, puede ocurrir que algunas distribuciones sean asimétricas por tener más datos que otras. Una asimetría de datos excesiva puede afectar al rendimiento de las consultas, ya que el resultado final de una consulta distribuida no estará listo hasta que finalice la distribución de ejecución más larga. Según cuál sea el grado de asimetría, es posible que tenga que solucionarla.

### <a name="identifying-skew"></a>Identificación de la asimetría
Una manera sencilla de identificar una tabla como asimétrica es usar `DBCC PDW_SHOWSPACEUSED`.  Esta es una forma rápida y sencilla de ver el número de filas de tabla que se almacena en cada una de las 60 distribuciones de la base de datos.  Recuerde que para obtener el máximo rendimiento equilibrado, las filas de la tabla distribuida se deben repartir uniformemente entre todas las distribuciones.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Sin embargo, si consulta las vistas de administración dinámica de Azure SQL Data Warehouse, puede realizar un análisis más detallado.  Para empezar, cree la vista [dbo.vTableSizes][dbo.vTableSizes] mediante la instrucción SQL del artículo sobre [información general de tablas][Overview].  Una vez que cree la vista, ejecute esta consulta para identificar qué tablas tienen más de un 10 % de asimetría de datos.

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Resolución de la asimetría de datos
No toda asimetría es suficiente para garantizar una corrección.  En algunos casos, el rendimiento de una tabla en algunas consultas puede superar el daño que supone la asimetría de datos.  Para decidir si merece la pena resolver la asimetría de datos en una tabla, conviene tener el mayor conocimiento posible sobre los volúmenes de datos y consultas en la carga de trabajo.   Una manera de examinar el impacto de la asimetría es utilizar los pasos del artículo [Supervisión de consultas][Query Monitoring]: con ellos, podrá conocer la influencia de la asimetría en el rendimiento de las consultas y, específicamente, en el tiempo que tardan en completarse en las distribuciones individuales.

Lo importante al distribuir datos es hallar el equilibrio perfecto entre minimizar la asimetría de datos y reducir el movimiento de datos. Ambas tareas pueden ser opuestas, de forma que, a veces, preferirá mantener la asimetría datos para reducir el movimiento de datos. Por ejemplo, cuando la columna de distribución es frecuentemente la columna compartida en las combinaciones y agregaciones, reducirá el movimiento de datos. La ventaja que reporta reducir el movimiento de datos al mínimo podría compensar el impacto de tener asimetría de datos.

La forma habitual para resolver la asimetría de datos consiste en volver a crear la tabla con una columna de distribución diferente. Dado que no hay ninguna forma de cambiar la columna de distribución en una tabla existente, la única forma de hacerlo es volver a crearla con una [[CTAS]].  Estos son dos ejemplos de cómo resolver la asimetría de datos:

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Ejemplo 1: volver a crear la tabla con una columna de distribución nueva
En este ejemplo se usa [CTAS][] para volver a crear una tabla con una columna de distribución por hash diferente.

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

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Ejemplo 2: volver a crear la tabla mediante una distribución Round Robin
En este ejemplo se usa [CTAS][] para volver a crear una tabla con Round Robin, en lugar de una distribución por hash. Este cambio producirá incluso la distribución de datos a costa de un mayor movimiento de datos.

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

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca del diseño de tablas, consulte los artículos sobre [distribución][Distribute], [índices][Index], [partición][Partition], [tipos de datos][Data Types], [estadísticas][Statistics] y [tablas temporales][Temporary].

Para acceder a información general sobre los procedimientos recomendados, vea [Procedimientos recomendados para SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Query Monitoring]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
