<properties
	pageTitle="Script de PowerShell para identificar bases de datos individuales adecuadas para un grupo"
	description="Un grupo de bases de datos elásticas es una colección de recursos disponibles que comparte un grupo de bases de datos elásticas. Este documento ofrece un script de Powershell para ayudarle a evaluar la idoneidad de usar un grupo de bases de datos elásticas para un grupo de base de datos."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>

# Script de PowerShell para identificar bases de datos adecuadas para un grupo de bases de datos elásticas

El script de ejemplo de PowerShell que se ofrece en este artículo calcula los valores agregados de eDTU para bases de datos de usuario de un servidor de Base de datos SQL. El script recopila datos mientras se ejecuta y, para una carga de trabajo de producción típica, debe ejecutar el script durante al menos un día. Lo ideal es ejecutar el script durante un tiempo que represente la carga de trabajo típica de sus bases de datos, es decir, lo suficientemente largo como para capturar datos que representen el uso normal y de máxima demanda para las bases de datos. La ejecución del script una semana o incluso más tiempo probablemente dará un cálculo más preciso.

Este script es especialmente útil para evaluar las bases de datos en servidores v11, donde no se admiten grupos, y para migrar a servidores v12, donde se admiten los grupos. En servidores v12, Base de datos SQL cuenta con inteligencia integrada que analiza la telemetría del historial de uso y recomienda un grupo cuando resulte más rentable. Para obtener información sobre cómo usar esta característica, consulte [Monitor, manage, and size an elastic database pool (Supervisión, administración y cambio de tamaño de un grupo de bases de datos elásticas)](sql-database-elastic-pool-manage-portal.md).

> [AZURE.IMPORTANT] Debe mantener la ventana de PowerShell abierta cuando ejecute el script. No cierre la ventana de PowerShell hasta que haya ejecutado el script durante la cantidad de tiempo necesario.

## Requisitos previos 

Instale lo siguientes antes de ejecutar el script:

- Las últimas [herramientas de línea de comandos de Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- El [paquete de características de SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).

### Detalles del script

Puede ejecutar el script desde la máquina local o una máquina virtual en la nube. Cuando lo ejecute desde la máquina local, es posible que se produzcan cargos de salida de datos porque el script tiene que descargar datos de las bases de datos de destino. A continuación se muestra la estimación de volumen de datos según el número de bases de datos de destino y la duración de la ejecución del script. Para ver los costes de transferencia de datos de Azure, consulte [Detalles de precios de transferencia de datos](https://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 base de datos por hora = 38 KB
 -     1 base de datos por día = 900 KB
 -     1 base de datos por semana = 6 MB
 -     100 bases de datos por día = 90 MB
 -     500 bases de datos por semana = 3 GB

El script excluye las bases de datos que no son buenos candidatos para la oferta de vista previa pública actual del nivel Estándar. Si necesita excluir bases de datos adicionales del servidor de destino, cambie el script para que cumpla los criterios. De forma predeterminado, el script no compila información para lo siguiente:

* Bases de datos elásticas (bases de datos que ya se encuentran en un grupo elástico)
* La base de datos maestra del servidor

El script necesita una base de datos de salida para almacenar datos intermedios para el análisis. Puede usar una nueva base de datos o una existente. Aunque no se requiere técnica mente que la herramienta se ejecute, la base de datos de salida debe estar en un servidor distinto para evitar el impacto en el resultado del análisis. El nivel de rendimiento de la base de datos de salida debe ser al menos S0 o superior. Cuando recopile datos para un número grande de bases de datos durante un período largo de tiempo, puede ser conveniente actualizar la base de datos de salida a un nivel de rendimiento superior.

El script tiene que proporcionarle las credenciales para conectarse al servidor de destino (el candidato del grupo de bases de datos elásticas) con un nombre de servidor completo, <*nombrebasededatos*>**.database.windows.net**. El script no es compatible con el análisis de varios servidores a la vez.

Después de enviar valores para el conjunto inicial de parámetros, se le pedirá que inicie sesión en la cuenta de Azure. Esto es para iniciar sesión en el servidor de destino, no en el servidor de base de datos de salida.
	
Si detecta las siguientes advertencias cuando ejecute el script, puede ignorarlas:

- ADVERTENCIA: El cmdlet Switch-AzureMode está en desuso.
- ADVERTENCIA: No se pudo obtener la información de servicio de SQL Server. Se produjo un error al intentar conectar con VMI en 'Microsoft.Azure.Commands.Sql.dll' con el siguiente error: El servidor RPC no está disponible.

Cuando el script se completa, proporciona el número estimado de eDTU necesarias para que un grupo contenga todas las bases de datos candidatas en el servidor de destino. Estas eDTU estimadas se pueden usar para crear y configurar el grupo. Una vez que se crea el grupo y las bases de datos se trasladan al grupo, supervise este estrechamente durante algunos días y realice los ajustes que se requieran en la configuración de eDTU de grupo. Consulte [Monitor, manage, and size an elastic database pool (Supervisión, administración y cambio de tamaño de un grupo de bases de datos elásticas)](sql-database-elastic-pool-manage-portal.md).


   [AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)
    
    param (
	[Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
	[Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
	[Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
	[Parameter(Mandatory=$true)][string]$username, # user name
	[Parameter(Mandatory=$true)][string]$serverPassword, # password
	[Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
	[Parameter(Mandatory=$true)][string]$outputdatabaseName,
	[Parameter(Mandatory=$true)][string]$outputDBUsername,
	[Parameter(Mandatory=$true)][string]$outputDBpassword,
	[Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
	)
	
	Login-AzureRmAccount
	Select-AzureSubscription $AzureSubscriptionName
	
	$server = Get-AzureRmSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
	
	# Check version/upgrade status of the server
	$upgradestatus = Get-AzureRmSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
	$version = ""
	if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
	{
	$version = $upgradestatus.Status
	}
	else
	{
	$version = $server.ServerVersion
	}
	
	# For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
	$ListOfDBs = Get-AzureRmSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
	
	$outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
	$destinationTableName = "resource_stats_output"
	
	# Create a table in output database for metrics collection
	$sql = "
	IF  NOT EXISTS (SELECT * FROM sys.objects 
	WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
	
	BEGIN
	Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
	Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
	END
	TRUNCATE TABLE $($destinationTableName);
	"
	Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
	
	# waittime (minutes) is interval between data collection queries in the loop below.
	$Waittime = 10
	$end_Time = [DateTime]::UtcNow
	$start_time = $end_time.AddMinutes(-$Waittime)
	$finish_time = $end_Time.AddMinutes($duration_minutes)
	
	While ($end_time -lt $finish_time)
	{
	Write-Host "Collecting metrics..." 
	foreach ($db in $ListOfDBs)
	{
	if ($version -in ("12.0", "Completed")) # for V12 databases 
	{
	$sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
	$sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceObjectiveName)';"
	$sql+= "
	Declare @DTU_cap int, @db_size float;
	Select @DTU_cap = CASE @SLO 
	WHEN 'Basic' THEN 5
	WHEN 'S0' THEN 10
	WHEN 'S1' THEN 20
	WHEN 'S2' THEN 50
	WHEN 'S3' THEN 100
	WHEN 'P1' THEN 125
	WHEN 'P2' THEN 250
	WHEN 'P3' THEN 1000
	ELSE 50 -- assume Web/Business DBs
	END
	SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
	SELECT @dbname as database_name, @SLO as SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
	as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
	WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
	" 
	}
	else
	{
	$sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
	$sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceObjectiveName)';"
	$sql+= "
	Declare @DTU_cap int, @db_size float;
	Select @DTU_cap = CASE @SLO 
	WHEN 'Basic' THEN 5
	WHEN 'S0' THEN 10
	WHEN 'S1' THEN 20
	WHEN 'S2' THEN 50
	WHEN 'P1' THEN 100
	WHEN 'P2' THEN 200
	WHEN 'P3' THEN 800
	ELSE 50 -- assume Web/Business DBs
	END
	SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
	SELECT @dbname as database_name, @SLO as SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
	as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
	WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
	" 
	}
	
	$result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
	#bulk copy the metrics to output database
	$bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
	$bulkCopy.BulkCopyTimeout = 600
	$bulkCopy.DestinationTableName = "$destinationTableName";
	$bulkCopy.WriteToServer($result);
	
	}
	
	$start_time = $start_time.AddMinutes($Waittime)
	$end_time = $end_time.AddMinutes($Waittime)
	Write-Host $start_time
	Write-Host $end_time
	do {
	Start-Sleep 1
	   }
	until (([DateTime]::UtcNow) -ge $end_time)
	}
	
	Write-Host "Analyzing the collected metrics...."
	# Analysis query that does aggregation of the resource metrics to calculate pool size.
	$sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
	WITH group_stats AS
	(
	SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
	FROM resource_stats_output 
	WHERE slo LIKE '
	
	$sql2 = '
	GROUP BY end_time
	)
	-- calculate aggregate storage and DTUs for all DBs in the group
	, group_DTU AS
	(
	SELECT end_time, avg_group_Storage, 
	(SELECT Max(v)
	   FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
	FROM group_stats
	)
	-- Get top 1 percent of the storage and DTU utilization samples.
	, top1_percent AS (
	SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
	)
	
	-- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
	--SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
	SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
	IF @DTU_Storage > @DTU_Perf_99 
	SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
	ELSE 
	SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
	
	#check if there are any web/biz edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "Shared*")
	{
	write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'Shared%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]}
	}
	
	#check if there are any basic edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "Basic*")
	{
	write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'Basic%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]} 
	}
	
	#check if there are any standard edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "S*")
	{
	write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]}
	}
	
	#check if there are any premium edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "P*")
	{
	write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'P%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]}
}

        

<!---HONumber=AcomDC_0413_2016-->