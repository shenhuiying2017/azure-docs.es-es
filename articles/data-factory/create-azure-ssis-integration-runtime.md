---
title: Crear Integration Runtime autohospedado en Azure Data Factory | Microsoft Docs
description: "Sepa cómo usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una Base de datos SQL de Azure o en un Almacenamiento de datos SQL de Azure desde una canalización de Factoría de datos."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: be9ffaac1f25068f1ad575c14ffb6666752159d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Crear una instancia de Integration Runtime de Azure SSIS en Azure Data Factory
En este artículo se describen los pasos necesarios para aprovisionar una instancia de Integration Runtime de SSIS de Azure en Azure Data Factory. Posteriormente, podrá usar SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) para implementar paquetes de SQL Server Integration Services (SSIS) en este entorno de ejecución de Azure.

El tutorial: [Implementación de paquetes de SQL Server Integration Services en Azure](tutorial-deploy-ssis-packages-azure.md) muestra cómo crear una instancia de Integration Runtime (IR) de SSIS de Azure mediante Azure SQL Database como almacén del catálogo de SSIS. Este artículo permite ampliar el tutorial y muestra cómo hacer lo siguiente: 

- Utilice la instancia administrada de Azure SQL (versión preliminar privada) para hospedar un catálogo de SSIS (base de datos SSISDB).
- Unión de Integration Runtime de SSIS de Azure a una red virtual (VNet) de Azure. 

Para obtener información conceptual sobre la unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual y sobre la configuración de una red virtual en Azure Portal, consulte [Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). 

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de Azure**. Si no tiene ninguna suscripción, puede crear una [cuenta de evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).
- **Servidor de Azure SQL Database** o **Instancia administrada de SQL Server (versión preliminar privada) (versión preliminar privada extendida)**. Si aún no tiene ningún servidor de base de datos, cree uno en Azure Portal antes de empezar a trabajar. Este servidor hospeda la base de datos de catálogo de SSIS (SSISDB). Le recomendamos que cree el servidor de base de datos en la misma región de Azure que la instancia de Integration Runtime. Esta configuración permite que la instancia de Integration Runtime escriba registros de ejecución en SSISDB sin traspasar regiones de Azure. Anote el plan de tarifa de su instancia de Azure SQL Server. Para una lista de los planes de tarifa admitidos para Azure SQL Database, consulte [Límites de recursos de SQL Database](../sql-database/sql-database-resource-limits.md).
- **Virtual Network (VNet) clásico (opcional)**. Debe tener una instancia de Azure Virtual Network (VNet) si se cumple al menos una de las siguientes condiciones:
    - Hospeda la base de datos de catálogo de SSIS en una instancia administrada de SQL Server (versión preliminar privada) que forme parte de una red virtual.
    - Quiere conectarse a almacenes de datos locales desde paquetes de SSIS que se ejecutan en una instancia de Integration Runtime de SSIS de Azure.
- **Azure PowerShell**. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps). Usa PowerShell para ejecutar un script para aprovisionar una instancia de Integration Runtime de SSIS de Azure que ejecuta paquetes de SSIS en la nube. 

## <a name="create-variables"></a>Creación de variables
Defina variables para usarlas en el script de este tutorial:

```powershell
# Azure Data Factory version 2 information
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" # In public preview, only EastUS|EastUS2 are supported.

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" # In public preview, only EastUS|NorthEurope are supported.
$AzureSSISNodeSize = "Standard_A4_v2" # In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeNumber = 2 # In public preview, only 1-10 nodes are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 # In public preview, only 1-8 parallel executions per node are supported.

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## Remove these two variables if you are using Azure SQL Database. 
## These two parameters apply if you are using VNet and Azure SQL Managed Instance (private preview). 
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In public preview, only classic virtual network (VNet) is supported.
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In public preview, only classic VNet is supported.

```

## <a name="validate-the-connection-to-database"></a>Validación de la conexión a la base de datos
Agregue el siguiente script para validar el punto de conexión del servidor de Azure SQL Database server.database.windows.net o de la instancia administrada de Azure SQL (versión preliminar privada). 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

## <a name="log-in-and-select-subscription"></a>Inicio de sesión y selección de la suscripción
Agregue el código siguiente al script para iniciar sesión y seleccione su suscripción a Azure: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="configure-virtual-network"></a>Configuración de una red virtual
Agregue el siguiente script para configurar automáticamente los permisos o la configuración de la red virtual para unir la instancia de Integration Runtime de SSIS de Azure.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo. En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Crear una factoría de datos
Ejecute el comando siguiente para crear una factoría de datos:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Creación de una instancia de Integration Runtime
Ejecute el siguiente comando para crear una instancia de Integration Runtime de SSIS de Azure que ejecute paquetes de SSIS en Azure: 

Si usa **Azure SQL Database** para hospedar la base de datos SSISDB (catálogo de SSIS): 


```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode
```

No es necesario pasar valores para VNetId y Subnet, a menos que necesite acceder a los datos locales, es decir, que tenga orígenes o destinos de datos locales en los paquetes SSIS. Debe pasar el valor para el parámetro CatalogPricingTier. Para una lista de los planes de tarifa admitidos para Azure SQL Database, consulte [Límites de recursos de SQL Database](../sql-database/sql-database-resource-limits.md).

Si usa una **instancia administrada de Azure SQL (versión preliminar privada)** para hospedar la base de datos SSISDB:

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

Tiene que pasar los valores para los parámetros VnetId y Subnet con la instancia administrada de Azure SQL (versión preliminar privada) que permite unirse a una red virtual. El parámetro CatalogPricingTier no se aplica a la instancia administrada de Azure SQL. 

## <a name="start-integration-runtime"></a>Inicio de la instancia de Integration Runtime
Ejecute el siguiente comando para iniciar la instancia de Integration Runtime de SSIS de Azure: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Este comando tarda entre **20 y 30 minutos** en completarse. 

## <a name="deploy-ssis-packages"></a>Implementación de paquetes SSIS
Luego, use SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) para implementar los paquetes de SSIS en Azure. Conéctese a la instancia de SQL Server de Azure que hospeda el catálogo de SSIS (SSISDB). El nombre de la instancia de SQL Server de Azure tiene el siguiente formato: &lt;nombreservidor&gt;.database.windows.net (para Azure SQL Database). Vea el artículo [Implementación de paquetes](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) para obtener instrucciones. 

## <a name="next-steps"></a>Pasos siguientes
Consulte los restantes temas sobre instancias de Integration Runtime de SSIS de Azure en esta documentación:

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual acerca de Integration Runtime en general, lo que incluye Integration Runtime de SSIS de Azure. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de IR de SSIS de Azure y se usa una base de datos de SQL Azure para hospedar el catálogo de SSIS. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar información sobre una instancia de IR de SSIS de Azure, junto con descripciones de los estados en la información devuelta. 
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo detener, iniciar o quitar una instancia de IR de SSIS de Azure. También muestra cómo escalar horizontalmente la instancia de Integration Runtime de SSIS de Azure al agregar más nodos a Integration Runtime. 
- [Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). En este artículo se proporciona información conceptual sobre cómo unir una instancia de Integration Runtime de SSIS de Azure a una red virtual de azure (VNet). También se proporcionan los pasos para configurar la red virtual mediante Azure Portal para que se una la instancia de Integration Runtime de SSIS de Azure. 