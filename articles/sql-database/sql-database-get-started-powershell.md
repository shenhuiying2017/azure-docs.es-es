---
title: Nueva configuración de Base de datos SQL con PowerShell | Microsoft Docs
description: Aprenda a crear una Base de datos SQL con PowerShell. Las tareas de configuración comunes de la base de datos pueden administrarse mediante los cmdlets de PowerShell.
keywords: creación de una nueva base de datos SQL,configuración de base de datos
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: cgronlun

ms.service: sql-database
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/19/2016
ms.author: sstein

---
# Creación de una nueva Base de datos SQL y realización de tareas comunes de configuración de base de datos con los cmdlets de PowerShell
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-get-started.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> * [C#](sql-database-get-started-csharp.md)
> 
> 

Obtenga información sobre cómo crear una nueva base de datos SQL mediante cmdlets de PowerShell. (Para crear bases de datos elásticas, consulte [Creación de un nuevo grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-create-powershell.md)).

[!INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-database-powershell.md)]

## Configuración de la base de datos: creación de un grupo de recursos, un servidor y una regla de firewall
Una vez que dispone de acceso para ejecutar cmdlets en su suscripción de Azure seleccionada, el siguiente paso es establecer el grupo de recursos que contiene el servidor donde se creará la base de datos. Puede editar el comando siguiente para usar cualquier ubicación válida que elija. Ejecute **(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location** para ver una lista de las ubicaciones válidas.

Ejecute el comando siguiente para crear un grupo de recursos:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### Creación de un servidor
Las bases de datos SQL se crean en los servidores de Base de datos SQL de Azure. Ejecute **New-AzureRmSqlServer** para crear un servidor. El nombre del servidor debe ser único para todos los servidores de Base de datos SQL de Azure. Recibirá un mensaje de error si ya existe el nombre del servidor. También debe tener en cuenta que este comando puede tardar varios minutos en completarse. Puede editar el comando para usar cualquier ubicación válida que elija, pero debe utilizar la misma ubicación que empleó para el grupo de recursos creado en el paso anterior.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Al ejecutar este comando, se solicitará su nombre de usuario y contraseña. No escriba sus credenciales de Azure. En su lugar, escriba el nombre de usuario y la contraseña para crearlo como administrador del servidor. El script de la parte inferior de este artículo muestra cómo establecer las credenciales del servidor en el código.

Se mostrarán los detalles del servidor tras crear el servidor correctamente.

### Configuración de una regla de firewall para permitir el acceso al servidor
Establezca una regla de firewall para tener acceso al servidor. Ejecute el comando siguiente, reemplazando las direcciones IP inicial y final con los valores válidos para el equipo.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Se mostrarán los detalles de la regla de firewall tras crear la regla correctamente.

Para permitir que otros servicios de Azure tengan acceso al servidor, agregue una regla de firewall y establezca tanto StartIpAddress como EndIpAddress en 0.0.0.0. Esta regla permite que el tráfico de Azure de cualquier suscripción de Azure tenga acceso al servidor.

Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md).

## Creación de una Base de datos SQL
Ahora ya dispone de un grupo de recursos, un servidor y una regla de firewall configurados para poder tener acceso al servidor.

El siguiente comando crea una base de datos SQL (en blanco) en el nivel de servicio Standard con un nivel de rendimiento S1:

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Se mostrarán los detalles de la base de datos tras crear la base de datos correctamente.

## Creación de un script de PowerShell de base de datos SQL
El siguiente script de PowerShell permite crear una base de datos SQL y todos sus recursos dependientes. Reemplace todas las `{variables}` por valores y recursos específicos de la suscripción (quite los **{}** al establecer los valores).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation

    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"

    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds

    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip

    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp


    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"

    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo


    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## Pasos siguientes
Después de crear una Base de datos SQL y de realizar las tareas de configuración básica de la base de datos, está listo para lo siguiente:

* [Administración de Base de datos SQL con PowerShell](sql-database-manage-powershell.md)
* [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)

## Recursos adicionales
* [Base de datos SQL de Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1005_2016-->