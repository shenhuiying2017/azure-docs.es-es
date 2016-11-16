---
title: "Creación de un emparejamiento de VNET mediante plantillas de Resource Manager | Microsoft Docs"
description: Aprenda a crear un emparejamiento de red virtual mediante las plantillas de Resource Manager.
services: virtual-network
documentationcenter: 
author: narayanannamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 75f8d10e-23e8-44bd-9972-aab74048cf38
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayanannamalai;annahar
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5af02963f139648d9f1b662f2da913ffa0d6f128


---
# <a name="create-vnet-peering-using-resource-manager-templates"></a>Creación de un emparejamiento de VNET mediante plantillas de Azure Resource Manager
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para crear un emparejamiento de VNET mediante plantillas de Resource Manager, siga estos pasos:

1. Si es la primera vez que usa Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.
   
   > [!NOTE]
   > El cmdlet de PowerShell para administrar el emparejamiento de VNET se suministra con [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
   > 
   > 
2. En el texto siguiente se muestra la definición de un vínculo del emparejamiento de VNET entre VNet1 y VNet2, en función del escenario anterior. Copie el contenido siguiente y guárdelo en un archivo denominado VNetPeeringVNet1.json.
   
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
3. La siguiente sección muestra la definición de un vínculo de emparejamiento de VNET entre VNet2 y VNet1, en función del escenario anterior.  Copie el contenido siguiente y guárdelo en un archivo denominado VNetPeeringVNet2.json.
   
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
   
    Como se ve en plantilla anterior, hay algunas propiedades configurables en el emparejamiento de VNET:
   
   | Opción | Description | Valor predeterminado |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Si el espacio de direcciones de una red virtual emparejada se incluye como parte de la etiqueta virtual_network. |Sí |
   | AllowForwardedTraffic |Si el tráfico que no parte de una red virtual emparejada se acepta o rechaza. |No |
   | AllowGatewayTransit |Permite a la red virtual emparejada usar la puerta de enlace de la red virtual. |No |
   | UseRemoteGateways |Se usa la puerta de enlace de la red virtual emparejada. La red virtual emparejada debe tener configurada una puerta de enlace y AllowGatewayTransit debe estar seleccionado. Esta opción no se puede utilizar si hay una puerta de enlace configurada. |No |
   
    Cada vínculo de emparejamiento de VNET tiene el conjunto de propiedades anteriores. Por ejemplo, puede establecer AllowVirtualNetworkAccess en True para el vínculo de emparejamiento de VNET entre VNet1 y VNet2, y en False para el vínculo de emparejamiento de VNET en la otra dirección.
4. Para implementar el archivo de plantilla, puede ejecutar el cmdlet New-AzureRmResourceGroupDeployment para crear o actualizar la implementación. Para más información acerca de cómo usar las plantillas de Resource Manager, consulte este [artículo](../resource-group-template-deploy.md).
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all
   
   > [!NOTE]
   > Reemplace el nombre del grupo de recursos y el archivo de plantilla según corresponda.
   > 
   > 
   
    Este es un ejemplo basado en el escenario anterior:
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all
   
    La salida muestra:
   
        DeploymentName        : VNetPeeringVNet1
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all
   
    La salida muestra:
   
        DeploymentName        : VNetPeeringVNet2
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
5. Cuando finalice la implementación, puede ejecutar el siguiente cmdlet para ver el estado del emparejamiento:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2
   
    La salida muestra:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : VNet101
        VirtualNetworkName    : VNet1
        ProvisioningState        : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Después de que se establezca el emparejamiento en este escenario, debe poder iniciar la conexión entre dos máquinas virtuales cualesquiera de ambas redes virtuales. De manera predeterminada, el valor de AllowVirtualNetworkAccess es True y el emparejamiento de VNET aprovisionará las ACL adecuadas para permitir la comunicación entre redes virtuales. Se pueden seguir aplicando las reglas de grupo de seguridad de red (NSG) para bloquear la conectividad entre subredes o máquinas virtuales concretas para conseguir un mayor control del acceso entre dos redes virtuales.  Para más información sobre la creación de reglas de NSG, consulte este [artículo](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para crear un emparejamiento de VNET entre suscripciones, siga estos pasos:

1. Inicie sesión en Azure con la cuenta de UserA con privilegios en la suscripción A y ejecute el siguiente cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5
   
    Esto no es un requisito; se puede establecer el emparejamiento aunque los usuarios realicen individualmente solicitudes de emparejamiento de sus respectivas redes virtuales, siempre y cuando las solicitudes coincidan. La adición de usuarios con privilegios de la otra red virtual como usuarios de la red virtual local facilita la configuración.
2. Inicie sesión en Azure con la cuenta de UserB con privilegios para la suscripción B y ejecute el siguiente cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3
3. En la sesión de UserA, ejecute este cmdlet:
   
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
4. En la sesión de UserB, ejecute el siguiente cmdlet:
   
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

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. En este escenario, se puede implementar la siguiente plantilla de ejemplo para establecer el emparejamiento de VNET.  Es preciso establecer la propiedad AllowForwardedTraffic en True, lo que permite que la aplicación virtual de red envíe y reciba tráfico.
   
    Esta es la plantilla para crear un emparejamiento de VNET entre HubVNet y VNet1. Observe que AllowForwardedTraffic está establecido en False.
   
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
2. Esta es la plantilla para crear un emparejamiento de VNET entre VNet1 y HubVnet. Observe que AllowForwardedTraffic está establecido en True.
   
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
3. Después de establecer el emparejamiento, puede consultar este [artículo](virtual-network-create-udr-arm-ps.md) para establecer rutas definidas por el usuario (UDR) para redirigir el tráfico de VNet1 a través de una aplicación virtual para usar sus funcionalidades. Cuando especifique la dirección del próximo salto en la ruta, puede establecerla en la dirección IP de la aplicación virtual del HubVNet de la red virtual emparejada.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para crear un emparejamiento entre redes virtuales de diferentes modelos de implementación, siga estos pasos:

1. En el texto siguiente se muestra la definición de un vínculo de emparejamiento de redes virtuales entre VNET1 y VNET2 en este escenario. Solo se requiere un vínculo para emparejar una red virtual clásica a una red virtual de Azure Resource Manager.
   
    Asegúrese de colocar el identificador de suscripción con la ubicación de la red virtual clásica o VNET2 y cambiar MyResouceGroup al nombre de grupo de recursos adecuado.
   
    {  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  "contentVersion": "1.0.0.0",  "parameters": {  },  "variables": {  },  "resources": [
   
        {
        "apiVersion": "2016-06-01",
        "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
        "name": "VNET1/LinkToVNET2",
        "location": "[resourceGroup().location]",
        "properties": {
        "allowVirtualNetworkAccess": true,
        "allowForwardedTraffic": false,
        "allowGatewayTransit": false,
        "useRemoteGateways": false,
            "remoteVirtualNetwork": {
            "id": "[resourceId('Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"
    }
   
        }
        }
    ]  }
2. Para implementar el archivo de plantilla, ejecute el siguiente cmdlet para crear o actualizar la implementación.
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all
   
        Output shows:
   
        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
3. Una vez finalizada la implementación correctamente, podrá ejecutar el siguiente cmdlet para ver el estado del emparejamiento:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2
   
        Output shows:
   
        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Una vez establecido el emparejamiento entre una red virtual clásica y una red virtual de Resource Manager, podrá iniciar conexiones desde cualquier máquina virtual de VNET1 a cualquier máquina virtual de VNET2 y viceversa.




<!--HONumber=Nov16_HO2-->


