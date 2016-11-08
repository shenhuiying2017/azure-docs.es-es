---
title: Administración y cambio de tamaño de un grupo de bases de datos elásticas con C# | Microsoft Docs
description: Use técnicas de desarrollo de bases de datos de C# para administrar un grupo de bases de datos elásticas de Base de datos SQL de Azure.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein

---
# <a name="monitor-and-manage-an-elastic-database-pool-with-c&#x23;"></a>Administración y cambio de tamaño de un grupo de bases de datos elásticas con C&#x23;
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

Aprenda a administrar un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) mediante C&#x23;. 

> [!NOTE]
> Muchas de las nuevas características de SQL Database solo se admiten cuando se usa el [modelo de implementación de Azure Resource Manager](../resource-group-overview.md), así que debe usar siempre la **Biblioteca de administración de Azure SQL Database para .NET más reciente ([documentos](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Paquete de NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Las antiguas [bibliotecas basadas en el modelo de implementación clásico](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) solo se admiten para la compatibilidad con versiones anteriores, así que se recomienda usar las bibliotecas más basadas en Resource Manager más nuevas.
> 
> 

Para completar los pasos de este artículo, necesitará lo siguiente:

* Un grupo elástico (el grupo que quiere administrar). Para crear un grupo, consulte [Creación de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-create-csharp.md).
* Visual Studio. Para obtener una copia gratis de Visual Studio, consulte la página [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

## <a name="move-a-database-into-an-elastic-pool"></a>Movimiento de una base de datos a un grupo elástico
Puede mover una base de datos independiente dentro o fuera de un grupo.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Enumeración de bases de datos de un grupo elástico
Para recuperar todas las bases de datos de un grupo, llame al método [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Cambio de la configuración de rendimiento de un grupo
Recupere las propiedades del grupo existentes. Modifique los valores y ejecute el método CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latencia de las operaciones de grupos elásticos
* El cambio del número mínimo de eDTU por base de datos o del máximo de eDTU por base de datos suele completarse en cinco minutos o menos.
* El tiempo para cambiar el tamaño del grupo (eDTU) depende el tamaño combinado de todas las bases de datos del grupo. Los cambios tienen un duración media de 90 minutos o menos por cada 100 GB. Por ejemplo, si el espacio total que usan todas las bases de datos del grupo es de 200 GB, la latencia esperada para cambiar las eDTU de grupo por grupo será de 3 horas o menos.

## <a name="additional-resources"></a>Recursos adicionales
* [Códigos de error para las aplicaciones cliente de SQL Database: error de conexión de base de datos y otros problemas.](sql-database-develop-error-messages.md).
* [Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* [Creación de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-create-csharp.md)
* [¿Cuándo se debe utilizar un grupo de bases de datos elásticas?](sql-database-elastic-pool-guidance.md)
* Consulte [Escalado horizontal con Base de datos SQL de Azure](sql-database-elastic-scale-introduction.md): use herramientas de bases de datos elásticas para realizar un escalado horizontal, mover los datos, realizar consultas o crear transacciones.

<!--HONumber=Oct16_HO2-->


