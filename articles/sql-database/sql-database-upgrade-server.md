<properties 
	pageTitle="Actualizar un servidor SQL de Azure a V12 con PowerShell" 
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
	ms.date="09/05/2015" 
	ms.author="sstein"/>

# Actualizar el servidor SQL de Azure a V12 con PowerShell
 

En este artículo se muestra cómo actualizar un servidor de Base de datos SQL a V12 con recomendaciones del nivel de precios y grupo elástico.



## Requisitos previos 

Para actualizar un servidor a V12 con PowerShell, deberá tener Azure PowerShell instalado y en ejecución y, dependiendo de la versión, es posible que tenga que cambiarlos en el modo de administrador de recursos para acceder a los cmdlets de PowerShell del Administrador de recursos de Azure.

Puede descargar e instalar los módulos de Azure PowerShell mediante la ejecución del [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Los cmdlets para crear y administrar Bases de datos SQL de Azure se encuentran en el módulo del Administrador de recursos de Azure. Al iniciar Azure PowerShell, los cmdlets del módulo de Azure se importan de manera predeterminada. Para cambiar al módulo del Administrador de recursos de Azure, use el cmdlet **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

Si recibe una advertencia que indica que el cmdlet Switch-AzureMode está en desuso y se quitará en futuras versiones, puede omitirla y continuar con la siguiente sección.

Para obtener información detallada, vea [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).



## Configuración de las credenciales

Para ejecutar los cmdlets de PowerShell en su suscripción de Azure debe establecer el acceso a su cuenta de Azure. Ejecute lo siguiente y aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, se mostrará información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


## Selección de su suscripción a Azure

Para seleccionar la suscripción con la que quiere trabajar, necesita el identificador de suscripción (**-SubscriptionId**) o el nombre de suscripción (**-SubscriptionName**). Puede copiar el nombre o el identificador del paso anterior, o bien, si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción deseada del conjunto de resultados.

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


## Actualizar un servidor SQL de Azure


    # Adding the account
    #
    Add-AzureAccount
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName 
    $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Asignación de actualización personalizada

Si las recomendaciones no son adecuadas para su servidor y el caso de negocios, puede elegir cómo se actualizan las bases de datos y asignarlas a bases de datos únicas o elásticas.

Actualizar bases de datos en un grupo de bases de datos elásticas:

    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100  
    $elasticPool.DatabaseDtuMin = 0  
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"  
    $elasticPool.DatabaseCollection = ("DB1")  
    $elasticPool.Name = "elasticpool_1"  


Actualizar bases de datos en bases de datos elásticas:

    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties  $databaseMap.Name = "DB2"
    $databaseMap.TargetEdition = "Standard"
    $databaseMap.TargetServiceLevelObjective = "S0"
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool)
    




## Información relacionada

- [Cmdlets del Administrador de recursos de Base de datos SQL de Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Cmdlets de Administración de servicios de Base de datos SQL de Azure](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=Sept15_HO2-->