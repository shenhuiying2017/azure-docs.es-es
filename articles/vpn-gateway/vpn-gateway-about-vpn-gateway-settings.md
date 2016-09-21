<properties 
   pageTitle="Información acerca de la configuración de VPN Gateway para puertas de enlace de VPN| Microsoft Azure"
   description="Obtenga información sobre la configuración de Puerta de enlace de VPN para Red virtual de Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="cherylmc" />

# Acerca de la configuración de Puerta de enlace de VPN

Una solución de conexión de puerta de enlace de VPN se basa en la configuración de varios recursos para poder enviar tráfico de red entre redes virtuales y ubicaciones locales. Cada recurso contiene valores de configuración. La combinación de los recursos y la configuración determina el resultado de la conexión.

Las secciones de este artículo tratan los recursos y la configuración relacionados con una puerta de enlace de VPN. Puede resultarle útil para ver las configuraciones disponibles mediante diagramas de topología de conexión. Puede encontrar las descripciones y los diagramas de topología de cada solución de conexión en el artículo [Acerca de VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwtype"></a>Tipos de puerta de enlace

El tipo de puerta de enlace especifica cómo se conecta la puerta de enlace y es un valor de configuración necesario para el modelo de implementación de Resource Manager. Cada red virtual solo puede tener una puerta de enlace de red de cada tipo. Los valores disponibles para `-GatewayType` son:

- VPN
- ExpressRoute


Una puerta de enlace de VPN requiere el valor *Vpn* para -GatewayType, como se muestra en el siguiente ejemplo de PowerShell para el modelo de implementación de Resource Manager. Al crear una puerta de enlace de red virtual, debe asegurarse de que el tipo de puerta de enlace es el correcto para su configuración.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased
 

## <a name="gwsku"></a>SKU de puerta de enlace

Al crear una puerta de enlace de VPN, debe especificar la SKU de puerta de enlace de red virtual que desea usar. Las SKU de puerta de enlace se aplican a los tipos de puerta de enlace de VPN y ExpressRoute. Los precios difieren entre las SKU de puerta de enlace. Para obtener información acerca de los precios, consulte [Precios de puertas de enlace de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Para más información sobre ExpressRoute, consulte la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

Hay tres SKU de puerta de enlace de VPN:

- Básica
- Estándar
- HighPerformance

En el siguiente ejemplo de PowerShell se especifica `-GatewaySku` como *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased


###  <a name="aggthroughput"></a>Estimación del rendimiento agregado por tipo de SKU y tipo de puerta de enlace


En la tabla siguiente se muestran los tipos de puerta de enlace y el rendimiento agregado estimado. Esta tabla se aplica a los modelos de implementación del Administrador de recursos y clásico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]


## <a name="connectiontype"></a>Tipos de conexión

Cada configuración requiere un tipo de conexión de puerta de enlace de red virtual específico. Los valores de PowerShell de Resource Manager para `-ConnectionType` son:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

En el siguiente ejemplo de PowerShell, vamos a crear una conexión de S2S que requiere el tipo de conexión *IPsec*.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipos de VPN

Al crear la puerta de enlace de red virtual para una configuración de puerta de enlace de VPN, debe especificar un tipo de VPN. El tipo de VPN que elija dependerá de la topología de conexión que desee crear. Por ejemplo, una conexión de P2S requiere un tipo de VPN RouteBased. Un tipo de VPN también puede depender del hardware que se va a utilizar. Las configuraciones de S2S requieren un dispositivo VPN. Algunos dispositivos VPN solo serán compatibles con un determinado tipo de VPN.

El tipo de VPN que seleccione debe cumplir todos los requisitos de conexión de la solución que desea crear. Por ejemplo, si desea crear una conexión de puerta de enlace de VPN de S2S y una conexión de puerta de enlace de VPN de P2S para la misma red virtual, debería usar el tipo de VPN *RouteBased*, dado que P2S requiere un tipo de VPN RouteBased. También necesitaría comprobar que el dispositivo VPN admite una conexión de VPN RouteBased.

Una vez que se ha creado una puerta de enlace de red virtual, no puede cambiar el tipo de VPN. Tendrá que eliminar la puerta de enlace de red virtual y crear una nueva. Hay dos tipos de VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


En el siguiente ejemplo de PowerShell del modelo de implementación de Resource Manager, `-VpnType` está especificado como *RouteBased*. Al crear una puerta de enlace, debe asegurarse de que el tipo de VPN es el correcto para su configuración.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisitos de la puerta de enlace

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Subred de puerta de enlace

Para configurar una puerta de enlace de red virtual, primero deberá crear una subred de puerta de enlace para la red virtual. Para que la subred de puerta de enlace funcione correctamente, su nombre tiene que ser *GatewaySubnet*. Este nombre permite a Azure saber que esta subred se debe usar para la puerta de enlace. Si utiliza el portal clásico, la subred de puerta de enlace se denomina automáticamente *Gateway* en la interfaz del portal. Esto nombre únicamente se utiliza cuando se ve la subred de puerta de enlace en el Portal clásico. En este caso, el nombre de la subred que se crea en Azure es en realidad *GatewaySubnet* y puede verse en el Portal de Azure y en PowerShell.

El tamaño mínimo de la subred de puerta de enlace depende por completo de la configuración que desee crear. Aunque es posible crear una puerta de enlace tan pequeña como /29, le recomendamos que cree una puerta de enlace de /28 o mayor (/28, /27, /26 etc.).

Crear un tamaño mayor de la puerta de enlace evitará que se superen las limitaciones de tamaño de puerta de enlace. Por ejemplo, puede haber creado una puerta de enlace de red virtual con un tamaño de la subred de puerta de enlace /29 para una conexión S2S. Ahora quiere definir una configuración coexistente S2S/ExpressRoute. Dicha configuración requiere un tamaño mínimo de la subred de puerta de enlace /28. Para crear la configuración, tendría que modificar la subred de puerta de enlace para adaptarse al requisito mínimo para la conexión, que es /28.

En el ejemplo de PowerShell de Resource Manager siguiente, se muestra una subred de puerta de enlace con el nombre GatewaySubnet. Puede ver que la notación CIDR especifica /27, que permite suficientes direcciones IP para la mayoría de las configuraciones que existen.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Compruebe que subred de puerta de enlace no tiene un grupo de seguridad de red (NSG) aplicado, ya que esto puede causar errores en las conexiones.


## <a name="lng"></a>Puertas de enlace de red local

Al crear una solución de puerta de enlace de VPN, la puerta de enlace de red local a menudo representa la ubicación local. En el modelo de implementación clásica, la puerta de enlace de red local se conoce como un sitio local.

Debe asignar un nombre a la puerta de enlace de red local, así como la dirección IP pública del dispositivo VPN local, y especificar los prefijos de dirección que se encuentran en la ubicación local. Azure examina los prefijos de dirección de destino para el tráfico de red, consulta la configuración que especificó para la puerta de enlace de red local y enruta los paquetes según corresponda. También debe especificar puertas de enlace de red local para configuraciones de red virtual a red virtual local que usan una conexión de puerta de enlace de VPN.

En el ejemplo de PowerShell de Resource Manager siguiente, se crea una nueva puerta de enlace de red local:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

A veces es necesario modificar la configuración de la puerta de enlace de red local. Por ejemplo, al agregar o modificar el intervalo de direcciones, o si cambia la dirección IP del dispositivo VPN. Para una red virtual clásica, puede cambiar esta configuración en el portal clásico, en la página de redes locales. Para Resource Manager, consulte [Modificación de la configuración de la puerta de enlace de red local mediante PowerShell](vpn-gateway-modify-local-network-gateway.md)

## <a name="resources"></a>API de REST y cmdlets de PowerShell

Para más información sobre recursos técnicos y requisitos de sintaxis específicos al usar API de REST y cmdlets de PowerShell para configuraciones de VPN Gateway, consulte las páginas siguientes:

|**Clásico** | **Resource Manager**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API DE REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API DE REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Pasos siguientes

Consulte [Acerca de Puerta de enlace de VPN](vpn-gateway-about-vpngateways.md) para más información sobre las conexiones disponibles.







 

<!---HONumber=AcomDC_0907_2016-->