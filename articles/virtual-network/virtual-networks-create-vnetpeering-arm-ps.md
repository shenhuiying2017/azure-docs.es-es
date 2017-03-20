---
title: 'Emparejamiento de Azure Virtual Network: PowerShell | Microsoft Docs'
description: Aprenda a crear un emparejamiento de red virtual mediante Azure PowerShell.
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
translationtype: Human Translation
ms.sourcegitcommit: 3fcd6583b415cea6b2151651297c55c93e59c796
ms.openlocfilehash: b0375a99f5ea3d6af2d3ead382f9a43f1fd285f0
ms.lasthandoff: 02/22/2017


---
# <a name="create-a-virtual-network-peering-using-azure-powershell"></a>Creación de un emparejamiento de red virtual mediante Azure PowerShell
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para el emparejamiento de VNet mediante PowerShell, siga estos pasos:

1. Si es la primera vez que usa Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.

    > [!NOTE]
    > El cmdlet de PowerShell para administrar el emparejamiento de VNET se suministra con [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

2. Leer objetos de redes virtuales:

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    ```

3. Para establecer el emparejamiento de VNET es preciso crear dos vínculos, uno para cada dirección. En el siguiente paso, primero se creará un vínculo del emparejamiento de VNET de VNet1 a VNet2:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
    ```

    Salida que se devuelve:
        
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
            UseRemoteGateways    : False
            RemoteGateways        : null
            RemoteVirtualNetworkAddressSpace : null

4. En este paso se creará un vínculo del emparejamiento de VNET de VNet2 a VNet1:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
    ```

    Salida que se devuelve:

        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
5. Una vez creado el vínculo del emparejamiento de VNET, escriba el siguiente comando para ver el estado del mismo:

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
    ```

    Salida que se devuelve:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Hay algunas propiedades configurables para el emparejamiento de VNet:
   
   | Opción | Description | Valor predeterminado |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Si el espacio de direcciones de red virtual emparejada se incluye como parte de la etiqueta Virtual_network. |Sí |
   | AllowForwardedTraffic |Si el tráfico que no parte de una red virtual emparejada se acepta o rechaza. |No |
   | AllowGatewayTransit |Permite a la red virtual emparejada usar la puerta de enlace de red virtual. |No |
   | UseRemoteGateways |Se usa la puerta de enlace de la red virtual emparejada. La red virtual emparejada debe tener configurada una puerta de enlace y AllowGatewayTransit debe estar seleccionado. No se puede utilizar esta opción si tiene configurada una puerta de enlace. |No |

    Cada vínculo de un emparejamiento de VNET tiene el conjunto anterior de propiedades. Por ejemplo, puede establecer `AllowVirtualNetworkAccess` en *True* para el vínculo de emparejamiento de VNET entre VNet1 y VNet2, y en *False* para el vínculo de emparejamiento de VNET en la otra dirección.

    ```powershell
    $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 `
    -ResourceGroupName vnet101 -Name LinkToVNet2

    $LinktoVNet2.AllowForwardedTraffic = $true
    Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
    ```

    Puede ejecutar `Get-AzureRmVirtualNetworkPeering` para volver a comprobar el valor de la propiedad después del cambio. En la salida, puede ver que `AllowForwardedTraffic` cambia a *True* después de ejecutar los cmdlets anteriores.

        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null

    Una vez que se establece el emparejamiento, las máquinas virtuales deben poder comunicarse entre ellas en ambas redes virtuales. De manera predeterminada, el valor de `AllowVirtualNetworkAccess` es *True* y el emparejamiento de VNET aprovisionará las ACL adecuadas para permitir la comunicación entre redes virtuales. Se pueden seguir aplicando las reglas de grupo de seguridad de red (NSG) para bloquear la conectividad entre subredes o máquinas virtuales concretas para conseguir un mayor control del acceso entre dos redes virtuales. Consulte el artículo sobre [grupos de seguridad de red](virtual-networks-create-nsg-arm-ps.md) para obtener más información sobre estos grupos.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para crear un emparejamiento de VNET entre suscripciones mediante PowerShell, siga estos pasos:

1. Inicie sesión en Azure con la cuenta de UserA con privilegios para la suscripción A y ejecute el siguiente cmdlet:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
    ```

    Esto no es un requisito. El emparejamiento se puede establecer aunque los usuarios realicen individualmente solicitudes de emparejamiento de sus respectivas redes virtuales, siempre y cuando las solicitudes coincidan. La adición de usuarios con privilegios de la otra red virtual como usuarios de la red virtual local facilita la configuración.
2. Inicie sesión en Azure con la cuenta de UserB con privilegios para la suscripción B y ejecute el siguiente cmdlet:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
    ```

    > [!IMPORTANT]
    > Si el emparejamiento que va a crear es entre dos redes virtuales que se crearon mediante el modelo de implementación de Azure Resource Manager, continúe con los pasos restantes de esta sección. Si las dos redes virtuales se crearon mediante diferentes modelos de implementación, omita los pasos restantes de esta sección y siga los pasos de la sección [Emparejamiento redes virtuales creadas mediante diferentes modelos de implementación](#x-model) de este artículo.

3. En la sesión de inicio de UserA, ejecute el siguiente comando:

    ```powershell
    $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
    ```

4. En la sesión de inicio de UserB, ejecute el cmdlet siguiente:

    ```powershell
    $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
    ```

5. Después de que se establezca el emparejamiento, todas las máquinas virtuales de VNet3 deberían poder comunicarse con cualquier máquina virtual de VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. En este escenario, ejecute los siguientes cmdlets de PowerShell siguientes para establecer el emparejamiento de VNET. Es preciso establecer la propiedad `AllowForwardedTraffic` en *True* y vincular VNET1 con HubVNet, lo que permite el que entre tráfico de fuera del espacio de direcciones de red virtual de emparejamiento.

    ```powershell
    $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

    Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
    ```

2. Después de establecer el emparejamiento, puede consultar este [artículo](virtual-network-create-udr-arm-ps.md) y establecer la ruta definida por el usuario (UDR) para redirigir el tráfico de VNet1 a través de una aplicación virtual para usar sus funcionalidades. Cuando especifique la dirección del próximo salto en la ruta, puede establecerla en la dirección IP de la aplicación virtual del HubVNet de la red virtual emparejada. A continuación se muestra un ejemplo:

    ```powershell
    $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

    $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1
    ```

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Si va a crear un emparejamiento entre redes virtuales implementadas a través de diferentes modelos de la *misma* suscripción, vaya al paso 2. La capacidad para crear un emparejamiento de VNET entre redes virtuales implementadas a través de distintos modelos de implementación de suscripciones *diferentes* está en **versión preliminar**. Las funcionalidades que están en versión preliminar no tienen el mismo nivel de confiabilidad y el acuerdo de nivel de servicio que las funcionalidades generales de la versión. Si va a crear un emparejamiento entre redes virtuales implementadas a través de distintos modelos de suscripciones diferentes, primero debe completar las siguientes tareas:
    - Registre la funcionalidad de versión preliminar en su suscripción de Azure, para lo que debe escribir el siguiente comando de PowerShell: `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` y `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network`
    - Complete los pasos 1 y 2 de la sección [Emparejamiento entre suscripciones](#x-sub) de este artículo.
2. Lea el objeto de red virtual de **VNET1**, la red virtual de Azure Resource Manager, para lo que debe escribir el siguiente comando:

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    ```

3. Para establecer el emparejamiento de redes virtuales en este escenario, se necesita solo un vínculo y, más concretamente, un vínculo desde **VNET1** a **VNET2**. Este paso requiere conocer el identificador de recurso de la red virtual clásica. El formato del identificador del grupo de recursos se parece al del siguiente ejemplo:

           subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Asegúrese de reemplazar SubscriptionID, ResourceGroupName y VirtualNetworkName por los nombres correspondientes.

    Para ello, escriba el siguiente comando:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
    ```

4. Una vez creado el vínculo de emparejamiento de VNET, puede ver el estado del vínculo, como se muestra en la siguiente salida:
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-a-vnet-peering"></a>Eliminación de un emparejamiento de VNET
1. Para eliminar un emparejamiento de VNET, es preciso ejecutar el siguiente cmdlet:

    ```powershell
    Remove-AzureRmVirtualNetworkPeering
    ```

    Para eliminar los dos vínculos, escriba los siguientes comandos:

    ```powershell
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    ```

2. Una vez que se quita un vínculo de un emparejamiento de VNET, el estado del vínculo del emparejamiento cambia a *Desconectado*. En este estado, el vínculo no se puede volver a crear hasta que cambie el estado del vínculo de emparejamiento a *Iniciado*. Se recomienda quitar ambos vínculos antes de volver a crear el emparejamiento de VNET.


