<properties
    pageTitle="Consulta de bases de datos elásticas para consultas entre bases de datos (particionamiento vertical) | Microsoft Azure"
    description="cómo configurar consultas entre bases de datos en particiones verticales"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/28/2016"
    ms.author="torsteng;sidneyh" />

# Consulta de bases de datos elásticas para consultas entre bases de datos (particionamiento vertical)

![Consultas entre tablas de bases de datos diferentes][1]

Las bases de datos con particiones verticales usan distintos conjuntos de tablas en bases de datos diferentes. Esto significa que el esquema es diferente en las distintas bases de datos. Por ejemplo, todas las tablas de inventario se encuentran en una base de datos mientras que todas las relacionadas con la contabilidad se encuentran en otra.

## Requisitos previos

* El usuario debe poseer el permiso ALTER ANY EXTERNAL DATA SOURCE. Este permiso está incluido en el permiso ALTER DATABASE.
* Se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE para hacer referencia al origen de datos subyacente.

## Información general

**NOTA**: a diferencia del particionamiento horizontal, estas instrucciones DDL no dependen de la definición de una capa de datos con un mapa de particiones por medio de la biblioteca de cliente de bases de datos elásticas.

1. [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 


## Creación de clave maestra y credenciales con ámbito de base de datos 

La credencial utiliza la consulta elástica para conectarse a las bases de datos remotas.

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**Nota** Asegúrese de que *<username>* no incluya ningún sufijo *"@servername"*.

## Creación de orígenes de datos externos

Sintaxis:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**Importante** El parámetro TYPE debe establecerse en **RDBMS**.

### Ejemplo 

En el ejemplo siguiente se ilustra el uso de la instrucción CREATE para orígenes de datos externos.

	CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
	WITH 
	( 
		TYPE=RDBMS, 
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ReferenceData', 
		CREDENTIAL= SqlUser 
	); 
 
Para recuperar la lista de orígenes de datos externos actuales:

    select * from sys.external_data_sources; 

### Tablas externas 

Sintaxis:

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
	{ WITH ( <rdbms_external_table_options> ) } 
	)[;] 
	
	<rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### Ejemplo  

	CREATE EXTERNAL TABLE [dbo].[customer]( 
		[c_id] int NOT NULL, 
		[c_firstname] nvarchar(256) NULL, 
		[c_lastname] nvarchar(256) NOT NULL, 
		[street] nvarchar(256) NOT NULL, 
		[city] nvarchar(256) NOT NULL, 
		[state] nvarchar(20) NULL, 
		[country] nvarchar(50) NOT NULL, 
	) 
	WITH 
	( 
	       DATA_SOURCE = RemoteReferenceData 
	); 

En el ejemplo siguiente se muestra cómo recuperar la lista de tablas externas de la base de datos actual:

	select * from sys.external_tables; 

### Comentarios

La consulta elástica amplía la sintaxis de la tabla externa existente para que incluya la definición de tablas externas que usan orígenes de datos externos de tipo RDBMS. La definición de tabla externa para el particionamiento vertical abarca los siguientes aspectos:

* **Esquema**: el DDL de tabla externa define un esquema que las consultas pueden usar. El esquema proporcionado en la definición de tabla externa debe coincidir con el de las tablas de la base de datos remota donde se almacenan los datos en sí. 

* **Referencia a base de datos remota**: el DDL de tabla externa hace referencia a un origen de datos externo. El origen de datos externo especifica el nombre del servidor lógico y el nombre de la base de datos remota donde se almacenan los datos de la tabla.

Usando un origen de datos externo como se describe en la anterior sección, la sintaxis para crear tablas externas es la siguiente:

La cláusula DATA\_SOURCE define el origen de datos externo (es decir, la base de datos remota en el caso del particionamiento vertical) que se usa para la tabla externa.

Las cláusulas SCHEMA\_NAME y OBJECT\_NAME proporcionan la capacidad de asignar la definición de la tabla externa a una tabla en un esquema diferente de la base de datos remota, o bien a una tabla con un nombre diferente, respectivamente. Esto resulta útil si desea definir una tabla externa en una vista de catálogo o DMV en la base de datos remota, o en cualquier otra situación en que el nombre de la tabla remota ya se use localmente.

La siguiente instrucción DDL quita una definición de tabla externa existente del catálogo local. No afecta a la base de datos remota.

	DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Permisos para CREATE/DROP EXTERNAL TABLE**: se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE para el DDL de tabla externa que también se necesita para hacer referencia al origen de datos subyacente.

## Consideraciones sobre la seguridad
Los usuarios con acceso a la tabla externa obtienen automáticamente acceso a las tablas remotas subyacentes con la credencial proporcionada en la definición del origen de datos externo. Debe administrar con cuidado el acceso a la tabla externa para evitar la elevación de privilegios no deseada por medio de la credencial del origen de datos externo. Se pueden usar permisos SQL normales para conceder o revocar el acceso a una tabla externa como si fuera una tabla normal.


## Ejemplo: consulta de bases de datos con particiones verticales 

La consulta siguiente realiza una combinación tridireccional entre las dos tablas locales de pedidos y líneas de pedido, y la tabla remota de clientes. Es un ejemplo del caso de uso de datos de referencia para una consulta elástica:

	SELECT  	
	 c_id as customer,
	 c_lastname as customer_name,
	 count(*) as cnt_orderline, 
	 max(ol_quantity) as max_quantity,
	 avg(ol_amount) as avg_amount,
	 min(ol_delivery_d) as min_deliv_date
	FROM customer 
	JOIN orders 
	ON c_id = o_c_id
	JOIN  order_line 
	ON o_id = ol_o_id and o_c_id = ol_c_id
	WHERE c_id = 100


## Procedimiento almacenado para la ejecución remota de T-SQL: sp\_execute\_remote

La consulta elástica también incluye un procedimiento almacenado que proporciona acceso directo a las particiones. El procedimiento almacenado se denomina [sp\_execute\_remote](https://msdn.microsoft.com/library/mt703714) y sirve para ejecutar procedimientos almacenados remotos o código T-SQL en bases de datos remotas. Toma los parámetros siguientes:

* Nombre de origen de datos (nvarchar): nombre del origen de datos externo de tipo RDBMS. 
* Consulta (nvarchar): la consulta T-SQL que se va a ejecutar en cada partición. 
* Declaración de parámetro (nvarchar) - opcional: cadena con definiciones de tipos de datos de los parámetros usados en el parámetro Query (como sp\_executesql). 
* Lista de valores de los parámetros (opcional): lista separada por comas de valores de los parámetros (por ejemplo, sp\_executesql)

sp\_execute\_remote utiliza el origen de datos externo proporcionado en los parámetros de invocación para ejecutar la instrucción T-SQL determinada en las bases de datos remotas. Utiliza la credencial del origen de datos externo para conectarse a la base de datos de ShardMapManager y las bases de datos remotas.

Ejemplo:

	EXEC sp_execute_remote
		N'MyExtSrc',
		N'select count(w_id) as foo from warehouse' 


  
## Conectividad para herramientas

Puede usar cadenas de conexión de SQL Server normales para conectar sus herramientas de integración de datos y de BI con bases de datos en el servidor de base de datos SQL en que se habilitó la consulta elástica y se definieron las tablas externas. Asegúrese de que SQL Server se admite como origen de datos para la herramienta. Después, consulte la base de datos de consulta elástica y sus tablas externas como cualquier otra base de datos de SQL Server a la que se conectaría con su herramienta.

## Prácticas recomendadas 
 
* Asegúrese de que se conceda a la base de datos de punto de conexión de consulta elástica acceso a la base de datos remota habilitando el acceso a los Servicios de Azure en su configuración de firewall de base de datos SQL. Compruebe también que la credencial proporcionada en la definición del origen de datos externo pueda iniciar sesión correctamente en la base de datos remota y tenga los permisos para acceder a la tabla remota.  

* Una consulta elástica funciona mejor para aquellas consultas en que la mayor parte del cálculo se puede realizar en las bases de datos remotas. Normalmente, el máximo rendimiento de las consultas se obtiene con predicados de filtro selectivos que se pueden evaluar en las bases de datos remotas o combinaciones que se pueden realizar totalmente en la base de datos remota. Es posible que otros patrones de consulta necesiten cargar grandes cantidades de datos desde la base de datos remota y pueden experimentar un rendimiento deficiente.


## Pasos siguientes

Para consultar bases de datos con particiones horizontales (también llamadas bases de datos particionadas), vea [Consulta de bases de datos elásticas para consultas entre bases de datos (particionamiento vertical)](sql-database-elastic-query-horizontal-partitioning.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->

<!---HONumber=AcomDC_0504_2016-->