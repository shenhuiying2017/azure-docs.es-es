---
title: "Optimización de transacciones para SQL Data Warehouse | Microsoft Docs"
description: "Guía de procedimientos recomendados sobre la escritura de actualizaciones de transacciones eficientes en Almacenamiento de datos SQL de Azure"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 6f326f26-8a54-49df-a482-9c96a58db371
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: f9f19d75a37351b3562ce8c2f3629df14c5437c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Optimización de transacciones para Almacenamiento de datos SQL
En este artículo se explica cómo optimizar el rendimiento del código transaccional minimizando al mismo tiempo el riesgo de que se produzcan reversiones extensas.

## <a name="transactions-and-logging"></a>Transacciones y registro
Las transacciones son un componente importante de un motor de base de datos relacional. Almacenamiento de datos SQL usa transacciones durante la modificación de los datos. Estas transacciones pueden ser explícitas o implícitas. Las instrucciones `INSERT`, `UPDATE` y `DELETE` son ejemplos de transacciones implícitas. Un desarrollador escribe explícitamente las transacciones explícitas con `BEGIN TRAN`, `COMMIT TRAN` o `ROLLBACK TRAN`; normalmente, estas transacciones se utilizan cuando es necesario vincular varias instrucciones de modificación entre sí en una unidad atómica única. 

Almacenamiento de datos SQL de Azure confirma los cambios en la base de datos con registros de transacciones. Cada distribución tiene su propio registro de transacciones. Las escrituras en el registro de transacciones son automáticas. No es necesario realizar ninguna configuración. Sin embargo, si bien este proceso garantiza la escritura, introduce una sobrecarga en el sistema. Para minimizar este impacto, puede escribir código transaccionalmente eficiente. De forma amplia, un código transaccionalmente eficiente pertenece en dos categorías.

* Aproveche las construcciones con registro mínimo, siempre que sea posible.
* Procese datos con lotes con ámbito para evitar transacciones singulares de larga ejecución.
* Adopte un modelo de conmutación de particiones para realizar grandes modificaciones en una partición determinada.

## <a name="minimal-vs-full-logging"></a>Registro mínimo frente a registro completo
A diferencia de las operaciones con registro completo, que usan el registro de transacciones para realizar un seguimiento de cada cambio en las filas, las operaciones con registro mínimo solo realizan un seguimiento de las asignaciones de extensión y los cambios en los metadatos. Por lo tanto, el registro mínimo implica registrar solo la información necesaria para revertir la transacción si se produce un error o una solicitud explícita (`ROLLBACK TRAN`). Puesto que se realiza un seguimiento de mucha menos información en el registro de transacciones, una operación con registro mínimo funciona mejor que una operación con registro completo de tamaño similar. Además, dado que menos escrituras van al registro de transacciones, se genera una cantidad mucho menor de datos de registro y sus operaciones de E/S son más eficientes.

Los límites de seguridad de la transacción solo se aplican a las operaciones de registro completo.

> [!NOTE]
> Las operaciones con registro mínimo pueden participar en transacciones explícitas. Puesto que se realiza el seguimiento de todos los cambios en las estructuras de asignación, es posible revertir las operaciones con registro mínimo. Es importante comprender que el cambio se registra "mínimamente", no se elimina el registro.
> 
> 

## <a name="minimally-logged-operations"></a>Operaciones con registro mínimo
Las siguientes operaciones admiten el registro mínimo:

* CREATE TABLE AS SELECT ([CTAS][CTAS])
* INSERT..SELECT
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Las operaciones de movimiento de datos internos (como `BROADCAST` y `SHUFFLE`) no se ven afectados por el límite de seguridad de la transacción.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Registro mínimo con carga masiva
`CTAS` y `INSERT...SELECT` son operaciones de carga masiva. Sin embargo, ambas se ven afectadas por la definición de la tabla de destino y dependen del escenario de carga. A continuación se muestra una tabla que explica si la operación masiva tendrá un registro completo o mínimo:  

| Índice principal | Escenario de carga | Modo de registro |
| --- | --- | --- |
| Montón |Cualquiera |**Mínimo** |
| Índice agrupado |Tabla de destino vacía |**Mínimo** |
| Índice agrupado |Las filas cargadas no se superponen a páginas existentes en el destino |**Mínimo** |
| Índice agrupado |Las filas cargadas se superponen a páginas existentes en el destino |Completo |
| Índice de almacén de columnas agrupado |Tamaño del lote > = 102.400 por cada distribución con particiones alineadas |**Mínimo** |
| Índice de almacén de columnas agrupado |Tamaño del lote < = 102.400 por cada distribución con particiones alineadas |Completo |

Conviene tener en cuenta que cualquier escritura para actualizar índices secundarios o no agrupados será siempre una operación con registro completo.

> [!IMPORTANT]
> Almacenamiento de datos SQL tiene 60 distribuciones. Por lo tanto, suponiendo que todas las filas tengan una distribución uniforme y una sola partición como destino, el lote deberá contener 6.144.000 filas o más para un registro mínimo cuando se escribe en un índice de almacén de columnas agrupado. Si la tabla tiene particiones y las filas que se insertan traspasan los límites de las particiones, serán necesarias 6 144 000 filas por límite de partición, lo que supone una distribución de datos uniforme. Cada partición de cada distribución debe superar de forma independiente el umbral de 102.400 filas para que la inserción se registre mínimamente en la distribución.
> 
> 

A menudo, la carga de datos en una tabla no vacía con un índice agrupado puede contener una mezcla de filas con registro completo y filas con registro mínimo. Un índice agrupado es un árbol equilibrado (árbol B) de las páginas. Si la página que se escribe ya contiene filas de otra transacción, estas escrituras se registrarán completamente. Sin embargo, si la página está vacía, la escritura en esa página se registrará mínimamente.

## <a name="optimizing-deletes"></a>Optimización de eliminaciones
`DELETE` es una operación con registro completo.  Si necesita eliminar una gran cantidad de datos de una tabla o una partición, suele tener más sentido realizar una instrucción `SELECT` en los datos que desea conservar, que pueden ejecutarse como operación con registro completo.  Para ello, cree una nueva tabla con [CTAS][CTAS].  Cuando lo haga, utilice [RENAME][RENAME] para intercambiar la tabla por la que se acaba de crear.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Optimización de actualizaciones
`UPDATE` es una operación con registro completo.  Si necesita actualizar un gran número de filas de una tabla o una partición, suele ser mucho más eficiente utilizar una operación con registro mínimo, como [CTAS][CTAS], para hacerlo.

En el ejemplo siguiente, se ha convertido la actualización de una tabla completa en una operación `CTAS` para que sea posible el registro mínimo.

En este caso, estamos agregando retrospectivamente un importe de descuento a las ventas en la tabla:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Volver a crear tablas de gran tamaño puede beneficiarse del uso de las características de administración de cargas de trabajo de Almacenamiento de datos SQL. Para obtener más información, consulte la sección de administración de cargas de trabajo en el artículo sobre [simultaneidad][concurrency].
> 
> 

## <a name="optimizing-with-partition-switching"></a>Optimización con modificación de particiones
Cuando se enfrenta a modificaciones a gran escala dentro de una [partición de tabla][table partition], un patrón de modificación de particiones constituye un buen enfoque. Si la modificación de datos es importante y abarca varias particiones, basta con iterar en las particiones para obtener el mismo resultado.

Los pasos para realizar una conmutación de particiones son los siguientes:

1. Crear una partición vacía de salida
2. Realizar la actualización como una operación CTAS
3. Desactivar los datos existentes en la tabla de salida
4. Activar los datos nuevos
5. Limpiar los datos

Sin embargo, para ayudar a identificar las particiones que se van a conmutar, primero necesitamos crear un procedimiento auxiliar como el siguiente. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Este procedimiento maximiza la reutilización del código y mantiene el ejemplo de modificación de particiones más compacto.

El código siguiente muestra los cinco pasos mencionados anteriormente para conseguir una rutina de conmutación de particiones completa.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimización del registro con lotes pequeños
Para operaciones de modificación de datos de gran tamaño, puede tener sentido dividir la operación en fragmentos o lotes para definir el ámbito de la unidad de trabajo.

A continuación, se proporciona un ejemplo de trabajo. El tamaño del lote se estableció en un número trivial para resaltar la técnica. En realidad, el tamaño del lote sería mucho mayor. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Instrucciones de operaciones de pausa y escalado
Almacenamiento de datos SQL de Azure permite pausar, reanudar y escalar su almacenamiento de datos a petición. Al pausar o escalar Almacenamiento de datos SQL es importante entender que las transacciones en curso se terminan inmediatamente, lo que hace que las transacciones abiertas se reviertan. Si la carga de trabajo había emitido una modificación de datos incompleta y de larga ejecución antes de la operación de pausa o escalado, será necesario deshacer este trabajo. Esto puede afectar al tiempo que tarda en pausar completamente la base de datos de Almacenamiento de datos SQL de Azure. 

> [!IMPORTANT]
> `UPDATE` y `DELETE` son operaciones con registro completo y, por tanto, estas operaciones de deshacer y rehacer pueden tardar bastante más que las operaciones con registro mínimo equivalentes. 
> 
> 

Lo mejor es dejar que las transacciones de modificación de datos en curso se completen antes de pausar o escalar Almacenamiento de datos SQL. Sin embargo, esto no siempre es posible. Para mitigar el riesgo de que se produzca una reversión extensa, considere una de las siguientes opciones:

* Vuelva a escribir las operaciones de ejecución prolongada con [CTAS][CTAS].
* Divida la operación en fragmentos, lo que opera en un subconjunto de las filas

## <a name="next-steps"></a>Pasos siguientes
Consulte [Transactions in SQL Data Warehouse][Transactions in SQL Data Warehouse] (Transacciones en SQL Data Warehouse) para obtener más información sobre los niveles de aislamiento y los límites transaccionales.  Para obtener información general de otros procedimientos recomendados, consulte [Procedimientos recomendados para SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Transactions in SQL Data Warehouse]: ./sql-data-warehouse-develop-transactions.md
[table partition]: ./sql-data-warehouse-tables-partition.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

