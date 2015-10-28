<properties 
	pageTitle="Actualización de un servidor SQL de Azure a V12 con PowerShell" 
	description="Actualice un servidor SQL de Azure a V12 con PowerShell." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="sstein"/>

# Actualización a Base de datos SQL V12 con PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


En este artículo se muestra cómo actualizar a Base de datos SQL V12 con PowerShell.

Durante el proceso de actualización a Base de datos SQL V12, es necesario actualizar también las bases de datos Web y Business a un nuevo nivel de servicio. Las instrucciones siguientes incluyen el uso de recomendaciones de grupo elástico y plan de tarifa para ayudar con la [actualización de las bases de datos Web y Business](sql-database-upgrade-new-service-tiers.md) del servidor.


## Requisitos previos 

Para actualizar un servidor a V12 con PowerShell, deberá tener Azure PowerShell instalado y en ejecución y, dependiendo de la versión, es posible que tenga que cambiarlos en el modo de administrador de recursos para acceder a los cmdlets de PowerShell del Administrador de recursos de Azure.

> [AZURE.IMPORTANT]Tenga en cuenta que el cmdlet Switch-AzureMode ya no está disponible a partir de la versión Vista previa de Azure PowerShell 1.0, y que los cmdlets que estaban en el módulo de Azure ResourceManager han cambiado de nombre. En los ejemplos de este artículo usaremos la nueva convención de nomenclatura de Vista previa de PowerShell 1.0. Para obtener más información detallada, consulte [Degradación del cmdlet Switch-AzureMode en Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).

Para ejecutar los cmdlets de PowerShell, necesitará tener Azure PowerShell instalado y en marcha; asimismo, debido a la eliminación del cmdlet Switch-AzureMode, deberá descargar e instalar la versión más reciente de Azure PowerShell ejecutando el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).


## Configuración de las credenciales y selección de la suscripción

Para ejecutar los cmdlets de PowerShell en su suscripción de Azure debe establecer el acceso a su cuenta de Azure. Ejecute lo siguiente y aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, se mostrará información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.

Para seleccionar la suscripción con la que quiera trabajar, necesita el identificador de la suscripción (**-SubscriptionId**) o el nombre de la suscripción (**-SubscriptionName**). Puede copiar el nombre o el identificador del paso anterior, o bien, si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción deseada del conjunto de resultados.

Ejecute el siguiente cmdlet con la información de suscripción para establecer la suscripción actual:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Los siguientes comandos se ejecutarán en la suscripción que acaba de seleccionar.

## Obtener recomendaciones

Para obtener la recomendación para la actualización del servidor, ejecute el siguiente cmdlet:

    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Para obtener más información, consulte las [Recomendaciones acerca del grupo de bases de datos elásticas de Base de datos SQL de Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) y las [Recomendaciones acerca del nivel de precios de Base de datos SQL de Azure](sql-database-service-tier-advisor.md).



## Iniciar la actualización

Para iniciar la actualización del servidor, ejecute el siguiente cmdlet:

    Start-AzureRMSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Cuando ejecute este comando, comenzará el proceso de actualización. Puede personalizar la salida de la recomendación y ofrecer la recomendación editada para este cmdlet.


## Actualización de un servidor


    # Adding the account
    #
    Add-AzureAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRMSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Asignación de actualización personalizada

Si las recomendaciones no son adecuadas para su servidor y el caso de negocios, puede elegir cómo se actualizan las bases de datos y asignarlas a bases de datos únicas o elásticas.

Los parámetros ElasticPoolCollection y DatabaseCollection son opcionales:
    
    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties 
    $elasticPool.DatabaseDtuMax = 100 
    $elasticPool.DatabaseDtuMin = 0 
    $elasticPool.Dtu = 800 
    $elasticPool.Edition = "Standard" 
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”) 
    $elasticPool.Name = "elasticpool_1" 
    $elasticPool.StorageMb = 800 
     
    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap1.Name = "DBMain1" 
    $databaseMap1.TargetEdition = "Standard" 
    $databaseMap1.TargetServiceLevelObjective = "S0"
    
    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap2.Name = "DBMain2" 
    $databaseMap2.TargetEdition = "Standard" 
    $databaseMap2.TargetServiceLevelObjective = "S2"
     
    # Starting the upgrade
    #
    Start-AzureRMSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    




## Información relacionada

- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=Oct15_HO3-->