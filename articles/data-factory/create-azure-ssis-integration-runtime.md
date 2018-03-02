---
title: "Creación de un entorno de ejecución de SSIS en Azure en Azure Data Factory | Microsoft Docs"
description: "Obtenga información acerca de cómo crear un entorno de ejecución de SSIS en Azure para poder ejecutar el paquete SSIS en la nube de Azure."
services: data-factory
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
<<<<<<< HEAD
ms.author: douglasl
ms.openlocfilehash: 879489dffbf713b5fadb72a58638e462938aaf26
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
=======
<<<<<<< HEAD
ms.author: douglasl
ms.openlocfilehash: 879489dffbf713b5fadb72a58638e462938aaf26
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
=======
ms.author: spelluru
ms.openlocfilehash: f3616214dc63716a382b4374ff82bec33e876177
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
>>>>>>> 2b7eaf6c4cfe5413d9b581a669c96a2527a1b2ba
>>>>>>> eefc9d4b1876cab186dde3b22a97f8b1abe07f8b
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Crear una instancia de Integration Runtime de Azure SSIS en Azure Data Factory
En este artículo se describen los pasos necesarios para aprovisionar una instancia de Integration Runtime de SSIS de Azure en Azure Data Factory. Posteriormente, podrá usar SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) para implementar paquetes de SQL Server Integration Services (SSIS) en este entorno de ejecución de Azure.

El tutorial: [Implementación de paquetes de SQL Server Integration Services (SSIS) en Azure](tutorial-create-azure-ssis-runtime-portal.md) mostraba cómo crear un entorno de ejecución de SSIS en Azure utilizando Azure SQL Database como almacén del catálogo de SSIS. Este artículo permite ampliar el tutorial y muestra cómo hacer lo siguiente: 

- Utilice la instancia administrada de Azure SQL (versión preliminar privada) para hospedar un catálogo de SSIS (base de datos SSISDB).
- Unión de Integration Runtime de SSIS de Azure a una red virtual (VNet) de Azure. Para obtener información conceptual sobre la unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual y sobre la configuración de una red virtual en Azure Portal, consulte [Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-introduction.md).


## <a name="overview"></a>Información general
En este artículo se muestran diferentes maneras de aprovisionar un entorno de ejecución de SSIS en Azure:

- [portal de Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Plantilla de Azure Resource Manager](#azure-resource-manager-template)

Al crear un entorno de ejecución de SSIS en Azure, Data Factory se conecta a Azure SQL Database para preparar la base de datos de catálogo de SSIS (SSISDB). El script también configura los permisos y la configuración de la red virtual, si se especifica, y une la nueva instancia de Integration Runtime de SSIS de Azure a la red virtual.

Al aprovisionar una instancia del entorno de ejecución para la integración de SSIS en Azure, también se instala el paquete de características de Azure para SSIS y el acceso redistribuible. Estos componentes proporcionan conectividad a los archivos de Excel y Access, y a diversos orígenes de datos de Azure, además de los admitidos por los componentes integrados. No puede instalar componentes de terceros para SSIS en este momento (incluidos componentes de terceros de Microsoft, como los de Oracle y Teradata de Attunity y los componentes de inteligencia empresarial de SAP).

## <a name="prerequisites"></a>requisitos previos

- **Suscripción de Azure**. Si no tiene ninguna suscripción, puede crear una [cuenta de evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).
- **Servidor de Azure SQL Database** o **Instancia administrada de SQL Server (versión preliminar privada) (versión preliminar privada extendida)**. Si aún no tiene ningún servidor de base de datos, cree uno en Azure Portal antes de empezar a trabajar. Este servidor hospeda la base de datos de catálogo de SSIS (SSISDB). Le recomendamos que cree el servidor de base de datos en la misma región de Azure que la instancia de Integration Runtime. Esta configuración permite que la instancia de Integration Runtime escriba registros de ejecución en SSISDB sin traspasar regiones de Azure. Anote el plan de tarifa de su instancia de Azure SQL Server. Para una lista de los planes de tarifa admitidos para Azure SQL Database, consulte [Límites de recursos de SQL Database](../sql-database/sql-database-resource-limits.md).

    Confirme que el servidor de Azure SQL Database o SQL Server Managed Instance (versión preliminar privada extendida) no tienen un catálogo de SSIS (base de datos SSIDB). El aprovisionamiento de entornos de ejecución de SSIS en Azure no admite el uso de un catálogo de SSIS existente.
- **Red virtual (VNet) de Azure Resource Manager o clásica (opcional)**. Debe tener una instancia de Azure Virtual Network (VNet) si se cumple al menos una de las siguientes condiciones:
    - Hospeda la base de datos de catálogo de SSIS en una instancia administrada de SQL Server (versión preliminar privada) que forme parte de una red virtual.
    - Quiere conectarse a almacenes de datos locales desde paquetes de SSIS que se ejecutan en una instancia de Integration Runtime de SSIS de Azure.
- **Azure PowerShell**. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps). Usa PowerShell para ejecutar un script para aprovisionar una instancia de Integration Runtime de SSIS de Azure que ejecuta paquetes de SSIS en la nube. 

> [!NOTE]
> - Puede crear una factoría de datos de la versión 2 en las siguientes regiones: Este de EE. UU., Este de EE. UU. 2, Sudeste Asiático y Europa Occidental. 
> - Puede crear una instancia de IR de SSIS de Azure en las siguientes regiones: Este de EE. UU., Este de EE. UU. 2, Centro de EE. UU., Europa del Norte, Europa Occidental y Este de Australia.

## <a name="azure-portal"></a>Azure Portal
En esta sección, se usa Azure Portal, y en concreto la interfaz de usuario de Data Factory, para crear un entorno de ejecución de SSIS en Azure. 

### <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
2. Inicie sesión en [Azure Portal](https://portal.azure.com/).    
3. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. En la página **Nueva factoría de datos**, escriba **MyAzureSsisDataFactory** en el **nombre**. 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameMyAzureSsisDataFactory) e intente crearla de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
      Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V2 (versión preliminar)** como **versión**.
5. Seleccione la **ubicación** de Data Factory. En la lista solo se muestran las ubicaciones que se admiten para la creación de factorías de datos.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Create**(Crear).
8. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
   ![Página principal Factoría de datos](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Haga clic en **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Data Factory en una pestaña independiente. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionamiento de Integration Runtime de SSIS de Azure

1. En la página de introducción, haga clic en el icono **Configure SSIS Integration Runtime** (Configuración de Integration Runtime de SSIS). 

   ![Icono Configure SSIS Integration Runtime (Configuración de Integration Runtime de SSIS)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. En la página **General Settings** (Configuración general) de **Integration Runtime Setup** (Configuración de Integration Runtime), realice los pasos siguientes: 

   ![Configuración general](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Especifique un **nombre** para la instancia de Integration Runtime.
    2. Seleccione la **ubicación** para la instancia de Integration Runtime. Se muestran solo las ubicaciones admitidas.
    3. Seleccione el **tamaño del nodo** que se va a configurarse con la instancia de IR de SSIS.
    4. Especifique el **número de nodos** del clúster.
    5. Haga clic en **Next**. 
1. En **SQL Settings** (Configuración de SQL), realice los pasos siguientes: 

    ![Configuración de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Especifique la **suscripción** de Azure que tiene el servidor de Azure SQL. 
    2. Seleccione el servidor de Azure SQL Server de **Catalog Database Server Endpoint** (Punto de conexión del servidor de bases de datos de catálogo).
    3. Escriba el nombre de usuario del **administrador**.
    4. Escriba la **contraseña** del administrador.  
    5. Seleccione el **nivel de servicio** de la base de datos SSISDB. El valor predeterminado es Básico.
    6. Haga clic en **Next**. 
1.  En la página **Advanced Settings** (Configuración avanzada), seleccione un valor para **Maximum Parallel Executions Per Node** (Máximo de ejecuciones paralelas por nodo).   

    ![Configuración avanzada](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Este paso es **opcional**. Si tiene una red virtual (clásica o de Azure Resource Manager) a la que desee unir el entorno de ejecución de integración, seleccione la opción **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings** (Seleccionar una red virtual a la que unir el entorno de ejecución para la integración de SSIS en Azure y permitir que los servicios de Azure configuren los permisos y la configuración de la red) y realice los pasos siguientes: 

    ![Configuración avanzada con red virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. En **Subscription** (Suscripción), especifique la **suscripción** que tiene la red virtual. 
    2. En Tipo, especifique el **tipo** de la red virtual (red virtual clásica o red virtual de Azure Resource Manager). 
    3. En **VNet Name** (Nombre de red virtual), seleccione el nombre de la **red virtual**. 
    4. En **Subnet Name** (Nombre de subred), seleccione el nombre de la **subred** en la red virtual.
1. Haga clic en **Finish** (Finalizar) para iniciar la creación la instancia de Integration Runtime de SSIS de Azure. 

    > [!IMPORTANT]
    > - Este proceso tarda aproximadamente veinte minutos.
    > - El servicio Data Factory se conecta a Azure SQL Database para preparar la base de datos de catálogo de SSIS (SSISDB). El script también configura los permisos y la configuración de la red virtual, si se especifica, y une la nueva instancia de Integration Runtime de SSIS de Azure a la red virtual.
7. En la ventana **Connections**, cambie a **Integration Runtime** si es necesario. Haga clic en **Refresh** (Actualizar) para actualizar el estado. 

    ![Estado de la creación](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Use los vínculos de la columna **Acciones** para supervisar, detener, iniciar, editar o eliminar el entorno de ejecución de integración. Utilice el último vínculo para ver el código JSON de Integration Runtime. Los botones de edición y eliminación se habilitan únicamente cuando la instancia de IR se detiene. 

    ![IR de SSIS de Azure: acciones](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Instancias de Integration Runtime de SSIS de Azure en el portal

1. En la interfaz de usuario de Azure Data Factory, cambie a la pestaña **Edit** (Editar), haga clic en **Connections** (Conexiones) y cambie a la pestaña **Integration Runtime** para ver las instancias de Integration Runtime existentes en la factoría de datos. 
    ![Visualización de las instancias de IR existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Haga clic en **New** (Nuevo) para crear una instancia de IR de SSIS de Azure. 

    ![Integration Runtime a través del menú](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Para crear una instancia de Integration Runtime de SSIS de Azure, haga clic en **New** (Nuevo) tal como se muestra en la imagen. 
3. En la ventana de configuración de Integration Runtime, seleccione **Lift-and-shift existing SSIS packages to execute in Azure** (Migrar los paquetes de SSIS existentes mediante lift-and-shift para la ejecución en Azure) y haga clic en **Next** (Siguiente).

    ![Especificación del tipo de instancia de Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Consulte la sección [Aprovisionamiento de Integration Runtime de SSIS de Azure](#provision-an-azure-ssis-integration-runtime) para el resto de pasos de configuración de IR de SSIS de Azure.

## <a name="azure-powershell"></a>Azure PowerShell
En esta sección, se usa Azure PowerShell para crear un entorno de ejecución de SSIS en Azure.

### <a name="create-variables"></a>Creación de variables
Defina variables para usarlas en el script de este tutorial:

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"

# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, North Europe, West Europe, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>Inicio de sesión y selección de la suscripción
Agregue el código siguiente al script para iniciar sesión y seleccione su suscripción a Azure: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Validación de la conexión a la base de datos
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

### <a name="configure-virtual-network"></a>Configurar la red virtual
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
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo. En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Crear una factoría de datos
Ejecute el comando siguiente para crear una factoría de datos:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Creación de una instancia de Integration Runtime
Ejecute el siguiente comando para crear una instancia de Integration Runtime de SSIS de Azure que ejecute paquetes SSIS en Azure: use el script de la sección basado en el tipo de la base de datos (Azure SQL Database frente a Instancia administrada de Azure SQL (versión preliminar privada)) que está usando. 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Azure SQL Database para hospedar la base de datos SSISDB (catálogo de SSIS) 

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

#### <a name="azure-sql-managed-instance-private-preview-to-host-the-ssisdb-database"></a>Instancia administrada de Azure SQL (versión preliminar privada) para hospedar la base de datos SSISDB

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

### <a name="start-integration-runtime"></a>Inicio de la instancia de Integration Runtime
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


### <a name="full-script"></a>Script completo
Este es el script completo que crea una instancia de Integration Runtime de SSIS de Azure y la une a una red virtual. Este script considera que está usando la instancia administrada de Azure SQL para hospedar el catálogo de SSIS. 

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

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

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

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

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Plantilla del Administrador de recursos de Azure
En esta sección, se utiliza una plantilla de Azure Resource Manager para crear un entorno de ejecución de SSIS en Azure. Aquí se muestra el tutorial de ejemplo: 

1. Cree un archivo JSON con la siguiente plantilla de Resource Manager. Reemplace los valores de los corchetes angulares (marcadores de posición) con sus propios valores. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Para implementar la plantilla de Resource Manager, ejecute el comando New-AzureRmResourceGroupDeployment tal y como se muestra en el ejemplo siguiente. En este ejemplo, ADFTutorialResourceGroup es el nombre del grupo de recursos. ADFTutorialARM.json es el archivo que contiene la definición de JSON de la factoría de datos y del entorno de ejecución de SSIS en Azure. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Este comando crea la factoría de datos y un entorno de ejecución de SSIS en Azure, pero no inicia el entorno. 
3. Para iniciar el entorno de ejecución de SSIS en Azure, ejecute el comando AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Implementación de paquetes SSIS
Luego, use SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) para implementar los paquetes de SSIS en Azure. Conéctese a la instancia de SQL Server de Azure que hospeda el catálogo de SSIS (SSISDB). El nombre de la instancia de SQL Server de Azure tiene el siguiente formato: &lt;nombreservidor&gt;.database.windows.net (para Azure SQL Database). Vea el artículo [Implementación de paquetes](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) para obtener instrucciones. 

## <a name="next-steps"></a>pasos siguientes
Consulte los restantes temas sobre instancias de Integration Runtime de SSIS de Azure en esta documentación:

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual acerca de Integration Runtime en general, lo que incluye Integration Runtime de SSIS de Azure. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-create-azure-ssis-runtime-portal.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de IR de SSIS de Azure y se usa una base de datos de SQL Azure para hospedar el catálogo de SSIS. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar información sobre una instancia de IR de SSIS de Azure, junto con descripciones de los estados en la información devuelta. 
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo detener, iniciar o quitar una instancia de IR de SSIS de Azure. También muestra cómo escalar horizontalmente la instancia de Integration Runtime de SSIS de Azure al agregar más nodos a Integration Runtime. 
- [Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). En este artículo se proporciona información conceptual sobre cómo unir una instancia de Integration Runtime de SSIS de Azure a una red virtual de azure (VNet). También se proporcionan los pasos para configurar la red virtual mediante Azure Portal para que se una la instancia de Integration Runtime de SSIS de Azure. 