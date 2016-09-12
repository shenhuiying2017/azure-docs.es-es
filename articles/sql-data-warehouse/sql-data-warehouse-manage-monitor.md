<properties
   pageTitle="Supervisión de la carga de trabajo mediante DMV | Microsoft Azure"
   description="Obtenga información sobre cómo supervisar la carga de trabajo mediante DMV."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/28/2016"
   ms.author="sonyama;barbkess"/>

# Supervisión de la carga de trabajo mediante DMV

En este artículo se describe cómo usar las vistas de administración dinámica (DMV) para supervisar la carga de trabajo e investigar la ejecución de la consulta en Almacenamiento de datos SQL de Azure.

## Supervisión de conexiones

Todos los inicios de sesión en Almacenamiento de datos SQL se registran en [sys.dm\_pdw\_exec\_sessions][]. Esta DMV contiene los 10.000 últimos inicios de sesión. El campo session\_id es la clave principal y se asigna de forma secuencial para cada nuevo inicio de sesión.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## Supervisión de ejecuciones de consultas

Todas las consultas ejecutadas en Almacenamiento de datos SQL se registran en [sys.dm\_pdw\_exec\_requests][]. Esta DMV contiene las últimas 10.000 consultas ejecutadas. El campo request\_id identifica cada consulta de forma única y es la clave principal de esta DMV. Este campo se asigna de forma secuencial para cada nueva consulta y lleva el prefijo QID, que representa el identificador de consulta. Al consultar en esta DMV sobre un campo session\_id determinado, se muestran todas las consultas de un inicio de sesión concreto.

>[AZURE.NOTE] Los procedimientos almacenados utilizan varios identificadores de solicitud. Los identificadores de solicitud se asignan en orden secuencial.

Estos son los pasos que deben seguirse para investigar los planes de ejecución de consultas y las horas de una consulta determinada.

### PASO 1: Identificación de la consulta que quiere investigar

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

En los resultados de la consulta anterior, **fíjese en el id. de solicitud** de la consulta que quiere investigar.

Las consultas con estado **suspendido** se ponen en cola debido a los límites de simultaneidad. Estas consultas también aparecen en la consulta sys.dm\_pdw\_waits con un tipo de UserConcurrencyResourceType. Consulte [Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL][] para obtener más detalles sobre los límites de simultaneidad. Las consultas también pueden esperar por otros motivos, como los bloqueos de objetos. Si la consulta está esperando un recurso, consulte la sección [Supervisión de consultas en espera][] más adelante en este artículo.

Para simplificar la búsqueda de una consulta en la tabla sys.dm\_pdw\_exec\_requests, utilice [LABEL][] para asignar un comentario a la consulta que se pueda buscar en la vista sys.dm\_pdw\_exec\_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### PASO 2: investigación del plan de consulta

Use el identificador de solicitud para recuperar el plan de SQL distribuido (DSQL) de la consulta desde [sys.dm\_pdw\_request\_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Si un plan DSQL tarda más de lo esperado, es posible que sea un plan complejo con muchos pasos DSQL o con un solo paso que tarda mucho tiempo. Si el plan tiene muchos pasos con varias operaciones de movimiento, considere la posibilidad de optimizar las distribuciones de la tabla para reducir el movimiento de datos. El artículo [Distribución de tablas][] explica por qué los datos se deben mover para resolver una consulta y describe algunas estrategias de distribución para minimizar el movimiento de datos.

Para investigar más detalles acerca de un solo paso, compruebe la columna *operation\_type* del paso de consulta de larga ejecución y anote el valor de **Índice de pasos**:

- Continúe con el paso 3a sobre **operaciones SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Continúe con el paso 3b sobre **operaciones de movimiento de datos**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### PASO 3a: investigación de SQL en las bases de datos distribuidas

Use el identificador de solicitud y el índice de pasos para recuperar información de [sys.dm\_pdw\_sql\_requests][], que contiene detalles sobre la ejecución del paso de la consulta en todas las instancias distribuidas.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Si la consulta se está ejecutando, se puede utilizar [DBCC PDW\_SHOWEXECUTIONPLAN][] para recuperar el plan estimado de SQL Server de la caché de planes de SQL Server para el paso que se está ejecutando en una distribución particular.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### PASO 3b: investigación del movimiento de datos en las bases de datos distribuidas

Use el identificador de solicitud y el índice de paso para recuperar información sobre el paso de movimiento de datos que se ejecuta en cada distribución desde [sys.dm\_pdw\_dms\_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Compruebe la columna *total\_elapsed\_time* para ver si una distribución determinada tarda bastante más que otras en el movimiento de datos.
- Para la distribución de larga ejecución, compruebe la columna *rows\_processed* para ver si el número de filas que se mueven desde esa distribución es mucho mayor que para las demás. En ese caso, esto puede indicar un sesgo de los datos subyacentes.

Si la consulta se está ejecutando, [DBCC PDW\_SHOWEXECUTIONPLAN][] se puede utilizar para recuperar el plan estimado de SQL Server de la caché de planes de SQL Server para el paso de SQL que se está ejecutando en una distribución particular.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## Supervisión de consultas en espera

Si observa que la consulta no avanza porque está esperando un recurso, a continuación puede encontrar una consulta que muestra todos los recursos que está esperando una consulta.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Si la consulta espera activamente recursos de otra consulta, el estado será **AcquireResources**. Si la consulta tiene todos los recursos necesarios, el estado será **Concedido**.

## Pasos siguientes
Para obtener más información sobre las vistas de administración dinámica (DMV), consulte [Vistas de sistema][]. Para ver sugerencias sobre cómo administrar el Almacenamiento de datos SQL, consulte [Herramientas de administración para Almacenamiento de datos SQL][]. Paras consultar procedimientos recomendados, lea el artículo [Procedimientos recomendados para Almacenamiento de datos SQL de Azure][].

<!--Image references-->

<!--Article references-->
[Herramientas de administración para Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-manage.md
[Procedimientos recomendados para Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-best-practices.md
[Vistas de sistema]: ./sql-data-warehouse-reference-tsql-system-views.md
[Distribución de tablas]: ./sql-data-warehouse-tables-distribute.md
[Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL]: ./sql-data-warehouse-develop-concurrency.md
[Supervisión de consultas en espera]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

<!---HONumber=AcomDC_0831_2016-->