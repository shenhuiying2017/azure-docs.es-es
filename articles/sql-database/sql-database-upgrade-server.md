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
	ms.date="09/30/2015" 
	ms.author="sstein"/>

# Actualización a Base de datos SQL V12 con PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


En este artículo se muestra cómo actualizar a Base de datos SQL V12 con PowerShell.

Durante el proceso de actualización a Base de datos SQL V12, es necesario actualizar también las bases de datos Web y Business a un nuevo nivel de servicio. Las instrucciones siguientes incluyen el uso de recomendaciones de grupo elástico y plan de tarifa para ayudar con la [actualización de las bases de datos Web y Business](sql-database-upgrade-new-service-tiers.md) del servidor.


## Requisitos previos 

Para actualizar un servidor a V12 con PowerShell, deberá tener Azure PowerShell instalado y en ejecución y, dependiendo de la versión, es posible que tenga que cambiarlos en el modo de administrador de recursos para acceder a los cmdlets de PowerShell del Administrador de recursos de Azure.

Puede descargar e instalar los módulos de Azure PowerShell mediante la ejecución del [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Los cmdlets para crear y administrar Bases de datos SQL de Azure se encuentran en el módulo del Administrador de recursos de Azure. Al iniciar Azure PowerShell, los cmdlets del módulo de Azure se importan de manera predeterminada. Para cambiar al módulo del Administrador de recursos de Azure, use el cmdlet **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

Si recibe una advertencia que indica que el cmdlet Switch-AzureMode está en desuso y se quitará en futuras versiones, puede omitirla y continuar con la siguiente sección.

Para obtener información detallada, vea [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).



## Configuración de las credenciales y selección de la suscripción

Para ejecutar los cmdlets de PowerShell en su suscripción de Azure debe establecer el acceso a su cuenta de Azure. Ejecute lo siguiente y aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, se mostrará información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.

Para seleccionar la suscripción con la que desea trabajar, necesita el identificador de la suscripción (**-SubscriptionId**) o el nombre de la suscripción (**-SubscriptionName**). Puede copiar el nombre o el identificador del paso anterior, o bien, si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción deseada del conjunto de resultados.

Ejecute el siguiente cmdlet con la información de suscripción para establecer la suscripción actual:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Los siguientes comandos se ejecutarán en la suscripción que acaba de seleccionar.

## Obtener recomendaciones

Para obtener la recomendación para la actualización del servidor, ejecute el siguiente cmdlet:

    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Para obtener más información, vea [Recomendaciones de grupo de bases de datos elásticas de Base de datos SQL de Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) y [Recomendaciones del nivel de precios de Base de datos SQL de Azure](sql-database-service-tier-advisor.md).



## Iniciar la actualización

Para iniciar la actualización del servidor, ejecute el siguiente cmdlet:

    Start-AzureSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Cuando ejecute este comando, comenzará el proceso de actualización. Puede personalizar la salida de la recomendación y ofrecer la recomendación editada para este cmdlet.


## Actualización de un servidor


    # Adding the account
    #
    Add-AzureAccount
    
    # Switch mode
    #
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


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
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 
    




- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)



## Información relacionada

- [Cmdlets del Administrador de recursos de Base de datos SQL de Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)

<!---HONumber=Oct15_HO2-->