---
title: "Unión de Integration Runtime de SSIS de Azure a una red virtual | Microsoft Docs"
description: Aprenda a unir Integration Runtime de-SSIS de Azure a una red virtual de Azure.
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
ms.date: 09/27/2017
ms.author: spelluru
ms.openlocfilehash: aa570379890023c83383d291aa5d57fb79b2d5aa
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual
Debe unir Integration Runtime (IR) de SSIS de Azure a una red virtual de Azure (VNet) si se cumple una de las siguientes condiciones: 

- Hospeda la base de datos de catálogo de SSIS en una instancia administrada de SQL Server (versión preliminar privada) que forma parte de una red virtual.
- Quiere conectarse a almacenes de datos locales desde paquetes SSIS que se ejecutan en una instancia de Integration Runtime de SSIS de Azure.

 La versión 2 de Azure Data Factory (versión preliminar) le permite unir su instancia de Integration Runtime de SSIS de Azure a una red virtual clásica. Actualmente, no se admiten redes virtuales de Azure Resource Manager. Sin embargo, puede trabajar de forma alternativa como se muestra en la siguiente sección. 

 > [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory versión 1](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Acceso a los almacenes de datos locales
Si los paquetes SSIS acceden solo a almacenes de datos de la nube pública, no necesita unir IR de SSIS de Azure a una red virtual. Si los paquetes SSIS acceden a almacenes de datos locales, debe unir IR de SSIS de Azure a una red virtual que esté conectada a la red local. Si el catálogo de SSIS está hospedado en una instancia de Azure SQL Database que no está en la red virtual, deberá abrir los puertos adecuados. Si el catálogo de SSIS está hospedado en una instancia administrada de Azure SQL que se encuentra en una red virtual clásica, puede unir IR de SSIS de Azure a la misma red virtual clásica (o) a una diferente que tenga una conexión de red virtual clásica a clásica con la que tiene la instancia administrada de Azure SQL. En las siguientes secciones se proporciona más información.

Estos son algunos puntos importantes a tener en cuenta: 

- Si no hay ninguna red virtual existente conectada a la red local, cree primero una [red virtual clásica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para que se una su instancia de Integration Runtime de SSIS de Azure. A continuación, configure una conexión de [puerta de enlace VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) entre esa red virtual y la red local.
- Si hay una red de virtual clásica existente conectada a la red local en la misma ubicación que Integration Runtime de SSIS de Azure,, puede unir su instancia de Integration Runtime de SSIS de Azure a ella.
- Si hay una red virtual clásica existente conectada a la red local en una ubicación diferente a la de Integration Runtime de SSIS de Azure, cree primero una [red virtual clásica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para que se una su instancia de Integration Runtime de SSIS de Azure. A continuación, configure una conexión de [red virtual de clásica a clásica](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md).
- Si hay una red virtual de Azure Resource Manager existente conectada a su red local, primero cree una [red virtual clásica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para que se una su instancia de Integration Runtime de SSIS de Azure. A continuación, configure una conexión de [red virtual clásica a Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Servidor de Servicio de nombres de dominio 
Si tiene que usar su propio servidor de Servicio de nombres de dominio (DNS) en una red virtual a la que se ha unido su instancia de Integration Runtime de SSIS de Azure, siga estas instrucciones para [asegurarse de que los nodos de dicha instancia de la red virtual puedan resolver los puntos de conexión de Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Grupo de seguridad de red (NSG)
Si tiene que implementar grupos de seguridad de red (NSG) en una red virtual a la que se ha unido su instancia de Integration Runtime de SSIS de Azure, permita el tráfico entrante y saliente mediante los siguientes puertos:

| Puertos | Dirección | Protocolo de transporte | Propósito | Origen de entrada/destino de salida |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | Entrada | TCP | Los servicios de Azure usan estos puertos para comunicarse con los nodos de su instancia de Integration Runtime de SSIS de Azure de la red virtual. | Internet | 
| 443 | Salida | TCP | Los nodos de su instancia de Integration Runtime de SSIS de Azure de la red virtual usan este puerto para acceder a los servicios de Azure, por ejemplo, Azure Storage, Event Hubs, etc. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | Salida | TCP | Los nodos de su instancia de Integration Runtime de SSIS de Azure de la red virtual usan estos puertos para acceder a la SSISDB que hospeda el servidor de Azure SQL Database (no aplicable a la SSISDB hispedada por la instancia administrada de Azure SQL). | Internet | 

## <a name="configure-vnet"></a>Configurar red virtual
Debe configurar la red virtual de una de las maneras siguientes (script frente a Azure Portal) para poder unir una instancia de Integration Runtime de SSIS de Azure a la red virtual. 

### <a name="script-to-configure-vnet"></a>Script para configurar la red virtual 
Agregue el siguiente script para configurar automáticamente los permisos o la configuración de la red virtual para unir la instancia de Integration Runtime de SSIS de Azure a la red virtual.

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

### <a name="use-portal-to-configure-vnet"></a>Uso del portal para configurar la red virtual
La manera más sencilla de configurar la red virtual es ejecutar el script. Si no tiene acceso para configurar esa red virtual o la configuración automática genera error, el propietario de la red virtual o usted pueden intentar configurarla manualmente en los siguientes pasos:

### <a name="find-the-resource-id-for-your-azure-vnet"></a>Busque el identificador de recurso de la red virtual de Azure.
 
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Haga clic en **Más servicios**. Filtre y seleccione **Redes virtuales (clásicas)**.
3. Filtre y seleccione su **red virtual** de la lista. 
4. En la página de la red virtual (clásica), seleccione **Propiedades**. 

    ![Identificador de recurso de red virtual clásica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Haga clic en el botón de copia en **ID. DE RECURSO** para copiar el identificador de recurso de la red clásico en el Portapapeles. Guarde el identificador del Portapapeles en OneNote o en un archivo.
6. Haga clic en **Subredes** en el menú izquierdo y asegúrese de que el número de **direcciones disponibles** es mayor que los nodos de su instancia de Integration Runtime de SSIS de Azure.

    ![Número de direcciones disponibles en la red virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Una **MicrosoftAzureBatch** al rol **Colaborador de la máquina virtual clásica** de la red virtual. 
    1. Haga clic en Control de acceso (IAM) en el menú de la izquierda y haga clic en **Agregar** en la barra de herramientas.
    
        ![Control de acceso -> Agregar](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. En la página **Agregar permisos**, seleccione **Colaborador de la máquina virtual clásica** en **Rol**. Copie y pegue **ddbf3205-c6bd-46ae-8127-60eb93363864** en el cuadro de texto **Seleccionar** y, después, seleccione **Microsoft Azure Batch** en la lista de resultados de la búsqueda. 
    
        ![Adición de permisos: buscar](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. Haga clic en Guardar para guardar la configuración y cerrar la página.
    
        ![Guardado de la configuración de acceso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. Confirme que ve **MicrosoftAzureBatch** en la lista de colaboradores.
    
        ![Confirmación del acceso a AzureBatch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Compruebe que el proveedor de Azure Batch está registrado en la suscripción de Azure que tiene la red virtual; en caso contrario, regístrelo. Si ya tiene una cuenta de Azure Batch en su suscripción, entonces su suscripción está registrada para Azure Batch.
    1. En Azure Portal, haga clic en **Suscripciones** en el menú izquierdo. 
    2. Seleccione su **suscripción**. 
    3. Haga clic en **Proveedores de recursos** a la izquierda y confirme que `Microsoft.Batch` es un proveedor registrado. 
    
        ![confirmación de registrado para batch](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Si no ve `Microsoft.Batch` en la lista, regístrelo. Para ello, [cree una cuenta de Azure Batch vacía](../batch/batch-account-create-portal.md) en su suscripción. Puede eliminarlo más tarde. 

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Crear una instancia de Integration Runtime de SSIS de Azure y unirla a una red virtual
Puede crear una instancia de Integration Runtime de SSIS de Azure y unirla a la red virtual al mismo tiempo. Para obtener el script y las instrucciones completas para crear una instancia de Integration Runtime de SSIS de Azure y unirla a una red virtual al mismo tiempo, consulte [Crear una instancia de Integration Runtime de Azure SSIS](create-azure-ssis-integration-runtime.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Unir una instancia de Integration Runtime de SSIS de Azure a una red virtual
El script del artículo [Crear una instancia de Integration Runtime de Azure SSIS](create-azure-ssis-integration-runtime.md) muestra cómo crear una instancia de Integration Runtime de SSIS de Azure y cómo conectarla a una red virtual en el mismo script. Si ya tiene una instancia de Integration Runtime de SSIS de Azure, realice los pasos siguientes para unirla a la red virtual. 

1. Detenga la instancia de Integration Runtime de SSIS de Azure.
2. Configure la instancia de Integration Runtime de SSIS de Azure para unirla a la red virtual. 
3. Inicie la instancia de Integration Runtime de SSIS de Azure. 

## <a name="define-the-variables"></a>Definir las variables

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
# Get the following information from the properties page for your Classic Virtual Network in the Azure portal
# It should be in the format: 
# $VnetId = "/subscriptions/<Azure Subscription ID>/resourceGroups/<Azure Resource Group>/providers/Microsoft.ClassicNetwork/virtualNetworks/<Class Virtual Network Name>"
$VnetId = "<Name of your Azure classic virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Detener la instancia de Integration Runtime de SSIS de Azure
Detener el tiempo de ejecución de la instancia de Integration Runtime de SSIS de Azure para poder unirla a la red virtual. Este comando libera todos sus nodos y detiene la facturación.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Configurar la red virtual para unir la instancia de Integration Runtime de SSIS de Azure
Registro del proveedor de recursos de Azure Batch:

```powershell
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

### <a name="configure-the-azure-ssis-ir"></a>Configurar la instancia de Integration Runtime de SSIS de Azure
Ejecute el comando Set-AzureRmDataFactoryV2IntegrationRuntime para configurar el tiempo de ejecución de la instancia de Integration Runtime de SSIS de Azure para unirse a la red virtual: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

## <a name="start-the-azure-ssis-ir"></a>Iniciar la instancia de Integration Runtime de SSIS de Azure
Ejecute el siguiente comando para iniciar la instancia de Integration Runtime de SSIS de Azure: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Este comando tarda entre **20 y 30 minutos** en completarse.

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes temas para más información sobre Integration Runtime de SSIS de Azure: 

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual acerca de Integration Runtime en general, lo que incluye Integration Runtime de SSIS de Azure. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de IR de SSIS de Azure y se usa una base de datos de SQL Azure para hospedar el catálogo de SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Creación de una instancia de Integration Runtime de SSIS de Azure). Este artículo amplía el tutorial y proporciona instrucciones sobre el uso de la instancia administrada de Azure SQL (versión preliminar privada) y la unión de Integration Runtime a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar información sobre una instancia de IR de SSIS de Azure, junto con descripciones de los estados en la información devuelta. 
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo detener, iniciar o quitar una instancia de IR de SSIS de Azure. También se muestra cómo escalar horizontalmente IR de SSIS de Azure mediante la adición de más nodos a IR. 
