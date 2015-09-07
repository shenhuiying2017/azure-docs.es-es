<properties
   pageTitle="Creación de una red virtual con una conexión VPN sitio a sitio mediante el Administrador de recursos de Azure y PowerShell | Microsoft Azure"
	description="Creación de una conexión VPN de sitio a sitio desde la red virtual a su ubicación local mediante el Administrador de recursos de Azure y PowerShell"
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""
	tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/21/2015"
	ms.author="cherylmc"/>

# Creación de una red virtual con una conexión VPN sitio a sitio mediante el Administrador de recursos de Azure y PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


Este tema le guiará por la creación de una red virtual del Administrador de recursos de Azure y una conexión VPN de sitio a sitio a su red local.

Actualmente, Azure tiene dos modelos de implementación: el modelo de implementación clásica y el modelo de implementación Administrador de recursos de Azure. La instalación de sitio a sitio es diferente, dependiendo del modelo que se usó para implementar la red virtual. Estas instrucciones se aplican al Administrador de recursos. Si desea crear una conexión de puerta de enlace de VPN de sitio a sitio mediante el modelo de implementación clásico, consulte [Creación de una conexión VPN de sitio a sitio en el Portal de administración](vpn-gateway-site-to-site-create.md).


## Antes de empezar

Antes de empezar, compruebe que tiene lo siguiente:

- Un dispositivo VPN compatible y alguien que pueda configurarlo. Consulte [Acerca de los dispositivos VPN](vpn-gateway-vpn-devices.md).
- Una dirección IP pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
- La versión más reciente de los cmdlets de Azure PowerShell Puede descargar e instalar la versión más reciente desde la sección Windows PowerShell de la [página de descarga](http://azure.microsoft.com/downloads/). 
- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).
	

## su suscripción 


Abra la consola de PowerShell y cambie al modo Administrador de recursos de Azure. Use el siguiente ejemplo para ayudarle a conectarse:

		Add-AzureAccount

Ejecute Select-AzureSubscription para conectarse a la suscripción que desea usar.

		Select-AzureSubscription "yoursubscription"

A continuación, cambie al modo ARM. Esto cambiará el modo para que pueda usar los cmdlets de ARM.

		Switch-AzureMode -Name AzureResourceManager


## Creación de una red virtual y una puerta de enlace

- Si ya tiene una red virtual con una subred de puerta de enlace, puede avanzar a [Incorporación del sitio local](#add-your-local-site). 
- Si tiene una red virtual y desea agregar una subred de puerta de enlace a la red virtual, consulte [Incorporación de una subred de puerta de enlace a una red virtual](#gatewaysubnet).

### Para crear una red virtual y una subred de puerta de enlace

Use el ejemplo siguiente para crear una red virtual y una subred de puerta de enlace. Sustituya los valores por los suyos.

Primero, cree un grupo de recursos:

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

A continuación, cree su red virtual En el siguiente ejemplo se crea una red virtual llamada *testvnet* y dos subredes: *GatewaySubnet* y *subred1*. Es importante crear una subred denominada específicamente *GatewaySubnet*. Si la asigna otro nombre, se producirá un error en la configuración de la conexión.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2


### <a name="gatewaysubnet"></a>Para agregar una subred de puerta de enlace a una red virtual

Si ya tiene una red virtual existente y desea agregar una subred de puerta de enlace a ella, puede crear la subred de puerta de enlace con el ejemplo siguiente. Asegúrese de asignar el nombre 'GatewaySubnet' a la subred de puerta de enlace. Si la asigna otro nombre, la configuración de VPN no funcionará según lo esperado.


	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## Incorporación del sitio local

En una red virtual, el *sitio local* suele hacer referencia a la ubicación local. Asigne a ese sitio un nombre por el que Azure pueda hacer referencia a él.

Especifique también el prefijo del espacio de direcciones para el sitio local. Azure usará el prefijo de dirección IP que especifique para identificar qué tráfico enviar al sitio local. Esto significa que tendrá que especificar cada prefijo de dirección que desee asociar al sitio local. Puede actualizar fácilmente estos prefijos si cambia su red local. Use los siguientes ejemplos de PowerShell para especificar el sitio local.

	
- *GatewayIPAddress* es la dirección IP del dispositivo VPN local. El dispositivo VPN no se encuentra detrás de un NAT. 
- *AddressPrefix* es el espacio de direcciones local.

Utilice este ejemplo para agregar un sitio local con un prefijo de dirección única.

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Si desea agregar un sitio local con varios prefijos de dirección, utilice este ejemplo.

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')


Para agregar prefijos de direcciones adicionales a un sitio local que ya haya creado, utilice el siguiente ejemplo.

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


Para quitar un prefijo de dirección de un sitio local, utilice el siguiente ejemplo. Omita los prefijos que ya no necesite. En este ejemplo, ya no necesitamos el prefijo 20.0.0.0/24 (del ejemplo anterior), por lo que se actualizará el sitio local y se excluirá ese prefijo.

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')


## Solicitud de una dirección IP pública para la puerta de enlace de red virtual

A continuación, solicitará que se asigne una dirección IP pública a la puerta de enlace de VPN de la red virtual de Azure. No es la misma dirección IP que se asigna al dispositivo VPN, sino que se asigna a la puerta de enlace de VPN de Azure en sí. No puede especificar la dirección IP que desea usar; se asigna dinámicamente a la puerta de enlace. Usará esta dirección IP al configurar el dispositivo VPN local para conectarse a la puerta de enlace.

Use el siguiente ejemplo de PowerShell. El método de asignación para esta dirección debe ser dinámico.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## Creación de la configuración de direccionamiento IP de la puerta de enlace

La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo siguiente para crear la configuración de la puerta de enlace.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## Creación de la puerta de enlace

En este paso, creará la puerta de enlace de red virtual. Use los valores siguientes:

- El tipo de puerta de enlace es *Vpn*.
- VpnType puede ser RouteBased* (llamado puerta de enlace dinámica en algunos documentos) o *Basado en directivas* (llamado puerta de enlace estática en algunos documentos). Para obtener más información acerca de los tipos de puerta de enlace de VPN, consulte [Información acerca de las puertas de enlace de VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## Configurar el dispositivo VPN

En este momento, necesitará la dirección IP pública de la puerta de enlace de red virtual para configurar el dispositivo VPN local. Trabaje con el fabricante del dispositivo para obtener información de configuración específica. Adicionalmente, consulte [Dispositivos VPN](http://go.microsoft.com/fwlink/p/?linkid=615099) para obtener más información.

Para buscar la dirección IP pública de la puerta de enlace de red virtual, use el siguiente ejemplo:

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## Creación de la conexión VPN

A continuación, creará la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN. Asegúrese de reemplazar los valores por los suyos. La clave compartida debe coincidir con el valor usado para la configuración del dispositivo VPN.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Después de unos minutos, se debe establecer la conexión. En este momento, las conexiones VPN de sitio a sitio creadas con el Administrador de recursos no son visibles en el Portal.


## Pasos siguientes

Agregue una máquina virtual a una red virtual. [Creación de una máquina virtual](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=August15_HO9-->