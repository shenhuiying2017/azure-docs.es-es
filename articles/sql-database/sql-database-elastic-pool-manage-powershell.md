<properties 
    pageTitle="Administración de un grupo de bases de datos elásticas (PowerShell) | Microsoft Azure" 
    description="Obtenga más información sobre cómo usar PowerShell para administrar un grupo de bases de datos elásticas."  
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="04/01/2016"
    ms.author="sstein"/>

# Supervisión, administración y ajuste de tamaño de un grupo de bases de datos elásticas con PowerShell 

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Obtenga más información sobre cómo administrar un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) mediante cmdlets de PowerShell.

Para ver los códigos de error comunes, consulte [Códigos de error para las aplicaciones cliente de la Base de datos SQL: error de conexión de base de datos y otros problemas](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12. Si tiene un servidor de Base de datos SQL V11, puede [usar PowerShell para actualizar a V12 y crear un grupo](sql-database-upgrade-server-portal.md) en un solo paso.

Debe ejecutar Azure PowerShell 1.0 o superior. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

## Creación de una nueva base de datos elástica en un grupo

Para crear una nueva base de datos directamente dentro de un grupo, use el cmdlet [AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) y establezca el parámetro **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Desplazamiento de una base de datos independiente a un grupo

Para mover una base de datos existente a un grupo, use el cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) y establezca el parámetro **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Cambio de la configuración de rendimiento de un grupo

Para cambiar la configuración de rendimiento de un grupo de bases de datos elásticas, use el cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx).

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Obtención del estado de las operaciones de los grupos

Puede realizar un seguimiento del estado de las operaciones del grupo, como la creación y las actualizaciones, mediante el cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx).

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## Obtención del estado entrada o salida de un grupo de una base de datos elástica

Puede realizar un seguimiento del estado de las operaciones de las bases de datos elásticas, como la creación y las actualizaciones, mediante el cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx).

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Obtención de datos de uso para un grupo

Métricas que se pueden recuperar como porcentaje del límite del grupo de recursos:

* Uso medio de CPU: cpu\_percent 
* Uso medio de ES: data\_io\_percent 
* Uso medio de registro: log\_write\_percent 
* Uso medio de memoria: memory\_percent 
* Promedio de uso de eDTU (como valor máximo de uso de CPU, E/S, registro: DTU\_percent 
* Número máximo de solicitudes de usuario simultáneas (trabajadores): max\_concurrent\_requests 
* Número máximo de sesiones de usuario simultáneas: max\_concurrent\_sessions 
* Tamaño de almacenamiento total para el grupo elástico: storage\_in\_megabytes 


Métricas de períodos de retención y granularidad:

* Los datos se devolverán con una granularidad de 5 minutos.  
* La retención de datos es de 14 días.  


Este cmdlet y la API limita el número de filas que se pueden recuperar en una llamada a 1000 filas (aproximadamente 3 días de datos con una granularidad de 5 minutos). Sin embargo, este comando se puede llamar varias veces con distintos intervalos de tiempo de inicio y fin para recuperar más datos.


Recuperación de las métricas:

	$metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Repita la llamada y anexe los datos para obtener días adicionales:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Asigne formato a la tabla:

    $table = Format-MetricsAsTable $metrics 

Exportación a un archivo CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

## Obtención de métricas de consumo de una base de datos elástica

Estas API son las mismas que las API (V12) actuales que se usan para supervisar el uso de recursos de una base de datos independiente, excepto por la diferencia semántica siguiente.

* Para esta API, las métricas recuperadas se expresan como un porcentaje de databaseDtuMax (o cap equivalente para la métrica subyacente como CPU, E/S etc.) establecido para ese grupo. Por ejemplo, el 50% de utilización de cualquiera de estas métricas indica que el consumo de recursos específicos es del 50% del límite de capacidad por cada base de datos para dicho recurso en el grupo principal. 

Obtenga las métricas:

    $metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Repita la llamada y anexe los datos para obtener días adicionales si es necesario:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Asigne formato a la tabla:

    $table = Format-MetricsAsTable $metrics 

Exportación a un archivo CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Latencia de las operaciones de grupos elásticos

- El cambio del número garantizado de eDTU por base de datos (DatabaseDtuMin) o del número máximo de eDTU por base de datos (DatabaseDtuMax) suele completarse en cinco minutos o menos.
- El cambio del límite de eDTU o de almacenamiento (DTU) del grupo depende de la cantidad total de espacio que usen todas las bases de datos del grupo. Los cambios tienen un duración media de 90 minutos o menos por cada 100 GB. Por ejemplo, si el espacio total que usan todas las bases de datos del grupo es de 200 GB, la latencia esperada para el cambio del límite de eDTU o de almacenamiento es de tres horas o menos.


## Ejemplo de PowerShell de supervisión y administración de un grupo


    $subscriptionId = '<Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'
    
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    
    Set-AzureRmSqlElasticPool –ResourceGroupName $resourceGroupName –ServerName $serverName –ElasticPoolName $poolName –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $databaseName 
    $startTime1 = '2/10/2016'
    $endTime1 = '2/14/2016'
    $startTime2 = '2/14/2016'
    $endTime2 = '2/18/2016'
    
    
    
    $metrics = (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics
    
    $metrics = $metrics + (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}
    
    $metrics = (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics = $metrics + (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Pasos siguientes

- [Creación de trabajos elásticos](sql-database-elastic-jobs-overview.md): los trabajos elásticos permiten ejecutar scripts de T-SQL en cualquier cantidad de bases de datos del grupo.

<!---HONumber=AcomDC_0413_2016-->