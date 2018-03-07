---
title: "Unión de una instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual | Microsoft Docs"
description: Aprenda a unir Integration Runtime de-SSIS de Azure a una red virtual de Azure.
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
ms.author: douglasl
ms.openlocfilehash: 3a5b68729d587e1365c42125108e610705965c86
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual
Una su instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual de Azure en los siguientes escenarios: 

- Va a hospedar el catálogo de SQL Server Integration Services (SSIS) en la instancia administrada de Azure SQL Database (versión preliminar privada) en una red virtual.
- Quiere conectarse a almacenes de datos locales desde paquetes de SSIS que se ejecutan en una instancia de Integration Runtime de SSIS de Azure.

 Azure Data Factory, versión 2 (versión preliminar) le permite unir su instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual creada mediante el modelo de implementación clásica o el modelo de implementación de Azure Resource Manager. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de esta versión de Data Factory](v1/data-factory-introduction.md).

## <a name="access-to-on-premises-data-stores"></a>Acceso a los almacenes de datos locales
Si los paquetes SSIS acceden solo a almacenes de datos de la nube pública, no necesita unir la instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual. Si los paquetes SSIS acceden a almacenes de datos locales, debe unir la instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual que esté conectada a la red local. 

Si el catálogo de SSIS está hospedado en una instancia de Azure SQL Database que no está en la red virtual, deberá abrir los puertos adecuados. 

Si el catálogo de SSIS está hospedado en la instancia administrada de SQL Database de una red virtual, puede unir una instancia de Integration Runtime para la integración de SSIS en Azure a:

- La misma red virtual.
- Una red virtual diferente que tenga una conexión red a red con la que tiene la instancia administrada de SQL Database. 

La red virtual puede implementarse mediante el modelo de implementación clásica o el modelo de implementación de Azure Resource Manager. Si tiene pensado unir la instancia de Integration Runtime para la integración de SSIS en Azure que se encuentra en la *misma red virtual* que la instancia administrada de SQL Database, asegúrese de que la primera de estas instancias se encuentre en una *subred diferente* de la que tiene la segunda.   

En las siguientes secciones se proporciona más información.

Estos son algunos puntos importantes a tener en cuenta: 

- Si no hay ninguna red virtual existente conectada a su red local, primero cree una [red virtual de Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) o una [red virtual clásica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para que se una su instancia de Integration Runtime para la integración de SSIS en Azure. A continuación, configure una [conexión de puerta de enlace VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) de sitio a sitio o de [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) entre esa red virtual y la red local.
- Si hay una red de virtual clásica o de Azure Resource Manager existente conectada a la red local en la misma ubicación que su instancia de Integration Runtime para la integración de SSIS en Azure, puede unir Integration Runtime a esa red virtual.
- Si hay una red virtual clásica existente conectada a la red local en una ubicación diferente a la de su instancia de Integration Runtime para la integración de SSIS en Azure, puede crear primero una [red virtual clásica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para que se una su instancia. A continuación, configure una [red virtual clásica a clásica](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). O también, puede crear una [red virtual de Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que se una su instancia de Integration Runtime para la integración de SSIS en Azure. A continuación, configure una conexión de [red virtual clásica a Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
- Si hay una red virtual de Azure Resource Manager existente conectada a la red local en una ubicación diferente a la de la instancia de Integration Runtime para la integración de SSIS en Azure, puede crear primero una [red virtual de Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) para que se una su instancia. A continuación, configure una conexión de red virtual de Azure Resource Manager a Azure Resource Manager. O bien, puede crear una [red virtual clásica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para que se una dicha instancia. A continuación, configure una conexión de [red virtual clásica a Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Servidor de Servicio de nombres de dominio 
Si tiene que usar su propio servidor de Servicio de nombres de dominio (DNS) en una red virtual a la que se ha unido su instancia de Integration Runtime para la integración de SSIS en Azure, siga estas instrucciones para [asegurarse de que los nodos de dicha instancia de la red virtual puedan resolver los puntos de conexión de Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Grupo de seguridad de red
Si tiene que implementar grupos de seguridad de red (NSG) en una red virtual a la que se ha unido su instancia de Integration Runtime para la integración de SSIS en Azure, permita el tráfico entrante y saliente mediante los siguientes puertos:

| Puertos | Dirección | Protocolo de transporte | Propósito | Origen de entrada/destino de salida |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100 (si une la instancia de Integration Runtime a una red virtual clásica)<br/><br/>29876, 29877 (si une la instancia de Integration Runtime a una red virtual de Azure Resource Manager) | Entrada | TCP | Los servicios de Azure usan estos puertos para comunicarse con los nodos de su instancia de Integration Runtime para la integración de SSIS en Azure de la red virtual. | Internet | 
| 443 | Salida | TCP | Los nodos de su instancia de Integration Runtime para la integración de SSIS en Azure de la red virtual usan este puerto para acceder a servicios de Azure, como Azure Storage y Azure Event Hubs. | Internet | 
| 1433<br/>11000-11999<br/>14000-14999  | Salida | TCP | Los nodos de su instancia de Integration Runtime para la integración de SSIS en Azure de la red virtual usan estos puertos para acceder a la SSISDB que hospeda el servidor de Azure SQL Database (esta finalidad no se aplica a la SSISDB hospedada por la instancia administrada de SQL Database). | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (interfaz de usuario de Data Factory)
En esta sección se muestra cómo unir un entorno de ejecución existente para la integración de SSIS en Azure a una red virtual (clásica o de Azure Resource Manager) mediante Azure Portal y la interfaz de usuario de Data Factory. En primer lugar, debe configurar la red virtual de forma adecuada antes de unir a ella su instancia de Integration Runtime para la integración de SSIS en Azure. Repase una de las dos secciones siguientes según el tipo de red virtual (clásica o de Azure Resource Manager). Después, continúe con la tercera sección para unir su instancia de Integration Runtime para la integración de SSIS en Azure a la red virtual. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Uso del portal para configurar una red virtual clásica
Antes de poder unir una instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual, debe configurar dicha red.

1. Inicie Microsoft Edge o Google Chrome. Actualmente, la interfaz de usuario de Data Factory solo se admite en estos exploradores web.
2. Inicie sesión en el [Azure Portal](https://portal.azure.com).
3. Seleccione **Más servicios**. Filtre y seleccione **Redes virtuales (clásicas)**.
4. Filtre y seleccione su red virtual en la lista. 
5. En la página **Red virtual (clásica)**, seleccione **Propiedades**. 

    ![Identificador de recurso de red virtual clásica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Seleccione el botón de copia en **ID. DE RECURSO** para copiar el identificador de recurso de la red clásica en el Portapapeles. Guarde el identificador del Portapapeles en OneNote o en un archivo.
6. En el menú de la izquierda, seleccione **Subredes**. Asegúrese de que el número de **direcciones disponibles** sea mayor que los nodos de su instancia de Integration Runtime para la integración de SSIS en Azure.

    ![Número de direcciones disponibles en la red virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Una **MicrosoftAzureBatch** al rol **Colaborador de la máquina virtual clásica** de la red virtual.

    a. Seleccione **Control de acceso (IAM)** en el menú izquierdo y seleccione **Agregar** en la barra de herramientas. 
       ![Botones "Control de acceso" y "Agregar"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. En la página **Agregar permisos**, seleccione **Colaborador de la máquina virtual clásica** en **Rol**. Pegue **ddbf3205-c6bd-46ae-8127-60eb93363864** en el cuadro de texto **Seleccionar** y, después, seleccione **Microsoft Azure Batch** en la lista de resultados de la búsqueda.   
       ![Resultados de búsqueda de la página "Agregar permisos"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Seleccione **Guardar** para guardar la configuración y cerrar la página.  
       ![Guardar la configuración de acceso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Confirme que ve **Microsoft Azure Batch** en la lista de colaboradores.  
       ![Confirmación del acceso a Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. Compruebe que el proveedor de Azure Batch está registrado en la suscripción de Azure que tiene la red virtual. O bien, registre el proveedor de Azure Batch. Si ya tiene una cuenta de Azure Batch en su suscripción, entonces su suscripción está registrada para Azure Batch.

   a. En Azure Portal, seleccione **Suscripciones** en el menú izquierdo.

   b. Seleccione su suscripción.

   c. Haga clic en **Proveedores de recursos** a la izquierda y confirme que **Microsoft.Batch** es un proveedor registrado.     
      ![Confirmación del estado "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Si no ve **Microsoft.Batch** en la lista, regístrelo. Para ello, [cree una cuenta de Azure Batch vacía](../batch/batch-account-create-portal.md) en su suscripción. Puede eliminarlo más tarde. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Uso del portal para configurar una red virtual de Azure Resource Manager
Antes de poder unir una instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual, debe configurar dicha red.

1. Inicie Microsoft Edge o Google Chrome. Actualmente, la interfaz de usuario de Data Factory solo se admite en esos exploradores web.
2. Inicie sesión en el [Azure Portal](https://portal.azure.com).
3. Seleccione **Más servicios**. Filtre y seleccione **Redes virtuales**.
4. Filtre y seleccione su red virtual en la lista. 
5. En la página **Red virtual**, seleccione **Propiedades**. 
6. Seleccione el botón de copia en **ID. DE RECURSO** para copiar el identificador de recurso de la red virtual en el Portapapeles. Guarde el identificador del Portapapeles en OneNote o en un archivo.
7. En el menú de la izquierda, seleccione **Subredes**. Asegúrese de que el número de **direcciones disponibles** sea mayor que los nodos de su instancia de Integration Runtime para la integración de SSIS en Azure.
8. Compruebe que el proveedor de Azure Batch está registrado en la suscripción de Azure que tiene la red virtual. O bien, registre el proveedor de Azure Batch. Si ya tiene una cuenta de Azure Batch en su suscripción, entonces su suscripción está registrada para Azure Batch.

   a. En Azure Portal, seleccione **Suscripciones** en el menú izquierdo.

   b. Seleccione su suscripción. 
   
   c. Haga clic en **Proveedores de recursos** a la izquierda y confirme que **Microsoft.Batch** es un proveedor registrado.     
      ![Confirmación del estado "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Si no ve **Microsoft.Batch** en la lista, regístrelo. Para ello, [cree una cuenta de Azure Batch vacía](../batch/batch-account-create-portal.md) en su suscripción. Puede eliminarlo más tarde.

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Unión de la instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual


1. Inicie Microsoft Edge o Google Chrome. Actualmente, la interfaz de usuario de Data Factory solo se admite en esos exploradores web.
2. En [Azure Portal](https://portal.azure.com), seleccione **Factorías de datos** en el menú de la izquierda. Si no ve **Factorías de datos** en el menú, seleccione **Más servicios** y seleccione **Factorías de datos** en la sección **INTELIGENCIA Y ANÁLISIS**. 
    
   ![Lista de factorías de datos](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. Seleccione su factoría de datos con la instancia de Integration Runtime para la integración de SSIS en Azure en la lista. Verá la página principal de la factoría de datos. Seleccione el icono **Crear e implementar**. Verá la interfaz de usuario de Data Factory en una pestaña independiente. 

   ![Página principal Factoría de datos](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. En la interfaz de usuario de Data Factory, vaya a la pestaña **Editar**, seleccione **Conexiones** y vaya a la pestaña **Integration Runtimes** (Entornos de ejecución de integración). 

   ![Pestañas "Integration runtimes" (Entornos de ejecución de integración)](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Si su instancia de Integration Runtime para la integración de SSIS en Azure está en funcionamiento, en la lista de entornos de ejecución de integración, seleccione el botón **Detener** de la columna **Acciones** de dicha instancia. No podrá editar el entorno de ejecución de integración hasta que lo detenga. 

   ![Detención de Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. En la lista de entornos de ejecución de integración, seleccione el botón **Editar** de la columna **Acciones** de su instancia de Integration Runtime para la integración de SSIS en Azure.

   ![Edición de Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. En la página **General Settings** (Configuración general) de la ventana **Integration Runtime Setup** (Configuración de Integration Runtime), seleccione **Siguiente**. 

   ![Configuración general de Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. En la página **Configuración de SQL**, escriba la contraseña del administrador y seleccione **Siguiente**.

   ![Configuración de SQL Server para Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. Haga lo siguiente en la página **Configuración avanzada**: 

   a. Active la casilla **Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings** (Seleccionar una red virtual a la que unir su instancia de Integration Runtime para la integración de SSIS en Azure y permitir que los servicios de Azure configuren los permisos y la configuración de la red virtual).

   b. En **Tipo**, especifique si la red virtual es una red virtual clásica o a una red virtual de Azure Resource Manager. 

   c. En **Nombre de red virtual**, seleccione la red virtual.

   d. En **Nombre de subred**, seleccione la subred de la red virtual.

   e. Seleccione **Actualizar**. 

   ![Configuración avanzada de Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Ahora, puede iniciar Integration Runtime mediante el botón **Iniciar** de la columna **Acciones** de su instancia de Integration Runtime para la integración de SSIS en Azure. Se tardan 20 minutos aproximadamente en iniciar una instancia de Integration Runtime para la integración de SSIS en Azure. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Configuración de una red virtual
Antes de poder unir una instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual, debe configurar dicha red. Para configurar automáticamente los permisos o los valores de la red virtual para que su instancia de Integration Runtime para la integración de SSIS en Azure se una a la red virtual, agregue el siguiente script.

```powershell
# Register to the Azure Batch resource provider
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
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Creación de una instancia de Integration Runtime para la integración de SSIS en Azure y su unión a una red virtual
Puede crear una instancia de Integration Runtime para la integración de SSIS en Azure y unirla a una red virtual al mismo tiempo. Para conocer el script y las instrucciones en su totalidad, consulte [Creación de una instancia de Integration Runtime para la integración de SSIS en Azure](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Unión de una instancia de Integration Runtime para la integración de SSIS en Azure ya existente a una red virtual
El script del artículo [Creación de una instancia de Integration Runtime para la integración de SSIS en Azure](create-azure-ssis-integration-runtime.md) muestra cómo crear una instancia de Integration Runtime para la integración de SSIS en Azure y unirla a una red virtual en el mismo script. Si ya tiene una instancia de Integration Runtime para la integración de SSIS en Azure, realice los pasos siguientes para unirla a la red virtual: 

1. Detenga la instancia de Integration Runtime de SSIS de Azure.
2. Configure la instancia de Integration Runtime para la integración de SSIS en Azure para unirla a la red virtual. 
3. Inicie la instancia de Integration Runtime de SSIS de Azure. 

### <a name="define-the-variables"></a>Definir las variables

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Detener la instancia de Integration Runtime de SSIS de Azure
Detenga la instancia de Integration Runtime para la integración de SSIS en Azure antes de unirla a una red virtual. Este comando libera todos sus nodos y detiene la facturación:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configuración de la red virtual para que se una la instancia de Integration Runtime para la integración de SSIS en Azure
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
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configurar la instancia de Integration Runtime de SSIS de Azure
Para configurar la instancia de Integration Runtime para la integración de SSIS en Azure para que se una a la red virtual, ejecute el comando `Set-AzureRmDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Iniciar la instancia de Integration Runtime de SSIS de Azure
Para iniciar la instancia de Integration Runtime para la integración de SSIS en Azure, ejecute el siguiente comando: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Este comando tarda entre 20 y 30 minutos en finalizar.

## <a name="next-steps"></a>pasos siguientes
Para más información sobre el tiempo de ejecución de integración de SSIS en Azure, consulte los temas siguientes: 

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual sobre los entornos de ejecución de integración en general, incluida la instancia de Integration Runtime para la integración de SSIS en Azure. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-create-azure-ssis-runtime-portal.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de Integration Runtime para la integración de SSIS en Azure. Se usa una instancia de Azure SQL Database para hospedar el catálogo de SSIS. 
- [Creación de una instancia de Integration Runtime de SSIS de Azure](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial y se proporcionan instrucciones sobre el uso de la instancia administrada de Azure SQL Database (versión preliminar privada) y la unión de Integration Runtime a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar información sobre una instancia de IR de SSIS de Azure, junto con descripciones de los estados en la información devuelta. 
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo detener, iniciar o quitar una instancia de IR de SSIS de Azure. También se muestra cómo escalar horizontalmente la instancia de Integration Runtime para la integración de SSIS en Azure mediante la adición de nodos. 
