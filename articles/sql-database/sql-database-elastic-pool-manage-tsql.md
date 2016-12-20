---
title: "Creación o movimiento de Azure SQL Database a un grupo elástico mediante T-SQL | Microsoft Docs"
description: "Use T-SQL para crear una Base de datos SQL de Azure en un grupo elástico. O bien, use T-SQL para mover la base de datos dentro y fuera de los grupos."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 4e288e17-bc3e-4255-9fbe-0a2ac0dbd7dd
ms.service: sql-database
ms.custom: sharded databases pool; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 17ec1ad8218824048179e32ae5059b6a6d77a367


---
# <a name="monitor-and-manage-an-elastic-database-pool-with-transact-sql"></a>Supervisión y administración de un grupo de bases de datos elásticas con Transact-SQL
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
>  

Utilice los comandos [Create Database (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) y [Alter Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) para crear e introducir y sacar las bases de datos de los grupos elásticos. El grupo elástico debe existir antes de poder utilizar estos comandos. Estos comandos afectan solo a las bases de datos. La creación de nuevos grupos y la configuración de las propiedades de grupo (por ejemplo, de los valores mín. y máx. de eDTU) no se puede cambiar mediante comandos de T-SQL.

## <a name="create-a-new-database-in-an-elastic-pool"></a>Creación de una nueva base de datos en un grupo elástico
Use el comando CREATE DATABASE con la opción SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Todas las bases de datos de un grupo elástico heredan el nivel de servicio del grupo elástico (Básico, Estándar, Premium). 

## <a name="move-a-database-between-elastic-pools"></a>Movimiento de una base de datos entre grupos elásticos
Utilice el comando ALTER DATABASE con MODIFY y configure la opción SERVICE\_OBJECTIVE como ELASTIC\_POOL. Establezca como nombre el nombre del grupo de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Movimiento de una base de datos a un grupo elástico
Utilice el comando ALTER DATABASE con MODIFY y configure la opción SERVICE\_OBJECTIVE como ELASTIC_POOL. Establezca como nombre el nombre del grupo de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Movimiento de una base de datos fuera de un grupo elástico
Utilice el comando ALTER DATABASE y establezca SERVICE_OBJECTIVE en uno de los niveles de rendimiento (S0, S1, etc).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Enumeración de bases de datos de un grupo elástico
Utilice la vista [sys.database\_service\_objectives](https://msdn.microsoft.com/library/mt712619) para enumerar todas las bases de datos de un grupo elástico. Inicie sesión en la base de datos maestra para efectuar una consulta en la vista.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>Obtención de datos de uso de recursos para un grupo
Utilice la vista [sys.elastic\_pool\_resource\_stats](https://msdn.microsoft.com/library/mt280062.aspx) para examinar las estadísticas de uso de los recursos de un grupo elástico en un servidor lógico. Inicie sesión en la base de datos maestra para efectuar una consulta en la vista.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>Obtención de datos de uso de recursos para una base de datos elástica
Utilice la vista [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) o la vista [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) para examinar las estadísticas de uso de los recursos de una base de datos en un grupo elástico. Este proceso es similar a la consulta de uso de recursos para cualquier base de datos única.

## <a name="next-steps"></a>Pasos siguientes
Tras la creación de un grupo de bases de datos elásticas, puede administrar las bases de datos elásticas del grupo mediante la creación de trabajos elásticos. Los trabajos elásticos facilitan la ejecución de secuencias de comandos de T-SQL con cualquier número de bases de datos en el grupo. Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md). 

Consulte [Información general de las características de bases de datos elásticas](sql-database-elastic-scale-introduction.md): use herramientas de bases de datos elásticas para realizar un escalado horizontal, mover los datos, consultar o crear transacciones.




<!--HONumber=Nov16_HO3-->


