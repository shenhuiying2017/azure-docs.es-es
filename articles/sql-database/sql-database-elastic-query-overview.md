<properties
    pageTitle="Información general sobre consulta de bases de datos elásticas de Base de datos SQL de Azure"
    description="Información general de la característica de consultas elásticas"
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/09/2015"
    ms.author="sidneyh" />

# Información general sobre consulta de bases de datos elásticas de Base de datos SQL de Azure (vista previa)

La **característica de consulta de Base de datos elástica**, en vista previa, le permite ejecutar una consulta de Transact-SQL que abarca varias bases de datos en Base de datos SQL de Azure. Permite conectar herramientas de Microsoft y de terceros (Excel, PowerBI, Tableau, etc.) a capas de datos con varias bases de datos, especialmente cuando esas bases de datos comparten un esquema común (lo que también se conoce como creación de partición horizontal o particionamiento). Con esta característica, puede escalar consultas horizontalmente a capas de datos de gran tamaño en Base de datos SQL y visualizar los resultados en los informes de inteligencia empresarial (BI).

Para empezar a crear una aplicación de consulta de bases de datos elásticas, vea [Introducción a la consulta de Base de datos elástica](sql-database-elastic-query-getting-started.md).

## Escenarios de consulta de base de datos elásticas

El objetivo de la consulta de Base de datos elástica es el de proporcionar escenarios de informes en los que varias bases de datos aportan filas a un único resultado global. La consulta puede estar compuesta por el usuario o por la aplicación directa o indirectamente a través de herramientas que están conectadas a la base de datos de consultas. Esto resulta especialmente útil cuando se crean informes, mediante herramientas de integración de datos o BI comerciales, o cualquier software que no se puede cambiar. Con una consulta de base de datos elástica, puede consultar fácilmente varias bases de datos utilizando la conocida experiencia de conectividad de SQL Server en herramientas como Excel, PowerBI, Tableau o Cognos.

Una consulta de bases de datos elásticas también facilita el acceso a toda una colección de bases de datos a través de las consultas emitidas por SQL Server Management Studio o Visual Studio. Asimismo, facilita las consultas entre bases de datos de Entity Framework u otros entornos de ORM. La ilustración 1 muestra un escenario donde una aplicación en la nube existente (que utiliza la biblioteca de herramientas de Base de datos elástica) se basa en una capa de datos escalada horizontalmente y se usa una consulta de bases de datos elásticas para informes entre bases de datos.

**Ilustración 1**

![Consulta de bases de datos elásticas usada en la capa de datos de escala horizontal][1]

La capa de datos se escala horizontalmente entre muchas bases de datos mediante un esquema común. Este enfoque también se conoce como partición horizontal o particionamiento. Las particiones se pueden realizar y administrar mediante (1) la [biblioteca de cliente de Base de datos elástica](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) o (2) usando un modelo específico de la aplicación para distribuir los datos entre varias bases de datos. Con esta topología, los informes a menudo tienen que abarcan varias bases de datos. Con una consulta de bases de datos elásticas, ahora puede conectarse a una base de datos SQL y los resultados de la consulta de la base de datos remota aparecerán como si se generaran a partir de una sola base de datos virtual.

> [AZURE.NOTE]Consulta de Base de datos elástica funciona mejor para escenarios de informes ocasionales, en los que la mayor parte del procesamiento se puede realizar en la capa de datos. Para grandes cargas de trabajo de informes o situaciones de almacenamiento de datos con consultas más complejas, considere también el uso de [Almacenamiento de datos SQL de Azure](http://azure.microsoft.com/services/sql-data-warehouse/).


## Topología de la consulta de Base de datos elástica

El uso de una consulta de bases de datos elásticas para realizar tareas de informes sobre una capa de datos con particiones horizontales requiere un mapa de particiones de escalado elástico para representar las bases de datos de la capa de datos. Normalmente, solo se usa un mapa de particiones en este escenario y una base de datos dedicada con capacidades de consulta de bases de datos elásticas que sirve como punto de entrada para las consultas de informes. Solo esta base de datos dedicada debe configurarse con objetos de consulta de bases de datos elásticas, como se describe a continuación. La ilustración 2 muestra esta topología y su configuración con la base de datos de consulta de bases de datos elástica y el mapa de particiones.

> [AZURE.NOTE]La base de datos de consulta de bases de datos elásticas dedicada debe ser una base de datos de SQL DB v12 e inicialmente solo se admite el nivel Premium. No existen restricciones en propias particiones.

**Ilustración 2**

![Usar consulta de Base de datos elástica para informes sobre capas particionadas][2]

(Un **shardlet** es todos los datos asociados con un valor único de una clave de particionamiento en una partición. Una **clave de particionamiento** es un valor de columna que determina cómo se distribuyen los datos entre particiones. Por ejemplo, los datos distribuidos por regiones pueden tener identificadores de región como clave de particionamiento. Para obtener más detalles, consulte el [glosario de escalado elástico](sql-database-elastic-scale-glossary.md)).


Con el tiempo, la característica de consulta de Base de datos elástica admitirá topologías adicionales. En este artículo se actualizará para reflejar nuevas características a medida que estén disponibles.

## Habilitar consultas elásticas mediante la configuración de un mapa de particiones

La creación de una solución de consulta de bases de datos elásticas requiere las herramientas de Base de datos elástica [**mapa de particiones**](sql-database-elastic-scale-shard-map-management.md) para representar las bases de datos remotas en una consulta de bases de datos elásticas. Si ya utiliza la biblioteca de cliente de Base de datos flexible, puede usar el mapa de particiones existente. De lo contrario, deberá crear un mapa de particiones mediante las herramientas de Base de datos elástica.

El siguiente código de ejemplo de C# muestra cómo crear un mapa de particiones con una sola base de datos remota agregada como una partición.

    ShardMapManagerFactory.CreateSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerCreateMode.ReplaceExisting, RetryBehavior.DefaultRetryBehavior);
    smm = ShardMapManagerFactory.GetSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerLoadPolicy.Lazy,
      RetryBehavior.DefaultRetryBehavior);
    map = smm.CreateRangeShardMap<int>("yourshardmapname");
    shard = map.CreateShard(new ShardLocation("yoursqldbserver", "yoursqldbdatabasename"));

Para obtener más detalles sobre mapas de particiones, consulte [Administración de mapas de particiones](sql-database-elastic-scale-shard-map-management.md).

Para utilizar la característica de consulta de Base de datos elástica, primero debe crear el mapa de particiones y registrar las bases de datos remotas como particiones. Esta operación solo se realiza una vez. Solo necesita cambiar el mapa de particiones cuando agregue o quite bases de datos remotas, que son operaciones incrementales en un mapa de particiones existente.


## Creación de objetos de base de datos de consulta de bases de datos elásticas

Para describir las tablas remotas a las que se puede tener acceso desde un extremo de consulta de bases de datos elásticas, presentamos la capacidad para definir tablas externas que aparecerán en su aplicación y en herramientas de terceros como si fueran tablas locales. Las consultas se pueden realizar en estos objetos de base de datos implícitamente a través de herramientas o explícitamente desde SQL Server Management Studio, Visual Studio Data Tools o desde una aplicación. La consulta de bases de datos elásticas se ejecuta como cualquier otra instrucción de Transact-SQL, con la significativa diferencia de que esa consulta distribuirá el procesamiento entre las posibles bases de datos remotas que subyacen en los objetos externos.

La característica de consulta de Base de datos elástica se basa en estas cuatro instrucciones de DDL. Normalmente, estas instrucciones de DDL se utilizan una vez o casi nunca cuando se cambia el esquema de la aplicación.

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
*    [CREATE CREDENTIAL](https://msdn.microsoft.com/library/ms189522.aspx)
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### Clave maestra y credenciales de ámbito de base de datos

Una credencial representa el identificador y la contraseña de usuario que usará la consulta de bases de datos elásticas para conectarse al mapa de particiones de escalado elástico y a las bases de datos remotas de Base de datos SQL de Azure. Puede crear la clave maestra y la credencial necesarias utilizando la sintaxis siguiente:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';  
    CREATE CREDENTIAL <credential_name> ON DATABASE
    WITH IDENTITY = '<shard_map_username>',
    SECRET = '<shard_map_password>'
     [;]
Asegúrese de que &lt; shard_map_username > no incluye ningún sufijo "@servername".

Puede usar la siguiente sintaxis para quitar la clave maestra y las credenciales:

    DROP CREDENTIAL <credential_name> ON DATABASE;
    DROP MASTER KEY;  

### Orígenes de datos externos

En el siguiente paso, deberá registrar su mapa de particiones como origen de datos externo. La sintaxis para crear y quitar orígenes de datos externos se define como sigue:

      CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
                     (TYPE = SHARD_MAP_MANAGER,
                     LOCATION = '<fully_qualified_server_name>',
                     DATABASE_NAME = '<shardmap_database_name>',
                     CREDENTIAL = <credential_name>,
                     SHARD_MAP_NAME = '<shardmapname>'
    ) [;]

Puede utilizar la instrucción siguiente para quitar un origen de datos externo:

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

El usuario debe poseer el permiso ALTER ANY EXTERNAL DATA SOURCE. Este permiso está incluido en el permiso ALTER DATABASE.

**Ejemplo**

En el ejemplo siguiente se ilustra el uso de la instrucción CREATE para orígenes de datos externos.

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
    );

Puede recuperar la lista de orígenes de datos externos actuales a partir de la vista de catálogo siguiente:

    select * from sys.external_data_sources;

Tenga en cuenta que se utilizan las mismas credenciales para leer el mapa de particiones y tener acceso a los datos de las bases de datos remotas durante el procesamiento de la consulta.


### Tablas externas

Con la consulta de Base de datos elástica, ampliaremos la sintaxis de la tabla externa existente para hacer referencia a las tablas que están particionadas en (varias) bases de datos remotas en Base de datos SQL de Azure. Con el concepto de origen de datos externo anterior, la sintaxis para crear y quitar las tablas externas se define como sigue:

    CREATE EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    )[;]

    <sharded_external_table_options> ::=
          DATA_SOURCE = <External_Data_Source>,
          DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED | ROUND_ROBIN

La directiva de particionamiento controla si una tabla se trata como una tabla particionada o como una tabla replicada. Con una tabla particionada, los datos de diferentes particiones no se superponen. Las tablas replicadas tienen los mismos datos en cada partición. El procesador de consultas se basa en esta información para llevar a cabo un procesamiento de consultas correcto y más eficaz. La distribución round robin indica que se usa un método específico de la aplicación para distribuir los datos de esa tabla.

    DROP EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name[;]

Permisos para **CREATE/DROP EXTERNAL TABLE**: se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE que también son necesarios para hacer referencia al origen de datos subyacente.

**Ejemplo**: en el ejemplo siguiente se muestra cómo crear una tabla externa:

    CREATE EXTERNAL TABLE [dbo].[order_line](
        [ol_o_id] [int] NOT NULL,
        [ol_d_id] [tinyint] NOT NULL,
        [ol_w_id] [int] NOT NULL,
        [ol_number] [tinyint] NOT NULL,
        [ol_i_id] [int] NOT NULL,
        [ol_delivery_d] [datetime] NOT NULL,
        [ol_amount] [smallmoney] NOT NULL,
        [ol_supply_w_id] [int] NOT NULL,
        [ol_quantity] [smallint] NOT NULL,
        [ol_dist_info] [char](24) NOT NULL
    )
    WITH
    (
        DATA_SOURCE = MyExtSrc,
        DISTRIBUTION=SHARDED(ol_w_id)
    );

En el ejemplo siguiente se muestra cómo recuperar la lista de tablas externas de la base de datos actual:

    select * from sys.external_tables;


## Informes y consultas

### Consultas
Una vez que ha definido los orígenes de datos externos y las tablas, puede utilizar conocidas cadenas de conexión de Base de datos SQL para conectarse a la base de datos que tenga habilitada la característica de consulta de Base de datos elástica. Ahora puede ejecutar consultas de solo lectura completas sobre las tablas externas, con algunas limitaciones que se explica en la [sección de limitaciones](#preview-limitations) a continuación.

**Ejemplo**: la consulta siguiente realiza una combinación en tres direcciones entre almacenes, pedidos y líneas de pedido y utiliza varios agregados y un filtro selectivo. Suponiendo que los almacenes, los pedidos y las líneas de pedido están particionadas por la columna de identificador de almacén, una consulta de base de datos elástica puede colocar las combinaciones en las bases de datos remotas y escalar horizontalmente el procesamiento de la parte costosa de la consulta.

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

### Procedimiento almacenado SP_ EXECUTE_FANOUT

SP_EXECUTE_FANOUT es un procedimiento almacenado que proporciona acceso a las bases de datos representadas por un mapa de particiones. El procedimiento almacenado toma los parámetros siguientes:

-    **Nombre del servidor** (nvarchar): nombre completo del servidor lógico que hospeda el mapa de particiones.
-    **Nombre de la base de datos del mapa de particiones** (nvarchar): nombre de la base de datos del mapa de particiones.
-    **Nombre del usuario** (nvarchar): nombre del usuario para iniciar sesión en la base de datos del mapa de particiones y las bases de datos remotas.
-    **Contraseña** (nvarchar): contraseña del usuario.
-    **Nombre del mapa de particiones** (nvarchar): nombre del mapa de particiones que se va a usar para la consulta.
-    **Consulta**: consulta que se va a ejecutar en cada partición.

Utiliza la información del mapa de particiones proporcionada en los parámetros de invocación para ejecutar la instrucción dada en todas las particiones registradas con el mapa de particiones. Los resultados se combinan con la semántica UNION ALL similar a las consultas de varias particiones. El resultado también incluye la columna ‘virtual’ adicional con el nombre de la base de datos remota.

Tenga en cuenta que se usan las mismas credenciales para conectarse a la base de datos del mapa de particiones y para las particiones.

**Ejemplo**:

    sp_execute_fanout 'myserver.database.windows.net', N'ShardMapDb', N'myuser', N'MyPwd', N'ShardMap', N'select count(w_id) as foo from warehouse'

## Conectividad para herramientas
Puede utilizar cadenas de conexión de Base de datos SQL conocidas en la consulta de Base de datos elástica para conectar las herramientas de integración de datos y BI. Asegúrese de que SQL Server se admite como origen de datos para la herramienta. A continuación, use objetos externos en la base de datos de consulta de Base de datos elástica exactamente igual que con cualquier base datos SQL Server a la se que conectaría con la herramienta.

## Prácticas recomendadas
*    Asegúrese de que la base de datos de administrador del mapa de particiones y las bases de datos definidas en el mapa de particiones permiten el acceso desde Microsoft Azure en sus reglas de firewall. Esto es necesario para que la base de datos de consulta de Base de datos elástica pueda conectarse a ellas. Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).
*    Una consulta de base de datos elástica no valida o exige la distribución de datos definida por la tabla externa. Si la distribución de datos real es diferente de la distribución especificada en la definición de tabla, las consultas pueden arrojar resultados inesperados.
*    Una consulta de bases de datos elásticas funciona mejor para consultas donde la mayor parte del cálculo se puede realizar en las particiones. Normalmente el máximo rendimiento de consultas se obtiene con predicados de filtros selectivos que se puede evaluar en las particiones o combinaciones sobre las claves de particiones que se pueden realizar en consonancia con la partición en todas las particiones. Otros patrones de consulta puede que necesiten cargar grandes cantidades de datos desde las particiones al nodo principal y pueden experimentar un rendimiento deficiente.

## Coste

La consulta de Base de datos elástica se incluye con el costo de las bases de datos de Base de datos SQL de Azure. Tenga en cuenta que las topologías se admiten donde las bases de datos remotas se encuentran en un centro de datos diferente al extremo de consulta de bases de datos elásticas, pero la salida de datos de las bases de datos remotas se cobra a tarifas de salida normal de Azure.

## Limitaciones de vista previa

Hay algunas cosas que se deben tener en cuenta con la vista previa:

*    La característica de consulta de Base de datos elástica inicialmente solo estará disponible en el nivel de rendimiento Premium de SQL DB v12, aunque las bases de datos remotas a las que tiene acceso una consulta de bases de datos elásticas puede ser de cualquier nivel.
* Las tablas externas a las que hace referencia el origen de datos externo solo admiten operaciones de lectura en las bases de datos remotas. Sin embargo, puede señalar toda la funcionalidad de Transact-SQL en la base de datos de consulta de bases de datos elásticas donde reside la propia definición de tabla externa. Esto puede ser útil, por ejemplo, para conservar los resultados temporales mediante SELECT lista_columnas INTO tabla_local o para definir procedimientos almacenados en la base de datos de consulta de bases de datos elásticas que hacen referencia a tablas externas.
*    Los parámetros de consultas actualmente no se pueden trasladar a bases de datos remotas. Las consultas parametrizadas, necesitarán poner todos los datos en el nodo principal y pueden sufrir un mal rendimiento en función del tamaño de datos. Una solución temporal es evitar parámetros en las consultas o usar la opción RECOMPILE para tener parámetros reemplazados automáticamente por sus valores actuales.
* Actualmente las estadísticas de nivel de columna a través de tablas externas no se admiten.
* La consulta de Base de datos elástica actualmente no realiza eliminación de particiones cuando los predicados de la clave de particiones permitirían excluir de forma segura determinadas bases de datos remotas del procesamiento. Por lo tanto, las consultas siempre tocarán todas las bases de datos remotas representadas por los orígenes de datos externos de la consulta.
* Todas las consultas que impliquen combinaciones entre tablas de bases de datos diferentes pueden devolver gran cantidad de filas a la base de datos de consulta de bases de datos elásticas para su procesamiento, lo que da lugar a un costo de rendimiento. Es mejor desarrollar consultas que se puedan procesar localmente en cada base de datos remota, o usar cláusulas WHERE para restringir filas afectadas de cada base de datos antes de realizar la combinación.
*    La sintaxis usada para la definición de metadatos de la consulta de Base de datos elástica cambiará durante la vista previa.
*    La funcionalidad de scripts de Transact-SQL en SSMS o SSDT actualmente no funciona con objetos de consulta de bases de datos elásticas.

## Comentarios
Comparta sus comentarios sobre su experiencia con nosotros en Disqus o Stackoverflow. Estamos interesados en todo tipo de comentarios sobre el servicio (defectos, particularidades, deficiencias de característica, etc.).

## Pasos siguientes
Para comenzar a explorar la consulta de Base de datos elástica, pruebe nuestro tutorial paso a paso para disponer de un completo ejemplo de trabajo en funcionamiento en minutos: [Introducción a la consulta de Base de datos elástica](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png

<!--anchors-->

<!---HONumber=July15_HO4-->