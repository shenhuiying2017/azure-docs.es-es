---
title: "Uso del módulo de Azure Database Migration Service en Microsoft Azure PowerShell para migrar instancias locales de SQL Server a Azure SQL DB | Microsoft Docs"
description: "En este artículo se describe información sobre cómo migrar de instancias locales de SQL Server a Azure SQL mediante Azure PowerShell."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 8569bf65d04f677a45935284dc61d68879014c10
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migración de SQL Server local a Azure SQL Database mediante Azure PowerShell
En este artículo, migrará la base de datos **Adventureworks2012** restaurada en una instancia local de SQL Server 2016 a una instancia de Azure SQL Database mediante Microsoft Azure PowerShell. Puede migrar bases de datos desde una instancia de SQL Server local a Azure SQL Database mediante el módulo `AzureRM.DataMigration` en Microsoft Azure PowerShell.

En este artículo, aprenderá a:
> [!div class="checklist"]
> * Cree un grupo de recursos.
> * Crear una instancia de Azure Database Migration Service.
> * Crear un proyecto de migración en una instancia de Azure Database Migration Service.
> * Ejecutar la migración.

## <a name="prerequisites"></a>requisitos previos
Para completar estos pasos, necesitará lo siguiente:

- [SQL Server 2016 o posterior](https://www.microsoft.com/sql-server/sql-server-downloads) (cualquier edición).
- El protocolo TCP/IP está deshabilitado de forma predeterminada con la instalación de SQL Server Express. Habilítelo siguiendo las [instrucciones de este artículo](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Configurar [Firewall de Windows para el acceso al motor de base de datos](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Una instancia de Azure SQL Database. Puede crear una instancia de Azure SQL Database siguiendo la información del artículo sobre cómo [crear una instancia de Azure SQL Database en Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (versión 3.3 o posterior).
- Azure Database Migration Service requiere una red virtual creada mediante el modelo de implementación de Azure Resource Manager, que proporciona conectividad de sitio a sitio a los servidores de origen local utilizando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- La evaluación completada de la base de datos local y la migración del esquema con Data Migration Assistant como se describe en el artículo [ Performing a SQL Server migration assessment](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) (Realización de una evaluación de la migración de SQL Server).
- Descargar e instalar el módulo AzureRM.DataMigration de la Galería de PowerShell con el [cmdlet Install-Module de PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
- Las credenciales usadas para conectarse a la instancia de SQL Server de origen deben tener el permiso [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Las credenciales usadas para conectarse a la instancia de Azure SQL Database de destino deben tener el permiso CONTROL DATABASE en las bases de datos Azure SQL Database de destino.
- Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Inicio de sesión en la suscripción de Microsoft Azure
Utilice las instrucciones que aparecen en el artículo [Inicio de sesión con Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) para iniciar sesión en su suscripción de Azure mediante PowerShell.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Cree un grupo de recursos para poder crear una máquina virtual.

Cree un grupo de recursos mediante el comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1). 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la región *EastUS*.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>Creación de una instancia de Azure Database Migration Service 
Puede crear una instancia nueva de Azure Database Migration Service mediante el cmdlet `New-AzureRmDataMigrationService`. Este cmdlet espera los siguientes parámetros requeridos:
- *Nombre del grupo de recursos de Azure*. Puede usar el comando [AzureRmResourceGroup New](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) para crear el grupo de recursos de Azure como se mostró anteriormente y proporcionar su nombre como un parámetro.
- *Nombre del servicio*. Cadena que se corresponde con el nombre de servicio único para Azure Database Migration Service. 
- *Ubicación*. Especifica la ubicación del servicio. Especifique una ubicación de centro de datos de Azure, por ejemplo, Oeste de EE. UU. o Sudeste Asiático.
- *SKU*. Este parámetro corresponde al nombre de SKU de DMS. Los nombres de SKU admitidos actualmente son *Basic_1vCore*, *Basic_2vCores* y *GeneralPurpose_4vCores*.
- *Identificador de subred virtual*. Puede usar el cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) para crear una subred. 

En el ejemplo siguiente se crea un servicio denominado *MyDMS* en el grupo de recursos *MyDMSResourceGroup*, que se encuentra en la región *Este de EE. UU.* mediante una red virtual denominada *MyVNET* y una subred llamada *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Creación de un proyecto de migración
Cree un proyecto de migración después de crear una instancia de Azure Database Migration Service. Un proyecto de Azure Database Migration Service requiere información de conexión para las instancias de origen y de destino, así como una lista de bases de datos que desee migrar como parte del proyecto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Creación de un objeto de información de conexión de base de datos para las conexiones de origen y de destino
Puede crear un objeto de información de conexión de base de datos mediante el cmdlet `New-AzureRmDmsConnInfo`. Este cmdlet espera los siguientes parámetros:
- *ServerType*. El tipo de conexión de base de datos solicitado, por ejemplo, SQL, Oracle o MySQL. Use SQL para SQL Server y SQL Azure.
- *DataSource*. El nombre o la dirección IP de una instancia SQL o servidor SQL Azure.
- *AuthType*. El tipo de autenticación para la conexión, que puede ser SqlAuthentication o WindowsAuthentication.
- El parámetro *TrustServerCertificate* establece un valor que indica si el canal se cifra mientras se omite recorrer la cadena de certificados para validar la confianza. El valor puede ser "true" o "false".

En el ejemplo siguiente se crea el objeto de información de conexión para el SQL Server de origen denominado MySQLSourceServer mediante la autenticación de SQL. 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

En el ejemplo siguiente se muestra la creación de la información de conexión para el servidor de base de datos de SQL Azure denominado MySQLAzureTarget mediante la autenticación de SQL

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Ofrecimiento de bases de datos para el proyecto de migración
Cree una lista de objetos `AzureRmDataMigrationDatabaseInfo` que especifique las bases de datos como parte del proyecto de Azure Database Migration que puede proporcionarse como parámetro para la creación del proyecto. El cmdlet `New-AzureRmDataMigrationDatabaseInfo` puede utilizarse para crear AzureRmDataMigrationDatabaseInfo. 

En el ejemplo siguiente se crea el proyecto `AzureRmDataMigrationDatabaseInfo` para la base de datos AdventureWorks2016 y lo agrega a la lista para proporcionarlo como parámetro para la creación del proyecto.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Creación de un objeto del proyecto
Por último, puede crear el proyecto de Azure Database Migration denominado *MyDMSProject* ubicado en el *Este de EE. UU.* con `New-AzureRmDataMigrationProject` y agregar las conexiones de origen y de destino creadas anteriormente y la lista de bases de datos que migrar.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfos $dbList
```

## <a name="create-and-start-a-migration-task"></a>Creación e inicio de una tarea de migración
Por último, cree e inicie la tarea de Azure Database Migration. La tarea de Azure Database Migration requiere información de credenciales de conexión de origen y destino, y una lista de las tablas de base de datos que se van a migrar, además de la información ya proporcionada con el proyecto creado como requisito previo. 

### <a name="create-credential-parameters-for-source-and-target"></a>Creación de parámetros de credenciales de origen y de destino
Las credenciales de seguridad de conexión pueden crearse como un objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0). 

En el ejemplo siguiente se muestra la creación de objetos *PSCredential* para las conexiones de origen y destino proporcionando contraseñas como variables de cadena *$sourcePassword* y *$targetPassword*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Crear una asignación de tabla y selección de parámetros de origen y de destino para la migración
Otro parámetro necesario para la migración es la asignación de tablas del origen al destino que se va a migrar. Cree un diccionario de tablas que proporcione una asignación entre las tablas de origen y de destino para la migración. En el ejemplo siguiente se muestra la asignación entre las tablas de origen y de destino de esquema de Recursos Humanos para la base de datos de AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

El paso siguiente consiste en seleccionar las bases de datos de origen y de destino y proporcionar asignaciones de tabla que migrar como un parámetro con el cmdlet `New-AzureRmDmsSqlServerSqlDbSelectedDB`, tal y como se muestra en el ejemplo siguiente:

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Creación e inicio de una tarea de migración

Utilice el cmdlet `New-AzureRmDataMigrationTask` para crear e iniciar una tarea de migración. Este cmdlet espera los siguientes parámetros:
- *TaskType*. Se espera el tipo de tarea de migración que crear para SQL Server para el tipo de migración de SQL Azure *MigrateSqlServerSqlDb*. 
- *Nombre del grupo de recursos*. Nombre del grupo de recursos de Azure en el que se crea la tarea.
- *ServiceName*. Instancia de Azure Database Migration Service en la que crear la tarea.
- *ProjectName*. Nombre del proyecto de Azure Database Migration en el que crear la tarea. 
- *TaskName*. Nombre de la tarea que se va a crear. 
- *Conexión de origen*. Objeto AzureRmDmsConnInfo que representa la conexión de origen.
- *Conexión de destino*. Objeto AzureRmDmsConnInfo que representa la conexión de destino.
- *SourceCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para la conexión al servidor de origen.
- *TargetCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para la conexión al servidor de destino.
- *SelectedDatabase*. Objeto AzureRmDmsSqlServerSqlDbSelectedDB que representa la asignación de base de datos de origen y de destino.

En el siguiente ejemplo se crea y se inicia una tarea de migración denominada myDMSTask:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>Supervisión de la migración
Puede supervisar la tarea de migración que se ejecuta mediante la consulta de la propiedad de estado de la tarea como se muestra en el siguiente ejemplo:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>pasos siguientes
- Revise las directrices de migración en la [Guía de migración de bases de datos](https://datamigration.microsoft.com/).