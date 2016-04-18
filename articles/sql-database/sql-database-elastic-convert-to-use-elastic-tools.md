<properties
   pageTitle="Convert existing databases to use elastic database tools (Conversión de bases de datos existentes para usar herramientas para bases de datos elásticas)"
   description="Conversión de bases de datos particionadas para usar herramientas para bases de datos elásticas creando un administrador de mapas de particiones"
   services="sql-database"
   documentationCenter=""
   authors="SilviaDoomra"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="04/01/2016"
   ms.author="SilviaDoomra"/>

# Convert existing databases to use elastic database tools (Conversión de bases de datos existentes para usar herramientas para bases de datos elásticas)

Si tiene una solución escalada horizontalmente y particionada, puede aprovechar las herramientas para bases de datos elásticas, como la [biblioteca cliente de Base de datos elástica](sql-database-elastic-database-client-library.md) y la [herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md) empleando las técnicas descritas aquí.

Estas técnicas pueden implementarse mediante la [biblioteca de cliente de .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) o los scripts de PowerShell que se encuentran en [Azure SQL DB - Elastic Database tools scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db) (Azure SQL DB: scripts de herramientas para bases de datos elásticas). Los ejemplos siguientes usan los scripts de PowerShell.

Tenga en cuenta que debe crear las bases de datos antes de ejecutar los cmdlets Add-Shard y New-ShardMapManager. Los cmdlets no crean bases de datos automáticamente.

Hay cuatro pasos:

1. Preparar la base de datos de administrador de mapas de particiones.
2. Crear el mapa de particiones.
3. Preparar las particiones individuales.  
2. Añadir las asignaciones al mapa de particiones.

Para obtener más información sobre ShardMapManager, consulte [Administración de mapas de particiones](sql-database-elastic-scale-shard-map-management.md). Si desea obtener información general de las herramientas para bases de datos elásticas, consulte [Información general de las características de bases de datos elásticas](sql-database-elastic-scale-introduction.md).

## Preparación de la base de datos de administrador de mapas de particiones
Puede usar una base de datos nueva o existente como administrador de mapas de particiones.

## Paso 1: crear un administrador de mapas de particiones
Tenga en cuenta que una base de datos que actúa como administrador de mapas de particiones no debe ser la misma base de datos que la de una partición.

	# Create a shard map manager. 
	New-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 
	#<server_name> and <smm_db_name> are the server name and database name 
	# for the new or existing database that should be used for storing 
	# tenant-database mapping information.

### Para recuperar el administrador de mapas de particiones

Después de la creación, puede recuperar el administrador de mapas de particiones con este cmdlet. Este paso es necesario cada vez que tiene que usar el objeto ShardMapManager.

	# Try to get a reference to the Shard Map Manager  
	$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 

  
## Paso 2: crear un mapa de particiones

La opción es crear uno de los siguientes modelos:

1. Un solo inquilino por base de datos 
2. Varios inquilinos por base de datos (dos tipos):
	3. Asignación de intervalo
	4. Asignación de lista
 

Si usa un modelo de base de datos de inquilino único, use la asignación de lista. El modelo de inquilino único asigna una base de datos por inquilino. Se trata de un modelo eficaz para desarrolladores de SaaS, pues simplifica la administración.

![Asignación de lista][1]

Por el contrario, el modelo de base de datos de varios inquilinos asigna varios inquilinos a una base de datos única (y puede distribuir grupos de inquilinos entre varias bases de datos). Se trata de un modelo viable cuando se espera que la cantidad de datos por inquilino sea pequeña. En este modelo, asignamos un intervalo de inquilinos a una base de datos mediante *asignación de intervalo*.
 

![Asignación de intervalo][2]

También puede implementar un modelo de base de datos de varios inquilinos mediante una asignación de lista para asignar varios inquilinos a una sola base de datos. Por ejemplo, DB1 se usa para almacenar información sobre el id. de inquilino 1 y 5, mientras que DB2 almacena datos sobre el inquilino 7 y 10.

![Varios inquilinos en una sola base de datos][3]


## Paso 2, opción 1: crear un mapa de particiones para una asignación de lista
Cree un mapa de particiones con el objeto ShardMapManager.

	# $ShardMapManager is the shard map manager object. 
	$ShardMap = New-ListShardMap -KeyType $([int]) 
	-ListShardMapName 'ListShardMap' 
	-ShardMapManager $ShardMapManager 
 
 
## Paso 2, opción 2: crear un mapa de particiones para una asignación de intervalo

Tenga en cuenta que para usar este patrón de asignación, el id. de inquilino debe consistir en intervalos continuos y es aceptable que haya una brecha en los intervalos con solo omitir el intervalo al crear las bases de datos.

	# $ShardMapManager is the shard map manager object 
	# 'RangeShardMap' is the unique identifier for the range shard map.  
	$ShardMap = New-RangeShardMap 
	-KeyType $([int]) 
	-RangeShardMapName 'RangeShardMap' 
	-ShardMapManager $ShardMapManager 

## Paso 2, opción 3: asignaciones de lista en una base de datos única
La configuración de este patrón también requiere la creación de un mapa de lista, tal como se muestra en el paso 2, opción 1.

## Paso 3: preparar particiones individuales

Agregue cada partición (base de datos) al administrador de mapas de particiones. Esto prepara a las bases de datos individuales para almacenar información de asignación. Ejecute este método en cada partición.
	 
	Add-Shard 
	-ShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>'
	# The $ShardMap is the shard map created in step 2.
 

## Paso 4: agregar asignaciones

La adición de asignaciones depende del tipo de mapa de particiones creado. Si ha creado un mapa de lista, agregue asignaciones de lista. Si ha creado un mapa de intervalo, agregue asignaciones de intervalo.

### Paso 4, opción 1: asignar los datos de una asignación de lista

Asigne los datos agregando una asignación de lista para cada inquilino.

	# Create the mappings and associate it with the new shards 
	Add-ListMapping 
	-KeyType $([int]) 
	-ListPoint '<tenant_id>' 
	-ListShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 

### Paso 4, opción 2: asigne los datos de una asignación de intervalo

Agregue las asignaciones de intervalo para todas las asociaciones de base de datos de intervalo de id. de inquilino:

	# Create the mappings and associate it with the new shards 
	Add-RangeMapping 
	-KeyType $([int]) 
	-RangeHigh '5' 
	-RangeLow '1' 
	-RangeShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 


### Paso 4, opción 3: asignar los datos para varios inquilinos en una sola base de datos

Para cada inquilino, ejecute Add-ListMapping (opción 1, arriba).


## Comprobación de las asignaciones

Información sobre las particiones existentes y las asignaciones asociadas a ellas pueden consultarse mediante los comandos siguientes:

	# List the shards and mappings 
	Get-Shards -ShardMap $ShardMap 
	Get-Mappings -ShardMap $ShardMap 

## Resumen

Una vez haya completado la configuración, puede empezar a usar la Biblioteca de cliente de Base de datos elástica. También puede usar las características de [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) y [consulta a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md).

## Pasos siguientes


Obtenga los scripts de PowerShell de [Azure SQL DB-Elastic Database tools scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db) (Azure SQL DB: scripts de herramientas para bases de datos elásticas).

Las herramientas también se encuentran en GitHub: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Use la herramienta de división y combinación para mover datos a un modelo de varios inquilinos o desde este a un modelo de inquilino único. Consulte el artículo sobre la [herramienta de división y combinación](sql-database-elastic-scale-get-started.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 

<!---HONumber=AcomDC_0406_2016-->