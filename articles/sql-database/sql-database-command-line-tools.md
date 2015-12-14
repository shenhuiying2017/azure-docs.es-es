<properties 
	pageTitle="Administración de Base de datos SQL de Azure con PowerShell" 
	description="Administración de Base de datos SQL de Azure con PowerShell." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="sstein"/>

# Administración de Base de datos SQL de Azure con PowerShell


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

Este tema le proporciona los comandos de PowerShell que le ayudarán a realizar muchas de las tareas de la Base de datos SQL de Azure.


> [AZURE.IMPORTANT]Tenga en cuenta que el cmdlet Switch-AzureMode ya no está disponible a partir de la versión Vista previa de Azure PowerShell 1.0, y que los cmdlets que estaban en el módulo de Azure ResourceManager han cambiado de nombre. En los ejemplos de este artículo usaremos las nuevas convenciones de nomenclatura de Vista previa de PowerShell 1.0. Para obtener más información detallada, consulte [Degradación del cmdlet Switch-AzureMode en Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


Para ejecutar los cmdlets de PowerShell, necesitará tener Azure PowerShell instalado y en marcha; asimismo, debido a la eliminación del cmdlet Switch-AzureMode, deberá descargar e instalar la versión más reciente de Azure PowerShell ejecutando el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).



## Configuración de las credenciales

Para ejecutar los cmdlets de PowerShell en su suscripción de Azure debe establecer el acceso a su cuenta de Azure. Ejecute lo siguiente y aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el Portal de Azure clásico.

	Add-AzureAccount

Después de iniciar sesión correctamente, se mostrará información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


## Selección de su suscripción a Azure

Para seleccionar la suscripción con la que desea trabajar, necesita el identificador de la suscripción (**-SubscriptionId**) o el nombre de la suscripción (**-SubscriptionName**). Puede copiar el nombre o el identificador del paso anterior, o bien, si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción deseada del conjunto de resultados.

Ejecute el siguiente cmdlet con la información de suscripción para establecer la suscripción actual:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Los siguientes comandos se ejecutarán en la suscripción que acaba de seleccionar.

## Crear un grupo de recursos

Cree el grupo de recursos que contendrá el servidor. Puede editar el siguiente comando para usar cualquier ubicación válida.

Para obtener una lista de ubicaciones de servidor de Base de datos SQL, ejecute los siguientes cmdlets:

	$AzureSQLLocations = Get-AzureRMLocation | Where-Object Name -Like "*SQL/Servers"
	$AzureSQLLocations.Locations

Si ya dispone de un grupo de recursos, puede ir al paso siguiente para crear un servidor, o bien puede editar y ejecutar el comando siguiente para crear un nuevo grupo de recursos:

	New-AzureRMResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Creación de un servidor 

Para crear un nuevo servidor V12, use el cmdlet [New-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Reemplace server12 por el nombre de su servidor. Este debe ser único para los servidores SQL de Azure, por lo que si el nombre del servidor ya existe, verá un error aquí. También debe tener en cuenta que este comando puede tardar varios minutos en completarse. Se mostrarán los detalles del servidor y el símbolo del sistema de PowerShell tras crear el servidor correctamente. Puede editar el comando para usar cualquier ubicación válida.

	New-AzureRMSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Al ejecutar este comando, se abrirá una ventana para especificar el **Nombre de usuario** y la **Contraseña**. No especifique aquí sus credenciales de Azure, sino el nombre de usuario y contraseña serán las credenciales de administrador que desea crear para el nuevo servidor.

## Creación de una regla de firewall del servidor

Si desea crear una regla de firewall para obtener acceso al servidor, use el comando [New-AzureRMSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Ejecute el comando siguiente, reemplazando las direcciones IP inicial y final con los valores válidos para el cliente.

Si el servidor necesita permitir el acceso a otros servicios de Azure, agregue el conmutador **- AllowAllAzureIPs** que agregará una regla de firewall especial y permitirá todo el acceso de tráfico de Azure al servidor.

	New-AzureRMSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Para obtener más información, vea [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Creación de una Base de datos SQL

Para crear una base de datos, use el comando [New-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). Necesita un servidor para crear una base de datos. En el ejemplo siguiente se crea una Base de datos SQL denominada TestDB12. La base de datos se crea como una base de datos Standard S1.

	New-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Cambio del nivel de rendimiento de una base de datos SQL

Puede escalar o reducir verticalmente la base de datos con el comando [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). En el ejemplo siguiente se escala verticalmente una Base de datos SQL denominada TestDB12 a partir de su nivel de rendimiento actual a un nivel Standard S3.

	Set-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Eliminación de una Base de datos SQL

Puede eliminar una base de datos SQL con el comando [Remove-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). En el ejemplo siguiente se elimina una Base de datos SQL denominada TestDB12.

	Remove-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Eliminación de un servidor

Asimismo, también puede eliminar un servidor con el comando [Remove-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx). En el siguiente ejemplo se elimina un servidor con el nombre server12.

	Remove-AzureRMSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Si va a volver a crear estos recursos de SQL de Azure o unos recursos similares, puede realizar lo siguiente:

- Guarde este recurso como archivo de secuencia de comandos de PowerShell (*.ps1).
- Guardar este recurso como Runbook de automatización de Azure en la sección Automatización del Portal de Azure clásico. 

## Pasos siguientes

Combine comandos y automatización. Por ejemplo, reemplace todo lo que hay entre comillas, incluidos los caracteres < and > por los valores para crear un servidor, regla de firewall y base de datos:


    New-AzureRMResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRMSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRMSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRMSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Información relacionada

- [Cmdlets de la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_1203_2015-->