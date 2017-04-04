---
title: "Configuración de conexiones de ExpressRoute y VPN de sitio a sitio coexistentes: Resource Manager: Azure | Microsoft Docs"
description: "Este artículo le guiará en la configuración de conexiones ExpressRoute y VPN de sitio a sitio que puedan coexistir en el modelo de implementación de Resource Manager."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 17f179d9bd614b5ea05bef8c4ca4173eecc6b9be
ms.lasthandoff: 03/24/2017


---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections"></a>Configuración de conexiones coexistentes de ExpressRoute de sitio a sitio
> [!div class="op_single_selector"]
> * [PowerShell: administrador de recursos](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell: clásico](expressroute-howto-coexist-classic.md)
> 
> 

Tener la posibilidad de configurar VPN de sitio a sitio y ExpressRoute tiene varias ventajas. Puede configurar una VPN de sitio a sitio como una ruta de acceso seguro de conmutación por error para ExressRoute, o bien usar VPN de sitio a sitio para conectarse a sitios que no están conectados mediante ExpressRoute. En este artículo trataremos los pasos para configurar ambos escenarios. Este artículo se aplica al modelo de implementación de Resource Manager. Esta configuración no está disponible en el Portal de Azure.

**Información acerca de los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Los circuitos ExpressRoute deben configurarse previamente antes de seguir las instrucciones siguientes. Asegúrese de que ha seguido las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y [configurar el enrutamiento](expressroute-howto-routing-arm.md) antes de seguir los pasos que se indican a continuación.
> 
> 

## <a name="limits-and-limitations"></a>Límites y limitaciones
* **No se admite el enrutamiento transitorio.** No se puede realizar un enrutamiento (a través de Azure) entre una red local conectada a través de una VPN sitio a sitio y una red local conectada a través de ExpressRoute.
* **No se admite la puerta de enlace de la SKU de nivel Básico.** Debe utilizar una puerta de enlace de la SKU que no sea de nivel Básico tanto para la [puerta de enlace de ExpressRoute](expressroute-about-virtual-network-gateways.md) como para la [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Solo se admite la VPN Gateway basada en rutas.** Debe usar una [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) basada en rutas.
* **Se debe configurar una ruta estática para VPN Gateway.** Si la red local está conectada tanto a ExpressRoute como a una VPN de sitio a sitio, debe tener configurada una ruta estática en la red local para enrutar la conexión VPN de sitio a sitio a la red pública de Internet.
* **Primero debe configurarse la puerta de enlace de ExpressRoute.** La puerta de enlace de ExpressRoute se debe crear antes de agregar la puerta de enlace de la VPN de sitio a sitio.

## <a name="configuration-designs"></a>Diseños de configuración
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configuración de una VPN de sitio a sitio como una ruta de acceso de conmutación por error para ExpressRoute
Puede configurar una conexión VPN de sitio a sitio como una copia de seguridad para ExpressRoute. Esto se aplica únicamente a las redes virtuales vinculadas a la ruta de acceso de emparejamiento privada de Azure. No hay ninguna solución de conmutación por error basada en VPN para servicios accesibles a través de emparejamientos de Microsoft y públicos de Azure. El circuito ExpressRoute siempre es el vínculo principal. Los datos fluirán a través de la ruta de acceso de VPN de sitio a sitio solo si se produce un error en el circuito ExpressRoute.

> [!NOTE]
> Si bien se prefiere el circuito ExpressRoute a la VPN de sitio a sitio cuando ambas rutas son las mismas, Azure utilizará la coincidencia de prefijo más larga para elegir la ruta hacia el destino del paquete.
> 
> 

![Coexistencia](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configuración de una VPN de sitio a sitio para conectarse a sitios no conectados mediante ExpressRoute
Puede configurar la red para sitios que se conectan directamente a Azure mediante una VPN de sitio a sitio y otros que se conectan a través de ExpressRoute. 

![Coexistencia](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> No se puede configurar una red virtual como un enrutador de tránsito.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Selección de los pasos a seguir
Hay dos conjuntos diferentes de los procedimientos entre los que elegir para configurar las conexiones que pueden coexistir. El procedimiento de configuración que seleccione dependerá de si ya tiene una red virtual existente a la que quiere conectarse o si desea crear una nueva red virtual.

* No tengo una red virtual y necesito crear una.
  
    Si aún no tiene una red virtual, este procedimiento le guiará en la creación de una nueva red virtual mediante el modelo de implementación de Resource Manager y la creación de nuevas conexiones VPN de sitio a sitio y ExpressRoute. Para configurarla, siga los pasos que se describen en la sección del artículo [Creación de una nueva red virtual y conexiones coexistentes](#new).
* Ya tengo una red virtual del modelo de implementación de Resource Manager.
  
    Puede que ya tenga una red virtual con una conexión VPN de sitio a sitio o una conexión ExpressRoute existentes. La sección [Configuración de conexiones coexistentes para una red virtual existente](#add) le guiará en la eliminación de la puerta de enlace y la creación de nuevas conexiones VPN de sitio a sitio y ExpressRoute. Tenga en cuenta que al crear las nuevas conexiones, se deben completar los pasos en un orden muy específico. No utilice las instrucciones que aparecen en otros artículos para crear puertas de enlace y conexiones.
  
    En este procedimiento, para crear conexiones que puedan coexistir, tendrá que eliminar la puerta de enlace y luego configurar nuevas puertas de enlace. Esto significa que tendrá tiempo de inactividad para las conexiones entre entornos mientras elimina y vuelve a crear la puerta de enlace y las conexiones, pero no necesitará migrar las máquinas virtuales o servicios a una nueva red virtual. Las máquinas virtuales y los servicios podrán seguir comunicándose con el exterior a través del equilibrador de carga mientras configura la puerta de enlace si están configurados para ello.

## <a name="new"></a>Creación de una nueva red virtual y conexiones coexistentes
Este procedimiento le guiará en la creación de una red virtual y conexiones de sitio a sitio y ExpressRoute que coexistirán.

1. Deberá instalar la versión más reciente de los cmdlets de Azure PowerShell. Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para más información sobre cómo instalar los cmdlets de PowerShell. Tenga en cuenta que los cmdlets que se van a utilizar en esta configuración pueden ser ligeramente diferentes de aquellos con los que podría estar familiarizado. Asegúrese de usar los cmdlets especificados en estas instrucciones.
2. Inicie sesión en su cuenta y configure el entorno.
   
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
3. Cree una red virtual, incluida la subred de la puerta de enlace. Para más información sobre la configuración de la red virtual, consulte [Creación de una red virtual usando PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md).
   
   > [!IMPORTANT]
   > La subred de puerta de enlace debe ser /27 o un prefijo más corto (por ejemplo, /26 o /25).
   > 
   > 
   
    Cree una nueva red virtual.
   
        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 
   
    Agregue subredes.
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    Guarde la configuración de red virtual.
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
4. <a name="gw"></a>Cree una puerta de enlace de ExpressRoute. Para más información sobre la configuración de la puerta de enlace de ExpressRoute, consulte [Adición de una puerta de enlace de VPN a una red virtual de Resource Manager para una configuración de ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). El valor de GatewaySKU debe ser *Standard*, *HighPerformance* o *UltraPerformance*.
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 
5. Vincule la puerta de enlace de ExpressRoute al circuito ExpressRoute. Una vez completado este paso, se ha establecido la conexión entre su red local y Azure a través de ExpressRoute. Para más información sobre la operación de vinculación, consulte [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).
   
        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
6. <a name="vpngw"></a>A continuación, cree la puerta de enlace de la VPN de sitio a sitio. Para más información sobre la configuración de VPN Gateway, consulte [Creación de una red virtual con una conexión de sitio a sitio mediante PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). El valor de GatewaySKU debe ser *Standard*, *HighPerformance* o *UltraPerformance*. El valor de VpnType debe ser *RouteBased*.
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"
   
    Azure VPN Gateway admite el BGP. Puede especificar - EnableBgp en el siguiente comando.
   
        $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -EnableBgp $true
   
    Puede buscar el IP de emparejamiento BGP y el número de AS que Azure usa para VPN Gateway en $azureVpn.BgpSettings.BgpPeeringAddress y $azureVpn.BgpSettings.Asn de BGP. Para más información, consulte [Configuración de BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) para Azure VPN Gateway.
7. Cree una entidad de puerta de enlace de VPN de sitio local. Este comando no configura la puerta de enlace de VPN local. En su lugar, permite proporcionar la configuración de puerta de enlace local, como la dirección IP pública y el espacio de direcciones local, para que la puerta de enlace de VPN de Azure pueda conectarse a ella.
   
    Si el dispositivo VPN local solo admite enrutamiento estático, las rutas estáticas se pueden configurar como se indica a continuación.
   
        $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   
    Si el dispositivo VPN local admite el BGP y desea habilitar el enrutamiento dinámico, es preciso que conozca la IP de emparejamiento BGP y el número de AS que usa el dispositivo VPN local.
   
        $localVPNPublicIP = "<Public IP>"
        $localBGPPeeringIP = "<Private IP for the BGP session>"
        $localBGPASN = "<ASN>"
        $localAddressPrefix = $localBGPPeeringIP + "/32"
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
8. Configure el dispositivo VPN local para que se conecte a la nueva puerta de enlace de VPN de Azure. Para obtener más información sobre la configuración del dispositivo VPN, vea [Configuración de dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
9. Vincule la puerta de enlace de VPN sitio a sitio en Azure a la puerta de enlace local.
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>

## <a name="add"></a>Para configurar conexiones coexistentes para una red virtual ya existente
Si ya tiene una red virtual, compruebe el tamaño de la subred de puerta de enlace. Si la subred de puerta de enlace es /28 o /29, primero debe eliminar la puerta de enlace de red virtual y aumentar el tamaño de la subred de puerta de enlace. Los pasos de esta sección le mostrarán cómo hacerlo.

Si la puerta de enlace es /27 o mayor y la red virtual está conectada a través de ExpressRoute, puede omitir los pasos siguientes y continuar con ["Paso 6: Creación una puerta de enlace VPN de sitio a sitio"](#vpngw) en la sección anterior. 

> [!NOTE]
> Cuando elimine la puerta de enlace existente, las instalaciones locales perderán la conexión a la red virtual mientras trabaja en esta configuración. 
> 
> 

1. Deberá instalar la versión más reciente de los cmdlets de Azure PowerShell. Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para más información sobre cómo instalar los cmdlets de PowerShell. Tenga en cuenta que los cmdlets que se van a utilizar en esta configuración pueden ser ligeramente diferentes de aquellos con los que podría estar familiarizado. Asegúrese de usar los cmdlets especificados en estas instrucciones. 
2. Elimine la puerta de enlace de la VPN de ExpressRoute o de sitio a sitio. 
   
        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
3. Elimine la subred de puerta de enlace.
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
4. Agregue una subred de puerta de enlace que sea /27 o superior.
   
   > [!NOTE]
   > Si no tiene suficientes direcciones IP en la red virtual para aumentar el tamaño de la subred de puerta de enlace, debe agregar más espacio de direcciones IP.
   > 
   > 
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    Guarde la configuración de red virtual.
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
5. Ya tiene una red virtual sin puertas de enlace. Para crear puertas de enlace y completar las conexiones, continúe con el [Paso 4: Creación de una puerta de enlace de ExpressRoute](#gw), del conjunto de pasos anterior.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Incorporación de la configuración de punto a sitio a la puerta de enlace de VPN
Para agregar una configuración de punto a sitio a la puerta de enlace de VPN en una configuración de coexistencia, puede seguir los pasos que se indican a continuación.

1. Agregue el grupo de direcciones de clientes de VPN. 
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
2. En Azure, cargue el certificado raíz de VPN de la puerta de enlace de VPN. En este ejemplo, se supone que el certificado raíz se almacena en el equipo local donde se ejecutan los siguientes cmdlets de PowerShell. 
   
        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Para más información sobre la VPN de punto a sitio, consulte [Configuración de una conexión punto a sitio a una red virtual mediante PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).


