---
title: Creación y administración de conexiones VPN de sitio a sitio de Azure mediante PowerShell | Microsoft Docs
description: 'Tutorial: Creación y administración de conexiones VPN de sitio a sitio con el módulo de Azure PowerShell'
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: da077f013c558448be63dce9b215ded99362d22e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012948"
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>Creación y administración de conexiones VPN de sitio a sitio con el módulo de Azure PowerShell

Las conexiones VPN de sitio a sitio de Azure proporcionan conectividad segura entre locales entre el entorno local del cliente y Azure. Este tutorial le guía a través de los ciclos de conexión VPN de sitio a sitio de IPsec, como la creación y administración de una conexión VPN de sitio a sitio. Aprenderá a:

> [!div class="checklist"]
> * Crear una conexión VPN de sitio a sitio.
> * Actualizar la propiedad de conexión: clave precompartida, BGP, directiva IPsec/IKE.
> * Agregar más conexiones VPN.
> * Eliminar una conexión VPN.

El siguiente diagrama muestra la topología de este tutorial:

![Diagrama de conexiones VPN de sitio a sitio](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial se requiere la versión 5.3 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="requirements"></a>Requisitos

Complete el primer tutorial: "[Create and Manage VPN gateway with the Azure PowerShell module](vpn-gateway-tutorial-create-gateway-powershell.md)" (Creación de VPN Gateway con Azure PowerShell) para crear los siguientes recursos:

1. Grupo de recursos (TestRG1), red virtual (VNet1) y GatewaySubnet
2. VPN Gateway (VNet1GW)

A continuación, se muestran los valores de los parámetros de la red virtual. Tenga en cuenta los valores adicionales de la puerta de enlace de red local para representar su red local. Cambie los valores en función de la configuración de red y el entorno.

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

El flujo de trabajo para crear una conexión VPN de sitio a sitio es simple:

1. Crear una puerta de enlace de red local para representar su red local.
2. Crear una conexión entre su instancia de Azure VPN Gateway y la puerta de enlace de red local.

## <a name="create-a-local-network-gateway"></a>Creación de una puerta de enlace de red local

Una puerta de enlace de red local representa su red local. Puede especificar las propiedades de su red local en la puerta de enlace de red local, incluidos los elementos siguientes:

* Dirección IP pública del dispositivo VPN
* Espacio de direcciones local
* (Opcional) Atributos BGP (dirección IP del par BGP y número de AS)

Cree una puerta de enlace de red local con el comando [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway).

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Creación de una conexión VPN de sitio a sitio

A continuación, cree una conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN con el comando [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgatewayconnection). Observe que el valor de "-ConnectionType" para la VPN de sitio a sitio es *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Agregue la propiedad "**- EnableBGP $True**" opcional para habilitar BGP para la conexión si usa BGP. Esta opción está deshabilitada de manera predeterminada.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Actualización de la clave precompartida, BGP y la directiva IPsec/IKE de la conexión VPN

### <a name="view-and-update-your-pre-shared-key"></a>Ver y actualizar la clave precompartida

La conexión VPN de sitio a sitio de Azure usa una clave precompartida (secreto) para la autenticación entre el dispositivo VPN local y Azure VPN Gateway. Puede ver y actualizar la clave precompartida para una conexión con [Get AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/get-azurermvirtualnetworkgatewayconnectionsharedkey) y [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> La clave precompartida es una cadena de **caracteres ASCII imprimibles** de 128 caracteres de longitud como máximo.

Este comando muestra la clave precompartida para la conexión:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

La salida será "**Azure@!b2C3**" siguiendo el ejemplo anterior. Utilice el comando siguiente para cambiar el valor de la clave precompartida para "**Azure@!_b2=C3**":

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Habilitar BGP en una conexión VPN

Azure VPN Gateway admite el protocolo de enrutamiento dinámico de BGP. Puede habilitar BGP en cada conexión individual, dependiendo de si usa BGP en sus dispositivos y redes locales. Especifique las siguientes propiedades de BGP antes de habilitar BGP en la conexión:

* ASN de VPN de Azure (número de sistema autónomo)
* ASN de puerta de enlace de red local
* Dirección IP del par BGP de la puerta de enlace de red local

Si no ha configurado las propiedades de BGP, use los siguientes comandos para agregar estas propiedades a VPN Gateway y a la puerta de enlace de red local: [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgateway) y [ Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/set-azurermlocalnetworkgateway).

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Habilite BGP con [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

Para deshabilitar BGP, puede cambiar el valor de la propiedad "-EnableBGP" a **$False**. Consulte el tema sobre [BGP en instancias de Azure VPN Gateway](vpn-gateway-bgp-resource-manager-ps.md) para obtener más explicaciones de BGP sobre las instancias de Azure VPN Gateway.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Aplicar una directiva de IPsec o IKE personalizada en la conexión

Puede aplicar una directiva de IPsec o IKE opcional para especificar la combinación exacta de algoritmos criptográficos de IPsec/IKE y las principales ventajas de la conexión, en lugar de utilizar las [propuestas predeterminadas](vpn-gateway-about-vpn-devices.md#ipsec). El script de ejemplo siguiente crea una directiva de IPsec o IKE diferente con los algoritmos y parámetros siguientes:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, vigencia de SA de 14 400 segundos y 102 400 000 KB

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Consulte el tema sobre la [directiva de IPsec o IKE para conexiones VPN de sitio a sitio o de red virtual a red virtual](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obtener una lista completa de algoritmos e instrucciones.

## <a name="add-another-s2s-vpn-connection"></a>Agregar otra conexión VPN de sitio a sitio

Para agregar una conexión VPN de sitio a sitio adicional a la instancia de VPN Gateway, cree otra puerta de enlace de red local y una nueva conexión entre la nueva puerta de enlace de red local y la instancia de VPN Gateway. Para ello, siga el ejemplo de este artículo.

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Ahora hay dos conexiones VPN de sitio a sitio para Azure VPN Gateway.

![Conexiones VPN multisitio](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Eliminar una conexión VPN de sitio a sitio

Eliminar una conexión VPN de sitio a sitio con [Remove-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/remove-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Elimine la puerta de enlace de red local si ya no la necesita. No puede eliminar una puerta de enlace de red local si tiene otras conexiones asociadas.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo crear y administrar conexiones VPN de sitio a sitio. Por ejemplo:

> [!div class="checklist"]
> * Crear una conexión VPN de sitio a sitio.
> * Actualizar la propiedad de conexión: clave precompartida, BGP, directiva IPsec/IKE.
> * Agregar más conexiones VPN.
> * Eliminar una conexión VPN.

Avance a los siguientes tutoriales para aprender sobre las conexiones S2S, de red virtual a red virtual y P2S.

> [!div class="nextstepaction"]
> * [Creación de conexiones de red virtual a red virtual](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Creación de conexiones P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
