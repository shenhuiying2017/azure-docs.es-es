---
title: "Eliminación de una puerta de enlace de red virtual: PowerShell y Azure Resource Manager | Microsoft Docs"
description: "Elimine una puerta de enlace de red virtual mediante PowerShell en el modelo de implementación de Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3032b09d06a103f9cd915e3803355199243488f9
ms.lasthandoff: 03/21/2017


---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Eliminación de una puerta de enlace de red virtual mediante PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clásico: PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Hay un par de enfoques que puede adoptar cuando desee eliminar una puerta de enlace de red virtual correspondiente a una configuración de puerta de enlace VPN.

- Si quiere eliminar todo el contenido y volver a empezar, como en el caso de un entorno de prueba, puede suprimir un grupo de recursos completo. Cuando se elimina un grupo de recursos, se quitan todos los recursos de él. Esto solamente se recomienda si no desea conservar los recursos del grupo de recursos. Con este enfoque no podrá eliminar de forma selectiva solo unos pocos recursos.

- Si desea mantener algunos de los recursos en el grupo de recursos, será algo más complicado eliminar una puerta de enlace de red virtual. Antes de poder eliminar la puerta de enlace de red virtual, primero debe quitar todos los recursos que dependen de la puerta de enlace. Los pasos que seguirá dependerán del tipo de conexiones que creó y de los recursos dependientes de cada conexión.

##<a name="before-beginning"></a>Antes de comenzar

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Descargue la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager.
Descargue e instale la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para obtener más información sobre cómo instalar y configurar los cmdlets de PowerShell.

### <a name="2-connect-to-your-azure-account"></a>2. Conéctese a su cuenta de Azure. 
Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para conectarse:

    Login-AzureRmAccount

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

Si tiene varias suscripciones, seleccione la que quera usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

##<a name="S2S"></a>Eliminación de una VPN de sitio a sitio

Para eliminar una puerta de enlace de red virtual correspondiente a una configuración de S2S, primero debe eliminar cada recurso que pertenece a la puerta de enlace de red virtual. Los recursos deben eliminarse en un orden determinado debido a las dependencias. Cuando se trabaja con los ejemplos siguientes, deben proporcionarse expresamente algunos de los valores, mientras que otros son un resultado de salida. Los siguientes valores específicos de los ejemplos se utilizan para fines de demostración:

Nombre de la red virtual: VNet1<br>
Nombre del grupo de recursos: RG1<br>
Nombre de la puerta de enlace de red virtual: GW1<br>

Los siguientes pasos se aplican al modelo de implementación de Resource Manager.

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenga la puerta de enlace de red virtual que quiera eliminar.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Compruebe si la puerta de enlace de red virtual tiene todas las conexiones.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
    $Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}

###<a name="3-delete-all-connections"></a>3. Elimine todas las conexiones.
Se le pedirá que confirme la eliminación de cada una de las conexiones.

    $Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="4-get-the-list-of-the-corresponding-local-network-gateways"></a>4. Obtenga la lista de las puertas de enlace de red locales correspondientes.

    $LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
    
###<a name="5-delete-the-local-network-gateways"></a>5. Elimine las puertas de enlace de red locales.
Se le pedirá que confirme la eliminación de cada una de las puertas de enlace de red locales.

    $LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}

###<a name="6-delete-the-virtual-network-gateway"></a>6. Elimine la puerta de enlace de red virtual.
Se le pedirá que confirme la eliminación de la puerta de enlace.

>[!NOTE]
> Si tiene una configuración P2S en esta red virtual además de la configuración S2S, eliminando la puerta de enlace de red virtual se desconectarán automáticamente todos los clientes P2S sin previo aviso.
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="7-get-the-ip-configurations-of-the-virtual-network-gateway"></a>7. Obtenga las configuraciones de direcciones IP de la puerta de enlace de red virtual.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="8-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>8. Obtenga la lista de direcciones IP públicas que se usan para esta puerta de enlace de red virtual. 
Si la puerta de enlace de red virtual era activa-activa, verá dos direcciones IP públicas.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="9-delete-the-public-ips"></a>9. Elimine las direcciones IP públicas.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}

###<a name="10-delete-the-gateway-subnet-and-set-the-configuration"></a>10. Elimine la subred de puerta de enlace y establezca la configuración.

    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="v2v"></a>Eliminación de una puerta de enlace VPN de red virtual a red virtual

Para eliminar una puerta de enlace de red virtual correspondiente a una configuración de V2V, primero debe eliminar cada recurso que pertenece a la puerta de enlace de red virtual. Los recursos deben eliminarse en un orden determinado debido a las dependencias. Cuando se trabaja con los ejemplos siguientes, deben proporcionarse expresamente algunos de los valores, mientras que otros son un resultado de salida. Los siguientes valores específicos de los ejemplos se utilizan para fines de demostración:

Nombre de la red virtual: VNet1<br>
Nombre del grupo de recursos: RG1<br>
Nombre de la puerta de enlace de red virtual: GW1<br>

Los siguientes pasos se aplican al modelo de implementación de Resource Manager.

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenga la puerta de enlace de red virtual que quiera eliminar.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Compruebe si la puerta de enlace de red virtual tiene todas las conexiones.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
Puede haber otra conexión a la puerta de enlace de red virtual que forman parte de otro grupo de recursos. Busque conexiones adicionales en cada grupo de recursos extra. En este ejemplo, vamos a comprobar las conexiones desde RG2. Ejecute este comando en cada grupo de recursos que tenga que puede estar conectado a la puerta de enlace de red virtual.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="3-get-the-list-of-connections-in-both-directions"></a>3. Obtenga la lista de conexiones en ambas direcciones.
Como se trata de una configuración de red virtual a red virtual, necesita la lista de conexiones en ambas direcciones.

    $ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
En este ejemplo, vamos a comprobar las conexiones desde RG2. Ejecute este comando en cada grupo de recursos que tenga que puede estar conectado a la puerta de enlace de red virtual.
 
    $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="4-delete-all-connections"></a>4. Elimine todas las conexiones.
Se le pedirá que confirme la eliminación de cada una de las conexiones.

    $ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
    $ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="5-delete-the-virtual-network-gateway"></a>5. Elimine la puerta de enlace de red virtual.
Se le pedirá que confirme la eliminación de la puerta de enlace de red virtual.

>[!NOTE]
> Si tiene una configuración P2S en esta red virtual además de la configuración V2V, eliminando la puerta de enlace de red virtual se desconectarán automáticamente todos los clientes P2S sin previo aviso.
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="6-get-the-ip-configurations-of-the-virtual-network-gateway"></a>6. Obtenga las configuraciones de direcciones IP de la puerta de enlace de red virtual.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="7-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>7. Obtenga la lista de direcciones IP públicas que se usan para esta puerta de enlace de red virtual. 
Si la puerta de enlace de red virtual era activa-activa, verá dos direcciones IP públicas.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="8-delete-the-public-ips"></a>8. Elimine las direcciones IP públicas.
Se le pedirá que confirme la eliminación de la dirección IP pública.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="9-delete-the-gateway-subnet-and-set-the-configuration"></a>9. Elimine la subred de puerta de enlace y establezca la configuración.
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="deletep2s"></a>Eliminación de una puerta de enlace VPN de sitio a sitio

Para eliminar una puerta de enlace de red virtual correspondiente a una configuración de P2S, primero debe eliminar cada recurso que pertenece a la puerta de enlace de red virtual. Los recursos deben eliminarse en un orden determinado debido a las dependencias. Cuando se trabaja con los ejemplos siguientes, deben proporcionarse expresamente algunos de los valores, mientras que otros son un resultado de salida. Los siguientes valores específicos de los ejemplos se utilizan para fines de demostración:

Nombre de la red virtual: VNet1<br>
Nombre del grupo de recursos: RG1<br>
Nombre de la puerta de enlace de red virtual: GW1<br>

Los siguientes pasos se aplican al modelo de implementación de Resource Manager.


>[!NOTE]
> Cuando se elimina la puerta de enlace VPN, se desconectarán todos los clientes conectados de la red virtual sin previo aviso.
>
>

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenga la puerta de enlace de red virtual que quiera eliminar.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-delete-the-virtual-network-gateway"></a>2. Elimine la puerta de enlace de red virtual.
Se le pedirá que confirme la eliminación de la puerta de enlace de red virtual.

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="3-get-the-ip-configurations-of-the-virtual-network-gateway"></a>3. Obtenga las configuraciones de direcciones IP de la puerta de enlace de red virtual.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="4-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>4. Obtenga la lista de direcciones IP públicas que se usan para esta puerta de enlace de red virtual. 
Si la puerta de enlace de red virtual era activa-activa, verá dos direcciones IP públicas.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="5-delete-the-public-ips"></a>5. Elimine las direcciones IP públicas.
Se le pedirá que confirme la eliminación de la dirección IP pública.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="6-delete-the-gateway-subnet-and-set-the-configuration"></a>6. Elimine la subred de puerta de enlace y establezca la configuración.
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="delete"></a>Eliminación de una puerta de enlace VPN mediante la eliminación del grupo de recursos

Si no le preocupa mantener cualquiera de los recursos y desea empezar de nuevo, puede eliminar uno entero. Se trata de una forma rápida de quitarlos todos. Cuando se elimina un grupo de recursos entero, no se puede decidir qué recursos específicos eliminar. Por tanto, asegúrese de que es esto lo que quiere hacer antes de ejecutar el ejemplo.

Los siguientes pasos se aplican al modelo de implementación de Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Obtenga una lista de todos los grupos de recursos de la suscripción:

    Get-AzureRmResourceGroup
### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Localice el grupo de recursos que desea eliminar.
Localice el grupo de recursos que quiera eliminar y vea la lista de recursos de dicho grupo. En el ejemplo, el nombre del grupo de recursos es RG 1. Modifique el ejemplo para recuperar una lista de todos los recursos.

    Find-AzureRmResource -ResourceGroupNameContains RG1

### <a name="3-verify-the-resources-in-the-list"></a>3. Compruebe los recursos de la lista.
Cuando se devuelve la lista, revísela para comprobar que desea eliminar todos los recursos del grupo de recursos, así como dicho grupo. 

### <a name="4-delete-the-resource-group-and-resources"></a>4. Elimine el grupo de recursos y los recursos.
Para eliminar el grupo de recursos y todos los recursos de este, modifique el ejemplo y ejecútelo.

    Remove-AzureRmResourceGroup -Name RG1

### <a name="5-check-the-status"></a>5. Compruebe el estado.
Azure tarda unos minutos en eliminar todos los recursos. Puede comprobar el estado de su grupo de recursos con este cmdlet.

    Get-AzureRmResourceGroup -ResourceGroupName RG1

El resultado que se devuelve se muestra "Correcto".

    ResourceGroupName : RG1
    Location          : eastus
    ProvisioningState : Succeeded


