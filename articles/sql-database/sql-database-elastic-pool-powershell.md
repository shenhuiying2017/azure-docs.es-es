<properties 
   pageTitle="Creación y administración de un grupo de bases de datos SQL elásticas mediante PowerShell" 
   description="Creación y administración de un grupo de bases de datos SQL elásticas de Azure mediante PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="09/23/2015"
   ms.author="adamkr; sstein"/>

# Creación y administración de un grupo de bases de datos SQL elásticas mediante PowerShell

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Información general

Este artículo muestra cómo crear y administrar un grupo de bases de datos elásticas de Base de datos SQL mediante PowerShell.


Los pasos para crear un grupo de bases de datos elásticas con Azure PowerShell se detallan y explican para mayor claridad. Para aquellos que simplemente buscan una lista breve de comandos, vea la sección de **Resumen** al final de este artículo.

Este artículo muestra cómo crear los elementos necesarios para la creación y configuración de un grupo de bases de datos elásticas, excepto en la suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.

> [AZURE.NOTE]Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con Base de datos SQL V12.


## Requisitos previos

Para crear un grupo de bases de datos elásticas con PowerShell, deberá tener Azure PowerShell instalado y en ejecución y cambiar al modo de administrador de recursos para obtener acceso a los cmdlets de PowerShell del Administrador de recursos de Azure.Para crear un grupo elástico con PowerShell, deberá tener Azure PowerShell instalado y en ejecución y cambiar al modo de administrador de recursos para obtener acceso a los cmdlets de PowerShell del Administrador de recursos de Azure.

Puede descargar e instalar los módulos de Azure PowerShell mediante la ejecución del [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

Los cmdlets para crear y administrar bases de datos SQL de Azure y grupos de bases de datos elásticas se encuentran en el módulo del Administrador de recursos de Azure. Al iniciar Azure PowerShell, los cmdlets del módulo de Azure se importan de manera predeterminada. Para cambiar al módulo del Administrador de recursos de Azure, use el cmdlet Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Para obtener información detallada, vea [Uso de Windows PowerShell con el Administrador de recursos](powershell-azure-resource-manager.md).


## Configuración de las credenciales y selección de la suscripción

Ahora que está ejecutando el módulo del Administrador de recursos de Azure, tendrá acceso a todos los cmdlets necesarios para crear y configurar un grupo de bases de datos elásticas. En primer lugar, debe establecer el acceso a su cuenta de Azure. Ejecute lo siguiente y aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, se mostrará información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesitará el nombre o el identificador de suscripción (**-SubscriptionName**). Puede copiar el nombre o el identificador del paso anterior, o bien, si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción deseada del conjunto de resultados. Cuando tenga su suscripción, ejecute el siguiente cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Creación de un grupo de recursos, un servidor y una regla de firewall

Ya dispone de acceso para ejecutar cmdlets en su suscripción de Azure, por lo que el siguiente paso es establecer el grupo de recursos que contiene el servidor donde se creará el grupo de bases de datos elásticas. Puede editar el comando siguiente para usar cualquier ubicación válida que elija. Ejecute **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** para obtener una lista de ubicaciones válidas.

Si ya dispone de un grupo de recursos, puede ir al paso siguiente, o bien puede ejecutar el comando siguiente para crear un nuevo grupo de recursos:

	New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### Creación de un servidor 

Los grupos de bases de datos elásticas se crean en los servidores Base de datos SQL de Azure. Si ya dispone de un servidor, puede ir al paso siguiente, o bien puede ejecutar el comando siguiente para crear un nuevo servidor V12. Reemplace ServerName con el nombre de su servidor. Debe ser único para servidores SQL de Azure, por lo que es posible que obtenga un error si el nombre del servidor ya existe. También debe tener en cuenta que este comando puede tardar varios minutos en completarse. Se mostrarán los detalles del servidor y el símbolo del sistema de PowerShell tras crear el servidor correctamente. Puede editar el comando para usar cualquier ubicación válida que elija.

	New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Al ejecutar este comando, se abrirá una ventana para especificar el **Nombre de usuario** y la **Contraseña**. No especifique aquí sus credenciales de Azure, sino el nombre de usuario y contraseña serán las credenciales de administrador que desea crear para el nuevo servidor.


### Configuración de una regla de firewall para permitir el acceso al servidor

Establezca una regla de firewall para tener acceso al servidor. Ejecute el comando siguiente, reemplazando las direcciones IP inicial y final con los valores válidos para el equipo.

Si el servidor necesita permitir el acceso a otros servicios de Azure, agregue el conmutador **- AllowAllAzureIPs** que agregará una regla de firewall especial y permitirá todo el acceso de tráfico de Azure al servidor.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Creación de un grupo de bases de datos elásticas y de bases de datos elásticas

Ahora ya dispone de un grupo de recursos, un servidor y una regla de firewall configurados para poder tener acceso al servidor. El siguiente comando creará el grupo de bases de datos elásticas. Este comando crea un grupo que comparte un total de 400 eDTU. Se garantiza que cada base de datos del grupo tenga siempre 10 eDTU disponibles (DatabaseDtuMin). Las bases de datos individuales del grupo pueden consumir un máximo de 100 eDTU (DatabaseDtuMax). Para obtener una explicación detallada de los parámetros, vea [Grupos elásticos de bases de datos SQL de Azure](sql-database-elastic-pool.md).


	New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Creación o adición de bases de datos elásticas en un grupo de bases de datos elásticas

El grupo creado en el paso anterior está vacío, no tiene ninguna base de datos elástica. En las secciones siguientes se muestra cómo crear nuevas bases de datos dentro del grupo y también cómo agregar bases de datos existentes al grupo.

*Después de crear un grupo también puede usar Transact-SQL para crear nuevas bases de datos elásticas en el grupo y mover bases de datos existentes dentro y fuera de un grupo. Para obtener información detallada, consulte [Referencia de grupo de bases de datos elásticas - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

### Creación de nuevas bases de datos elásticas en un grupo de bases de datos elásticas

Para crear una nueva base de datos directamente dentro de un grupo, use el cmdlet **New-AzureSqlDatabase** y establezca el parámetro **ElasticPoolName**.


	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Movimiento de una base de datos existente a un grupo de bases de datos elásticas

Para mover una base de datos existente a un grupo, use el cmdlet **Set-AzureSqlDatabase** y establezca el parámetro **ElasticPoolName**.


Para obtener una demostración, cree una base de datos que no se encuentre en el grupo de bases de datos elásticas.

	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Mueva la base de datos existente a un grupo de bases de datos elásticas.

	Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Cambio de la configuración de rendimiento de un grupo de bases de datos elásticas


    Set-AzureSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Supervisión de bases de datos elásticas y de grupos de bases de datos elásticas

### Obtención del estado de las operaciones de grupos de bases de datos elásticas

Puede realizar el seguimiento del estado de las operaciones de grupos de bases de datos elásticas, incluida la creación y las actualizaciones.

	Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Obtención del estado del movimiento de una base de datos elástica dentro y fuera de un grupo de bases de datos elásticas

	Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Obtención de métricas de consumo de un grupo de bases de datos elásticas

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

	$metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Repita la llamada y anexe los datos para obtener días adicionales:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Asigne formato a la tabla:

    $table = Format-MetricsAsTable $metrics 

Exportación a un archivo CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Obtención de métricas de consumo de una base de datos elástica

Estas API son las mismas que las API (V12) actuales que se usan para supervisar el uso de recursos de una base de datos independiente, excepto por la diferencia semántica siguiente.

* Para esta API, las métricas recuperadas se expresan como un porcentaje de databaseDtuMax (o capacidad equivalente para la métrica subyacente como CPU, E/S etc.) establecido para ese grupo de bases de datos elásticas. Por ejemplo, un 50 % de utilización de cualquiera de estas métricas indica que el consumo específico del recurso está en el 50% del límite de capacidad por base de datos para dicho recurso en el grupo de bases de datos elásticas principal. 

Obtenga las métricas: $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

Repita la llamada y anexe los datos para obtener días adicionales si es necesario:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Asigne formato a la tabla:

    $table = Format-MetricsAsTable $metrics 

Exportación a un archivo CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Resumen


    Switch-AzureMode -Name AzureResourceManager
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Pasos siguientes

Tras la creación de un grupo de bases de datos elásticas, puede administrar las bases de datos elásticas del grupo mediante la creación de trabajos elásticos. Los trabajos elásticos facilitan la ejecución de secuencias de comandos de T-SQL con cualquier número de bases de datos en el grupo. Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).


## Referencia de bases de datos elásticas

Para obtener información detallada acerca de los grupos y las bases de datos elásticas, incluidos los detalles de errores y de API, vea la [Referencia acerca de los grupos de bases de datos elásticas](sql-database-elastic-pool-reference.md).

<!---HONumber=Oct15_HO1-->