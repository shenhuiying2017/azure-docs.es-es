---
title: Creación y administración de Azure VPN Gateway mediante PowerShell | Microsoft Docs
description: 'Tutorial: Creación y administración de Azure VPN Gateway con el módulo de Azure PowerShell'
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
ms.date: 04/24/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 867a1c55c0f96b17f77049d7f24d47f41a90d9f9
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Creación y administración de Azure VPN Gateway con el módulo de Azure PowerShell

Las puertas de enlace de VPN de Azure proporcionan conectividad entre locales entre el entorno local del cliente y Azure. En este tutorial se tratan los elementos básicos de implementación de Azure VPN Gateway, como crear y administrar una puerta de enlace de VPN. Aprenderá a:

> [!div class="checklist"]
> * Creación de una puerta de enlace de VPN
> * Cambio de tamaño de una puerta de enlace de VPN
> * Restablecimiento de una instancia de VPN Gateway

En el siguiente diagrama se muestran la red virtual y la puerta de enlace de VPN creadas como parte de este tutorial.

![VNet y VPN Gateway](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)


[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial se requiere la versión 5.3 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 

## <a name="common-network-parameter-values"></a>Valores de parámetros de red comunes

Cambie los valores siguientes, según la configuración de red y el entorno.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Primero se debe crear un grupo de recursos. En el ejemplo siguiente, se crea un grupo de recursos llamado *TestRG1* en la región *Este de EE. UU.*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Crear una red virtual

Azure VPN Gateway proporciona conectividad entre locales y funcionalidad de servidor VPN P2S para la red virtual. Agregue la puerta de enlace VPN a una red virtual existente o cree una nueva red virtual y la puerta de enlace. En este ejemplo se crea una nueva red virtual con tres subredes: Frontend, Backend y GatewaySubnet mediante [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) y [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Solicitud de una dirección IP pública para la puerta de enlace de VPN

Las puertas de enlace de VPN de Azure se comunican con los dispositivos VPN locales a través de Internet para realizar la negociación IKE (Intercambio de claves por red) y establecer los túneles IPsec. Cree y asigne una dirección IP pública a su puerta de enlace de VPN, como se muestra en el ejemplo siguiente con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) y [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> Actualmente, solo puede usar una dirección IP pública dinámica para la puerta de enlace. No se admite la dirección IP estática en puertas de enlace de VPN de Azure.

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>Creación de una puerta de enlace de VPN

Una instancia de VPN Gateway puede tardar 45 minutos o más en crearse. Una vez completada la creación de la puerta de enlace, puede crear una conexión entre su red virtual y otra red virtual. O bien, puede crear una conexión entre su red virtual y una ubicación local. Cree una instancia de VPN Gateway mediante el cmdlet [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Valores de parámetros de clave:
* GatewayType: use **Vpn** para conexiones de sitio a sitio y entre redes virtuales.
* VpnType: use **RouteBased** para interactuar con una amplia variedad de dispositivos VPN y más características de enrutamiento.
* GatewaySku: **VpnGw1** es el valor predeterminado; cámbielo por VpnGw2 o VpnGw3 si necesita mayores capacidades de proceso o más conexiones. Consulte [SKU de puertas de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwsku) para más información.

Cuando haya completado la creación de la puerta de enlace, puede crear una conexión entre su red virtual y otra red virtual, o bien crear una conexión entre la red virtual y una ubicación local. También puede configurar una conexión P2S a la red virtual desde un equipo cliente.

## <a name="resize-vpn-gateway"></a>Cambio de tamaño de la puerta de enlace de VPN

Puede cambiar la SKU de la puerta de enlace de VPN después de crear la puerta de enlace. Las distintas SKU de puerta de enlace admiten distintas especificaciones, como capacidades de proceso, número de conexiones, etc. En el ejemplo siguiente se usa [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) para cambiar el tamaño de la puerta de enlace de VpnGw1 a VpnGw2. Consulte [SKU de puertas de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwsku) para más información.

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Cambiar el tamaño de una puerta de enlace de VPN también tarda entre 30 y 45 minutos, aunque esta operación **no** interrumpe ni quita las conexiones y configuraciones existentes.

## <a name="reset-vpn-gateway"></a>Restablecimiento de la puerta de enlace de VPN

Como parte de los pasos de solución de problemas, puede restablecer la puerta de enlace de VPN de Azure para forzar a la puerta de enlace de VPN a reiniciar las configuraciones de túnel de IPsec/IKE. Use [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway) para restablecer la puerta de enlace.

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Para más información, consulte [Restablecimiento de una puerta de enlace](vpn-gateway-resetgw-classic.md).

## <a name="get-the-gateway-public-ip-address"></a>Obtención de la dirección IP pública de la puerta de enlace

Si conoce el nombre de la dirección IP pública, use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Reset-AzureRmPublicIpAddress) para mostrar la dirección IP pública asignada a la puerta de enlace.

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>Eliminación de puerta de enlace de VPN

Una configuración completa de conectividad entre entornos locales y redes virtuales requiere, además de la puerta de enlace de VPN, varios tipos de recursos. Elimine las conexiones asociadas con la puerta de enlace de VPN antes de eliminar la propia puerta de enlace. Una vez que se elimina la puerta de enlace, puede eliminar entonces las direcciones IP públicas asociadas. Para ver los pasos detallados, consulte [Eliminación de una puerta de enlace de VPN](vpn-gateway-delete-vnet-gateway-powershell.md).

Si la puerta de enlace es parte de un prototipo o prueba de concepto de implementación, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos, la puerta de enlace de VPN y todos los recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido conceptos básicos sobre la creación y administración de la puerta de enlace de VPN. Por ejemplo:

> [!div class="checklist"]
> * Creación de una puerta de enlace de VPN
> * Cambio de tamaño de una puerta de enlace de VPN
> * Restablecimiento de una instancia de VPN Gateway

Avance a los siguientes tutoriales para aprender sobre las conexiones S2S, de red virtual a red virtual y P2S.

> [!div class="nextstepaction"]
> * [Creación de conexiones S2S](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Creación de conexiones de red virtual a red virtual](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Creación de conexiones P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
