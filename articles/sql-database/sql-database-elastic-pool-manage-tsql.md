---
title: "T-SQL: Administración de un grupo elástico de Azure SQL Database | Microsoft Docs"
description: "Use T-SQL para administrar un grupo elástico de Azure SQL Database."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 4e288e17-bc3e-4255-9fbe-0a2ac0dbd7dd
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
ms.translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: a3acd5f4ec63061254b550737ae9fb4d39b343c6
ms.contentlocale: es-es
ms.lasthandoff: 03/10/2017


---
# <a name="monitor-and-manage-an-elastic-pool-with-transact-sql"></a>Supervisión y administración de un grupo elástico con Transact-SQL
En este tema se muestra cómo administrar [grupos elásticos](sql-database-elastic-pool.md) escalables con Transact-SQL.  También puede crear y administrar un grupo elástico de Azure en [Azure Portal](https://portal.azure.com/), [PowerShell](sql-database-elastic-pool-manage-powershell.md), la API de REST o [C#](sql-database-elastic-pool-manage-csharp.md). También puede crear y mover bases de datos dentro y fuera de los grupos elásticos mediante [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).


Utilice los comandos [Create Database (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) y [Alter Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) para crear e introducir y sacar las bases de datos de los grupos elásticos. El grupo elástico debe existir antes de poder utilizar estos comandos. Estos comandos afectan solo a las bases de datos. La creación de nuevos grupos y la configuración de las propiedades de grupo (por ejemplo, de los valores mín. y máx. de eDTU) no se puede cambiar mediante comandos de T-SQL.

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>Creación de una base de datos agrupada en un grupo elástico
Use el comando CREATE DATABASE con la opción SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in an elastic named S3M100.

Todas las bases de datos de un grupo elástico heredan el nivel de servicio del grupo elástico (Básico, Estándar, Premium). 

## <a name="move-a-database-between-elastic-pools"></a>Movimiento de una base de datos entre grupos elásticos
Utilice el comando ALTER DATABASE con MODIFY y configure la opción SERVICE\_OBJECTIVE como ELASTIC\_POOL. Establezca como nombre el nombre del grupo de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to an elastic named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Movimiento de una base de datos a un grupo elástico
Utilice el comando ALTER DATABASE con MODIFY y configure la opción SERVICE\_OBJECTIVE como ELASTIC_POOL. Establezca como nombre el nombre del grupo de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to an elastic named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Movimiento de una base de datos fuera de un grupo elástico
Utilice el comando ALTER DATABASE y establezca SERVICE_OBJECTIVE en uno de los niveles de rendimiento (como S0 o S1).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Enumeración de bases de datos de un grupo elástico
Utilice la vista [sys.database\_service\_objectives](https://msdn.microsoft.com/library/mt712619) para enumerar todas las bases de datos de un grupo elástico. Inicie sesión en la base de datos maestra para efectuar una consulta en la vista.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>Obtención de datos de uso de recursos para un grupo elástico
Utilice la vista [sys.elastic\_pool\_resource\_stats](https://msdn.microsoft.com/library/mt280062.aspx) para examinar las estadísticas de uso de los recursos de un grupo elástico en un servidor lógico. Inicie sesión en la base de datos maestra para efectuar una consulta en la vista.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-a-pooled-database"></a>Obtención de datos de uso de recursos para una base de datos agrupada
Utilice la vista [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) o la vista [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) para examinar las estadísticas de uso de los recursos de una base de datos en un grupo elástico. Este proceso es similar a la consulta de uso de recursos para una base de datos única.

## <a name="next-steps"></a>Pasos siguientes
Tras la creación de un grupo elástico, puede administrar las bases de datos elásticas del grupo mediante la creación de trabajos elásticos. Los trabajos elásticos facilitan la ejecución de secuencias de comandos de T-SQL con cualquier número de bases de datos en el grupo. Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md). 

Consulte [Escalado horizontal con Azure SQL Database](sql-database-elastic-scale-introduction.md): use herramientas de bases de datos elásticas para realizar un escalado horizontal, mover los datos, realizar consultas o crear transacciones.


