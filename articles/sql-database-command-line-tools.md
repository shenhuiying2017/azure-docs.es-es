<properties 
	pageTitle="Administración de recursos de bases de datos SQL de Azure con PowerShell" 
	description="Administración de la base de datos SQL de Azure con la línea de comandos" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="vinsonyu"/>

# Administración de recursos de bases de datos SQL de Azure con PowerShell


En este tema se usará una secuencia de comandos de PowerShell para crear un servidor lógico de base de datos SQL de Azure, una base de datos y una regla de firewall.

## Paso 1: Instalación del SDK de Azure

Si aún no lo ha hecho, siga las instrucciones descritas en [Instalación y configuración de Azure PowerShell para instalar Azure PowerShell](powershell-install-configure.md) en su equipo local. A continuación, abra un símbolo del sistema de Azure PowerShell.


## Paso 2: Configuración de las secuencias de comandos de PowerShell
Esta secuencia de comandos de PowerShell crea un servidor, una base de datos y una regla de firewall.


1. Copie el siguiente bloque de cmdlets de PowerShell en su editor de texto.
		
		
		Add-AzureAccount #Only needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "<Service Admin Login>" -AdministratorLoginPassword "<ServerLoginPassword>" -Location "<Location>" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "<Database1>" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "<FirewallRuleName>" -StartIpAddress "<IP4StartRange>" -EndIpAddress "<IP4EndRange>" -verbose

2. Reemplace todo el contenido que se muestra entre < > con los valores deseados. Para obtener una lista de ubicaciones de servidor de base de datos SQL de Azure válidas, puede ejecutar los siguientes cmdlets en la línea de comandos de Powershell de Azure

		Switch-AzureMode -Name AzureResourceManager
		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

##Paso 3: Ejecución de la secuencia de comandos de PowerShell

Revise los cmdlets de PowerShell de Azure.

Copie los cmdlets de PowerShell configurados en el paso 2 y pegue lo siguiente en el símbolo del sistema de PowerShell de Azure. Esto creará los cmdlets como una serie de comandos de PowerShell y se creará el servidor SQL de Azure, la base de datos y la regla de firewall.

Si va a volver a crear estos recursos de SQL de Azure o unos recursos similares, puede realizar lo siguiente:

- Guarde este recurso como archivo de secuencia de comandos de PowerShell (*.ps1).
- Guardar este recurso como Runbook de automatización de Azure en la sección Automatización del Portal de administración de Azure. 

##Ejemplos

Esta secuencia de comandos de PowerShell creará los recursos en el oeste de EE. UU.

		Add-AzureAccount #Needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "admin" -AdministratorLoginPassword "P@ssword" -Location "West US" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "Database1" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "MyFirewallRule" -StartIpAddress "192.168.1.1" -EndIpAddress "192.168.1.1" -verbose

##Recursos

Para obtener más información sobre los cmdlets de PowerShell de SQL de Azure, [haga clic aquí](https://msdn.microsoft.com/library/dn546726.aspx).

<!---HONumber=58-->