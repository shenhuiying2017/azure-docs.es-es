---
title: "Instrucciones de diseño para las tablas replicadas: Azure SQL Data Warehouse | Microsoft Docs"
description: "Recomendaciones para el diseño de tablas replicadas en el esquema de Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ronortloff
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/23/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: 575b3c5710d744e99c6e02439577a362eb17c67e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Instrucciones de diseño para el uso de tablas replicadas en Azure SQL Data Warehouse
En este artículo se proporcionan recomendaciones para el diseño de tablas replicadas en el esquema de SQL Data Warehouse. Siga estas recomendaciones para mejorar el rendimiento de las consultas al reducir el movimiento de datos y la complejidad de las consultas.

> [!NOTE]
> La característica de tabla replicada está actualmente en versión preliminar pública. Algunos comportamientos están sujetos a cambios.
> 

## <a name="prerequisites"></a>Requisitos previos
En este artículo se da por supuesto que está familiarizado con los conceptos de distribución y movimiento de datos en SQL Data Warehouse.  Para obtener más información, consulte el artículo [Arquitectura](massively-parallel-processing-mpp-architecture.md). 

Como parte del diseño de tablas, comprenda tanto como sea posible sobre los datos y cómo se consultan los datos.  Por ejemplo, considere estas preguntas:

- ¿Qué tamaño tiene la tabla?   
- ¿Con qué frecuencia se actualiza la tabla?   
- ¿Tiene tablas de hechos y dimensiones en un almacenamiento de datos?   

## <a name="what-is-a-replicated-table"></a>¿Qué es una tabla replicada?
Una tabla replicada tiene una copia completa de la tabla a la que se puede tener acceso en cada nodo de proceso. Al replicar una tabla se elimina la necesidad de transferir sus datos de un nodo de proceso a otro antes de una combinación o agregación. Como la tabla tiene varias copias, las tablas replicadas funcionan mejor cuando el tamaño de la tabla es inferior a 2 GB comprimido.

En el diagrama siguiente se muestra una tabla replicada a la que se puede tener acceso en cada nodo de proceso. En SQL Data Warehouse, la tabla replicada se copia completa en una base de datos de distribución en cada nodo de proceso. 

![Tabla replicada](media/guidance-for-using-replicated-tables/replicated-table.png "Tabla replicada")  

Las tablas replicadas funcionan correctamente para tablas de dimensiones pequeñas de un esquema de estrella. Las tablas de dimensiones suelen tener un tamaño que resulta adecuado para almacenar y mantener varias copias. Las dimensiones almacenan datos descriptivos que cambian lentamente, como el nombre y la dirección del cliente, y detalles del producto. La naturaleza de cambio lento de los datos genera menos recompilaciones de la tabla replicada. 

Considere la posibilidad de usar una tabla replicada cuando:

- El tamaño de la tabla en el disco es inferior a 2 GB, independientemente del número de filas. Para averiguar el tamaño de una tabla, puede usar el comando [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql): `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- La tabla se usa en combinaciones que en caso contrario requerirían el movimiento de datos. Por ejemplo, una combinación en tablas distribuidas por hash requiere el movimiento de datos cuando las columnas de combinación no son la misma columna de distribución. Si una de las tablas distribuidas por hash es pequeña, considere la posibilidad de usar una tabla replicada. Una combinación en una tabla Round Robin requiere movimiento de datos. Se recomienda usar tablas replicadas en lugar de tablas Round Robin en la mayoría de los casos. 


Considere la posibilidad de convertir una tabla distribuida existente en una tabla replicada:

- Los planes de consulta usan operaciones de movimiento de datos que difunden los datos a todos los nodos de proceso. La operación BroadcastMoveOperation usa muchos recursos y reduce el rendimiento de las consultas. Para ver las operaciones de movimiento de datos en los planes de consulta, use [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).
 
Es posible que las tablas replicadas no produzcan el mejor rendimiento de las consultas cuando:

- La tabla tiene operaciones frecuentes de inserción, actualización y eliminación. Estas operaciones de lenguaje de manipulación de datos (DML) requieren una recompilación de la tabla replicada. La recompilación puede provocar con frecuencia un rendimiento más lento.
- El almacenamiento de datos se escala con frecuencia. El escalado de un almacenamiento de datos cambia el número de nodos de proceso, lo que produce una recompilación.
- La tabla tiene un gran número de columnas, pero las operaciones de datos normalmente solo tienen acceso a un número de columnas reducido. En este escenario, en lugar de replicar toda la tabla, podría ser más eficaz distribuir por hash la tabla y después crear un índice en las columnas a las que se tiene acceso con frecuencia. Cuando una consulta necesita el movimiento de datos, SQL Data Warehouse solo mueve datos en las columnas solicitadas. 



## <a name="use-replicated-tables-with-simple-query-predicates"></a>Usar tablas replicadas con predicados de consulta simples
Antes de elegir entre distribuir o duplicar una tabla, piense en los tipos de consultas que se van a ejecutar en la tabla. Siempre que sea posible,

- Use tablas replicadas para consultas con predicados de consulta simples, como los de igualdad o desigualdad.
- Use tablas replicadas para consultas con predicados de consulta complejos, como los de LIKE o NOT LIKE.

Las consultas que consumen más CPU ofrecen un mejor comportamiento cuando el trabajo se distribuye entre todos los nodos de proceso. Por ejemplo, las consultas que ejecutan cálculos en todas las filas de una tabla funcionan mejor en tablas distribuidas que en tablas replicadas. Como una tabla replicada se almacena por completo en cada nodo de proceso, se ejecuta una consulta con gran consumo de CPU sobre una tabla replicada en toda la tabla en cada nodo de proceso. El cálculo adicional puede ralentizar el rendimiento de la consulta.

Por ejemplo, esta consulta tiene un predicado complejo.  Se ejecuta con mayor rapidez cuando el proveedor está una tabla distribuida en lugar de una tabla replicada. En este ejemplo, el proveedor se puede distribuir por hash o mediante el método Round Robin.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Convertir tablas Round Robin existentes en tablas replicadas
Si ya tiene tablas Round Robin, se recomienda convertirlas en tablas replicadas si cumplen con los criterios que se describen en este artículo. Las tablas replicadas mejoran el rendimiento con respecto a las tablas Round Robin porque eliminan la necesidad del movimiento de datos.  Una tabla Round Robin siempre requiere movimiento de datos para las combinaciones. 

En este ejemplo se usa [CTAS](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para convertir la tabla DimSalesTerritory en una tabla replicada. Este ejemplo funciona independientemente de si DimSalesTerritory se distribuye por hash o Round Robin.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

--Create statistics on new table
CREATE STATISTICS [SalesTerritoryKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryKey]);
CREATE STATISTICS [SalesTerritoryAlternateKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryAlternateKey]);
CREATE STATISTICS [SalesTerritoryRegion] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryRegion]);
CREATE STATISTICS [SalesTerritoryCountry] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryCountry]);
CREATE STATISTICS [SalesTerritoryGroup] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryGroup]);

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Ejemplo de rendimiento de consultas para tablas Round Robin frente a tablas replicadas 

Una tabla replicada no requiere ningún movimiento de datos para las combinaciones porque la tabla completa ya está presente en todos los nodo de proceso. Si las tablas de dimensiones se distribuyen con el método Round Robin, una combinación copia la tabla de dimensiones completa en todos los nodos de proceso. Para mover los datos, el plan de consulta contiene una operación llamada BroadcastMoveOperation. Este tipo de operación de movimiento de datos reduce el rendimiento de las consultas y se elimina mediante el uso de tablas replicadas. Para ver los pasos del plan de consulta, use la vista de catálogo del sistema [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql). 

Por ejemplo, en la siguiente consulta en el esquema de AdventureWorks, la tabla ` FactInternetSales` se distribuye por hash. Las tablas `DimDate` y `DimSalesTerritory` son tablas de dimensiones más pequeñas. Esta consulta devuelve el total de ventas en América del Norte para el año fiscal 2004:
 
```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
Se vuelve a crear `DimDate` y `DimSalesTerritory` como tablas Round Robin. Como resultado, la consulta muestra el plan de consulta siguiente, que tiene varias operaciones de movimiento de difusión: 
 
![Plan de consulta de round robin](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Se vuelve a crear `DimDate` y `DimSalesTerritory` como tablas replicadas y se ejecuta la consulta de nuevo. El plan de consulta resultante es mucho más corto y no tiene ningún movimiento de difusión.

![Plan de consulta replicado](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Consideraciones de rendimiento para modificar tablas replicadas
SQL Data Warehouse implementa una tabla replicada mediante el mantenimiento de una versión principal de la tabla. Copia la versión principal a una base de datos de distribución en cada nodo de proceso. Cuando se produce un cambio, SQL Data Warehouse actualiza primero la tabla principal. Después, requiere una recompilación de las tablas en cada nodo de proceso. Una recompilación de una tabla replicada incluye copiar la tabla en todos los nodos de proceso y, después, volver a generar los índices.

Las recompilaciones son necesarias después de que:
- Se carguen o modifiquen datos.
- El almacenamiento de datos se escale a un [nivel de servicio](performance-tiers.md#service-levels) diferente
- Se actualice la definición de tabla

Las recompilaciones no son necesarias después de que:
- Se pause la operación.
- Se reanude la operación.

La regeneración no se produzca inmediatamente después de que se modifiquen los datos. En su lugar, la regeneración se desencadena la primera vez que una consulta realiza una selección en la tabla.  En la instrucción de selección inicial de la tabla se incluyen los pasos para recompilar la tabla replicada.  Dado que la recompilación se realiza dentro de la consulta, el impacto en la instrucción de selección inicial podría ser significativo según el tamaño de la tabla.  Si intervienen varias tablas replicadas que necesitan una recompilación, cada copia se recompila en serie como pasos dentro de la instrucción.  Para mantener la coherencia de datos durante la recompilación de la tabla replicada, se toma un bloqueo exclusivo en la tabla.  El bloqueo impide el acceso a la tabla mientras dure la recompilación. 

### <a name="use-indexes-conservatively"></a>Usar índices de manera conservadora
Las prácticas recomendadas de indexación estándar se aplican a las tablas replicadas. SQL Data Warehouse recompila el índice de cada tabla replicada como parte de la recompilación. Use los índices solo cuando el aumento de rendimiento supere con creces el coste de recompilar los índices.  
 
### <a name="batch-data-loads"></a>Cargas de datos por lotes
Al cargar datos en las tablas replicadas, intente reducir las recompilaciones procesando las cargas por lotes. Ejecute todas las cargas por lotes antes de ejecutar las instrucciones de selección.

Por ejemplo, en este modelo de carga se cargan datos de cuatro orígenes y se invocan cuatro recompilaciones. 

- Carga desde el origen 1.
- La instrucción de selección desencadena la recompilación 1.
- Carga desde el origen 2.
- La instrucción de selección desencadena la recompilación 2.
- Carga desde el origen 3.
- La instrucción de selección desencadena la recompilación 3.
- Carga desde el origen 4.
- La instrucción de selección desencadena la recompilación 4.

Por ejemplo, en este modelo de carga se cargan datos de cuatro orígenes pero solo se invoca una recompilación.

- Carga desde el origen 1.
- Carga desde el origen 2.
- Carga desde el origen 3.
- Carga desde el origen 4.
- La instrucción de selección desencadena la recompilación.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Recompilar una tabla replicada después de una carga por lotes
Para garantizar tiempos de ejecución de consultas coherentes, se recomienda forzar una actualización de las tablas replicadas después de una carga por lotes. En caso contrario, la primera consulta debe esperar a que las tablas se actualicen, lo que incluye recompilar los índices. Según el tamaño y el número de tablas replicadas afectadas, el impacto en el rendimiento puede ser significativo.  

Esta consulta usa la DMV [sys.pdw_replicated_table_cache_state](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) para enumerar las tablas replicadas que se han modificado, pero no recompilado.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Para forzar una recompilación, ejecute la siguiente instrucción en cada tabla de la salida anterior. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Pasos siguientes 
Para crear una tabla replicada, use una de estas instrucciones:

- [CREATE TABLE (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE AS SELECT (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Para obtener información general de las tablas distribuidas, vea [tablas distribuidas](sql-data-warehouse-tables-distribute.md).



