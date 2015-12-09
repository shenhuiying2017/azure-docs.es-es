<properties
   pageTitle="Creación de una red virtual con una conexión VPN sitio a sitio mediante el Administrador de recursos de Azure y PowerShell | Microsoft Azure"
   description="Este artículo te guiará a través de la creación de una red virtual mediante el modelo de Administrador de recursos y su conexión a tu red local mediante una conexión de Puerta de enlace de VPN de sitio a sitio. Incluye los pasos adicionales para modificar los prefijos de direcciones IP para sitios locales existentes."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/30/2015"
   ms.author="cherylmc"/>

# Crear una red virtual con una conexión VPN de sitio a sitio mediante PowerShell

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Este artículo te guiará por la creación de una red virtual y una conexión VPN de sitio a sitio a tu red local mediante el modelo de implementación del Administrador de recursos de Azure. Puedes seleccionar el artículo para el modelo de implementación y la herramienta de implementación usando las pestañas superiores.

>[AZURE.NOTE]Es importante que sepa que Azure actualmente funciona con dos modelos de implementación: el Administrador de recursos y el clásico. Antes de comenzar con la configuración, asegúrate de que comprendes los modelos y las herramientas de implementación. Para obtener información acerca de los modelos de implementación, vea [Modelos de implementación de Azure](../azure-classic-rm.md).

## Antes de empezar

Antes de comenzar con la configuración, comprueba que dispones de los elementos siguientes:

- Un dispositivo VPN compatible y alguien que pueda configurarlo. Consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md). Si no estás familiarizado con la configuración de tu dispositivo VPN o con los intervalos de direcciones IP, ubicados en la configuración de la red local, tendrás que trabajar con alguien que pueda proporcionar estos detalles por ti.

- Una dirección IP pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
	
- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).

- Cmdlets de Azure PowerShell (1.0 o posterior). Puede descargar e instalar esta versión desde la sección Windows PowerShell de la [Página de descargas](http://azure.microsoft.com/downloads/).
	

## 1\. su suscripción 


Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del Administrador de recursos. Para obtener más información, consulte [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

		    Login-AzureRmAccount

Compruebe las suscripciones para la cuenta.

		    Get-AzureRmSubscription 

Especifique la suscripción que desea usar.

		    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


## 2\. Creación de una red virtual y una puerta de enlace

- Si ya tiene una red virtual con una subred de puerta de enlace, puede ir directamente a **Paso 3: Incorporación del sitio local**. 
- Si ya tiene una red virtual y quiere agregar una subred de puerta de enlace a la red virtual, consulte [Incorporación de una subred de puerta de enlace a una red virtual](#gatewaysubnet).

### Para crear una red virtual y una subred de puerta de enlace

Use el ejemplo siguiente para crear una red virtual y una subred de puerta de enlace. Sustituya los valores por los suyos.

En primer lugar, crea un grupo de recursos:

	
		New-AzureRmResourceGroup -Name testrg -Location 'West US'

A continuación, cree su red virtual Compruebe que los espacios de direcciones especificados no se superponen con los espacios de direcciones que existen en la red local.

En el ejemplo siguiente se crea una red virtual llamada *testvnet* y dos subredes: *GatewaySubnet* y *Subnet1*. Es importante crear una subred denominada específicamente *GatewaySubnet*. Si la asigna otro nombre, se producirá un error en la configuración de la conexión.

		$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Para agregar una subred de puerta de enlace a una red virtual (opcional)

Este paso solo es necesario si necesitas agregar una subred de puerta de enlace a una red virtual.

Si ya tiene una red virtual existente y desea agregar una subred de puerta de enlace a ella, puede crear la subred de puerta de enlace con el ejemplo siguiente. Asegúrese de asignar el nombre 'GatewaySubnet' a la subred de puerta de enlace. Si le asignas otro nombre, podrás crear una subred, pero Azure no la verá como una subred de puerta de enlace.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Ahora, puedes establecer la configuración.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. Incorporación del sitio local

En una red virtual, el *sitio local* suele hacer referencia a la ubicación local. Asigne a ese sitio un nombre por el que Azure pueda hacer referencia a él.

Especifique también el prefijo del espacio de direcciones para el sitio local. Azure usará el prefijo de dirección IP que especifique para identificar qué tráfico enviar al sitio local. Esto significa que tendrá que especificar cada prefijo de dirección que desee asociar al sitio local. Puede actualizar fácilmente estos prefijos si cambia su red local.

Al usar los ejemplos de PowerShell, ten en cuenta lo siguiente:
	
- *GatewayIPAddress* es la dirección IP del dispositivo VPN local. El dispositivo VPN no se encuentra detrás de un NAT. 
- *AddressPrefix* es el espacio de direcciones local.

Para agregar un sitio local con un único prefijo de dirección:

		New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para agregar un sitio local con varios prefijos de dirección:

		New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Para modificar los prefijos de direcciones IP de tu sitio local

Algunas veces pueden cambiar los prefijos tu sitio local. Los pasos necesarios para modificar los prefijos de direcciones IP dependen de si creaste o no una conexión de Puerta de enlace de VPN. Consulte [Modificar los prefijos de direcciones IP de un sitio local](#to-modify-ip-address-prefixes-for-a-local-site).


## 4\. Solicitar una dirección IP pública para la puerta de enlace

A continuación, solicitará que se asigne una dirección IP pública a la puerta de enlace de VPN de la red virtual de Azure. No es la misma dirección IP que se asigna al dispositivo VPN, sino que se asigna a la puerta de enlace de VPN de Azure en sí. No puede especificar la dirección IP que desea usar; se asigna dinámicamente a la puerta de enlace. Usará esta dirección IP al configurar el dispositivo VPN local para conectarse a la puerta de enlace.

Use el siguiente ejemplo de PowerShell. El método de asignación para esta dirección debe ser dinámico.

		$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\. Creación de la configuración de direccionamiento IP de la puerta de enlace

La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo siguiente para crear la configuración de la puerta de enlace.


		$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Creación de la puerta de enlace

En este paso, creará la puerta de enlace de red virtual. Ten en cuenta que la creación de una puerta de enlace lleva un tiempo en completarse.

Use los valores siguientes:

- El tipo de puerta de enlace es *Vpn*.
- El VpnType puede ser RouteBased* (llamado puerta de enlace dinámica en algunos documentos) o *Basado en directivas* (llamado puerta de enlace estática en algunos documentos). Para obtener más información acerca de los tipos de puertas de enlace de VPN, consulte [Información acerca de las puertas de enlace de VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. Configurar el dispositivo VPN

En este momento, necesitará la dirección IP pública de la puerta de enlace de red virtual para configurar el dispositivo VPN local. Trabaje con el fabricante del dispositivo para obtener información de configuración específica. Para obtener más información, también puede consultar [Dispositivos VPN](http://go.microsoft.com/fwlink/p/?linkid=615099).

Para buscar la dirección IP pública de la puerta de enlace de red virtual, use el siguiente ejemplo:

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Creación de la conexión VPN

A continuación, creará la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN. Asegúrese de reemplazar los valores por los suyos. La clave compartida debe coincidir con el valor usado para la configuración del dispositivo VPN.

		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Pasado un momento, se establecerá la conexión.

## 9\. Comprobar una conexión VPN

En este momento, las conexiones VPN de sitio a sitio creadas con el Administrador de recursos no están visibles en el Portal de vista previa. Pero se puede comprobar que la conexión se realizó correctamente mediante *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. En el futuro, tendremos un cmdlet para esto, así como la capacidad para ver la conexión en el Portal de vista previa.

Puedes usar el siguiente ejemplo de cmdlet, configurando los valores para que coincidan con los tuyos. Cuando se le pida, seleccione *A* para ejecutar Todo.

		Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Cuando el cmdlet haya finalizado, desplázate para ver los valores. En el ejemplo siguiente, el estado de conexión aparece como *Conectado* y pueden verse los bytes de entrada y salida.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## Para modificar los prefijos de direcciones IP de un sitio local

Si tienes que cambiar los prefijos de sitio local, usa las instrucciones siguientes. Se proporcionan dos conjuntos de instrucciones y dependen de si ya creaste o no la conexión VPN de puerta de enlace.

### Agregar o quitar los prefijos sin una conexión de Puerta de enlace de VPN


- **Para agregar** prefijos de dirección adicionales a un sitio local que creó, pero que todavía no dispone de una conexión de puerta de enlace de VPN, use el ejemplo siguiente.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para quitar** un prefijo de dirección de un sitio local que no dispone de una conexión VPN, use el ejemplo siguiente. Omita los prefijos que ya no necesite. En este ejemplo, ya no necesitamos el prefijo 20.0.0.0/24 (del ejemplo anterior), por lo que se actualizará el sitio local y se excluirá ese prefijo.

		local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Agregar o quitar los prefijos con una conexión de Puerta de enlace de VPN

Si creaste la conexión VPN y quieres agregar o quitar los prefijos de dirección IP contenidos en el sitio local, tendrás que realizar los pasos siguientes en orden. Esto provocará un tiempo de inactividad en la conexión de VPN, ya que tendrás que eliminar y volver a crear la puerta de enlace. Sin embargo, como solicitaste una dirección IP para la conexión, no tendrás que volver a configurar el enrutador VPN local a menos que decidas cambiar los valores que usaste anteriormente.

 
1. Quitar la conexión de puerta de enlace. 
2. Modificar los prefijos de tu sitio local. 
3. Crear una nueva conexión de puerta de enlace. 

Puedes usar el ejemplo siguiente como guía.


		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureRmVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## Pasos siguientes

Agregue una máquina virtual a una red virtual. [Cree una máquina virtual](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_1203_2015-->