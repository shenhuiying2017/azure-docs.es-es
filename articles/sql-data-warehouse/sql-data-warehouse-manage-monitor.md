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
   ms.date="07/22/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# Supervisión de la carga de trabajo mediante DMV

En este artículo se describe cómo usar las vistas de administración dinámica (DMV) para supervisar la carga de trabajo e investigar la ejecución de la consulta en Almacenamiento de datos SQL de Azure.

## Supervisión de conexiones

La vista [sys.dm\_pdw\_exec\_sessions][] le permite supervisar las conexiones con la base de datos del almacenamiento de datos SQL de Azure. Esta vista contiene sesiones activas, así como un historial de las sesiones desconectadas recientemente. El campo session\_id es la clave principal de esta vista y se asigna de forma secuencial para cada nuevo inicio de sesión.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## Supervisión de ejecuciones de consultas

Para supervisar la ejecución de la consulta, comience por [sys.dm\_pdw\_exec\_requests][]. Esta vista contiene las consultas en curso, así como un historial de las consultas que se han completado recientemente. El campo request\_id identifica cada consulta de forma única y es la clave principal de esta vista. El campo request\_id se asigna de forma secuencial para cada nueva consulta. Al consultar en esta tabla sobre un campo session\_id determinado, se mostrarán todas las consultas de un inicio de sesión concreto.

>[AZURE.NOTE] Los procedimientos almacenados utilizan varios request\_ids. Los identificadores de solicitudes se asignarán en orden secuencial.

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
```

En los resultados de la consulta anterior, **fíjese en el id. de solicitud** de la consulta que quiera investigar.

Las consultas con el estado Suspendido se ponen en cola debido a los límites de simultaneidad que se explican con más profundidad en [Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL][]. Estas consultas también aparecerán en la consulta de sys.dm\_pdw\_waits con un tipo de UserConcurrencyResourceType. Las consultas también pueden esperar por otros motivos, como los bloqueos. Si la consulta está esperando un recurso, consulte la sección [Supervisión de consultas en espera][].

### PASO 2: Búsqueda del paso de ejecución más largo del plan de consultas

En [sys.dm\_pdw\_request\_steps][], use el identificador de solicitud para recuperar una lista con los pasos del plan de consultas. Para encontrar el paso de larga ejecución, observe el tiempo total transcurrido.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Compruebe la columna *operation\_type* del paso de consulta de larga ejecución y anote el valor de **Step Index**:

- Continúe con el paso 3a sobre **operaciones SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Continúe con el paso 3b sobre **operaciones de movimiento de datos**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### PASO 3a: Búsqueda del progreso de la ejecución de un paso de SQL

Use el identificador de solicitud y el índice del paso para recuperar información de [sys.dm\_pdw\_sql\_requests][], que contiene detalles sobre la ejecución de la consulta en las instancias distribuidas de SQL Server.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Si la consulta se está ejecutando, [DBCC PDW\_SHOWEXECUTIONPLAN][] se puede utilizar para recuperar el plan estimado de SQL Server de la caché de planes de SQL Server para el paso de SQL que se está ejecutando en una distribución particular.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### PASO 3b: Búsqueda del progreso de la ejecución de un paso de DMS

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

Si se percata de que la consulta no avanza porque está esperando un recurso, a continuación puede encontrar una consulta que muestra todos los recursos que está esperando una consulta. Repe

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

<!---HONumber=AcomDC_0727_2016-->