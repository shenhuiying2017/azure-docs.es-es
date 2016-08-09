<properties
   pageTitle="Creación de un emparejamiento de VNET mediante plantillas de Azure Resource Manager | Microsoft Azure"
   description="Aprenda a crear un emparejamiento de red virtual mediante las plantillas de Resource Manager."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos"/>

# Creación de un emparejamiento de VNET mediante plantillas de Azure Resource Manager

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para crear un emparejamiento de VNET mediante plantillas de Resource Manager, siga estos pasos:

1. Si es la primera vez que usa Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.

Nota: El cmdlet de PowerShell para administrar el emparejamiento de VNET se suministra con [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. En la siguiente sección se muestra la definición del emparejamiento de VNET entre VNet1 y VNet2, basado en el escenario anterior. Copie el contenido que aquí se describe en un archivo y guárdelo en el archivo VNetPeeringVNet1.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }
    

3. En la siguiente sección se muestra la definición del emparejamiento de VNET entre VNet2 y VNet1, basado en el escenario anterior. Copie el contenido que aquí se describe en un archivo y guárdelo en el archivo VNetPeeringVNet2.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

Como se muestra en plantilla anterior, hay algunas propiedades configurables en el emparejamiento de VNET:

|Opción|Descripción|Valor predeterminado|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Si el espacio de direcciones de red virtual emparejada se incluye como parte de la etiqueta Virtual\_network.|Sí|
|AllowForwardedTraffic|Permite aceptar o rechazar el tráfico que no se origine en la red virtual emparejada.|No|
|AllowGatewayTransit|Permite a la red virtual emparejada usar la puerta de enlace de red virtual.|No|
|UseRemoteGateways|Se usa la puerta de enlace de la red virtual emparejada. La red virtual emparejada debe tener configurada una puerta de enlace y AllowGatewayTransit debe estar seleccionado. No se puede utilizar esta opción si tiene configurada una puerta de enlace.|No|

Cada vínculo de emparejamiento de VNET tiene varias de las propiedades anteriores. Por ejemplo, puede establecer AllowVirtualNetworkAccess en True para el vínculo de emparejamiento de VNET entre VNet1 y VNet2, y en False para el vínculo de emparejamiento de VNET de la otra dirección.


4. Para implementar el archivo de plantilla, puede ejecutar el cmdlet New-AzureRmResourceGroupDeployment para crear o actualizar la implementación. Para más información sobre cómo usar las plantillas de Resource Manager, consulte este [artículo](../resource-group-template-deploy.md).

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

> [AZURE.NOTE] Reemplace el nombre del grupo de recursos y el archivo de plantilla según corresponda.

Este es un ejemplo basado en el escenario anterior:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

La salida muestra:

        DeploymentName		: VNetPeeringVNet1
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:05:03 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

La salida muestra:

        DeploymentName		: VNetPeeringVNet2
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:07:22 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Cuando finalice la implementación, puede ejecutar el siguiente cmdlet para ver el estado de emparejamiento:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    La salida muestra:

        Name			: LinkToVNet2
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: VNet101
        VirtualNetworkName	: VNet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Después de que se establece el emparejamiento en este escenario, debería poder iniciar la conexión entre dos máquinas virtuales cualquiera de ambas redes virtuales. De forma predeterminada, AllowVirtualNetworkAccess es True y el emparejamiento de VNET aprovisionará las ACL adecuadas para permitir la comunicación entre redes virtuales. Sin embargo, puede seguir aplicando las reglas de NSG para bloquear la conectividad, por ejemplo, entre subredes o máquinas virtuales específicas para conseguir un mayor control de acceso entre dos redes virtuales. Para más información sobre la creación de reglas de NSG, consulte este [artículo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para crear el emparejamiento de VNET entre suscripciones, siga estos pasos:

1. Inicie sesión en Azure con User-A con privilegios para Subscription-A y ejecute el cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

Esto no es un requisito; se puede establecer emparejamiento incluso si los usuarios presentan individualmente solicitudes de emparejamiento de sus redes virtuales, siempre y cuando las solicitudes coincidan. Agregar usuarios con privilegios de la otra red virtual como usuarios de la red virtual local facilita la configuración.

2. Inicie sesión en Azure con User-B con privilegios para Subscription-B y ejecute el cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. A continuación, en la sesión de User-A, ejecute el cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Así es cómo se define el archivo JSON.
    
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }
   
4. A continuación, en el inicio de sesión de User-B, ejecute el cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
   
   Así es cómo se define el archivo JSON:

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }
 
 Después de que se establece el emparejamiento en este escenario, debería poder iniciar la conexión entre dos máquinas virtuales cualquiera de ambas redes virtuales entre diferentes suscripciones.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. En este escenario, puede implementar la misma plantilla que se indica a continuación para establecer el emparejamiento de VNET; en concreto, necesitará establecer la propiedad AllowForwardedTraffic en True, que permite que la aplicación virtual de red de la red virtual emparejada envíe y reciba tráfico. Aquí podemos ver la plantilla para crear el emparejamiento de VNET entre HubVNet y VNet1. Observe que AllowForwardedTraffic está establecido en False.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Esta es la plantilla para crear el emparejamiento de VNET entre VNet1 y HubVnet. Observe que AllowForwardedTraffic está establecido en False.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Después de establecer el emparejamiento, puede consultar este [artículo](virtual-network-create-udr-arm-ps.md) y establecer la ruta definida por el usuario (UDR) para redirigir el tráfico de VNet1 a través de una aplicación virtual para usar sus funcionalidades. Cuando se especifica la dirección Próximo salto en la ruta, puede establecerla en la dirección IP de la aplicación virtual de la red virtual HubVNet emparejada.

<!---HONumber=AcomDC_0803_2016-->