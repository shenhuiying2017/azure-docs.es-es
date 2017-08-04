---
title: "Configuración de dispositivos VPN de terceros para su conexión con puertas de enlace de VPN de Azure | Microsoft Docs"
description: "Este artículo proporciona información general sobre configuraciones de dispositivos VPN de terceros para conectarse a puertas de enlace de VPN de Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 72dab85bb882b05d72cef26bef70437695b70416
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="overview-of-3rd-party-vpn-device-configurations"></a>Información general sobre configuraciones de dispositivos VPN de terceros
Este artículo proporciona información general sobre configuraciones de dispositivos VPN locales para conectarse a puertas de enlace de VPN de Azure. La red virtual de Azure de ejemplo y la configuración de la puerta de enlace de VPN se usarán para conectarse a distintos dispositivos VPN locales con los mismos parámetros.

## <a name="device-requirements"></a>Requisitos del dispositivo
Las puertas de enlace de VPN de Azure usan los conjuntos de protocolos IPsec o IKE estándar para túneles VPN S2S. Consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obtener los parámetros detallados del protocolo IPsec o IKE y los algoritmos criptográficos predeterminados para las puertas de enlace de VPN de Azure. También puede especificar la combinación exacta de algoritmos criptográficos y niveles de clave para una conexión específica, como se describe en [Acerca de los requisitos criptográficos](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Túnel VPN único
Esta primera topología consta de un solo túnel VPN S2S entre una puerta de enlace de VPN de Azure y el dispositivo VPN local. Opcionalmente puede configurar BGP a través del túnel VPN.

![túnel único](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Consulte [Configurar una conexión de sitio a sitio](vpn-gateway-howto-site-to-site-resource-manager-portal.md) para obtener instrucciones detalladas paso a paso. Las secciones siguientes indican los parámetros y proporcionan un script de PowerShell de ejemplo para ayudarle a empezar a trabajar.

### <a name="network-and-vpn-gateway-information"></a>Información de puerta de enlace de VPN y de red
En esta sección se muestran los parámetros de este ejemplo.

| **Parámetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefijos de dirección de red virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| Dirección IP de la puerta de enlace de VPN de Azure         | Dirección IP de la puerta de enlace de VPN de Azure         |
| Prefijos de direcciones locales | 10.51.0.0/16<br>10.52.0.0/16 |
| Dirección IP del dispositivo VPN local    | Dirección IP del dispositivo VPN local    |
| *ASN de BGP de red virtual                | 65010                        |
| *Dirección IP del par BGP de Azure           | 10.12.255.30                 |
| *ASN de BGP local         | 65050                        |
| *Dirección IP del par BGP local     | 10.52.255.254                |

* (*) Parámetros opcionales solo para BGP

### <a name="sample-powershell-script"></a>Script de PowerShell de ejemplo
El artículo [Creación de una conexión VPN de S2S mediante PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) tiene instrucciones detalladas. En esta sección se proporciona un script de ejemplo para ayudarle a comenzar.

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>[Opcional] Usar directivas personalizadas de IPsec/IKE con "UsePolicyBasedTrafficSelectors"
Si los dispositivos VPN no admiten selectores de tráfico universales (configuración basada en enrutamiento y en VTI), debe crear una directiva personalizada de IPsec/IKE y configurar la opción "UsePolicyBasedTrafficSelectors" como se describe en [este artículo](vpn-gateway-connect-multiple-policybased-rm-ps.md).

> [!IMPORTANT]
> Debe crear una directiva IPsec/IKE para habilitar la opción "UsePolicyBasedTrafficSelectors" en la conexión.

El script de ejemplo siguiente crea una directiva IPsec/IKE con los algoritmos y parámetros siguientes:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, vigencia de SA 7200 segundos y 20480000 KB (20 GB)

A continuación, se aplica la directiva y permite "UesPolicyBasedTrafficSelectors" en la conexión.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>[Opcional] Usar BGP en una conexión VPN S2S
También puede usar BGP en la conexión. Consulte [BGP para puerta de enlace de VPN](vpn-gateway-bgp-resource-manager-ps.md). Hay dos diferencias:

Los prefijos de dirección local pueden ser una dirección de host único, la dirección IP del par BGP local:

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

Debe establecer "-EnableBGP" en $True al crear la conexión:

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

## <a name="next-steps"></a>Pasos siguientes
Consulte [Configuración activa-activa de puertas de enlace de VPN para conexiones entre locales y de red virtual a red virtual](vpn-gateway-activeactive-rm-powershell.md) para ver los pasos para configurar de modo activo-activo conexiones entre locales y de red virtual a red virtual.


