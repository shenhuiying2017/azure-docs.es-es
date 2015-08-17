<properties 
	pageTitle="Consideraciones de precio y rendimiento par aun grupo de bases de datos elásticas | Grupos de bases de datos elásticas de Base de datos SQL de Azure" 
	description="Un grupo de bases de datos elásticas es una colección de recursos disponibles que comparte un grupo de bases de datos elásticas. Este documento ofrece orientación para ayudarle a evaluar la idoneidad de usar un grupo de bases de datos elásticas para un grupo de base de datos." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/24/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas


Este documento ofrece orientación para ayudar a evaluar si usar un grupo de bases de datos elásticas para un grupo de bases de datos es rentable según los patrones de uso de la base de datos y las diferencias de precios entre un grupo elástico y bases de datos únicas. También se proporciona orientación adicional para ayudar a determinar el tamaño actual del grupo necesario para un conjunto de bases de datos SQL existente.

- Para obtener información general de grupos de base de datos elásticas, vea [Grupos de bases de datos elásticas de Base de datos SQL](sql-database-elastic-pool.md).
- Para obtener información detallada sobre los grupos de bases de datos elásticas, vea [Referencia de grupos de bases de datos elásticas de Base de datos SQL](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Los grupos elásticos están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12.

## Grupos de bases de datos elásticas

Los ISV de SaaS desarrollan aplicaciones basadas en los niveles superiores de datos de la escala que constan de varias bases de datos. Un patrón de aplicación común es disponer de clientes distintos para cada una de estas bases de datos con patrones de uso no predecibles y variables exclusivamente. Puede ser complicado para ISV predecir los requisitos del recurso de cada base de datos individualmente. En estas circunstancias, el ISV puede realizar un aprovisionamiento excesivo de los recursos con un gasto considerable para garantizar un rendimiento y unos tiempos de repuesta favorables para todas las bases de datos. El ISV también puede gastar menos y arriesgar en una experiencia de rendimiento insuficiente para sus clientes.

Los grupos de bases de datos elásticas en Base de datos SQL de Azure permiten a los ISV de SaaS optimizar el rendimiento del precio para un grupo de bases de datos dentro de un presupuesto prescrito a la vez que se ofrece elasticidad de rendimiento para cada base de datos. Los grupos elásticos permiten al ISV adquirir unidades de rendimiento de base de datos (DTU) para un grupo de elástico compartido por varias bases de datos con el fin de acomodar períodos que no se pueden predecir de uso por parte de bases de datos individuales. El requisito de DTU para un grupo elástico está determinado por la utilización de la adición de sus bases de datos. La cantidad de DTU disponible para el grupo elástico está controlada por el presupuesto de ISV. Los grupos elásticos facilitan al ISV razonar el impacto del presupuesto en el rendimiento y viceversa para su grupo. El ISV simplemente agrega bases de datos al grupo elástico, establece las garantías o limitaciones de DTU necesarias para las bases de datos y luego establece la DTU del grupo según el presupuesto. Mediante el uso de grupos elásticos, los ISV pueden aumentar de forma eficiente su servicio a partir de un método Lean Startup hasta un negocio con madurez a una escala cada vez mayor.
  


## Cuándo considerar un grupo de bases de datos elásticas

Los grupos elásticos son apropiados para un amplio número de bases de datos con patrones de utilización específicos. Para una base de datos determinada, este patrón está caracterizado por una utilización media baja con picos de utilización relativamente poco frecuentes.

Conforme más bases de datos pueda agregar a un grupo, más ahorros supondrá, pero según el patrón de utilización de la aplicación, es posible que se produzcan ahorros tan solo en 4 bases de datos S3.

Las siguientes secciones le ayudarán a comprender cómo evaluar si la recopilación específica de bases de datos se beneficiará del uso de un grupo elástico.

### Evaluación de los patrones de utilización de base de datos

La siguiente ilustración muestra un ejemplo de una base de datos que está mucho tiempo inactiva, pero que también tiene picos periódicos de actividad. Se trata de un patrón de utilización que es apropiado para un grupo elástico:
 
   ![una base de datos][1]

Para el período de una hora que aparece a continuación, DB1 llega a las 90 DTU, pero el uso medio es inferior a 5 DTU. Se requiere un nivel de rendimiento S3 para ejecutar esta carga de trabajo en una única base de datos. Sin embargo, esto hace que la mayoría de los recursos no se use durante períodos de baja actividad.

Un grupo elástico permite que estas DTU sin usar se compartan en varias bases de datos y de esa manera se reduce la cantidad total de DTU necesaria y el coste general.

Según el ejemplo anterior, suponga que existen bases de datos adicionales con patrones de utilización similares como DB1. En las siguientes dos figuras, la utilización de 4 bases de datos y 20 bases de datos se estratifican en el mismo gráfico para mostrar la naturaleza de no solapamiento de la utilización con el paso del tiempo:

   ![cuatro bases de datos][2]

   ![veinte bases de datos][3]

La utilización de la DTU agregada en las 20 bases de datos se muestra con la línea negra en la ilustración anterior. Esto muestra que la utilización de la DTU agregada nunca supera las 100 DTU e indica que las 20 bases de datos pueden compartir 100 DTU en este período. Esto produce una reducción multiplicada por 20 en las DTU y una reducción del precio 6 veces menor en comparación con la colocación de cada base de datos en los niveles de rendimiento S3 para bases de datos únicas.


Este ejemplo es ideal por las siguientes razones:

- Existen grandes diferencias entre la utilización de picos y la utilización media por base de datos.  
- La utilización de picos para cada base de dato se produce en puntos de tiempo distintos. 
- Las DTU se comparten entre un gran número de base de datos.


El precio para un grupo elástico es una función de las DTU de grupo y el número de bases de datos que contiene. Aunque el precio unitario de DTU para un grupo en el precio de GA es 3 veces mayor que el precio unitario de DTU para una única base de datos, las DTU de grupo pueden compartirse con muchas bases de datos y en muchos casos reducir las DTU totales necesarias. Estas distinciones de precio y uso compartido de la DTU son la base de la posibilidad de ahorro en el precio que pueden proporcionar los grupos.

<br>

Las siguientes reglas generales relacionadas con el recuento de base de datos y la utilización de base de datos ayudan a garantizar que un grupo elástico proporciona costes reducidos en comparación con el uso de niveles de rendimiento para bases de datos únicas. La guía se basa en los precios de disponibilidad general (GA). Tenga en cuenta que los precios de GA tienen un descuento del 50 % durante la vista previa, por lo que debe tener en cuenta que estas reglas generales están sujetas a cambio.


### Número mínimo de bases de datos

Con el precio de GA, un grupo elástico se convierte en una opción de rendimiento rentable si se puede compartir 1 DTU mediante más de 3 bases de datos. Esto significa que la suma de DTU de niveles de rendimiento para base de datos únicas es tres veces superior a las DTU del grupo. Para tamaños disponibles, consulte [Límites de DTU y de almacenamiento de grupos y bases de datos elásticas](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


***Ejemplo***<br> Al menos 4 bases de datos S3 o 36 bases de datos S0 son necesarias para que un grupo elástico de 100 DTU sea más rentable que usar niveles de rendimiento para bases de datos únicas. (Tenga en cuenta que con los precios de vista previa, el punto del umbral de precios basado en el recuento de base de datos se reduce a 2 bases de datos S3 o 17 bases de datos S0).



### Número máximo de bases de datos de picos simultáneamente

Con el uso compartido de DTU, no todas las bases de datos en un grupo pueden usar de forma simultánea DTU hasta el límite disponible cuando se usan niveles de rendimiento para bases de datos únicas. Conforme menos bases de datos con un pico simultáneo hayan, menos DTU de grupo pueden establecerse y más rentable es. En general, no más de 1/3 de las bases de datos en el grupo deben establecer un pico de forma simultánea con el límite de DTU.

***Ejemplo***<br> Para reducir los costes de 4 bases de datos S3 en un grupo de 200 DTU, como máximo 2 de estas bases de datos pueden establecer el pico de forma simultánea en su utilización. De lo contrario, si más de 2 de estas 4 bases de datos S3 establecen el pico de forma simultánea, tendría que establecerse un tamaño del grupo en más de 200 DTU. Además, si se cambia el tamaño del grupo a más de 200 DTU, sería necesario agregar más bases de datos S3 al grupo para que los costes siguieran siendo inferiores a los niveles de rendimiento para bases de datos únicas.


Tenga en cuenta que este ejemplo no tiene en cuenta la utilización de otras bases de datos en el grupo. Si todas las bases de datos tienen una utilización en un punto determinado en el tiempo, menos de 1/3 de las bases de datos podrán establecer un pico de forma simultánea.


### Utilización de DTU por base de datos

Una gran diferencia entre el pico y la utilización media de una base de datos indica largos períodos de poca utilización y breves períodos de uso intenso. Este patrón de uso es ideal para compartir recursos entre bases de datos. Debe considerarse una base de datos para un grupo cuando la utilización pico es aproximadamente 3 veces mayor que su utilización media.

    
***Ejemplo***<br> Una base de datos S3 que establece un pico a 100 DTU y de media usa 30 DTU o menos es una buena candidata para compartir DTU en un grupo elástico. O bien, una base de datos S1 con un pico de hasta 20 DTU y que de media usa 7 DTU o menos es una buena candidata para un grupo elástico.
    

## Heurística para comparar la diferencia de precio entre un grupo elástico y bases de datos únicas 

La siguiente heurística puede ayudar a estimar si un grupo elástico es más rentable que el uso de bases de datos únicas individuales.

1. Estime las DTU necesarias para el grupo de la siguiente forma:
    
    MAX(*Número total de BD* * *uso medio de DTU por BD*, *número de BD con picos simultáneos* * *Uso de DTU de pico por BD*)

2. Seleccione el valor de DTU disponible más pequeño para el grupo que sea superior al estimado del paso 1. Para ver las opciones de DTU disponibles, consulte los valores válidos para DTU que aparecen aquí [Límites de DTU y de almacenamiento de grupos y bases de datos elásticas](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

    


3. Cálculo del precio del grupo de la siguiente forma:

    precio del grupo = (*DTU de grupo* * *precio unitario de DTU de grupo*) + (*número total de BD* * *precio unitario de BD de grupo*)

    Consulte [Precios de Base de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/) para obtener información sobre precios.


4. Compare el precio del grupo del paso 3 con el precio de uso de los niveles de rendimiento adecuados para bases de datos únicas.



## Determinación del mejor tamaño de DTU de grupo para Bases de datos SQL existentes 

El mejor tamaño para un grupo elástico depende de las DTU agregadas y los recursos de almacenamiento necesarios para todas las bases de datos en el grupo. Esto implica determinar la cantidad mayor de las siguientes dos cantidades:

* Número máximo de DTU utilizado por todas las bases de datos en el grupo.
* Número máximo de bytes de almacenamiento utilizado por todas las bases de datos en el grupo. 

Tenga en cuenta que para el nivel de servicio Standard, se permite 1 GB de almacenamiento para 1 DTU configurada para el grupo. Por ejemplo, si se ha configurado un grupo con 200 DTU, el límite de almacenamiento es de 200 GB.

### Use Service Tier Advisor (STA) y Vistas de administración dinámica (DMV) para ver las recomendaciones de tamaño   

STA y DMV proporcionan distintas opciones de herramientas distintas y capacidades para establecer el tamaño de un grupo elástico. Independientemente de la opción de herramienta usada, la estimación del tamaño solo debe usarse para la evaluación inicial y la creación de grupos elásticos. Una vez que se crea un grupo elástico, el uso de recursos debe supervisarse de forma apropiada y la configuración del rendimiento del grupo debe aumentarse o reducirse según sea necesario.

**STA**<br>STA es una herramienta integrada en el Portal de Azure que evalúa automáticamente el historial de uso de recursos de bases de datos en un servidor de Base de datos SQL existente y recomienda una configuración de grupo elástico apropiada.

**Herramienta de tamaño de DMV**<br>La herramienta de tamaño de DMV se proporciona como script de PowerShell y habilita la personalización de las estimaciones del tamaño de un grupo elástico para bases de datos existentes en un servidor.

### Elección entre la herramienta DMV y STA 

Seleccione la herramienta apropiada para el análisis de la aplicación específica. La siguiente tabla resume las diferencias entre estas dos herramientas de establecimiento de tamaño:

| Capacidad | STA | DMV |
| :--- | :--- | :--- |
| Granularidad de las muestras de datos utilizadas para el análisis. | 15 segundos | 15 segundos |
| Tiene en cuenta las diferencias de precio entre un grupo y los niveles de rendimiento de bases de datos únicas. | Sí | No |
| Permite personalizar la lista de las bases de datos analizados dentro de un servidor. | No | Sí |
| Permite personalizar el período que se utiliza en el análisis. | No | Sí |


### Estimación del tamaño del grupo elástico mediante STA  

STA evalúa el historial de uso de las bases de datos y recomienda un grupo elástico cuando sea más rentable que el uso de niveles de rendimiento para bases de datos únicas. Si se recomienda un grupo elástico, la herramienta proporciona una lista de las bases de datos recomendadas y también la cantidad recomendada de DTU del grupo y la configuración de DTU mín./máx. para cada base de datos elástica. Para que una base de datos se considere una candidata para un grupo elástico, debe tener una existencia mínima de 14 días. La vista previa pública del grupo de base de datos elástica está limitada solo a la base de datos Standard, por lo que las Premium no se consideran aún candidatas para un grupo elástico.

STA está disponible en el Portal de Azure cuando se agrega una herramienta elástica a un servidor existente. Si las recomendaciones para un grupo elástico están disponibles para ese servidor, se muestran en la hoja de creación Grupo de bases de datos elásticas. Los clientes siempre pueden cambiar las configuraciones recomendadas para crear sus propios grupos elásticos.

### Estimación del tamaño de grupos elásticos con vista de administración dinámica (DMV) 

[sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV mide el uso de recursos de una base de datos individual. Este DMV proporciona CPU, IO, registro y utilización de registros de una base de datos como un porcentaje del límite del nivel de rendimiento de la base de datos. Este dato puede usarse para calcular la utilización de DTU de una base de datos en un intervalo determinado de 15 segundos.

La utilización de DTU agregada para un grupo elástico en un intervalo de 15 segundos puede estimarse mediante la adición de la utilización de DTU para todas las bases de datos de candidatos durante ese período. Según los objetivos de rendimiento específicos, puede tener sentido descartar un pequeño porcentaje de datos de muestra. Por ejemplo, puede aplicarse un valor con un percentil 99 de DTU agregada en todos los intervalos de tiempo para excluir los elementos atípicos y proporcionar una DTU de grupo elástico para que coincida con el 99 % de los intervalos de tiempo de la muestra.

## Script de PowerShell para la estimación del uso de la DTU agregada de bases de datos

Un script de PowerShell de ejemplo se proporciona aquí para calcular los valores agregados de DTU para bases de datos de usuario en un servidor de base de datos SQL.

El script solo recopila datos cuando está en ejecución. Para una carga de trabajo de producción típica, debe ejecutar el script para al menos un día, aunque una semana e incluso más ofrecerá probablemente una estimación más apropiada. Ejecute el script para una duración que represente la carga de trabajo típica de las bases de datos.

> [AZURE.IMPORTANT]Debe mantener la ventana de PowerShell abierta cuando ejecute el script. No cierre la ventana de PowerShell hasta que haya ejecutado el script durante una cantidad de tiempo suficiente y capturado suficientes datos para representar la carga de trabajo típica que divide los tiempos de uso normales y de picos.

### Requisitos previos de script 

Instale lo siguientes antes de ejecutar el script:

- Las últimas [herramientas de la línea de comandos de Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- El [paquete de características de SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).


### Detalles del script


Puede ejecutar el script desde la máquina local o una máquina virtual en la nube. Cuando lo ejecute desde la máquina local, es posible que se produzcan cargos de salida de datos porque el script tiene que descargar datos de las bases de datos de destino. A continuación se muestra la estimación de volumen de datos según el número de bases de datos de destino y la duración de la ejecución del script. Para consultar los costos de transferencia de datos de Azure, consulte [Detalles de precios de transferencia de datos](http://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 base de datos por hora = 38 KB
 -     1 base de datos por día = 900 KB
 -     1 base de datos por semana = 6 MB
 -     100 bases de datos por día = 90 MB
 -     500 bases de datos por semana = 3 GB

El script excluye determinadas bases de datos que no son buenas candidatas para la oferta de vista previa pública actual del nivel de grupo elástico estándar. Si necesita excluir bases de datos adicionales del servidor de destino, puede cambiar el script para cumplir con los criterios. De forma predeterminado, el script no compila información para lo siguiente:

* Todas las bases de datos Premium en servidores V12.
* Bases de datos P2 y P3 en servidores V11.
* Bases de datos elásticas (bases de datos ya en un grupo elástico).
* La base de datos maestra del servidor.

El script necesita una base de datos de salida para almacenar datos intermedios para el análisis. Puede usar una nueva base de datos o una existente. Aunque no se requiere técnica mente que la herramienta se ejecute, la base de datos de salida debe estar en un servidor distinto para evitar el impacto en el resultado del análisis. Sugiera que el nivel de rendimiento de la base de datos de salida sea al menos S0 o superior. Cuando recopile una duración larga de datos para un número grande de bases de datos, puede considerar actualizar la base de datos de salida a un nivel de rendimiento superior.

El script tiene que proporcionarle las credenciales para conectarse al servidor de destino (el candidato del grupo de bases de datos elásticas) con el nombre de servidor completo como “abcdef.database.windows.net”. Actualmente, el script no es compatible con el análisis de varios servidores a la vez.



Después de enviar valores para el conjunto inicial de parámetros, se le solicitará que inicie sesión en la cuenta de Azure. Esto es para iniciar sesión en el servidor de destino, no en el servidor de base de datos de salida.
	
Si detecta las siguientes advertencias cuando ejecute el script, puede ignorarlas:

- ADVERTENCIA: El cmdlet Switch-AzureMode está en desuso.
- ADVERTENCIA: No se pudo obtener la información de servicio de SQL Server. Se produjo un error al intentar conectar con VMI en 'Microsoft.Azure.Commands.Sql.dll' con el siguiente error: El servidor RPC no está disponible.

Cuando el script se completa, proporcionará el número estimado de DTU necesario para que un grupo elástico contenga todas las bases de datos de candidatos en el servidor de destino. Esta DTU estimada puede usarse para crear y configurar un grupo elástico para conservar estas bases de datos. Una vez que se crea el grupo y las bases de datos se trasladan al grupo, debe supervisarse de cerca durante algunos días y deben realizarse algunos ajustes en la configuración de la DTU de grupo según sea necesario.


Para seleccionar el script completo para la copia, haga clic en el texto en el script tres veces (triple clic).

    
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
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, Premium SKU (P1 in previous version still qualifies), and any databases that are already in a pool. You may add more to the excluded list below as needed
    if ($version -in ("12.0", "Completed")) # This is on Azure Database V12. 
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceLevelObjectiveName -notlike ("P*") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notlike ("P*")}
    }
    else # This is previous versions of Azure Database
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool", "P2", "P3") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool", "P2", "P3")}
    }
    
    # Due to pricing model difference, we don't recommend to put less than 3 databases in a pool. 
    if ($ListOfDBs.Count -lt 3)
    {
    Write-Host "There are less than 3 qualified standalone databases in this server. Not a good candidate for Elastic Database Pool!" -ForegroundColor Red
    }
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
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
     foreach ($db in $ListOfDBs)
     {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100 -- for old version of SQL database, P1 has same performance capacity as S3.
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    #write-host $sql
    
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
    
    
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output
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
    SELECT @DTU_Storage AS "Pool Size DTU dominated by storage"
    ELSE 
    SELECT @DTU_Perf_99 AS "Pool Size DTU dominated by resource consumption";'
    
    #write-host $sql
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    
    

## Resumen

No todas las bases de datos únicas son candidatas óptimas para los grupos de bases de datos elásticas. Las bases de datos con patrones de uso que se caractericen por una utilización media baja y picos de utilización relativamente poco frecuentes son candidatas excelentes para los grupos elásticos. Los patrones de uso de aplicaciones son dinámicos. Por lo tanto, use la información y las herramientas descritas en este artículo para realizar una evaluación inicial para ver si un grupo de bases de datos elásticas es una buena opción para algunas de las bases de datos o para todas. Este artículo es tan solo el punto de partida para ayudarle a tomar la decisión de si el grupo de bases de datos elásticas es apropiado para usted. Recuerde que debe seguir supervisando el historial de uso de recursos (con STA o DMV) y continuar reevaluando constantemente los niveles de rendimiento de todas las bases de datos. Tenga en cuenta que puede trasladar fácilmente bases de datos dentro y fuera de grupos elásticos, y si dispone de un gran número de bases de datos, puede contar con varios grupos de distintos tamaños en los que puede dividir las bases de datos.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=August15_HO6-->