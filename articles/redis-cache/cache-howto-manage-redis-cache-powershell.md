<properties 
 pageTitle="Administración de Caché en Redis de Azure con Azure PowerShell" 
 description="Aprenda a realizar tareas administrativas para Caché en Redis de Azure usando Azure PowerShell." 
 services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple" 
   ms.date="04/23/2015"
   ms.author="riande"/>

# Administración de Caché en Redis de Azure con Azure PowerShell

Este tutorial proporciona un inicio rápido para crear, actualizar y eliminar una Caché en Redis de Azure.

## Requisitos previos ##

Para poder usar Windows PowerShell con el Administrador de recursos, necesita lo siguiente:

- Windows PowerShell, versión 3.0 o 4.0. Para buscar la versión de Windows PowerShell, escriba:`$PSVersionTable` y compruebe que el valor de `PSVersion` es 3.0 o 4.0. Para instalar una versión compatible, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).
	
- La versión 0.8.0 o posterior de Azure PowerShell. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Este tutorial está diseñado para principiantes de Windows PowerShell. Para obtener más información acerca de Windows PowerShell, consulte [Introducción a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet Add-AzureAccount, escriba:

	Get-Help Add-AzureAccount -Detailed

## Un script de Azure PowerShell sencillo para Caché en Redis  ##

El siguiente script muestra la creación, actualización y eliminación de una Caché en Redis de Azure.

		# Redis cache operations require mode set to AzureResourceManager.
		Switch-AzureMode AzureResourceManager
		$VerbosePreference = "Continue" 
		
		# Create a new cache.
		$cacheName = "MovieCache91117"
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
			$cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {       
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName         
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -MaxMemoryPolicy AllKeysLRU -Name $cacheName -ResourceGroupName $resourceGroupName
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force 

## Pasos siguientes

Para obtener más información acerca de Windows PowerShell con Azure, consulte los siguientes recursos:
 
- [Cmdlets de Administrador de recursos de Azure](http://go.microsoft.com/fwlink/?LinkID=394765): obtenga información acerca del uso de los cmdlets en el módulo AzureResourceManager.
- [Uso de grupos de recursos para administrar los recursos de Azure](../azure-portal/resource-group-portal): obtenga información acerca de la creación y administración de grupos de recursos en el Portal de administración de Azure.
- [Blog de Azure](http://blogs.msdn.com/windowsazure): obtenga información acerca de las nuevas características de Azure.
- [Blog de Windows PowerShell](http://blogs.msdn.com/powershell): obtenga información acerca de las nuevas características de Windows PowerShell.
- [Blog ¡Hola, chicos del scripting!](http://blogs.technet.com/b/heyscriptingguy/): Obtenga sugerencias y trucos del mundo real de la comunidad de Windows PowerShell.

<!---HONumber=July15_HO3-->