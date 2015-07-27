<properties 
	pageTitle="Desinstalación de la herramienta de trabajo de bases de datos elásticas" 
	description="Desinstalación de la herramienta de trabajo de bases de datos elásticas" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh"/>

# Desinstalación de los componentes de trabajo de base de datos elástica

Si se produce un error al intentar instalar el servicio de trabajo de base de datos elástica, elimine el grupo de recursos del servicio.

## Desinstalación de los componentes de servicio

1. Abra el [Portal de vista previa de Azure](https://ms.portal.azure.com/).
2. Desplácese hasta la suscripción que contiene el trabajo elástico.
3. Haga clic en **Examinar** y haga clic en **Grupos de recursos**.
4. Seleccione el grupo de recursos denominado "__ElasticDatabaseJob".
5. Elimine el grupo de recursos.

De manera alternativa, use la siguiente secuencia de comandos de PowerShell:

1. Inicie una [ventana de PowerShell de Microsoft Azure](../powershell-install-configure.md). 
2. Asegúrese de que está usando PowerShell SDK versión 0.8.10 o posterior.
3. Ejecute la secuencia de comandos:

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## Pasos siguientes

Para reinstalar los trabajos de bases de datos elásticas, consulte [Instalación del servicio de trabajo de base de datos elástica](sql-database-elastic-jobs-service-installation.md).

Para obtener información general acerca del servicio de trabajo de base de datos elástica, consulte [Información general acerca de los trabajos elásticos](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/
 

<!---HONumber=July15_HO3-->