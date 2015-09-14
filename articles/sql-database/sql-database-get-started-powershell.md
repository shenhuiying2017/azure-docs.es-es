<properties 
    pageTitle="Creación de una Base de datos SQL de Azure con PowerShell"
	description="Creación de una Base de datos SQL de Azure con PowerShell"
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
	ms.date="09/01/2015"
	ms.author="sstein"/>

# Creación de una Base de datos SQL con PowerShell

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


## Información general

Este artículo muestra cómo crear una base de datos SQL mediante PowerShell.


Para completar este artículo, necesitará lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Azure PowerShell. Puede descargar e instalar los módulos de Azure PowerShell mediante la ejecución del [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

Los cmdlets para crear y administrar Bases de datos SQL de Azure se encuentran en el módulo del Administrador de recursos de Azure. Al iniciar Azure PowerShell, los cmdlets del módulo de Azure se importan de manera predeterminada. Para cambiar al módulo del Administrador de recursos de Azure, use el cmdlet Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Si ejecuta **Switch-AzureMode** y ve la advertencia: El *cmdlet Switch-AzureMode está en desuso y se quitará en futuras versiones*, eso está bien; solo tiene que ir al paso siguiente para configurar sus credenciales.

Para obtener información detallada, vea [Uso de Windows PowerShell con el Administrador de recursos](powershell-azure-resource-manager.md).


## Configuración de las credenciales y selección de la suscripción

Ahora que está ejecutando el módulo del Administrador de recursos de Azure, tendrá acceso a todos los cmdlets necesarios para crear una base de datos SQL.

Lo primero que debe hacer es establecer el acceso a su cuenta de Azure, de modo que ejecute el siguiente cmdlet y verá una pantalla de inicio de sesión para escribir sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesita su id. de suscripción. Puede copiar el nombre o el identificador del paso anterior, o bien, si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción deseada del conjunto de resultados. Cuando tenga su suscripción, ejecute el siguiente cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Después de ejecutar correctamente **Select-AzureSubscription** volverá al símbolo del sistema de PowerShell. Si tiene más de una suscripción, puede ejecutar **Get-AzureSubscription** y comprobar que la suscripción que desea usar muestra **IsCurrent: True**.

## Creación de un grupo de recursos, un servidor y una regla de firewall

Ya dispone de acceso para ejecutar cmdlets en su suscripción de Azure seleccionada, por lo que el siguiente paso es establecer el grupo de recursos que contiene el servidor donde se creará la base de datos. Puede editar el comando siguiente para usar cualquier ubicación válida que elija. Ejecute **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** para obtener una lista de ubicaciones válidas.

Ejecute el comando siguiente para crear un nuevo grupo de recursos:

	New-AzureResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Después de crear correctamente el nuevo grupo de recursos, puede ver información en pantalla que incluye **ProvisioningState: Succeeded**.


### Creación de un servidor 

Las bases de datos SQL se crean en los servidores de Base de datos SQL de Azure. Ejecute **New-AzureSqlServer** para crear un nuevo servidor. Reemplace ServerName con el nombre de su servidor. Debe ser único para todos los servidores SQL de Azure, por lo que obtendrá un error aquí si el nombre del servidor ya existe. También debe tener en cuenta que este comando puede tardar varios minutos en completarse. Puede editar el comando para usar cualquier ubicación válida que elija, pero debe utilizar la misma ubicación que empleó para el grupo de recursos creado en el paso anterior.

	New-AzureSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Al ejecutar este comando, se abrirá una ventana para especificar el **Nombre de usuario** y la **Contraseña**. No especifique aquí sus credenciales de Azure, sino el nombre de usuario y contraseña serán las credenciales de administrador que desea crear para el nuevo servidor.

Se mostrarán los detalles del servidor tras crear el servidor correctamente.

### Configuración de una regla de firewall para permitir el acceso al servidor

Establezca una regla de firewall para tener acceso al servidor. Ejecute el comando siguiente, reemplazando las direcciones IP inicial y final con los valores válidos para el equipo.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Se mostrarán los detalles de la regla de firewall tras crear la regla correctamente.

Para permitir que otros servicios de Azure tengan acceso al servidor, agregue una regla de firewall y establezca tanto StartIpAddress como EndIpAddress en 0.0.0.0. Tenga en cuenta que esto permite que el tráfico de Azure de cualquier suscripción de Azure tenga acceso al servidor.

Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Creación de una Base de datos SQL

Ahora ya dispone de un grupo de recursos, un servidor y una regla de firewall configurados para poder tener acceso al servidor.

El siguiente comando crea una nueva base de datos SQL (en blanco) en el nivel de servicio Standard con un nivel de rendimiento S1:


	New-AzureSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Se mostrarán los detalles de la base de datos tras crear la base de datos correctamente.

## Creación de un script de PowerShell de base de datos SQL

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Pasos siguientes

- [Conexión con SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)


## Recursos adicionales

- [Base de datos SQL de Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=September15_HO1-->