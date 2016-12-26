---
title: "Nueva configuración de SQL Database con PowerShell | Microsoft Docs"
description: "Aprenda a crear una Base de datos SQL con PowerShell. Las tareas de configuración comunes de la base de datos pueden administrarse mediante los cmdlets de PowerShell."
keywords: "creación de una nueva base de datos SQL,configuración de base de datos"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 12/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: b557c6d3591b1d56144651e876fa4bb1b0646935


---

# <a name="get-started-with-azure-sql-database-servers-databases-and-firewall-rules-by-using-azure-powershell"></a>Introducción a los servidores, las bases de datos y las reglas de firewall de Azure SQL Database mediante Azure PowerShell

En este tutorial de introducción, aprenderá a usar PowerShell para:

* Crear un nuevo grupo de recursos de Azure
* Crear un servidor lógico de Azure SQL
* Ver las propiedades del servidor de Azure SQL
* Crear una regla de firewall de nivel de servidor
* Crear la base de datos de ejemplo AdventureWorksLT como una base de datos independiente
* Ver las propiedades de la base de datos de ejemplo AdventureWorksLT
* Crear una base de datos independiente en blanco

En este tutorial, también aprenderá a hacer lo siguiente:

* Conectar con el servidor lógico y su base de datos maestra
* Ver las propiedades de una base de datos maestra
* Conectar con la base de datos de ejemplo
* Ver las propiedades de una base de datos de usuarios

Cuando termine este tutorial, tendrá una base de datos de ejemplo y una base de datos vacía en ejecución en un grupo de recursos de Azure y se conectan a un servidor lógico. También tendrá una regla de firewall de nivel de servidor configurada para que la entidad de seguridad de nivel de servidor pueda iniciar sesión en el servidor desde una dirección IP especificada (o intervalo de direcciones IP). 

**Tiempo estimado**: este tutorial le llevará aproximadamente 30 minutos (si ya cumple los requisitos previos).


> [!TIP]
> Puede realizar las mismas tareas del tutorial de introducción con [Azure Portal](sql-database-get-started.md).
>


## <a name="prerequisites"></a>Requisitos previos

* Necesitará una cuenta de Azure. Puede [abrir una cuenta gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas que disfrutan los suscriptores de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Debe poder iniciar sesión mediante una cuenta que sea miembro del rol de colaborador o propietario de la suscripción. Para más información sobre el acceso basado en roles (RBAC), consulte [Introducción a la administración de acceso en Azure Portal](../active-directory/role-based-access-control-what-is.md).

* Necesita el archivo .bacpac de la base de datos de ejemplo AdventureWorksLT en Azure Blob Storage.

### <a name="download-the-adventureworkslt-sample-database-bacpac-file-and-save-it-in-azure-blob-storage"></a>Descargue el archivo .bacpac de la base de datos de ejemplo AdventureWorksLT y guárdelo en Azure Blob Storage.

Este tutorial crea una base de datos AdventureWorksLT al importar un archivo .bacpac de Azure Storage. El primer paso es obtener una copia de AdventureWorksLT.bacpac y cargarlo en Blob Storage.
Los siguientes pasos preparan la base de datos de ejemplo para importarla:

1. [Descargue AdventureWorksLT.bacpac](https://sqldbbacpacs.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac) y guárdelo con la extensión de archivo .bacpac.
2. [Cree una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account): puede crearla con la configuración predeterminada.
3. Cree un **contenedor**; para ello, vaya a la cuenta de almacenamiento, seleccione **Blobs** y haga clic en **+Contenedor**.
4. Cargue el archivo .bacpac en el contenedor de blobs de la cuenta de almacenamiento. Puede usar el botón **Cargar**, situado en la parte superior de la página de contenedor, o [use AzCopy](../storage/storage-use-azcopy.md#blob-upload). 
5. Después de guardar el archivo AdventureWorksLT.bacpac, necesitará la dirección URL y la clave de la cuenta de almacenamiento para el fragmento de código de importación más adelante, en este tutorial. 
   * Seleccione el archivo bacpac y copie la dirección URL. Será similar a https://{nombre-cuenta-almacenamiento}.blob.core.windows.net/{nombre-contenedor}/AdventureWorksLT.bacpac. En la página de la cuenta de almacenamiento, haga clic en **Claves de acceso** y copie **key1**.


[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="create-a-new-logical-sql-server-using-azure-powershell"></a>Creación de un servidor SQL Server lógico con Azure PowerShell

Necesita un grupo de recursos que contenga el servidor, por lo que el primer paso es crear un grupo de recursos y el servidor ([New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroup), [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver)), o bien obtener referencias a unos existentes ([Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/get-azurermresourcegroup), [Get-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserver)).
Los siguientes fragmentos de código crearán un grupo de recursos y un servidor SQL Server de Azure si aún no existen:

Para ver una lista de las ubicaciones de Azure válidas y el formato de cadena, consulte la sección [Fragmentos de código auxiliar](#helper-snippets), más adelante.
```
# Create new, or get existing resource group
############################################

$resourceGroupName = "{resource-group-name}"
$resourceGroupLocation = "{resource-group-location}"

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-Output "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-Output "Resource group $resourceGroupName already exists:"
}
$myResourceGroup



# Create a new, or get existing server
######################################

$serverName = "{server-name}"
$serverVersion = "12.0"
$serverLocation = $resourceGroupLocation
$serverResourceGroupName = $resourceGroupName

$serverAdmin = "{server-admin}"
$serverAdminPassword = "{server-admin-password}"

$securePassword = ConvertTo-SecureString –String $serverAdminPassword –AsPlainText -Force
$serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-Output "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-Output "SQL server $serverName already exists:"
}
$myServer

```


## <a name="view-the-logical-sql-server-properties-using-azure-powershell"></a>Visualización de las propiedades del servidor SQL Server lógico con Azure PowerShell

```
#$serverResourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin
```


## <a name="create-a-server-level-firewall-rule-using-azure-powershell"></a>Creación de una regla de firewall de nivel de servidor con Azure PowerShell

Necesita conocer la dirección IP pública para establecer la regla de firewall. Puede obtener la dirección IP mediante el explorador que prefiera (pregunte ¿Cuál es mi IP?). Para más información, consulte las [reglas de firewall](sql-database-firewall-configure.md).

En el siguiente fragmento de código se utilizan los cmdlets [Get-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserverfirewallrule) y [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserverfirewallrule) para obtener una referencia o crear una regla. Este fragmento de código, si la regla ya existe, solo obtiene una referencia a ella, no actualiza las direcciones IP inicial y final. Siempre puede modificar la cláusula **else** para que utilice [Set-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverfirewallrule) para crear o actualizar la funcionalidad.

> [!NOTE] 
> Puede abrir el firewall de SQL Database en el servidor para una sola dirección IP o para un intervalo de direcciones. Abrir el firewall permite a los administradores y usuarios de SQL iniciar sesión en cualquier base de datos del servidor para el que tengan unas credenciales válidas.
>

```
#$serverName = "{server-name}"
#$serverResourceGroupName = "{resource-group-name}"
$serverFirewallRuleName = "{server-firewall-rule-name}"
$serverFirewallStartIp = "{server-firewall-rule-startIp}"
$serverFirewallEndIp = "{server-firewall-rule-endIp}"

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule
```


## <a name="connect-to-sql-server-using-azure-powershell"></a>Conexión a un servidor SQL Server con Azure PowerShell

Permite ejecutar una consulta rápida en la base de datos maestra para comprobar que podemos conectarnos al servidor. El siguiente fragmento de código utiliza el [proveedor de .NET Framework para SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) para conectarse y consultar la base de datos maestra del servidor. Genera una cadena de conexión basada en las variables que hemos usado en los fragmentos de código anteriores. Reemplace los marcadores de posición con el administrador de SQL Server y la contraseña que usó para crear el servidor en los pasos anteriores.


```
#$serverName = "{server-name}"
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"



$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```


## <a name="create-new-adventureworkslt-sample-database-using-azure-powershell"></a>Creación de la base de datos de ejemplo AdventureWorksLT con Azure PowerShell

El siguiente fragmento de código importa un archivo bacpac de la base de datos de ejemplo AdventureWorksLT mediante el cmdlet [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport). El archivo bacpac se encuentra en Azure Blob Storage. Después de ejecutar el cmdlet de importación, puede supervisar el progreso de la operación de importación mediante el cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus).
$storageUri es la propiedad URL del archivo bacpac cargado anteriormente en el portal, y debe ser similar a https://{cuenta-almacenamiento}.blob.core.windows.net/{contenedor}/AdventureWorksLT.bacpac

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "StorageAccessKey"
$storageUri = "{storage-uri}" # URL of bacpac file you uploaded to your storage account
$storageKey = "{storage-key}" # key1 in the Access keys setting of your storage account

$importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $resourceGroupName –ServerName $serverName –DatabaseName $databaseName –StorageKeytype $storageKeyType –StorageKey $storageKey -StorageUri $storageUri –AdministratorLogin $serverAdmin –AdministratorLoginPassword $securePassword –Edition $databaseEdition –ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000


Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus
```



## <a name="view-database-properties-using-azure-powershell"></a>Visualización de las propiedades de base de datos con Azure PowerShell

Después de crear la base de datos, consulte algunas de sus propiedades.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
#$databaseName = "{database-name}"

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName


Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status
```

## <a name="connect-and-query-the-sample-database-using-azure-powershell"></a>Conexión y consulta de la base de datos de ejemplo con Azure PowerShell

Permite ejecutar una consulta rápida en la base de datos AdventureWorksLT para comprobar que podemos conectarnos a ella. El siguiente fragmento de código utiliza el [proveedor de .NET Framework para SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) para conectarse y consultar la base de datos. Genera una cadena de conexión basada en las variables que hemos usado en los fragmentos de código anteriores. Reemplace el marcador de posición con la contraseña de administrador de SQL Server.

```
#$serverName = {server-name}
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
#$databaseName = {database-name}

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```

## <a name="create-a-new-blank-database-using-azure-powershell"></a>Creación de una base de datos en blanco con Azure PowerShell

```
#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```


## <a name="complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database"></a>Script de Azure PowerShell para crear un servidor, una regla de firewall y una base de datos



```
# Sign in to Azure and set the subscription to work with
########################################################

$SubscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myResourceGroupLocation = "{resource-group-location}"

$myServerName = "{server-name}"
$myServerVersion = "12.0"
$myServerLocation = $myResourceGroupLocation
$myServerResourceGroupName = $myResourceGroupName
$myServerAdmin = "{server-admin}"
$myServerAdminPassword = "{server-admin-password}" 

$myServerFirewallRuleName = "{server-firewall-rule-name}"
$myServerFirewallStartIp = "{start-ip}"
$myServerFirewallEndIp = "{end-ip}"

$myDatabaseName = "AdventureWorksLT"
$myDatabaseEdition = "Basic"
$myDatabaseServiceLevel = "Basic"

$myStorageKeyType = "StorageAccessKey"
# Get these values from your Azure storage account:
$myStorageUri = "{http://your-storage-account.blob.core.windows.net/your-container/AdventureWorksLT.bacpac}"
$myStorageKey = "{your-storage-key}"


# Create new, or get existing resource group
############################################

$resourceGroupName = $myResourceGroupName
$resourceGroupLocation = $myResourceGroupLocation

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-host "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-host "Resource group $resourceGroupName already exists:"
}
$myResourceGroup


# Create a new, or get existing server
######################################

$serverName = $myServerName
$serverVersion = $myServerVersion
$serverLocation = $myServerLocation
$serverResourceGroupName = $myServerResourceGroupName

$serverAdmin = $myServerAdmin
$serverAdminPassword = $myServerAdminPassword

$securePassword = ConvertTo-SecureString –String $serverAdminPassword –AsPlainText -Force
$serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-host "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-host "SQL server $serverName already exists:"
}
$myServer


# View server properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin


# Create or update server firewall rule
#######################################

$serverFirewallRuleName = $myServerFirewallRuleName
$serverFirewallStartIp = $myServerFirewallStartIp
$serverFirewallEndIp = $myServerFirewallEndIp

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule


# Connect to the server and master database
###########################################
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create the AdventureWorksLT database from a bacpac
####################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$databaseName = $myDatabaseName
$databaseEdition = $myDatabaseEdition
$databaseServiceLevel = $myDatabaseServiceLevel

$storageKeyType = $myStorageKeyType
$storageUri = $myStorageUri
$storageKey = $myStorageKey

$importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $resourceGroupName –ServerName $serverName –DatabaseName $databaseName –StorageKeytype $storageKeyType –StorageKey $storageKey -StorageUri $storageUri –AdministratorLogin $serverAdmin –AdministratorLoginPassword $securePassword –Edition $databaseEdition –ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000

Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus


# View database properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseName

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status


# Query the database
####################

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create a blank database
#########################

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```



> [!TIP]
> Elimine las bases de datos que no use para ahorrar dinero mientras aprende. Las bases de datos de Basic edition se pueden restaurar en un plazo de siete días. Sin embargo, no elimine un servidor. Si lo hace, no podrá recuperar el servidor ni las bases de datos eliminadas.

## <a name="helper-snippets"></a>Fragmentos de código auxiliar

```
# Get a list of Azure regions where you can create SQL resources

$sqlRegions = (Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" })
foreach ($region in $sqlRegions)
{
   $region.Location
}

# Clean up
# Delete a resource group (and all contained resources)
Remove-AzureRmResourceGroup -Name {resource-group-name}
```

> [!TIP]
> Elimine las bases de datos que no use para ahorrar dinero mientras aprende. Las bases de datos de la edición Básica se pueden restaurar en un plazo de siete días. Sin embargo, no elimine el servidor. Si lo hace, no podrá recuperar el servidor ni las bases de datos eliminadas.
>

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este primer tutorial de introducción y ha creado una base de datos con algunos datos de ejemplo, hay otros tutoriales que quizás desee explorar para afianzar lo que ha aprendido en este tutorial. 

* Si desea empezar a explorar la seguridad de Azure SQL Database, consulte el artículo de [introducción a la seguridad](sql-database-get-started-security.md).
* Si conoce Excel, consulte [Conexión a una base de datos SQL con Excel](sql-database-connect-excel.md).
* Si está listo para comenzar a codificar, elija el lenguaje de programación en [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md).
* Si desea mover las bases de datos de SQL Server locales a Azure, consulte [Migrar una base de datos a SQL Database](sql-database-cloud-migrate.md).
* Si desea cargar datos en una tabla nueva desde un archivo CSV con la herramienta de línea de comandos BCP, consulte cómo [cargar datos en SQL Database desde un archivo CSV con BCP](sql-database-load-from-csv-with-bcp.md).
* Si desea empezar a crear tablas y otros objetos, consulte el tema "Crear una tabla" en [Creación de una tabla](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Recursos adicionales
[¿Qué es Base de datos SQL?](sql-database-technical-overview.md)


<!--HONumber=Dec16_HO2-->


