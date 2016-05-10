<properties 
    pageTitle="Creación o movimiento de una Base de datos SQL de Azure a un grupo elástico mediante T-SQL | Microsoft Azure" 
    description="Use T-SQL para crear una Base de datos SQL de Azure en un grupo elástico. O bien, use T-SQL para mover la base de datos dentro y fuera de los grupos." 
	services="sql-database" 
    documentationCenter="" 
    authors="sidneyh" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/28/2016"
    ms.author="sidneyh"/>

# Supervisión y administración de un grupo de bases de datos elásticas con Transact-SQL  

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Utilice los comandos [Create Database (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/dn268335.aspx) y [Alter Database (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/mt574871.aspx) para crear e introducir y sacar las bases de datos de los grupos elásticos. El grupo elástico debe existir antes de poder utilizar estos comandos. Estos comandos afectan solo a las bases de datos. La creación de nuevos grupos y la configuración de las propiedades de grupo (por ejemplo, de los valores mín. y máx. de eDTU) no se puede cambiar mediante comandos de T-SQL.


> [AZURE.NOTE] Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12. Si tiene un servidor de Base de datos SQL V11, puede [usar PowerShell para actualizar a V12 y crear un grupo](sql-database-upgrade-server-portal.md) en un solo paso.


## Creación de una nueva base de datos en un grupo elástico
Use el comando CREATE DATABASE con la opción SERVICE\_OBJECTIVE.

	CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
	-- Create a database named db1 in a pool named S3M100.

Todas las bases de datos de un grupo elástico heredan el nivel de servicio del grupo elástico (Básico, Estándar, Premium).


## Movimiento de una base de datos entre grupos elásticos
Utilice el comando ALTER DATABASE con las opciones MODIFY y SERVICE\_OBJECTIVE como ELASTIC\_POOL. Establezca como nombre el nombre del grupo de destino.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
	-- Move the database named db1 to a pool named P1M125  

## Movimiento de una base de datos a un grupo elástico 
Utilice el comando ALTER DATABASE con las opciones MODIFY y SERVICE\_OBJECTIVE como ELASTIC\_POOL. Establezca como nombre el nombre del grupo de destino.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
	-- Move the database named db1 to a pool named S3100.

## Movimiento de una base de datos fuera de un grupo elástico
Utilice el comando ALTER DATABASE y establezca SERVICE\_OBJECTIVE en uno de los niveles de rendimiento (S0, S1, etc).

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
	-- Changes the database into a stand-alone database with the service objective S1.

## Enumeración de bases de datos de un grupo elástico
Utilice la [vista sys.database\_service \_objectives](https://msdn.microsoft.com/library/mt712619) para enumerar todas las bases de datos de un grupo elástico. Inicie sesión en la base de datos maestra para efectuar una consulta en la vista.

	SELECT d.name, slo.*  
	FROM sys.databases d 
	JOIN sys.database_service_objectives slo  
	ON d.database_id = slo.database_id
	WHERE elastic_pool_name = 'MyElasticPool'; 

## Obtención de datos de uso de recursos para un grupo

Utilice la [vista sys.elastic\_pool \_resource \_stats](https://msdn.microsoft.com/library/mt280062.aspx) para examinar las estadísticas de uso de los recursos de un grupo elástico en un servidor lógico. Inicie sesión en la base de datos maestra para efectuar una consulta en la vista.

	SELECT * FROM sys.elastic_pool_resource_stats 
	WHERE elastic_pool_name = 'MyElasticPool'
	ORDER BY end_time DESC;

## Obtención de datos de uso de recursos para una base de datos elástica

Utilice la [vista sys.dm\_ db\_ resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) o la [vista sys.resource \_stats](https://msdn.microsoft.com/library/dn269979.aspx) para examinar las estadísticas de uso de los recursos de una base de datos en un grupo elástico. Este proceso es similar a la consulta de uso de recursos para cualquier base de datos única.

## Pasos siguientes

Tras la creación de un grupo de bases de datos elásticas, puede administrar las bases de datos elásticas del grupo mediante la creación de trabajos elásticos. Los trabajos elásticos facilitan la ejecución de secuencias de comandos de T-SQL con cualquier número de bases de datos en el grupo. Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

Consulte [Información general de las características de bases de datos elásticas](sql-database-elastic-scale-introduction.md): use herramientas de bases de datos elásticas para realizar un escalado horizontal, mover los datos, consultar o crear transacciones.

<!---HONumber=AcomDC_0504_2016-->