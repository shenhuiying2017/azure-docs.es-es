<properties
   pageTitle="Creación de una red virtual con una conexión VPN de sitio a sitio mediante Azure Resource Manager y PowerShell | Microsoft Azure"
   description="Este artículo le guiará a través de la creación de una red virtual mediante el modelo de implementación de Resource Manager y su conexión a la red local mediante una conexión de Puerta de enlace de VPN S2S."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# Creación de una red virtual con una conexión de sitio a sitio mediante PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portal de Azure clásico](vpn-gateway-site-to-site-create.md)
- [PowerShell: administrador de recursos](vpn-gateway-create-site-to-site-rm-powershell.md)

Este artículo lo guiará por la creación de una red virtual y una conexión VPN de sitio a sitio en una red local mediante el **modelo de implementación de Azure Resource Manager**. Se pueden utilizar conexiones de sitio a sitio para las configuraciones híbridas y entre locales.

![Diagrama de sitio a sitio](./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "De sitio a sitio")


### Modelos de implementación y herramientas para las conexiones de sitio a sitio

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Si desea conectar las redes virtuales entre sí pero no está creando una conexión a una ubicación local, consulte [Configurar una conexión de red virtual a red virtual en el Portal de Azure clásico](vpn-gateway-vnet-vnet-rm-ps.md).


## Antes de empezar

Antes de comenzar con la configuración, comprueba que dispones de los elementos siguientes:

- Un dispositivo VPN compatible y alguien que pueda configurarlo. Consulte [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](vpn-gateway-about-vpn-devices.md). Si no conoce la configuración de su dispositivo VPN o los intervalos de direcciones IP ubicados en la configuración de la red local, necesita trabajar con alguien que pueda proporcionarle estos detalles.

- Una dirección IP pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
	
- Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratis](https://azure.microsoft.com/pricing/free-trial/).
	
- La versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar los cmdlets de PowerShell.


## 1\. su suscripción 

Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del Administrador de recursos. Para obtener más información, consulte [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

	Login-AzureRmAccount

Compruebe las suscripciones para la cuenta.

	Get-AzureRmSubscription 

Especifique la suscripción que desea usar.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2\. Creación de una red virtual y una puerta de enlace

Los ejemplos usan una subred de puerta de enlace de /28. Aunque es posible crear una subred de puerta de enlace tan pequeña como /29, no es recomendable. Se recomienda crear una subred de puerta de enlace /27 o mayor (/ 26, /25, etc.) con el fin de adaptarse a los requisitos de características adicionales.

Si ya tiene una red virtual con una subred de puerta de enlace /29 o mayor, puede ir directamente a [Incorporación de la puerta de enlace de red local](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### Para crear una red virtual y una subred de puerta de enlace

Use el ejemplo siguiente para crear una red virtual y una subred de puerta de enlace. Sustituya los valores por los suyos.

En primer lugar, crea un grupo de recursos:
	
	New-AzureRmResourceGroup -Name testrg -Location 'West US'

A continuación, cree su red virtual Compruebe que los espacios de direcciones especificados no se superponen con los espacios de direcciones que existen en la red local.

En el ejemplo siguiente se crea una red virtual llamada *testvnet* y dos subredes: *GatewaySubnet* y *Subnet1*. Es importante crear una subred denominada específicamente *GatewaySubnet*. Si la asigna otro nombre, se producirá un error en la configuración de la conexión.

Establezca las variables.

	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
	$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Cree la red virtual.

	New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
	-Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Para agregar una subred de puerta de enlace a una red virtual que ya ha creado

Este paso solo es necesario si necesita agregar una subred de puerta de enlace a una red virtual creada anteriormente.

Puede crear la subred de puerta de enlace mediante el ejemplo siguiente. Asegúrese de asignar el nombre 'GatewaySubnet' a la subred de puerta de enlace. Si le asigna otro nombre, crea una subred, pero Azure no la tratará como subred de puerta de enlace.

Establezca las variables.

	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Cree la subred de la puerta de enlace.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Establezca la configuración.

	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. <a name="localnet"></a>Incorporación de la puerta de enlace de red local

En una red virtual, la puerta de enlace de red local suele hacer referencia a la ubicación local. Asigne a ese sitio un nombre que sirva de referencia a Azure y especifique también el prefijo de espacio de direcciones para la puerta de enlace de la red local.

Azure usa el prefijo de dirección IP que especifique para identificar qué tráfico enviar a la ubicación local. Esto significa que tiene que especificar cada prefijo de dirección que desee asociar a la puerta de enlace de red local. Puede actualizar fácilmente estos prefijos si cambia su red local.

Al usar los ejemplos de PowerShell, ten en cuenta lo siguiente:
	
- *GatewayIPAddress* es la dirección IP del dispositivo VPN local. El dispositivo VPN no se encuentra detrás de un NAT.
- *AddressPrefix* es el espacio de direcciones local.

Para agregar una puerta de enlace de red local con un único prefijo de dirección:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para agregar una puerta de enlace de red local con varios prefijos de dirección:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Para modificar los prefijos de dirección IP de su puerta de enlace de red local

A veces los prefijos de la puerta de enlace de red local cambian. Los pasos necesarios para modificar los prefijos de las direcciones IP dependen de si creó una conexión de puerta de enlace de VPN. Consulte la sección [Para modificar los prefijos de dirección IP de una puerta de enlace de red local](#modify) de este artículo.


## 4\. Solicitud de una dirección IP pública para la puerta de enlace de VPN

A continuación, solicite que se asigne una dirección IP pública a la puerta de enlace de VPN de la red virtual de Azure. No se trata de la misma dirección IP que se asigna al dispositivo VPN, sino que se asigna a la misma puerta de enlace de VPN de Azure. No puede especificar la dirección IP que desea usar. Se asigna dinámicamente a la puerta de enlace. Use esta dirección IP al configurar el dispositivo VPN local para conectarlo a la puerta de enlace.

La puerta de enlace de VPN para el modelo de implementación de Administrador de recursos solo admite de momento direcciones IP públicas mediante el método de asignación dinámica. Sin embargo, esto no significa que la dirección IP pueda cambiar. La única vez que cambia la dirección IP de Puerta de enlace de VPN de Azure es cuando se elimina y se vuelve a crear la puerta de enlace. La dirección IP pública de la puerta de enlace seguirá siendo la misma aunque se cambie el tamaño de la puerta de enlace de VPN de Azure, se restablezca o se lleven a cabo actualizaciones o mantenimiento interno en ella.

Utilice el siguiente ejemplo de PowerShell.

	$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\. Creación de la configuración de direccionamiento IP de la puerta de enlace

La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo siguiente para crear la configuración de la puerta de enlace.

	$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Creación de la puerta de enlace de red virtual

En este paso, va a crear la puerta de enlace de red virtual. La creación de una puerta de enlace puede tardar bastante tiempo en completarse. A menudo, 45 minutos o más.

Use los valores siguientes:

- El valor *-GatewayType* para una configuración de sitio a sitio es *Vpn*. El tipo de puerta de enlace siempre es específico de la configuración que se va a implementar. Por ejemplo, otras configuraciones de puerta de enlace pueden requerir GatewayType ExpressRoute.

- El valor de *-VpnType* puede ser *RouteBased* (la llamada puerta de enlace dinámica en algunos documentos) o *PolicyBased* (la llamada puerta de enlace estática en algunos documentos). Para obtener más información sobre los tipos de Puertas de enlace de VPN, consulte [Información acerca las puertas de enlace de VPN](vpn-gateway-about-vpngateways.md#vpntype).
- *- GatewaySku* puede ser *Basic*, *Standard* o *HighPerformance*.

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
		-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
		-VpnType RouteBased -GatewaySku Standard

## 7\. Configurar el dispositivo VPN

En este momento, necesita la dirección IP pública de la puerta de enlace de red virtual para configurar el dispositivo VPN local. Trabaje con el fabricante del dispositivo para obtener información de configuración específica. Para obtener más información, también puede consultar [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](vpn-gateway-about-vpn-devices.md).

Para buscar la dirección IP pública de la puerta de enlace de red virtual, use el siguiente ejemplo:

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Creación de la conexión VPN

Ahora va a crear la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN. Asegúrese de reemplazar los valores por los suyos. La clave compartida debe coincidir con el valor usado para la configuración del dispositivo VPN. Tenga en cuenta que `-ConnectionType` para sitio a sitio es *IPsec*.

Establezca las variables.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Cree la conexión.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Pasado un momento, se establecerá la conexión.

## <a name="toverify"></a>Para comprobar una conexión VPN

Hay varias maneras diferentes de comprobar la conexión VPN.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Para modificar los prefijos de dirección IP de una puerta de enlace de red local

Si tiene que cambiar los prefijos de la puerta de enlace de red local, siga estas instrucciones. Se proporcionan dos conjuntos de instrucciones: Las instrucciones que elija dependen de si ya se ha creado la conexión de la puerta de enlace.

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Para modificar la dirección IP de una puerta de enlace de red local

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## Pasos siguientes

- Puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para ver los pasos.

- Para más información acerca de BGP, consulte [Información general de BGP](vpn-gateway-bgp-overview.md) y [Configuración de BGP](vpn-gateway-bgp-resource-manager-ps.md).

<!---HONumber=AcomDC_0831_2016-->