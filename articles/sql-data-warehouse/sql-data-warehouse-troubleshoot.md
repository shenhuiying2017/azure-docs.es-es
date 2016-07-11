<properties
   pageTitle="Solución de problemas de Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Cómo solucionar los problemas de Almacenamiento de datos SQL de Azure."
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
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess"/>

# Solución de problemas de Almacenamiento de datos SQL de Azure
En el siguiente tema se muestran algunos de los problemas más comunes que podría encontrarse con Almacenamiento de datos SQL de Azure.


##Errores de conexión

Si tiene problemas de conexión, a continuación presentamos algunos de los problemas más comunes notificados por los clientes.

### Error CTAIP
Este error puede producirse cuando se ha creado un inicio de sesión en la base de datos maestra de SQL Server, pero no en la base de datos de Almacenamiento de datos SQL. Si se produce este error, eche un vistazo al artículo sobre la [información general de seguridad][]. En este artículo se explica cómo crear un inicio de sesión en una base de datos maestra y luego cómo crear un usuario en la base de datos de Almacenamiento de datos SQL.

### Reglas de firewall
Las bases de datos SQL de Azure están protegidas por firewalls de nivel de servidor y base de datos para garantizar que solo las direcciones IP conocidas tienen acceso a una base de datos. Los firewalls están protegidos de manera predeterminada, lo que significa que debe habilitar explícitamente una dirección IP o un intervalo de direcciones para poder conectarse. Para configurar el firewall para el acceso, siga los pasos de la sección [Configure server firewall access for your client IP][] (Configurar el acceso del firewall del servidor para su IP de cliente) en las [instrucciones de aprovisionamiento][].

### Uso de herramientas o protocolos no admitidos
Almacenamiento de datos SQL recomienda el uso de [Visual Studio 2013 o 2015][] para consultar los datos. Para la conectividad de cliente, se recomienda [SQL Server Native Client 10/11 (ODBC)][]. Aún no se admite SQL Server Management Studio (SSMS) y, aunque funciona parcialmente, el árbol del explorador de objetos no funciona con Almacenamiento de datos SQL y es posible que funcione la consulta después de ignorar algunos mensajes de error.


## Problemas de rendimiento

El mejor lugar para empezar a aprender formas de mejorar el rendimiento de las consultas es el artículo [Procedimientos recomendados para Almacenamiento de datos SQL de Azure][]. Si está intentando solucionar problemas de una consulta determinada, otro buen lugar para empezar es el artículo para [aprender a supervisar las consultas][]. A veces, para que una consulta se ejecute más rápido, basta con agregar más potencia de proceso a las consultas [escalando el Almacenamiento de datos SQL][].

## Calidad de segmento de almacén de columnas en clúster

La calidad de segmento de almacén de columnas en clúster es importante para lograr un rendimiento de consultas óptimo de las tablas de almacén de columnas en clúster. La calidad de segmento se puede medir por el número de filas de un grupo de filas comprimido. La siguiente consulta identificará las tablas con segmentos de índices de almacén de columnas en mal estado y generará T-SQL para recompilar el índice de almacén de columnas en estas tablas. La primera columna de este resultado de la consulta proporcionará T-SQL para recompilar cada uno de los índices. La segunda columna proporcionará una recomendación de la clase de recurso mínima que debe usarse para optimizar la compresión.
 
**PASO 1:** ejecute esta consulta en cada base de datos de Almacenamiento de datos SQL para identificar los índices de almacén de columnas en clúster que no son óptimos. Si no se devuelven filas, no es necesario realizar ninguna acción adicional.

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
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000 OR
    AVG(CASE WHEN rg.State = 0 THEN rg.Total_rows ELSE NULL END) < 100000

ORDER BY 
    s.name, t.name
```
 
**PASO 2:** aumente la clase de recurso de un usuario que tenga permisos para recompilar el índice de esta tabla en la clase de recurso recomendada de la segunda columna de la consulta anterior.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  LoadUser anterior debe tratarse de un usuario válido creado para ejecutar la instrucción ALTER INDEX. No se puede cambiar la clase de recurso del usuario db\_owner. En el vínculo siguiente encontrará más información sobre las clases de recurso y cómo crear un nuevo usuario.

 
**PASO 3:** inicie sesión como usuario del paso 2 (por ejemplo, "LoadUser"), que ahora usa una clase de recurso superior, y ejecute las instrucciones ALTER INDEX generadas por la consulta en el PASO 1. Asegúrese de que este usuario tiene el permiso ALTER para las tablas identificadas en la consulta del PASO 1.
 
**PASO 4:** vuelva a ejecutar la consulta del paso 1. Si los índices se han creado de forma eficaz, esta consulta no debe devolver ninguna fila. Si no se devuelve ninguna fila, ya ha terminado. Si tiene varias bases de datos de Almacenamiento de datos SQL, querrá repetir este proceso en cada una de sus bases de datos. Si se devuelven filas, continúe con el paso 5.
 
**PASO 5:** si se devuelven filas al volver a ejecutar la consulta del paso 1, es posible que tenga tablas con filas más anchas que necesiten una elevada cantidad de memoria para crear de forma óptima los índices de almacén de columnas en clúster. Si es así, pruebe de nuevo este proceso para las tablas que usan la clase xlargerc. Para cambiar la clase de recurso, repita el paso 2 mediante xlargerc. A continuación, repita el paso 3 para las tablas que aún tienen índices que no son óptimos. Si usa DW100 - DW300 y ya ha utilizado xlargerc, puede elegir entre dejar los índices tal cual o aumentar DWU de forma temporal para proporcionar más memoria a esta operación.
 
**PASOS FINALES:** la clase de recurso designada anteriormente es la clase de recurso mínima recomendada para crear los índices de almacén de columnas de mayor calidad. Se recomienda conservar esta configuración para el usuario que carga sus datos. Sin embargo, si desea deshacer el cambio del paso 2, puede hacerlo con el siguiente comando.

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```

Las instrucciones de carga a una tabla de CCI para la clase de recurso mínima consisten en usar xlargerc para DW100 a DW300, largerc para DW400 a DW600 y mediumrc para cualquier Almacenamiento de datos igual o mayor que 1000. Estas instrucciones son recomendables para la mayoría de las cargas de trabajo. El objetivo es dar a cada operación de creación de índices 400 MB de memoria o más. Sin embargo, no hay una solución única para todos los casos. La memoria necesaria para optimizar un índice de almacén de columnas depende de los datos cargados, que se ven principalmente afectados por el tamaño de fila. Las tablas con anchos de fila más reducidos necesitan menos memoria, mientras que los anchos de fila más amplios necesitan más. Si desea experimentar, puede usar la consulta del Paso 1 para ver si obtiene índices de almacén de columnas óptimos en asignaciones de memoria más pequeñas. Como mínimo, desea una media de más de 100 000 filas por cada grupo de filas. Más de 500 000 es mejor aún. Lo máximo que verá es 1 millón de filas por cada grupo de filas. Para obtener más información sobre cómo administrar clases de recurso y simultaneidad, consulte el siguiente vínculo.


## Pasos siguientes

Si no ha podido encontrar una solución a su problema con estos pasos, estos son otros recursos que puede probar.

- [Blogs]
- [Solicitud de función]
- [Vídeos]
- [Blogs del equipo de CAT]
- [Creación de una incidencia de soporte técnico]
- [Foro de MSDN]
- [Foro Stack Overflow]
- [Twitter]

<!--Image references-->

<!--Article references-->
[información general de seguridad]: ./sql-data-warehouse-overview-manage-security.md
[Creación de una incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[escalando el Almacenamiento de datos SQL]: ./sql-data-warehouse-manage-compute-overview.md
[aprender a supervisar las consultas]: ./sql-data-warehouse-manage-monitor.md
[instrucciones de aprovisionamiento]: ./sql-data-warehouse-get-started-provision.md
[configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 o 2015]: ./sql-data-warehouse-get-started-connect.md
[Procedimientos recomendados para Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs del equipo de CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitud de función]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Foro de MSDN]: https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureSQLDataWarehouse
[Foro Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0629_2016-->