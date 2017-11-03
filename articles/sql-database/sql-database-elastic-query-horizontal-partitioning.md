---
title: Informes de bases de datos escaladas horizontalmente en la nube | Microsoft Docs
description: "Configuración de las consultas elásticas en particiones horizontales"
services: sql-database
documentationcenter: 
manager: jhubbard
author: MladjoA
ms.assetid: f86eccb8-6323-4ba7-8559-8a7c039049f3
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: mlandzic
ms.openlocfilehash: 41accea2e94fc763d0dcbba709829ec07453da78
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Informes de bases de datos escaladas horizontalmente en la nube (vista previa)
![Consultas entre particiones][1]

Filas de distribución de bases de datos particionadas en un nivel de datos escalado horizontalmente. El esquema es idéntico en todas las bases de datos participantes, también conocidos como partición horizontal. Utilice una consulta elástica para crear informes que abarquen todas las bases de datos en una base de datos particionada.

Para un inicio rápido, consulte [Informes de bases de datos escaladas horizontalmente en la nube](sql-database-elastic-query-getting-started.md).

Para bases de datos no particionadas, consulte [Consulta de bases de datos elásticas para consultas entre bases de datos (particionamiento vertical)](sql-database-elastic-query-vertical-partitioning.md). 

## <a name="prerequisites"></a>Requisitos previos
* Cree un mapa de particiones con una biblioteca de cliente de bases de datos elásticas. Consulte [Administración de asignaciones particionadas](sql-database-elastic-scale-shard-map-management.md). También puede usar la aplicación de ejemplo en [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md).
* También puede consultar [Migración de bases de datos existentes a bases de datos de escalado horizontal](sql-database-elastic-convert-to-use-elastic-tools.md).
* El usuario debe poseer el permiso ALTER ANY EXTERNAL DATA SOURCE. Este permiso está incluido en el permiso ALTER DATABASE.
* Se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE para hacer referencia al origen de datos subyacente.

## <a name="overview"></a>Información general
Estas instrucciones crean la representación de los metadatos de la capa de datos particionada en la base de datos de consulta elástica. 

1. [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Creación de clave maestra y credenciales con ámbito de base de datos
La credencial utiliza la consulta elástica para conectarse a las bases de datos remotas.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Asegúrese de que *"\<nombre de usuario\>"* no incluya ningún sufijo *"@servername"*. 
> 
> 

## <a name="12-create-external-data-sources"></a>1.2 Creación de orígenes de datos externos
Sintaxis:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                              
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Ejemplo
    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );

Recuperación de la lista de orígenes de datos externos actual: 

    select * from sys.external_data_sources; 

El origen de datos externo hace referencia al mapa de particiones. Una consulta elástica usa el origen de datos externo y el mapa de particiones subyacente para enumerar las bases de datos que participan en la capa de datos. Se usan las mismas credenciales para leer el mapa de particiones y para tener acceso a los datos de las particiones durante el procesamiento de una consulta elástica. 

## <a name="13-create-external-tables"></a>1.3 Creación de tablas externas
Sintaxis:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Ejemplo**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 

    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
         SCHEMA_NAME = 'orders', 
         OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Recuperación de la lista de tablas externas de la base de datos actual: 

    SELECT * from sys.external_tables; 

Para eliminar tablas externas:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Comentarios
La cláusula DATA\_SOURCE define el origen de datos externo (un mapa de particiones) que se usa para la tabla externa.  

Las cláusulas SCHEMA\_NAME y OBJECT\_NAME asignan la definición de tabla externa a una tabla en un esquema diferente. Si se omite, se considera que el esquema del objeto remoto es "dbo" y que su nombre es idéntico al nombre de la tabla externa que se está definiendo. Esto es útil si el nombre de la tabla remota ya existe en la base de datos donde desea crear la tabla externa. Por ejemplo, quiere definir una tabla externa para obtener una vista agregada de las vistas de catálogo o DMV en la capa de datos con escalado horizontal. Puesto que las vistas de catálogo y DMV ya existen localmente, no se pueden usar sus nombres para la definición de la tabla externa. En su lugar, use un nombre diferente y el nombre de la vista de catálogo o la DMV en las cláusulas SCHEMA\_NAME u OBJECT\_NAME. (Consulte el ejemplo siguiente). 

La cláusula DISTRIBUTION especifica la distribución de datos que se usa en esta tabla. El procesador de consultas usa la información proporcionada en la cláusula DISTRIBUTION para crear los planes de consulta más eficaces.  

1. **SHARDED** significa que los datos se han particionado horizontalmente en la base de datos. La clave de creación de particiones para la distribución de datos es el parámetro **<sharding_column_name>**.
2. **REPLICATED** significa que copias idénticas de la tabla están presentes en cada base de datos. Es responsabilidad suya asegurarse de que las réplicas son idénticas en las bases de datos.
3. **ROUND\_ROBIN** significa que la tabla tiene particiones horizontales mediante un método de distribución que depende de la aplicación. 

**Referencia de capa de datos**: el DDL de tabla externa hace referencia a un origen de datos externo. El origen de datos externo especifica un mapa de particiones que proporciona a la tabla externa la información necesaria para localizar todas las bases de datos en la capa de datos. 

### <a name="security-considerations"></a>Consideraciones sobre la seguridad
Los usuarios con acceso a la tabla externa obtienen automáticamente acceso a las tablas remotas subyacentes con la credencial proporcionada en la definición del origen de datos externo. Evite la elevación no deseada de privilegios a través de la credencial del origen de datos externo. Use GRANT o REVOKE para una tabla externa como si fuera una tabla normal.  

Una vez que defina el origen de datos externo y las tablas externas, puede usar el T-SQL completo en las tablas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Ejemplo: consulta de bases de datos con particiones horizontales
La consulta siguiente realiza una combinación en tres direcciones entre almacenes, pedidos y líneas de pedido y utiliza varios agregados y un filtro selectivo. Asume (1) la partición horizontal y (2) que los almacenes, pedidos y líneas de pedido se particionan por la columna del identificador de almacén y que la consulta elástica puede colocar las combinaciones en las particiones y procesar la parte cara de la consulta en las particiones en paralelo. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Procedimiento almacenado para la ejecución remota de T-SQL: sp\_execute_remote
La consulta elástica también incluye un procedimiento almacenado que proporciona acceso directo a las particiones. El procedimiento almacenado se denomina [sp\_execute\_remote](https://msdn.microsoft.com/library/mt703714) y sirve para ejecutar procedimientos almacenados remotos o código T-SQL en bases de datos remotas. Toma los parámetros siguientes: 

* Nombre de origen de datos (nvarchar): nombre del origen de datos externo de tipo RDBMS. 
* Consulta (nvarchar): la consulta T-SQL que se va a ejecutar en cada partición. 
* Declaración de parámetro (nvarchar) - opcional: cadena con definiciones de tipos de datos de los parámetros usados en el parámetro Query (como sp_executesql). 
* Lista de valores de los parámetros (opcional): lista separada por comas de valores de los parámetros (por ejemplo, sp_executesql)

sp\_execute\_remote utiliza el origen de datos externo proporcionado en los parámetros de invocación para ejecutar la instrucción T-SQL determinada en las bases de datos remotas. Utiliza la credencial del origen de datos externo para conectarse a la base de datos de ShardMapManager y las bases de datos remotas.  

Ejemplo: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Conectividad para herramientas
Use cadenas de conexión de SQL Server normales para conectar su aplicación, sus herramientas de integración de datos o de BI a bases de datos con sus definiciones de tablas externas. Asegúrese de que SQL Server se admite como origen de datos para la herramienta. A continuación, haga referencia a la base de datos de consulta elástica como cualquier otra base de datos de SQL Server conectada a la herramienta y use las tablas externas desde su herramienta o aplicación como si fueran tablas locales. 

## <a name="best-practices"></a>Prácticas recomendadas
* Asegúrese de que se ha concedido acceso a la base de datos de puntos de conexión de consulta elástica para la base de datos del mapa de particiones y todas las particiones a través de los firewalls de la base de datos SQL.  
* Valide o aplique la distribución de datos definida por la tabla externa. Si la distribución de datos real es diferente de la distribución especificada en la definición de tabla, las consultas pueden arrojar resultados inesperados. 
* La consulta elástica actualmente no realiza la eliminación de particiones cuando los predicados de la clave de particiones permitirían excluir de forma segura determinadas bases de datos remotas del procesamiento.
* Una consulta elástica funciona mejor para consultas en que la mayor parte del cálculo se puede realizar en las particiones. Normalmente el máximo rendimiento de consultas se obtiene con predicados de filtros selectivos que se puede evaluar en las particiones o combinaciones sobre las claves de particiones que se pueden realizar en consonancia con la partición en todas las particiones. Otros patrones de consulta pueden necesitar cargar grandes cantidades de datos desde las particiones al nodo principal y pueden experimentar un rendimiento deficiente

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información general sobre las consultas elásticas, consulte [Información general sobre las consultas elásticas](sql-database-elastic-query-overview.md).
* Para obtener un tutorial sobre la creación de particiones verticales, consulte [Introducción a las consultas entre bases de datos (particiones verticales)](sql-database-elastic-query-getting-started-vertical.md).
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento vertical, consulte [Consulta de datos particionados verticalmente](sql-database-elastic-query-vertical-partitioning.md)
* Para obtener un tutorial sobre la creación de particiones horizontales (particionamiento), consulte [Introducción a las consultas elásticas para las particiones horizontales (particionamiento)](sql-database-elastic-query-getting-started.md).
* Consulte [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) para ver un procedimiento almacenado que ejecuta una instrucción de Transact-SQL en una sola instancia remota de Azure SQL Database o un conjunto de bases de datos que actúan como particiones en un esquema de particiones horizontales.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
