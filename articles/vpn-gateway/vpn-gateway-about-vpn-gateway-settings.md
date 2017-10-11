---
title: "Configuración de una puerta de enlace de VPN para las conexiones de Azure entre locales | Microsoft Docs"
description: "Obtenga información sobre la configuración de VPN Gateway para las puertas de enlace de red virtual de Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2017
ms.author: cherylmc
ms.openlocfilehash: 07aa6946b9c3994c5afc5c88837f23567b95d8a5
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="about-vpn-gateway-configuration-settings"></a>Acerca de la configuración de la puerta de enlace de VPN

Una puerta de enlace de VPN es un tipo de puerta de enlace de red virtual que envía tráfico cifrado entre la red virtual y la ubicación local a través de una conexión pública. También puede utilizar una puerta de enlace de VPN para enviar tráfico entre redes virtuales a través de la red troncal de Azure.

Una conexión de puerta de enlace de VPN se basa en la configuración de varios recursos, cada uno de los cuales contiene valores configurables. Las secciones de este artículo tratan los recursos y la configuración relacionados con una puerta de enlace de VPN para una red virtual creada en el modelo de implementación de Resource Manager. Puede encontrar las descripciones y los diagramas de topología de cada solución de conexión en el artículo [Acerca de VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwtype"></a>Tipos de puerta de enlace

Cada red virtual solo puede tener una puerta de enlace de red de cada tipo. Al crear una puerta de enlace de red virtual, debe asegurarse de que el tipo de puerta de enlace es el correcto para su configuración.

Los valores disponibles para -GatewayType son:

* VPN
* ExpressRoute

Una puerta de enlace de VPN requiere `-GatewayType` *Vpn*.

Ejemplo:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>SKU de puerta de enlace

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-the-gateway-sku"></a>Configuración de la SKU de puerta de enlace

#### <a name="azure-portal"></a>Portal de Azure

Si usa Azure Portal para crear una puerta de enlace de red virtual de Resource Manager, puede seleccionar la SKU de la puerta de enlace con el menú desplegable. Las opciones que se presentan corresponden con el tipo de puerta de enlace y tipo de VPN que seleccione.

#### <a name="powershell"></a>PowerShell

En el siguiente ejemplo de PowerShell se especifica `-GatewaySku` como VpnGw1.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="resize"></a>Cambio de tamaño de una SKU de puerta de enlace

Si quiere actualizar la SKU de su puerta de enlace a una SKU más eficaz, puede usar el cmdlet `Resize-AzureRmVirtualNetworkGateway` de PowerShell. También puede cambiar a una versión anterior del tamaño de la SKU de puerta de enlace con este cmdlet.

En el siguiente ejemplo de PowerShell se muestra una SKU de puerta de enlace cuyo tamaño se ha cambiado a VpnGw2.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

## <a name="connectiontype"></a>Tipos de conexión

En el modelo de implementación de Resource Manager, cada configuración requiere un tipo de conexión de puerta de enlace de red virtual específico. Los valores de PowerShell de Resource Manager para `-ConnectionType` son:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

En el siguiente ejemplo de PowerShell, vamos a crear una conexión de S2S que requiere el tipo de conexión *IPsec*.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>Tipos de VPN

Al crear la puerta de enlace de red virtual para una configuración de puerta de enlace de VPN, debe especificar un tipo de VPN. El tipo de VPN que elija dependerá de la topología de conexión que desee crear. Por ejemplo, una conexión de P2S requiere un tipo de VPN RouteBased. Un tipo de VPN también puede depender del hardware que se esté usando. Las configuraciones de S2S requieren un dispositivo VPN. Algunos dispositivos VPN solo serán compatibles con un determinado tipo de VPN.

El tipo de VPN que seleccione debe cumplir todos los requisitos de conexión de la solución que desea crear. Por ejemplo, si desea crear una conexión de puerta de enlace de VPN de S2S y una conexión de puerta de enlace de VPN de P2S para la misma red virtual, debería usar el tipo de VPN *RouteBased* , dado que P2S requiere un tipo de VPN RouteBased. También necesitaría comprobar que el dispositivo VPN admite una conexión de VPN RouteBased. 

Una vez que se ha creado una puerta de enlace de red virtual, no puede cambiar el tipo de VPN. Tendrá que eliminar la puerta de enlace de red virtual y crear una nueva. Hay dos tipos de VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

En el siguiente ejemplo de PowerShell se especifica `-VpnType` como *RouteBased*. Al crear una puerta de enlace, debe asegurarse de que el tipo de VPN es el correcto para su configuración.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Requisitos de la puerta de enlace

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Subred de puerta de enlace

Antes de crear una puerta de enlace de VPN, debe crear una subred de puerta de enlace. La subred de puerta de enlace contiene las direcciones IP que usan los servicios y las máquinas virtuales de la puerta de enlace de red virtual. Al crear la puerta de enlace de red virtual, las máquinas virtuales de puerta de enlace se implementan en la subred de puerta de enlace, y se configuran con las opciones de puerta de enlace de VPN necesarias. No debe implementar nunca nada más (por ejemplo, máquinas virtuales adicionales) en la subred de puerta de enlace. Para que la subred de puerta de enlace funcione correctamente, su nombre tiene que ser “GatewaySubnet2”. La asignación del nombre "GatewaySubnet" a la subred de puerta de enlace permite a Azure saber que se trata de la subred donde se implementarán las máquinas virtuales y los servicios de la puerta de enlace de red virtual.

Al crear la subred de puerta de enlace, especifique el número de direcciones IP que contiene la subred. Las direcciones IP de la subred de puerta de enlace se asignan a las máquinas virtuales y los servicios de puerta de enlace. Algunas configuraciones requieren más direcciones IP que otras. Consulte los requisitos de configuración que desea crear y compruebe que la subred de puerta de enlace que desea crear los cumple. Debe asegurarse también de que la subred de puerta de enlace contenga suficientes direcciones IP para acomodar posibles configuraciones adicionales. Aunque es posible crear una subred de puerta de enlace tan pequeña como /29, se recomienda que sea /28 o mayor (/28, /27, /26, etc.). De este modo, si agrega funcionalidad en el futuro, no tendrá que retirar la puerta de enlace y luego eliminar y volver a crear la subred de puerta de enlace para admitir más direcciones IP.

En el ejemplo de PowerShell de Resource Manager siguiente, se muestra una subred de puerta de enlace con el nombre GatewaySubnet. Puede ver que la notación CIDR especifica /27, que permite suficientes direcciones IP para la mayoría de las configuraciones que existen.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Puertas de enlace de red local

Al crear una configuración de puerta de enlace de VPN, la puerta de enlace de red local a menudo representa la ubicación local. En el modelo de implementación clásica, la puerta de enlace de red local se conoce como un sitio local. 

Debe asignar un nombre a la puerta de enlace de red local, así como la dirección IP pública del dispositivo VPN local, y especificar los prefijos de dirección que se encuentran en la ubicación local. Azure examina los prefijos de dirección de destino para el tráfico de red, consulta la configuración que especificó para la puerta de enlace de red local y enruta los paquetes según corresponda. También debe especificar puertas de enlace de red local para configuraciones de red virtual a red virtual local que usan una conexión de puerta de enlace de VPN.

En el ejemplo siguiente de PowerShell, se crea una nueva puerta de enlace de red local:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

A veces es necesario modificar la configuración de la puerta de enlace de red local. Por ejemplo, al agregar o modificar el intervalo de direcciones, o si cambia la dirección IP del dispositivo VPN. Para una red virtual clásica, puede cambiar esta configuración en el portal clásico, en la página de redes locales. Para Resource Manager, consulte [Modificación de la configuración de la puerta de enlace de red local mediante PowerShell](vpn-gateway-modify-local-network-gateway.md)

## <a name="resources"></a>API de REST y cmdlets de PowerShell

Para más información sobre los recursos técnicos y los requisitos de sintaxis específicos para usar las API de REST y los cmdlets de PowerShell para configuraciones de VPN Gateway, consulte las páginas siguientes:

| **Clásico** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/azure#networking) |[PowerShell](/powershell/module/azurerm.network#vpn) |
| [API DE REST](https://msdn.microsoft.com/library/jj154113) |[API de REST](/rest/api/network/virtualnetworkgateways) |
| No compatible | [CLI de Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las configuraciones de conexión disponibles, vea [About VPN Gateway (Acerca de VPN Gateway)](vpn-gateway-about-vpngateways.md).