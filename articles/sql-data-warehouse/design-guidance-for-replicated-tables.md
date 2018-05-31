---
title: 'Instrucciones de diseño para las tablas replicadas: Azure SQL Data Warehouse | Microsoft Docs'
description: Recomendaciones para el diseño de tablas replicadas en el esquema de Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/23/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 1cc796061056ff017e3d778ebb2e50e13d55a4c1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189571"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Instrucciones de diseño para el uso de tablas replicadas en Azure SQL Data Warehouse
En este artículo se proporcionan recomendaciones para el diseño de tablas replicadas en el esquema de SQL Data Warehouse. Siga estas recomendaciones para mejorar el rendimiento de las consultas al reducir el movimiento de datos y la complejidad de las consultas.

## <a name="prerequisites"></a>requisitos previos
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
- La tabla se usa en combinaciones que en caso contrario requerirían el movimiento de datos. Al unir tablas que no están distribuidas en la misma columna, como una tabla distribuida mediante hash a una tabla round robin, es necesario realizar la operación de movimiento de datos para completar la consulta.  Si una de las tablas es pequeña, considere la posibilidad de usar una tabla replicada. Se recomienda usar tablas replicadas en lugar de tablas Round Robin en la mayoría de los casos. Para ver las operaciones de movimiento de datos en los planes de consulta, use [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  BroadcastMoveOperation es la típica operación de movimiento de datos que se puede eliminar mediante una tabla replicada.  
 
Es posible que las tablas replicadas no produzcan el mejor rendimiento de las consultas cuando:

- La tabla tiene operaciones frecuentes de inserción, actualización y eliminación. Estas operaciones de lenguaje de manipulación de datos (DML) requieren una recompilación de la tabla replicada. La recompilación puede provocar con frecuencia un rendimiento más lento.
- El almacenamiento de datos se escala con frecuencia. El escalado de un almacenamiento de datos cambia el número de nodos de proceso, lo que produce una recompilación.
- La tabla tiene un gran número de columnas, pero las operaciones de datos normalmente solo tienen acceso a un número de columnas reducido. En este escenario, en lugar de replicar toda la tabla, podría ser más eficaz distribuir la tabla y después crear un índice en las columnas a las que se tiene acceso con frecuencia. Cuando una consulta necesita el movimiento de datos, SQL Data Warehouse solo mueve los datos de las columnas solicitadas. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Usar tablas replicadas con predicados de consulta simples
Antes de elegir entre distribuir o duplicar una tabla, piense en los tipos de consultas que se van a ejecutar en la tabla. Siempre que sea posible,

- Use tablas replicadas para consultas con predicados de consulta simples, como los de igualdad o desigualdad.
- Use tablas replicadas para consultas con predicados de consulta complejos, como los de LIKE o NOT LIKE.

Las consultas que consumen más CPU ofrecen un mejor comportamiento cuando el trabajo se distribuye entre todos los nodos de proceso. Por ejemplo, las consultas que ejecutan cálculos en todas las filas de una tabla funcionan mejor en tablas distribuidas que en tablas replicadas. Como una tabla replicada se almacena por completo en cada nodo de proceso, se ejecuta una consulta con gran consumo de CPU sobre una tabla replicada en toda la tabla en cada nodo de proceso. El cálculo adicional puede ralentizar el rendimiento de la consulta.

Por ejemplo, esta consulta tiene un predicado complejo.  Se ejecuta con mayor rapidez cuando el proveedor está una tabla distribuida en lugar de una tabla replicada. En este ejemplo, el proveedor se puede distribuir mediante el método round robin.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Convertir tablas Round Robin existentes en tablas replicadas
Si ya tiene tablas Round Robin, se recomienda convertirlas en tablas replicadas si cumplen con los criterios que se describen en este artículo. Las tablas replicadas mejoran el rendimiento con respecto a las tablas Round Robin porque eliminan la necesidad del movimiento de datos.  Una tabla Round Robin siempre requiere movimiento de datos para las combinaciones. 

En este ejemplo se usa [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para convertir la tabla DimSalesTerritory en una tabla replicada. Este ejemplo funciona independientemente de si DimSalesTerritory se distribuye por hash o Round Robin.

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

Una tabla replicada no requiere ningún movimiento de datos para las combinaciones porque la tabla completa ya está presente en todos los nodo de proceso. Si las tablas de dimensiones se distribuyen con el método Round Robin, una combinación copia la tabla de dimensiones completa en todos los nodos de proceso. Para mover los datos, el plan de consulta contiene una operación llamada BroadcastMoveOperation. Este tipo de operación de movimiento de datos reduce el rendimiento de las consultas y se elimina mediante el uso de tablas replicadas. Para ver los pasos del plan de consulta, use la vista de catálogo del sistema [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql). 

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
SQL Data Warehouse implementa una tabla replicada mediante el mantenimiento de una versión principal de la tabla. Copia la versión principal a una base de datos de distribución en cada nodo de proceso. Cuando se produce un cambio, SQL Data Warehouse actualiza primero la tabla principal. A continuación, recompila las tablas en cada nodo de ejecución. Una recompilación de una tabla replicada incluye copiar la tabla en todos los nodos de ejecución y, a continuación, compilar los índices.  Por ejemplo, una tabla replicada en un nivel DW400 tiene 5 copias de los datos.  Una copia maestra y una copia completa en cada nodo de ejecución.  Todos los datos se almacenan en bases de datos de distribución. SQL Data Warehouse utiliza este modelo para admitir instrucciones de modificación de datos más rápidas y operaciones de escalado flexibles. 

Las recompilaciones son necesarias después de que:
- Se carguen o modifiquen datos.
- El almacenamiento de datos se escale a una configuración DWU diferente
- Se actualice la definición de tabla

Las recompilaciones no son necesarias después de que:
- Se pause la operación.
- Se reanude la operación.

La regeneración no se produzca inmediatamente después de que se modifiquen los datos. En su lugar, la regeneración se desencadena la primera vez que una consulta realiza una selección en la tabla.  La consulta que desencadenó la reconstrucción se lee inmediatamente en la versión maestra de la tabla, mientras que los datos se copian de forma asincrónica en cada nodo de ejecución. Hasta que se no complete la copia de datos, las consultas posteriores continuarán utilizando la versión maestra de la tabla.  Si se produce alguna actividad en la tabla replicada que obligue a realizar otro proceso de recompilación, la copia de datos se invalida y la siguiente instrucción que se seleccione desencadenará de nuevo la copia de datos. 

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
Para garantizar tiempos de ejecución de consultas coherentes, se recomienda forzar la compilación de las tablas replicadas después de una carga por lotes. En caso contrario, la primera consulta seguirá usando el movimiento de datos para completar la consulta. 

Esta consulta usa la DMV [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) para enumerar las tablas replicadas que se han modificado, pero no recompilado.

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
 
Para desencadenar una recompilación, ejecute la siguiente instrucción en cada tabla del resultado anterior. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Pasos siguientes 
Para crear una tabla replicada, use una de estas instrucciones:

- [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE AS SELECT (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Para obtener información general de las tablas distribuidas, vea [tablas distribuidas](sql-data-warehouse-tables-distribute.md).



