---
title: 'Guía de inicio rápido: Creación de una instancia de Azure SQL Data Warehouse: Azure PowerShell | Microsoft Docs'
description: Cree rápidamente un servidor lógico de SQL Database, una regla de firewall de nivel de servidor y una base de datos de almacenamiento de datos con Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e0bb014ec0706d458ff2f38e409efba5d66aaf18
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31529238"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Guía de inicio rápido: Creación y consulta de una instancia de Azure SQL Data Warehouse con Azure PowerShell

Cree rápidamente una instancia de Azure SQL Data Warehouse con Azure PowerShell.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

Para realizar este tutorial, es necesaria la versión 5.1.1 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión que tiene actualmente. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell). 


> [!NOTE]
> La creación de una instancia de SQL Data Warehouse puede dar lugar a un nuevo servicio facturable.  Para más información, consulte [Precios de Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en la suscripción de Azure con el comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) y siga las instrucciones de la pantalla.

```powershell
Add-AzureRmAccount
```

Para ver qué suscripción está usando, ejecute [AzureRmSubscription Get](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Si necesita usar una suscripción diferente de la predeterminada, ejecute [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Creación de variables

Defina las variables que va a usar en los scripts de esta guía de inicio rápido.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo. En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>un servidor lógico

Cree un [servidor lógico de Azure SQL](/powershell/module/azurerm.sql/new-azurermsqlserver) con el comando [New-AzureRmSqlServer](../sql-database/sql-database-servers-databases.md#what-is-an-azure-sql-logical-server). Un servidor lógico contiene un conjunto de bases de datos administradas como un grupo. En el ejemplo siguiente se crea un servidor con nombre aleatorio en el grupo de recursos con un inicio de sesión de administrador denominado `ServerAdmin` y una contraseña `ChangeYourAdminPassword1`. Cambie estos valores predefinidos por los que prefiera.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configuración de una regla de firewall del servidor

Para crear una [regla de firewall de nivel de servidor de Azure SQL](../sql-database/sql-database-firewall-configure.md), ejecute el comando [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule). Una regla de firewall de nivel de servidor permite a una aplicación externa, como SQL Server Management Studio o la utilidad SQLCMD conectarse a una instancia de SQL Data Warehouse a través del firewall del servicio de SQL Data Warehouse. En el ejemplo siguiente, el firewall está abierto solo para otros recursos de Azure. Para habilitar la conectividad externa, cambie la dirección IP a una dirección apropiada para su entorno. Para abrir todas las direcciones IP, utilice 0.0.0.0 como la dirección IP inicial y 255.255.255.255 como la dirección final.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database y SQL Data Warehouse se comunican a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. Si es así, no podrá conectarse al servidor SQL de Azure a menos que el departamento de TI abra el puerto 1433.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Creación de una base de datos de almacenamiento de datos con datos de ejemplo
En este ejemplo se crea una base de datos de almacenamiento de datos con las variables definidas anteriormente.  Especifica el objetivo del servicio como DW400, que es un punto inicial a bajo costo para la base de datos de almacenamiento de datos. 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Los parámetros obligatorios son:

* **RequestedServiceObjectiveName**: la cantidad de [unidades de almacenamiento de datos](what-is-a-data-warehouse-unit-dwu-cdwu.md) que solicita. Si se aumenta esta cantidad, aumentará el costo de proceso. Para ver una lista de los valores compatibles, consulte los [límites de memoria y simultaneidad](memory-and-concurrency-limits.md).
* **DatabaseName**: el nombre de SQL Data Warehouse que está creando.
* **ServerName**: el nombre del servidor que se usa para la creación.
* **ResourceGroupName**: el grupo de recursos que está usando. Para buscar grupos de recursos que estén disponibles en su suscripción, use Get-AzureResource.
* **Edition**: la edición debe ser "DataWarehouse" para crear un SQL Data Warehouse.

Los parámetros opcionales son:

- **CollationName**: la intercalación predeterminada cuando no se especifica otra es SQL_Latin1_General_CP1_CI_AS. No se puede cambiar la intercalación de una base de datos.
- **MaxSizeBytes**: el tamaño máximo predeterminado de una base de datos es 10 GB.

Para más información sobre las opciones de parámetro, consulte [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Limpieza de recursos

Otros tutoriales de inicio rápido de esta colección se basan en esta guía. 

> [!TIP]
> Si tiene previsto seguir trabajando con los tutoriales siguientes, no elimine los recursos creados en esta guía. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Pasos siguientes

Ahora ha creado un almacenamiento de datos, ha creado una regla de firewall, la ha conectado a su almacén de datos y ha ejecutado algunas consultas. Para más información sobre Azure SQL Data Warehouse, siga el tutorial para cargar los datos.
> [!div class="nextstepaction"]
>[Carga de datos en una instancia de SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
