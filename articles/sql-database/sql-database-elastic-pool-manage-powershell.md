---
title: "PowerShell: Creación y administración de un grupo elástico de Azure SQL | Microsoft Docs"
description: "Aprenda a usar PowerShell para administrar un grupo elástico."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 61289770-69b9-4ae3-9252-d0e94d709331
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 06/22/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 637171b775d01e16cec1a7e9ef6fad73875eac69
ms.openlocfilehash: e04a802b662954ff22cf44e144982dbf0964da8e
ms.lasthandoff: 02/08/2017


---

# <a name="create-and-manage-an-elastic-pool-with-powershell"></a>Creación y administración de un grupo elástico con PowerShell
En este tema se muestra cómo crear y administrar [grupos elásticos](sql-database-elastic-pool.md) escalables con PowerShell.  También puede crear y administrar un grupo elástico de Azure en [Azure Portal](https://portal.azure.com/), la API de REST o [C#][Creación y administración de un grupo elástico con C#](sql-database-elastic-pool-manage-csharp.md). También puede crear y mover bases de datos dentro y fuera de los grupos elásticos mediante [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-elastic-pool"></a>Creación de un grupo elástico
El cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) crea un grupo elástico. Los valores de eDTU por grupo, DTU mín. y máx. están limitados por el valor de nivel de servicio (basic, standard o premium). Consulte la sección [Límites de almacenamiento y de eDTU para grupos elásticos y bases de datos agrupadas](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>Creación de una base de datos agrupada en un grupo elástico
Use el cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) y establezca el parámetro **ElasticPoolName** en el grupo de destino. Para mover una base de datos existente a un grupo elástico, consulte [Movimiento de una base de datos a un grupo elástico](sql-database-elastic-pool-manage-powershell.md#move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### <a name="complete-script"></a>Completar script
Este script crea un grupo de recursos de Azure y un servidor. Cuando se le solicite, proporcione un nombre de usuario y una contraseña de administrador para el nuevo servidor (no las credenciales de Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB

## <a name="create-an-elastic-pool-and-add-multiple-pooled-databases"></a>Creación de un grupo elástico y adición de varias bases de datos agrupadas
La creación de varias bases de datos en un grupo elástico puede tardar tiempo cuando se realiza mediante el portal o los cmdlets de PowerShell que crean una base de datos única cada vez. Para automatizar la creación de un grupo elástico, consulte [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="move-a-database-into-an-elastic-pool"></a>Movimiento de una base de datos a un grupo elástico
Puede mover una base de datos dentro o fuera de un grupo elástico con el cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx).

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-an-elastic-pool"></a>Cambio de la configuración de rendimiento de un grupo elástico
Cuando el rendimiento se ve afectado, puede cambiar la configuración del grupo para adaptarse al crecimiento. Utilice el cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511\(v=azure.300\).aspx). Establezca el parámetro -Dtu en las eDTU por grupo. Consulte los posibles valores en el artículo sobre [límites de almacenamiento y de eDTU](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).  

    Set-AzureRmSqlElasticPool -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1” -Dtu 1200 -DatabaseDtuMax 100 -DatabaseDtuMin 50

## <a name="get-the-status-of-pool-operations"></a>Obtención del estado de las operaciones de los grupos
La creación de un grupo elástico puede llevar tiempo. Puede realizar un seguimiento del estado de las operaciones del grupo, como la creación y las actualizaciones, mediante el cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812\(v=azure.300\).aspx).

    Get-AzureRmSqlElasticPoolActivity -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1”

## <a name="get-the-status-of-moving-a-database-into-and-out-of-an-elastic-pool"></a>Obtención del estado del movimiento de una base de datos dentro y fuera de un grupo elástico
El proceso de mover bases de datos puede llevar tiempo. Realice un seguimiento del estado del movimiento mediante el cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687\(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>Obtención de datos de uso de recursos para un grupo elástico
Métricas que se pueden recuperar como porcentaje del límite del grupo de recursos:   

| Nombre de métrica | Descripción |
|:--- |:--- |
| cpu\_percent |Uso de proceso medio en porcentaje del límite del grupo. |
| physical\_data\_read\_percent |Uso de E/S medio en porcentaje basado en el límite del grupo. |
| log\_write\_percent |Uso de recursos de escritura medio en porcentaje del límite del grupo. |
| DTU\_consumption\_percent |Uso de eDTU medio en porcentaje del límite de eDTU del grupo. |
| storage\_percent |Uso de almacenamiento medio en porcentaje del límite de almacenamiento del grupo. |
| workers\_percent |Cantidad máxima de trabajos simultáneos (solicitudes) en porcentaje basado en el límite del grupo. |
| sessions\_percent |Cantidad máxima de sesiones simultáneas en porcentaje basado en el límite del grupo. |
| eDTU_limit |Configuración de cantidad máxima de DTU de grupos elásticos actual para este grupo elástico durante este intervalo. |
| storage\_limit |Configuración de límite máximo de almacenamiento de grupos elásticos actual para este grupo elástico en megabytes durante este intervalo. |
| eDTU\_used |Cantidad media de eDTU que usa el grupo en este intervalo. |
| storage\_used |Cantidad media de almacenamiento en bytes que usa el grupo en este intervalo. |

**Métricas de períodos de retención y granularidad:**

* Los datos se devuelven con una granularidad de 5 minutos.  
* La retención de datos es de 35 días.  

Este cmdlet y la API limitan el número de filas que se pueden recuperar en una llamada a 1000 filas (aproximadamente 3 días de datos con una granularidad de 5 minutos). Sin embargo, este comando se puede llamar varias veces con distintos intervalos de tiempo de inicio y fin para recuperar más datos.

Para recuperar las métricas, siga estos pasos:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  

## <a name="get-resource-usage-data-for-a-database-in-an-elastic-pool"></a>Obtención de datos de uso de recursos de una base de datos en un grupo elástico
Estas API son las mismas que las API actuales (V12) que se utilizan para supervisar el uso de recursos de una base de datos única, salvo por la diferencia semántica siguiente: las métricas recuperadas se expresan como un porcentaje del eDTU máximo por base de datos (o límite equivalente para la métrica subyacente como CPU o E/S) establecido para ese grupo. Por ejemplo, el 50 % de uso de cualquiera de estas métricas indica que el consumo de recursos específicos es del 50 % del límite de capacidad por cada base de datos de dicho recurso del grupo principal.

Para recuperar las métricas, siga estos pasos:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

## <a name="add-an-alert-to-an-elastic-pool-resource"></a>Adición de una alerta a un recurso de grupos elásticos
Puede agregar reglas de alerta a un grupo elástico para enviar notificaciones por correo electrónico o cadenas de alertas a [puntos de conexión de URL](https://msdn.microsoft.com/library/mt718036.aspx) cuando un grupo elástico alcanza el umbral de utilización establecido. Use el cmdlet Add-AzureRmMetricAlertRule.

> [!IMPORTANT]
> La supervisión del uso de recursos de grupos elásticos tiene un retraso de, al menos, 20 minutos. En estos momentos, no se pueden establecer alertas de menos de 30 minutos para grupos elásticos. Es posible que no se desencadenen las alertas establecidas para grupos elásticos con un período inferior a 30 minutos (parámetro denominado "WindowSize" en la API de PowerShell). Asegúrese de que las alertas que defina para grupos elásticos utilicen un período (WindowSize) de 30 minutos o más.
>
>

En este ejemplo se agrega una alerta para recibir una notificación cuando el consumo de eDTU de un grupo elástico supere un umbral determinado.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

## <a name="add-alerts-to-all-databases-in-an-elastic-pool"></a>Adición de alertas a todas las bases de datos de un grupo elástico
Puede agregar reglas de alerta a todas las bases de datos de un grupo elástico para enviar notificaciones por correo electrónico o cadenas de alerta a [puntos de conexión de URL](https://msdn.microsoft.com/library/mt718036.aspx) cuando un recurso alcanza el umbral de utilización establecido por la alerta.

> [!IMPORTANT]
> La supervisión del uso de recursos de grupos elásticos tiene un retraso de, al menos, 20 minutos. En estos momentos, no se pueden establecer alertas de menos de 30 minutos para grupos elásticos. Es posible que no se desencadenen las alertas establecidas para grupos elásticos con un período inferior a 30 minutos (parámetro denominado "WindowSize" en la API de PowerShell). Asegúrese de que las alertas que defina para grupos elásticos utilicen un período (WindowSize) de 30 minutos o más.
>
>

En este ejemplo se agrega una alerta a cada una de las bases de datos de un grupo elástico para recibir una notificación cuando el consumo de DTU de esas bases de datos supere un umbral determinado.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    }

## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Recopilación y supervisión de los datos de uso de recursos entre varios grupos de una suscripción
Si hay muchas bases de datos en una suscripción, es complicado supervisar cada grupo elástico por separado. En su lugar, se pueden combinar las consultas de T-SQL y los cmdlets de PowerShell de Base de datos SQL para recopilar datos de uso de recursos de varios grupos y sus bases de datos para la supervisión y el análisis del uso de recursos. Puede encontrar una [implementación de ejemplo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) de un conjunto similar de scripts de PowerShell en el repositorio de ejemplos de SQL Server de GitHub junto con documentación sobre lo que hace y cómo se utiliza.

Para utilizar esta implementación de ejemplo siga estos pasos.

1. Descargue los [scripts y la documentación](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modifique los scripts para su entorno. Especifique uno o más servidores en los que se alojan los grupos elásticos.
3. Especifique una base de datos de telemetría donde se puedan almacenar las métricas recopiladas.
4. Personalice el script para especificar el tiempo de ejecución de los scripts.

En un nivel alto, los scripts realizan lo siguiente:

* Enumera todos los servidores de una determinada suscripción de Azure (o una lista de servidores especificada).
* Ejecuta un trabajo en segundo plano para cada servidor. El trabajo se ejecuta en un bucle a intervalos regulares y recopila los datos de telemetría de todos los grupos en el servidor. A continuación, carga los datos recopilados en la base de datos de telemetría especificada.
* Enumera una lista de bases de datos en cada grupo para recopilar los datos de uso de recursos de base de datos. A continuación, carga los datos recopilados en la base de datos de telemetría.

Para supervisar el estado de los grupos elásticos y de las bases de datos en tales grupos, se pueden analizar las métricas recopiladas en la base de datos de telemetría. El script también instala una función de valores de tabla (TVF) predefinida en la base de datos de telemetría para ayudar a agregar las métricas para un período de tiempo especificado. Por ejemplo, los resultados de la función TVF pueden utilizarse para mostrar "los N grupos elásticos que presentan el máximo uso de eDTU en un período de tiempo determinado". Opcionalmente, puede utilizar herramientas de análisis como Excel o Power BI para consultar y analizar los datos recopilados.

### <a name="example-retrieve-resource-consumption-metrics-for-an-elastic-pool-and-its-databases"></a>Ejemplo: recuperación de métricas de consumo de recursos de un grupo elástico y de sus bases de datos
En este ejemplo se recuperan las métricas de consumo de un grupo elástico determinado y de todas sus bases de datos. Se da formato a los datos recopilados y se escriben en un archivo .csv. El archivo puede consultarse con Excel.

    $subscriptionId = '<Azure subscription id>'          # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name

    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC

    # Construct the pool resource ID and retrive pool metrics at 5-minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }

    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) {
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name;
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId
          }
          $table = $table += $sx
      }
    }

    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }

    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv

    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv

## <a name="latency-of-elastic-pool-operations"></a>Latencia de las operaciones de grupos elásticos
* El cambio del número mínimo de eDTU por base de datos o del máximo de eDTU por base de datos suele completarse en cinco minutos o menos.
* El cambio de eDTU por grupo depende de la cantidad total de espacio que usen todas las bases de datos del grupo. Los cambios tienen un duración media de 90 minutos o menos por cada 100 GB. Por ejemplo, si el espacio total que usan todas las bases de datos del grupo es de 200 GB, la latencia esperada para cambiar las eDTU de grupo por grupo es de 3 horas o menos.

Para obtener documentación de referencia acerca de estos cmdlets de Powershell, consulte:

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582\(v=azure.300\).aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403\(v=azure.300\).aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589\(v=azure.300\).aspx)

El cmdlet Stop- significa cancelar, no pausar. La única forma de reanudar una actualización es volver a iniciarla desde el principio. El cmdlet Stop- limpia y libera todos los recursos adecuados.

## <a name="next-steps"></a>Pasos siguientes
* [Creación de trabajos elásticos](sql-database-elastic-jobs-overview.md) : los trabajos elásticos le permiten ejecutar scripts de T-SQL en cualquier cantidad de bases de datos del grupo.
* Consulte [Escalado horizontal con Azure SQL Database](sql-database-elastic-scale-introduction.md): use herramientas elásticas para realizar un escalado horizontal, mover los datos, realizar consultas o crear transacciones.

