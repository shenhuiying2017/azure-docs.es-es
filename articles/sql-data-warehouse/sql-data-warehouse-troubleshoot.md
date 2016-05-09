<properties
   pageTitle="Solución de problemas | Microsoft Azure"
   description="Solución de problemas del Almacenamiento de datos SQL."
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
   ms.date="04/20/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Solución de problemas
En el siguiente tema se muestran algunos de los problemas más comunes que los clientes encuentran con el Almacenamiento de datos SQL de Azure.

## Conectividad
Se puede producir un error en la conexión al Almacenamiento de datos SQL debido a dos motivos habituales:

- No se establecen las reglas de firewall.
- Uso de herramientas o protocolos no admitidos.

### Reglas de firewall
Las bases de datos SQL de Azure están protegidas por firewalls de nivel de servidor y base de datos para garantizar que solo las direcciones IP conocidas pueden tener acceso a las bases de datos. Los firewall son seguros de forma predeterminada, lo que significa que debe permitir el acceso de dirección IP para poder conectarse.

Para configurar el firewall para el acceso, siga los pasos de la sección [Configurar el acceso del firewall del servidor para su IP de cliente][] de la página [Aprovisionar][].

### Uso de herramientas o protocolos no admitidos.
Almacenamiento de datos de SQL admite [Visual Studio 2013/2015][] como entornos de desarrollo y [SQL Server Native Client 10 y 11 (ODBC)][] para conectividad de cliente.

Vea nuestras páginas [Conectar][] para más información.

## Rendimiento de las consultas

### Estadísticas

Las [estadísticas][] son objetos que contienen información sobre el intervalo y la frecuencia de valores en una columna de base de datos. El motor de consultas usa estas estadísticas para optimizar la ejecución de consultas y mejorar el rendimiento de las consultas. A diferencia de SQL Server o Base de datos SQL, Almacenamiento de datos SQL no crea ni actualiza automáticamente las estadísticas. Las estadísticas deben mantenerse manualmente en todas las tablas.

Puede usar la siguiente consulta para determinar la última vez que se actualizaron las estadísticas en cada tabla.

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

### Calidad de segmento de almacén de columnas en clúster

La calidad de segmento de almacén de columnas en clúster es importante para lograr un rendimiento de consultas óptimo de las tablas de almacén de columnas en clúster. La calidad de segmento se puede medir por el número de filas de un grupo de filas comprimido. La siguiente consulta identificará las tablas con segmentos de índices de almacén de columnas en mal estado y generará T-SQL para recompilar el índice de almacén de columnas en estas tablas. La primera columna de este resultado de la consulta proporcionará T-SQL para recompilar cada uno de los índices. La segunda columna proporcionará una recomendación de la clase de recurso mínima que debe usarse para optimizar la compresión.
 
**PASO 1:** ejecute esta consulta en cada base de datos de Almacenamiento de datos SQL para identificar los índices de almacén de columnas en clúster que no son óptimos. Si no se devuelve ninguna fila, esta regresión no le ha afectado y no será necesario hacer nada más.

```sql
SELECT 
     'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;' AS [T-SQL to Rebuild Index]
    ,CASE WHEN n.nbr_nodes < 3 THEN 'xlargerc' WHEN n.nbr_nodes BETWEEN 4 AND 6 THEN 'largerc' ELSE 'mediumrc' END AS [Resource Class Recommendation]
    ,s.name AS [Schema Name]
    ,t.name AS [Table Name]
    ,AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) AS [Ave Rows in Compressed Row Groups]
FROM 
    sys.pdw_nodes_column_store_row_groups rg
    JOIN sys.pdw_nodes_tables pt 
        ON rg.object_id = pt.object_id AND rg.pdw_node_id = pt.pdw_node_id AND pt.distribution_id = rg.distribution_id
    JOIN sys.pdw_table_mappings tm 
        ON pt.name = tm.physical_name
    INNER JOIN sys.tables t 
        ON tm.object_id = t.object_id
INNER JOIN sys.schemas s
    ON t.schema_id = s.schema_id
CROSS JOIN (SELECT COUNT(*) nbr_nodes  FROM sys.dm_pdw_nodes WHERE type = 'compute') n
GROUP BY 
    n.nbr_nodes, s.name, t.name
HAVING 
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000
ORDER BY 
    s.name, t.name
```
 
**PASO 2:** aumente la clase de recurso de un usuario que tenga permisos para recompilar el índice de esta tabla en la clase de recurso recomendada de la segunda columna de la consulta anterior.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  LoadUser anterior debe tratarse de un usuario válido creado para ejecutar la instrucción ALTER INDEX. No se puede cambiar la clase de recurso del usuario db\_owner. En el vínculo siguiente encontrará más información sobre las clases de recurso y cómo crear un nuevo usuario.

 
**PASO 3:** inicie sesión como usuario del paso 2 (por ejemplo, “LoadUser”), que ahora usa una clase de recurso superior y ejecute las instrucciones ALTER INDEX generadas por la consulta en el PASO 1. Asegúrese de que este usuario tiene el permiso ALTER para las tablas identificadas en la consulta del PASO 1.
 
**PASO 4:** vuelva a ejecutar la consulta del paso 1. Si los índices se han creado de forma eficaz, esta consulta no debe devolver ninguna fila. Si no se devuelve ninguna fila, ya ha terminado. Si tiene varias bases de datos de Almacenamiento de datos SQL, querrá repetir este proceso en cada una de sus bases de datos. Si se devuelven filas, continúe con el paso 5.
 
**PASO 5:** si se devuelven filas al volver a ejecutar la consulta del paso 1, es posible que tenga tablas con filas más anchas que necesiten una elevada cantidad de memoria para crear de forma óptima los índices de almacén de columnas en clúster. Si es así, pruebe de nuevo este proceso para las tablas que usan la clase xlargerc. Para cambiar la clase de recurso, repita el paso 2 mediante xlargerc. A continuación, repita el paso 3 para las tablas que aún tienen índices que no son óptimos. Si usa DW100 - DW300 y ya ha utilizado xlargerc, puede elegir entre dejar los índices tal cual o aumentar DWU de forma temporal para proporcionar más memoria a esta operación.
 
**PASOS FINALES:** la clase de recurso designada anteriormente es la clase de recurso mínima recomendada para crear los índices de almacén de columnas de mayor calidad. Se recomienda conservar esta configuración para el usuario que carga sus datos. Sin embargo, si desea deshacer el cambio del paso 2, puede hacerlo con el siguiente comando.

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```


Las instrucciones de carga a una tabla de CCI para la clase de recurso mínima consisten en usar xlargerc para DW100-DW300, largerc para DW400-DW600 y mediumrc para cualquier Almacenamiento de datos igual o mayor que 1000. Estas instrucciones son recomendables para la mayoría de las cargas de trabajo. El objetivo es dar a cada operación de creación de índices 400 MB de memoria o más. Sin embargo, no hay una solución única para todos los casos. La memoria necesaria para optimizar un índice de almacén de columnas depende de los datos cargados, que se ven principalmente afectados por el tamaño de fila. Las tablas con anchos de fila más reducidos necesitan menos memoria, mientras que los anchos de fila más amplios necesitan más. Si desea experimentar, puede usar la consulta del Paso 1 para ver si obtiene índices de almacén de columnas óptimos en asignaciones de memoria más pequeñas. Como mínimo, desea una media de más de 100 000 filas por cada grupo de filas. Más de 500 000 es mejor aún. Lo máximo que verá es 1 millón de filas por cada grupo de filas. Para obtener más información sobre cómo administrar clases de recurso y simultaneidad, consulte el siguiente vínculo.


### Principales conceptos sobre rendimiento

Consulte los artículos siguientes para comprender mejor algunos conceptos fundamentales adicionales sobre rendimiento y escala:

- [rendimiento y escala][]
- [modelo de simultaneidad][]
- [diseño de tablas][]
- [selección de una clave de distribución hash para una tabla][]

## Pasos siguientes
Consulte el artículo [Introducción al desarrollo][] para obtener orientación sobre la creación de soluciones de Almacenamiento de datos SQL.

<!--Image references-->

<!--Article references-->
[rendimiento y escala]: sql-data-warehouse-performance-scale.md
[modelo de simultaneidad]: sql-data-warehouse-develop-concurrency.md
[diseño de tablas]: sql-data-warehouse-develop-table-design.md
[selección de una clave de distribución hash para una tabla]: sql-data-warehouse-develop-hash-distribution-key
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md
[Aprovisionar]: sql-data-warehouse-get-started-provision.md
[Configurar el acceso del firewall del servidor para su IP de cliente]: sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip
[Visual Studio 2013/2015]: sql-data-warehouse-get-started-connect.md
[Conectar]: sql-data-warehouse-get-started-connect.md
[estadísticas]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[SQL Server Native Client 10 y 11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other web references-->

<!---HONumber=AcomDC_0427_2016-->