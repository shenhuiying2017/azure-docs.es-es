<properties 
   pageTitle="Acerca la configuración de Puerta de enlace de VPN | Microsoft Azure"
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
   ms.date="08/17/2016"
   ms.author="cherylmc" />

# Acerca de la configuración de Puerta de enlace de VPN

Puerta de enlace de VPN es una colección de ajustes que se utilizan para enviar tráfico de red entre redes virtuales y ubicaciones locales. También puede utilizar Puerta de enlace de VPN para enviar el tráfico entre las redes virtuales de Azure. Las secciones en este artículo tratan los ajustes relacionados con Puerta de enlace de VPN.

A veces resulta útil para ver las configuraciones disponibles mediante diagramas de conexión. Puede encontrar diagramas de implementación de cada configuración en la sección sobre las conexiones en el artículo [Acerca de Puerta de enlace de VPN](vpn-gateway-topology.md).


## <a name="gwsku"></a>SKU de puerta de enlace

Cuando se crea una instancia de Puerta de enlace de VPN, debe especificar la SKU de puerta de enlace que desea usar. Las SKU de puerta de enlace se aplican a los tipos de puerta de enlace de VPN y ExpressRoute. Los precios difieren entre las SKU de puerta de enlace. Para obtener información acerca de los precios, consulte [Precios de puertas de enlace de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Para más información sobre ExpressRoute, consulte la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

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

## <a name="gwtype"></a>Tipos de puerta de enlace

El tipo de puerta de enlace especifica cómo se conecta la puerta de enlace y es un valor de configuración necesario para el modelo de implementación de Resource Manager. No confunda el tipo de puerta de enlace con el tipo de VPN, que especifica el tipo de enrutamiento de la VPN. Los valores disponibles para `-GatewayType` son:

- VPN
- ExpressRoute


En este ejemplo de PowerShell para el modelo de implementación de Resource Manager, -GatewayType está especificado como *Vpn*. Al crear una puerta de enlace, debe asegurarse de que el tipo de puerta de enlace es el correcto para su configuración.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased

## <a name="connectiontype"></a>Tipos de conexión

Cada configuración requiere un tipo de conexión específico. Los valores de PowerShell de Resource Manager para `-ConnectionType` son:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

En el siguiente ejemplo de PowerShell, vamos a crear una conexión de S2S que requiere el tipo de conexión "IPsec".

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipos de VPN

Cada configuración requiere un tipo específico de VPN para funcionar. Al crear la configuración, seleccione el tipo de VPN que se requiere para la conexión.

Por ejemplo, si desea conectarse a una red virtual tanto de sitio a sitio (S2S) como de punto a sitio (P2S), debe utilizar un tipo de VPN que cumpla los requisitos de conexión para las dos configuraciones. Las conexiones P2S requieren un tipo de VPN basado en enrutamiento (puerta de enlace de enrutamiento dinámico), mientras que las conexiones S2S a veces admiten un tipo de VPN basado en directivas. Esto significa si ya tiene una conexión S2S que utiliza un tipo de VPN basado en directivas (enrutamiento estático), para admitir la conexión P2S, debe volver a crear la puerta de enlace con el tipo de VPN basado en directivas (enrutamiento dinámico).

Hay dos tipos de VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]



En este ejemplo de PowerShell del modelo de implementación de Resource Manager, `-VpnType` está especificado como *RouteBased*. Al crear una puerta de enlace, debe asegurarse de que el tipo de VPN es el correcto para su configuración.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisitos de la puerta de enlace

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Subred de puerta de enlace

Para configurar una puerta de enlace de VPN, primero deberá crear una subred de puerta de enlace para la red virtual. Para que la subred de puerta de enlace funcione correctamente, su nombre tiene que ser *GatewaySubnet*. Con este nombre, Azure sabe que esta subred debe usarse como puerta de enlace.<BR>En la interfaz del Portal de Azure clásico, a la subred de puerta de enlace se le asigna automáticamente el nombre *Gateway*. Esto nombre únicamente se utiliza cuando se ve la subred de puerta de enlace en el Portal clásico. En este caso, el nombre de la subred que se crea en Azure es en realidad *GatewaySubnet* y puede verse en el Portal de Azure y en PowerShell.

El tamaño mínimo de la subred de puerta de enlace depende por completo de la configuración que desee crear. Aunque es posible crear una puerta de enlace tan pequeña como /29, le recomendamos que cree una puerta de enlace de /28 o mayor (/28, /27, /26 etc.).

Crear un tamaño mayor de la puerta de enlace evitará que se superen las limitaciones de tamaño de puerta de enlace. Por ejemplo, puede haber creado una puerta de enlace de red virtual con un tamaño de la subred de puerta de enlace /29 para una conexión S2S. Ahora quiere definir una configuración coexistente S2S/ExpressRoute. Dicha configuración requiere un tamaño mínimo de la subred de puerta de enlace /28. Para crear la configuración, tendría que modificar la subred de puerta de enlace para adaptarse al requisito mínimo para la conexión, que es /28.

En el ejemplo siguiente se muestra una subred de puerta de enlace con el nombre GatewaySubnet. Puede ver que la notación CIDR especifica /27, que permite suficientes direcciones IP para la mayoría de las configuraciones que existen.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Compruebe que subred de puerta de enlace no tiene un grupo de seguridad de red (NSG) aplicado, ya que esto puede causar errores en las conexiones.


## <a name="lng"></a>Puertas de enlace de red local

La puerta de enlace de red local suele hacer referencia a la ubicación local. En el modelo de implementación clásica, la puerta de enlace de red local se conoce como un sitio local. Debe asignar un nombre a la puerta de enlace de red local, así como la dirección IP pública del dispositivo VPN local, y especificar los prefijos de dirección que se encuentran en la ubicación local. Azure examina los prefijos de dirección de destino para el tráfico de red, consulta la configuración que especificó para la puerta de enlace de red local y enruta los paquetes según corresponda.

A veces es necesario modificar la configuración de la puerta de enlace de red local. Por ejemplo, al agregar o modificar el intervalo de direcciones, o si cambia la dirección IP del dispositivo VPN. Para una red virtual clásica, puede cambiar esta configuración en el portal clásico, en la página de redes locales. Para Resource Manager, consulte [Modificación de la configuración de la puerta de enlace de red local mediante PowerShell](vpn-gateway-modify-local-network-gateway.md)

## <a name="resources"></a>API de REST y PowerShell

Para más información sobre recursos técnicos y requisitos de sintaxis específicos al usar la API de REST y PowerShell, consulte las páginas siguientes:

|**Clásico** | **Resource Manager**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API DE REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API DE REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Pasos siguientes

Consulte las [conexiones de Puerta de enlace de VPN](vpn-gateway-topology.md) para más información sobre las conexiones disponibles.







 

<!---HONumber=AcomDC_0824_2016-->