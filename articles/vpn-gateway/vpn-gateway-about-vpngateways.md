---
title: "Información general sobre VPN Gateway: creación de conexiones VPN entre locales con redes virtuales de Azure | Microsoft Docs"
description: "Esta información general sobre VPN Gateway explica las distintas formas de conectarse a redes virtuales de Azure mediante una conexión VPN a través de Internet. Se incluyen los diagramas de las configuraciones de conexión básica."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 44b393a2316825fd335bca2a1a7bb7033c10a565
ms.lasthandoff: 03/14/2017


---
# <a name="about-vpn-gateway"></a>Acerca de Puerta de enlace de VPN
Para enviar tráfico de red entre su red virtual de Azure y los sitios locales, es preciso que cree una puerta de enlace de red virtual para la red virtual. Una puerta de enlace VPN es un tipo de puerta de enlace de red virtual que envía tráfico cifrado a través de una conexión pública. También puede utilizar instancias de VPN Gateway para enviar tráfico entre las redes virtuales de Azure a través de la red de Microsoft.

Existen dos tipos de puertas de enlace de red virtual: "ExpressRoute" y "Vpn". Al crear una puerta de enlace de red virtual, debe especificar el tipo de puerta de enlace que desea crear. Una puerta de enlace VPN es una puerta de enlace de red virtual que utiliza el tipo de puerta de enlace "Vpn". 

Cada red virtual solo puede tener dos puertas de enlace de red virtual, pero solo una de cada tipo. Según la configuración que elija, puede crear varias conexiones a una única instancia de VPN Gateway. Un ejemplo de esto es una configuración de conexión multisitio. Al crear varias conexiones a la misma instancia de VPN Gateway, todos los túneles VPN, incluidos las VPN de punto a sitio, comparten el ancho de banda que está disponible para la puerta de enlace.

## <a name="configuring-a-vpn-gateway"></a>Configuración de una puerta de enlace de aplicaciones
Una conexión de puerta de enlace de VPN se basa en varios recursos con una configuración específica. La mayoría de los recursos puede configurarse por separado, aunque en algunos casos es necesario seguir un orden determinado.

###<a name="settings"></a>Configuración
La configuración que ha elegido para cada recurso es fundamental para crear una conexión correcta. Para más información sobre los recursos individuales y la configuración de VPN Gateway, consulte [Acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md). Puede encontrar información que le ayudará a entender los tipos de puerta de enlace, los tipos de VPN, los tipos de conexión, las subredes de puerta de enlace, las puertas de enlace de red local y otras configuraciones de recursos que puede tener en cuenta.

###<a name="deployment-tools"></a>Herramientas de implementación
Puede empezar a crear y configurar recursos mediante una herramienta de configuración, como el portal de Azure. Después, puede decidir cambiar a otra herramienta, como PowerShell, para configurar recursos adicionales o para modificar los existentes cuando sea aplicable. Actualmente, no se pueden configurar todos los recursos ni establecer todas las configuraciones de recurso en el portal de Azure. Las instrucciones de los artículos para cada topología de configuración indican cuándo se necesita una herramienta de configuración específica. 

###<a name="deployment-model"></a>Modelo de implementación
Cuando configure una instancia de VPN Gateway, los pasos que realice dependen del modelo de implementación que ha utilizado para crear la red virtual. Por ejemplo, si creó la red virtual con el modelo de implementación clásica, use las instrucciones y directrices del modelo de implementación clásica para crear y configurar la puerta de enlace de VPN. Para más información sobre los modelos de implementación, consulte [Descripción de los modelos de implementación clásica y de Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).



## <a name="diagrams"></a>Diagramas de la topología de conexión
Es importante saber que hay distintas configuraciones disponibles para las conexiones de VPN Gateway. Es preciso determinar qué configuración es la que mejor se adapta a sus necesidades. En las secciones siguientes, puede ver información y diagramas de topología sobre las siguientes conexiones de VPN Gateway. En las secciones siguientes se incluyen tablas que enumeran:

* Modelo de implementación disponible
* Herramientas de configuración disponibles
* Vínculos que llevan directamente a un artículo, si lo hay

Use los gráficos y las descripciones como ayuda para seleccionar la topología de conexión que mejor se ajuste a sus requisitos. Los diagramas muestran las principales topologías de referencia, pero también se pueden crear configuraciones más complejas con los diagramas como guía.


## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Sitio a sitio y multisitio (túnel VPN de IPsec/IKE)
### <a name="S2S"></a>De sitio a sitio
Una conexión de puerta de enlace de VPN de sitio a sitio (S2S) es una conexión a través de un túnel VPN IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada y que no se encuentre detrás de NAT. Se pueden utilizar conexiones S2S para las configuraciones híbridas y entre locales.   

![Ejemplo de conexión de sitio a sitio de Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Multisitio
Este tipo de conexión es una variación de la conexión de sitio a sitio. Puede crear más de una conexión VPN desde la puerta de enlace de red virtual, normalmente conectándose a varios sitios locales. Cuando trabaje con varias conexiones, debe usar una VPN de tipo RouteBased (conocida como puerta de enlace dinámica al trabajar con redes virtuales clásicas). Como cada red virtual solo puede tener una puerta de enlace de red virtual, todas las conexiones a través de la puerta de enlace comparten el ancho de banda disponible. A menudo, esto se llama conexión "multisitio".

![Ejemplo de conexión multisitio de Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modelos de implementación y métodos para conexiones de sitio a sitio y multisitio
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Punto a sitio (VPN a través de SSTP)
Una configuración de puerta de enlace de VPN de punto a sitio (P2S) permite crear una conexión segura a su red virtual desde un equipo cliente individual. P2S es una conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros). Las conexiones P2S no requieren un dispositivo VPN ni una dirección IP pública para funcionar. Para establecer la conexión VPN, iníciela desde el equipo cliente. Esta solución es útil cuando desea conectarse a la red virtual desde una ubicación remota, como desde casa o desde una conferencia, o si solo tiene unos pocos clientes que necesitan conectarse a una red virtual. Se pueden usar conexiones P2S con conexiones S2S a través de la misma instancia de VPN Gateway, siempre que todos los requisitos de configuración para ambas conexiones sean compatibles.

![Ejemplo de conexión de punto a sitio de Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-point-to-site-connection-diagram.png)

### <a name="deployment-models-and-methods-for-point-to-site"></a>Métodos y modelos de implementación disponibles para conexiones de punto a sitio
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>Conexiones de red virtual a red virtual (túnel VPN de IPsec/IKE)
La conexión de una red virtual a otra es muy parecida a la conexión de una red virtual a una ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro con IPsec/IKE. Incluso puede combinar la comunicación de red virtual a red virtual con configuraciones de conexión multisitio. Esto permite establecer topologías de red que combinen la conectividad entre entornos con la conectividad entre redes virtuales.

Las redes virtuales que conecta pueden:

* estar en la misma región o en distintas;
* pertenecer a la misma suscripción o a distintas; 
* usar el mismo modelo de implementación o dos diferentes.

![Ejemplo de conexión de red virtual a red virtual de Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

###<a name="connections-between-deployment-models"></a>Conexiones entre modelos de implementación
Actualmente, Azure tiene dos modelos de implementación: el clásico y el de Resource Manager. Si lleva un tiempo usando Azure, es probable que tenga máquinas virtuales de Azure y roles de instancia que se ejecuten en una red virtual clásica. Es posible que sus máquinas virtuales e instancias de roles más recientes se estén ejecutando en una red virtual creada en Resource Manager. Puede crear una conexión entre las redes virtuales para permitir que los recursos de una red virtual se comuniquen directamente con los recursos de otra.

###<a name="vnet-peering"></a>Emparejamiento de VNET
Es posible que pueda usar el emparejamiento de VNET para crear la conexión, siempre que la red virtual cumpla determinados requisitos. El emparejamiento de VNET no utiliza una puerta de enlace de red virtual. Para más información, consulte [Emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md).

###<a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implementación y métodos para conexiones de red virtual a red virtual
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (conexión privada dedicada)
[!INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Una conexión de ExpressRoute no utiliza una instancia de VPN Gateway, aunque lo use una puerta de enlace de red virtual como parte de su configuración obligatoria. En una conexión de ExpressRoute, se configura una puerta de enlace de red virtual con el tipo de puerta de enlace "ExpressRoute", en lugar de "Vpn". Para más información sobre ExpressRoute, vea la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Conexiones de sitio a sitio y de ExpressRoute coexistentes
ExpressRoute es una conexión dedicada directa desde la WAN (no a través de Internet) a servicios Microsoft, incluido Azure. El tráfico VPN de sitio a sitio viaja cifrado a través de la red pública de Internet. Poder configurar las conexiones VPN de sitio a sitio y ExpressRoute para la misma red virtual tiene varias ventajas.

Puede configurar una VPN de sitio a sitio como una ruta de acceso seguro de conmutación por error para ExpressRoute, o bien usar la VPN de sitio a sitio para conectarse a sitios que no forman parte de su red, pero que están conectados a través de ExpressRoute. Tenga en cuenta que esta configuración requiere dos puertas de enlace de red virtual en la misma red virtual, una con el tipo de puerta de enlace "Vpn" y otra con -el tipo de puerta de enlace "ExpressRoute".

![Ejemplo de conexiones coexistentes en ExpressRoute y VPN Gateway](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Métodos y modelos de implementación disponibles para conexiones S2S y ExpressRoute
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Precios
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>SKU de puerta de enlace
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Para más información acerca de las SKU de puerta de enlace para VPN Gateway, consulte [SKU de puerta de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Rendimiento agregado estimado por SKU
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="next-steps"></a>Pasos siguientes
- Planee la configuración de puerta de enlace de VPN. Consulte [Planeamiento y diseño de VPN Gateway](vpn-gateway-plan-design.md).
- Consulte las [Preguntas más frecuentes sobre VPN Gateway](vpn-gateway-vpn-faq.md) para más información.
- Consulte [Límites del servicio y la suscripción](../azure-subscription-service-limits.md#networking-limits).


