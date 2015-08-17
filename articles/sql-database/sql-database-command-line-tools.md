<properties 
	pageTitle="Administración de recursos de bases de datos SQL de Azure con PowerShell" 
	description="Administración de Base de datos SQL de Azure con PowerShell." 
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
	ms.date="07/28/2015" 
	ms.author="vinsonyu"/>

# Administración de recursos de bases de datos SQL de Azure con PowerShell


Este tema proporciona los comandos de PowerShell para realizar muchas tareas de Base de datos SQL de Azure mediante los cmdlets del Administrador de recursos de Azure.


## Requisitos previos

Para ejecutar los cmdlets de PowerShell, deberá tener Azure PowerShell instalado y en ejecución y, dependiendo de la versión, es posible que tenga que cambiarlos en el modo de administrador de recursos para acceder a los cmdlets de PowerShell del Administrador de recursos de Azure.

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

## Crear un grupo de recursos

Cree el grupo de recursos que contendrá el servidor. Puede editar el siguiente comando para usar cualquier ubicación válida.

Para obtener una lista de ubicaciones de servidor de Base de datos SQL, ejecute los siguientes cmdlets:

	$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
	$AzureSQLLocations.Locations

Si ya dispone de un grupo de recursos, puede ir al paso siguiente para crear un servidor, o bien puede editar y ejecutar el comando siguiente para crear un nuevo grupo de recursos:

	New-AzureResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Creación de un servidor 

Para crear un nuevo servidor V12, use el comando [New-AzureSqlServer](https://msdn.microsoft.com/library/mt163526.aspx). Reemplace server12 por el nombre de su servidor. Debe ser único para servidores SQL de Azure, por lo que es posible que obtenga un error si el nombre del servidor ya existe. También debe tener en cuenta que este comando puede tardar varios minutos en completarse. Se mostrarán los detalles del servidor y el símbolo del sistema de PowerShell tras crear el servidor correctamente. Puede editar el comando para usar cualquier ubicación válida.

	New-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Al ejecutar este comando, se abrirá una ventana para especificar el **Nombre de usuario** y la **Contraseña**. No especifique aquí sus credenciales de Azure, sino el nombre de usuario y contraseña serán las credenciales de administrador que desea crear para el nuevo servidor.

## Creación de una regla de firewall del servidor

Para crear una regla de firewall para obtener acceso al servidor, use el comando [New-AzureSqlServerFirewallRule](https://msdn.microsoft.com/library/mt125953.aspx). Ejecute el comando siguiente, reemplazando las direcciones IP inicial y final con los valores válidos para el cliente.

Si el servidor necesita permitir el acceso a otros servicios de Azure, agregue el conmutador **- AllowAllAzureIPs** que agregará una regla de firewall especial y permitirá todo el acceso de tráfico de Azure al servidor.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Creación de una Base de datos SQL

Para crear una base de datos, use el comando [New-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125915.aspx). Necesita un servidor para crear una base de datos. En el ejemplo siguiente se crea una Base de datos SQL denominada TestDB12. La base de datos se crea como una base de datos Standard S1.

	New-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Cambio del nivel de rendimiento de una base de datos SQL

Puede escalar hacia arriba o hacia abajo la base de datos con el comando [Set-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125814.aspx). En el ejemplo siguiente se escala verticalmente una Base de datos SQL denominada TestDB12 a partir de su nivel de rendimiento actual a un nivel Standard S3.

	Set-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Eliminación de una Base de datos SQL

Puede eliminar una Base de datos SQL con el comando [Remove-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125977.aspx). En el ejemplo siguiente se elimina una Base de datos SQL denominada TestDB12.

	Remove-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Eliminación de un servidor

También puede eliminar un servidor con el comando [Remove-AzureSqlServer](https://msdn.microsoft.com/library/mt125891.aspx). En el siguiente ejemplo se elimina un servidor con el nombre server12.

	Remove-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Si va a volver a crear estos recursos de SQL de Azure o unos recursos similares, puede realizar lo siguiente:

- Guarde este recurso como archivo de secuencia de comandos de PowerShell (*.ps1).
- Guardar este recurso como Runbook de automatización de Azure en la sección Automatización del Portal de administración de Azure. 

## Pasos siguientes

Combine comandos y automatización. Por ejemplo, reemplace todo lo que hay entre comillas, incluidos los caracteres < and > por los valores para crear un servidor, regla de firewall y base de datos:


    New-AzureResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Información relacionada

- [Cmdlets del Administrador de recursos de Base de datos SQL de Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Cmdlets de Administración de servicios de Base de datos SQL de Azure](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=August15_HO6-->