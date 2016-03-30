<properties 
   pageTitle="Acerca de las puertas de enlace de VPN para la conectividad entre entornos locales en redes virtuales | Microsoft Azure"
   description="Obtenga información acerca de las puertas de enlace de VPN, que se pueden usar en conexiones locales de sitio a sitio para configuraciones híbridas, en conexiones entre redes virtuales y en conexiones de punto a sitio."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# Información acerca de las puertas de enlace de VPN

Las puertas de enlace de VPN, también llamadas puertas de enlace de red virtual de Azure, se usan para enviar tráfico de red entre las redes virtuales y las ubicaciones locales. También se usan para enviar el tráfico entre varias redes virtuales dentro de Azure (red virtual a red virtual). Las secciones siguientes describen los elementos relacionados con una puerta de enlace de VPN.

Las instrucciones que se usan para crear la puerta de enlace de VPN dependerán del modelo de implementación usado para crear la red virtual. Por ejemplo, si la red virtual se creó con el modelo de implementación clásica, usará las instrucciones y directrices del modelo de implementación clásica para crear y configurar la puerta de enlace de VPN. No se puede crear una puerta de enlace de VPN de Resource Manager para una red virtual del modelo de implementación clásica.

Vea [Descripción de los modelos de implementación clásica y de Resource Manager](../resource-manager-deployment-model.md) para más información sobre los modelos de implementación.


## <a name="gwsub"></a>Subred de puerta de enlace

Para configurar una puerta de enlace de VPN, primero deberá crear una subred de puerta de enlace para la red virtual. Para que funcionen correctamente, todas las subredes de puerta de enlace se deben llamar GatewaySubnet.

El tamaño mínimo de la subred de puerta de enlace depende por completo de la configuración que desee crear. Aunque es posible crear una puerta de enlace tan pequeña como /29 para algunas configuraciones, le recomendamos que cree una puerta de enlace de /28 o mayor (/28, /27, /26, etc.).

Crear un tamaño mayor de la puerta de enlace evitará que se superen las limitaciones de tamaño de puerta de enlace. Por ejemplo, si creó una puerta de enlace con un tamaño de subred de puerta de enlace /29 y desea definir configuraciones coexistentes de sitio-a-sitio o ExpressRoute, tendrá que eliminar la puerta de enlace, eliminar la subred de puerta de enlace, crear la puerta de enlace como /28 o mayor y, después, volver a crear la puerta de enlace.

Si crea una puerta de enlace de un tamaño mayor desde el principio, ahorrará tiempo más adelante cuando agregue nuevas características de configuración a su entorno de red.

El ejemplo siguiente muestra una subred de puerta de enlace con el nombre GatewaySubnet. Puede ver que la notación CIDR especifica /27, lo que permite suficientes direcciones IP para la mayoría de las configuraciones que existen en este momento.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27


## <a name="gwtype"></a>Tipos de puerta de enlace

El tipo de puerta de enlace especifica cómo se conecta la puerta de enlace y es un valor de configuración necesario para el modelo de implementación de Resource Manager. No confunda el tipo de puerta de enlace con el tipo de VPN, que especifica el tipo de enrutamiento de la VPN. Los valores disponibles de GatewayType son: *Vpn* y *ExpressRoute*.


Este ejemplo del modelo de implementación de Resource Manager especifica -GatewayType como *Vpn*. Al crear una puerta de enlace, debe asegurarse de que el tipo de puerta de enlace es el correcto para su configuración.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>SKU de puerta de enlace

Cuando se crea una puerta de enlace de VPN, debe especificar la SKU de puerta de enlace que desea usar. Hay tres SKU de puerta de enlace de VPN:

- Básica
- Standard
- HighPerformance

El ejemplo siguiente especifica GatewaySku como *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

### Rendimiento agregado estimado por tipo de SKU y de puerta de enlace


La tabla siguiente muestra los tipos de puerta de enlace y el rendimiento agregado estimado. Los precios difieren entre las SKU de puerta de enlace. Para obtener información acerca de los precios, consulte [Precios de puertas de enlace de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Esta tabla se aplica a los modelos de implementación del Administrador de recursos y clásico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="vpntype"></a>Tipos de VPN

Cada configuración requiere un tipo específico de VPN para funcionar. Si combina dos configuraciones, como la creación de una conexión de sitio a sitio y una conexión de punto a sitio a la misma red virtual, debe usar un tipo de VPN que cumpla ambos requisitos de conexión. En el caso de las conexiones de punto a sitio y de sitio a sitio coexistentes, debe usar un tipo de VPN basado en la ruta cuando se trabaja con el modelo de implementación de Resource Manager, o una puerta de enlace dinámica si trabaja con el modo de implementación clásica.

Al crear la configuración, seleccione el tipo de VPN que se requiere para la conexión.

Hay dos tipos de VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Este ejemplo del modelo de implementación de Resource Manager especifica -VpnType como *RouteBased*. Al crear una puerta de enlace, debe asegurarse de que el tipo de VPN es el correcto para su configuración.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## <a name="lng"></a>Puertas de enlace de red local

La puerta de enlace de red local suele hacer referencia a la ubicación local. En el modelo de implementación clásica, la puerta de enlace de red local se conoce como un sitio local. Debe asignar un nombre a la puerta de enlace de red local, así como la dirección IP pública del dispositivo VPN local, y especificar los prefijos de dirección que se encuentran en la ubicación local. Azure examinará los prefijos de dirección de destino para el tráfico de red, consultará la configuración que especificó para la puerta de enlace de red local, y enrutará los paquetes según corresponda. Puede modificar estos prefijos de dirección según sea necesario.



### Modificación de los prefijos de dirección: Resource Manager

El procedimiento para modificar los prefijos de dirección varía según si ya creó la puerta de enlace de VPN. Consulte la sección del artículo [Modificar prefijos de dirección para una puerta de enlace de red local](vpn-gateway-create-site-to-site-rm-powershell.md#modify).

En el ejemplo siguiente verá cómo se especifica una puerta de enlace de red local llamada MyOnPremiseWest, que contiene dos prefijos de dirección IP.

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### Modificar prefijos de dirección: implementación clásica

Si necesita modificar los sitios locales al usar el modelo de implementación clásica, en este momento puede usar la página de configuración Redes locales en el portal clásico, o modificar directamente el archivo de configuración de red NETCFG.XML.


## Dispositivos VPN

Debe asegurarse de que el dispositivo VPN que planea usar admite el tipo de VPN necesario para la configuración. Para más información acerca de los dispositivos VPN compatibles, consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md).

## Requisitos de las puertas de enlace


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## Pasos siguientes

Consulte el artículo [Preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md) para más información antes de continuar con la planeación y el diseño de la configuración.





 

<!---HONumber=AcomDC_0323_2016-->