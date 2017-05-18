---
title: "Conexión de la red local a una red virtual de Azure: VPN de sitio a sitio: PowerShell | Microsoft Docs"
description: "Pasos para crear una conexión de IPsec desde la red local a una red virtual de Azure a través de la red pública de Internet. Estos pasos le ayudarán a crear una conexión de VPN Gateway de sitio a sitio entre locales mediante PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: f10a6889944b1dde4f579e575389fa7bab28c51a
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Creación de una red virtual con una conexión VPN de sitio a sitio mediante PowerShell

Este artículo muestra cómo usar PowerShell para crear una conexión de puerta de enlace VPN de sitio a sitio desde la red local a la red virtual. Los pasos descritos en este artículo se aplican al modelo de implementación de Resource Manager. También se puede crear esta configuración con una herramienta o modelo de implementación distintos, mediante la selección de una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Clásico - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clásico - Portal clásico](vpn-gateway-site-to-site-create.md)
> 
>


Se utiliza una conexión de puerta de enlace VPN de sitio a sitio para conectar su red local a una red virtual de Azure a través de un túnel VPN de IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada. Para más información acerca de las puertas de enlace VPN, consulte [Acerca de VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagrama de la conexión entre locales de VPN Gateway de sitio a sitio](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-connection-diagram.png)

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con la configuración, compruebe que se cumplen los criterios siguientes:

* Compruebe que desea trabajar con el modelo de implementación de Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Un dispositivo VPN compatible y alguien que pueda configurarlo. Para más información acerca de los dispositivos VPN compatibles y su configuración, consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md).
* Una dirección IPv4 pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
* Si no está familiarizado con los intervalos de direcciones IP ubicados en la red local, necesita trabajar con alguien que pueda proporcionarle estos detalles. Al crear esta configuración, debe especificar los prefijos del intervalo de direcciones IP al que Azure enrutará la ubicación local. Ninguna de las subredes de la red local puede superponerse con las subredes de la red virtual a la que desea conectarse.
* La versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview) para más información sobre cómo instalar los cmdlets de PowerShell.

### <a name="example-values"></a>Valores de ejemplo

Los ejemplos de este artículo utilizan los valores siguientes. Puede usar estos valores para crear un entorno de prueba o hacer referencia a ellos para comprender mejor los ejemplos de este artículo.

```
#Example values

VnetName                = testvnet 
ResourceGroup           = testrg 
Location                = West US 
AddressSpace            = 10.0.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.0.1.0/28 
GatewaySubnet           = 10.0.0.0/27
LocalNetworkGatewayName = LocalSite
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24','20.0.0.0/24
Gateway Name            = vnetgw1
PublicIP                = gwpip
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = myGWConnection
```

## <a name="Login"></a>1. su suscripción

[!INCLUDE [vpn-gateway-ps-login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="VNet"></a>2. Creación de una red virtual y una puerta de enlace

Si no tiene una red virtual, créela. Al crear una red virtual, compruebe que los espacios de direcciones especificados no se superponen con los espacios de direcciones que existen en la red local. Para esta configuración, también es necesaria una subred de la puerta de enlace. La puerta de enlace de la red virtual usa una subred de la puerta de enlace que contiene las direcciones IP que usan los servicios de la puerta de enlace VPN. Cuando se crea una subred de la puerta de enlace, debe tener el nombre 'GatewaySubnet'. Si le asigna otro nombre, crea una subred, pero Azure no la tratará como subred de puerta de enlace.

El tamaño de la subred de la puerta de enlace que especifique depende de la configuración de la puerta de enlace VPN que desea crear. Aunque es posible crear una subred de puerta de enlace tan pequeña como /29, se recomienda que cree una subred mayor que incluya más direcciones seleccionando al menos /27 o /28. El uso de la subred de la puerta de enlace mayor permite suficientes direcciones IP para dar cabida a posibles configuraciones futuras.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Para crear una red virtual y una subred de puerta de enlace

Este ejemplo crea una red virtual y una subred de la puerta de enlace. Si ya tiene una red virtual que necesite agregar a una subred de puerta de enlace, consulte [Para agregar una subred de puerta de enlace a una red virtual que ya ha creado](#gatewaysubnet).

Cree un grupo de recursos:

```powershell
New-AzureRmResourceGroup -Name testrg -Location 'West US'
```

Cree la red virtual. 

1. Establezca las variables.

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
  ```
2. Cree la red virtual.

  ```powershell
  New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
  -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>Para agregar una subred de puerta de enlace a una red virtual que ya ha creado

1. Establezca las variables.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
  ```
2. Cree la subred de la puerta de enlace.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27 -VirtualNetwork $vnet
  ```
3. Establezca la configuración.

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3. <a name="localnet"></a>Creación de la puerta de enlace de red local

La puerta de enlace de red local suele hacer referencia a la ubicación local. Asigne al sitio un nombre al que Azure pueda hacer referencia y, luego, especifique la dirección IP del dispositivo VPN local con la que creará una conexión. Especifique también los prefijos de dirección IP que se enrutarán a través de la puerta de enlace VPN al dispositivo VPN. Los prefijos de dirección que especifique son los prefijos que se encuentran en la red local. Puede actualizar fácilmente estos prefijos si cambia su red local.

Use los valores siguientes:

* *GatewayIPAddress* es la dirección IP del dispositivo VPN local. El dispositivo VPN no se encuentra detrás de un NAT.
* *AddressPrefix* es el espacio de direcciones local.

- Para agregar una puerta de enlace de red local con un único prefijo de dirección:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.0.0.0/24'
  ```

- Para agregar una puerta de enlace de red local con varios prefijos de dirección:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

- Para modificar los prefijos de dirección IP de su puerta de enlace de red local:<br>
A veces los prefijos de la puerta de enlace de red local cambian. Los pasos necesarios para modificar los prefijos de las direcciones IP dependen de si creó una conexión de puerta de enlace de VPN. Consulte la sección [Para modificar los prefijos de dirección IP de una puerta de enlace de red local](#modify) de este artículo.

## <a name="PublicIP"></a>4. Solicite una dirección IP pública

Una puerta de enlace VPN debe tener una dirección IP pública. Primero se solicita el recurso de la dirección IP y, después, se hace referencia a él al crear la puerta de enlace de red virtual. La dirección IP se asigna dinámicamente al recurso cuando se crea la puerta de enlace VPN. Actualmente, VPN Gateway solo admite la asignación de direcciones IP públicas *dinámicas*. No se puede solicitar una asignación de direcciones IP públicas estáticas. Sin embargo, esto no significa que la dirección IP cambia después de que se ha asignado a una puerta de enlace VPN. La única vez que la dirección IP pública cambia es cuando la puerta de enlace se elimina y se vuelve a crear. No cambia cuando se cambia el tamaño, se restablece o se realizan actualizaciones u otras operaciones de mantenimiento interno de una puerta de enlace VPN.

Solicite una dirección IP pública que se asignará a la puerta de enlace VPN de la red virtual.

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Creación de la configuración de direccionamiento IP de la puerta de enlace

La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo siguiente para crear la configuración de la puerta de enlace:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. Creación de la puerta de enlace VPN

Cree la puerta de enlace VPN de la red virtual. Crear una puerta de enlace VPN puede tardar 45 minutos o más en completarse.

Use los valores siguientes:

* El valor *-GatewayType* para una configuración de sitio a sitio es *Vpn*. El tipo de puerta de enlace siempre es específico de la configuración que se va a implementar. Por ejemplo, otras configuraciones de puerta de enlace pueden requerir GatewayType ExpressRoute.
* El valor de *-VpnType* puede ser *RouteBased* (la llamada puerta de enlace dinámica en algunos documentos) o *PolicyBased* (la llamada puerta de enlace estática en algunos documentos). Para más información sobre los tipos de Puertas de enlace de VPN, consulte [Información acerca de VPN Gateway](vpn-gateway-about-vpngateways.md).
* *-GatewaySku* puede ser Basic, Standard, o HighPerformance. Hay limitaciones de configuración para determinadas SKU. Consulte [SKU de puertas de enlace](vpn-gateway-about-vpngateways.md#gateway-skus) para más información.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku Standard
```

## <a name="ConfigureVPNDevice"></a>7. Configurar el dispositivo VPN

Las conexiones de sitio a sitio a una red local requieren un dispositivo VPN. En este paso, se configura el dispositivo VPN. Al configurar el dispositivo VPN, necesita lo siguiente:

- Una clave compartida. Se trata de la misma clave compartida que se especifica al crear la conexión VPN de sitio a sitio. En estos ejemplos se utiliza una clave compartida básica. Se recomienda que genere y utilice una clave más compleja.
- La dirección IP pública de la puerta de enlace de red virtual. Puede ver la dirección IP pública mediante Azure Portal, PowerShell o la CLI. Para buscar la dirección IP pública de la puerta de enlace de red virtual con PowerShell, utilice el ejemplo siguiente:

  ```powershell
  Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>8. Creación de la conexión VPN

Ahora va a crear la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN. Asegúrese de reemplazar los valores por los suyos. La clave compartida debe coincidir con el valor usado para la configuración del dispositivo VPN. Tenga en cuenta que el valor de '-ConnectionType' para la configuración sitio a sitio es *IPsec*.

1. Establezca las variables.
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
  $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
  ```

2. Cree la conexión.
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName testrg `
  -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

Pasado un momento, se establecerá la conexión.

## <a name="toverify"></a>9. Comprobación de la conexión VPN

Hay varias maneras diferentes de comprobar la conexión VPN.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Conexión a una máquina virtual

[!INCLUDE [Connect to VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Modificación de los prefijos las direcciones IP de una puerta de enlace de red local

Si cambian los prefijos de las direcciones IP que desea enrutar a una ubicación local, puede modificar la puerta de enlace de red local. Se proporcionan dos conjuntos de instrucciones: Las instrucciones que elija dependen de si ya se ha creado la conexión de la puerta de enlace.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Modificación de la dirección IP de una puerta de enlace de red local

[!INCLUDE [Modify gw IP](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes

*  Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.
* Para más información acerca de BGP, consulte [Información general de BGP](vpn-gateway-bgp-overview.md) y [Configuración de BGP](vpn-gateway-bgp-resource-manager-ps.md).
