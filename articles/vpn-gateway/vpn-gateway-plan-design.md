---
title: "Planeamiento y diseño de conexiones entre locales: Azure VPN Gateway | Microsoft Docs"
description: "Obtenga información acerca del planeamiento y diseño de puerta de enlace de VPN para conexiones locales, híbridas y de red virtual a red virtual"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: d5aaab83-4e74-4484-8bf0-cc465811e757
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: d2087d4a1844379ae642c9ce5b08d62edef2fccf
ms.openlocfilehash: 21723b3c8ab636f6c4a497abece59ce83a46d65f


---
# <a name="planning-and-design-for-vpn-gateway"></a>Planeamiento y diseño de puerta de enlace de VPN
El planeamiento y el diseño de las configuraciones entre locales y de red virtual a red virtual pueden ser simples o complejos, según las necesidades de la red. Este artículo le guía a través de las consideraciones de diseño y planificación básicas.

## <a name="planning"></a>Planificación
### <a name="a-namecompareacross-premises-connectivity-options"></a><a name="compare"></a>Opciones de conectividad entre locales
Si quiere conectar los sitios locales de manera a una red virtual de manera segura, existen tres maneras distintas para hacerlo: de sitio a sitio, de punto a sitio y ExpressRoute. Compare las distintas conexiones locales que se encuentran disponibles. La opción que elija depende de varios factores, como, por ejemplo:

* ¿Qué tipo de rendimiento requiere la solución?
* ¿Desea comunicarse a través de la red pública de Internet mediante una VPN segura o a través de una conexión privada?
* ¿Dispone de una dirección IP pública?
* ¿Planea usar un dispositivo VPN? Si es así, ¿es compatible?
* ¿Va a conectar solo algunos equipos o desea establecer una conexión persistente para su sitio?
* ¿Qué tipo de puerta de enlace de VPN se necesita para la solución que desea crear?
* ¿Qué SKU de puerta de enlace se debe usar?

La tabla siguiente puede ayudarle a decidir la mejor opción de conectividad para su solución.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="a-namegwrequireagateway-requirements-by-vpn-type-and-sku"></a><a name="gwrequire"></a>Requisitos de puerta de enlace por tipo de VPN y SKU
[!INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Para más información acerca de las SKU de puerta de enlace, consulte [Configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

#### <a name="aggregate-throughput-by-sku-and-vpn-type"></a>Rendimiento agregado por el tipo de VPN y SKU
[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

#### <a name="supported-configurations-by-sku-and-vpn-type"></a>Configuraciones admitidas por el tipo de VPN y SKU
[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

### <a name="a-namewfaworkflow"></a><a name="wf"></a>Flujo de trabajo
La lista siguiente describe el flujo de trabajo común para la conectividad en la nube:

1. Diseñe y planee la topología de conectividad y enumere los espacios de direcciones para todas las redes que desea conectar.
2. Cree una red virtual de Azure. 
3. Cree una puerta de enlace de VPN para la red virtual.
4. Cree y configure conexiones a redes locales o a otras redes virtuales (según sea necesario).
5. Cree y configure una conexión de punto a sitio para la puerta de enlace de VPN de Azure (según sea necesario).

## <a name="design"></a>Diseño
### <a name="a-nametopologiesaconnection-topologies"></a><a name="topologies"></a>Topologías de conexión
Para comenzar, consulte los diagramas del artículo [Acerca de Puerta de enlace de VPN](vpn-gateway-about-vpngateways.md) . El artículo contiene diagramas básicos, los modelos de implementación de cada topología (Resource Manager o clásico) y las herramientas de implementación que puede usar para implementar la configuración.   

### <a name="a-namedesignbasicsadesign-basics"></a><a name="designbasics"></a>Conceptos básicos del diseño
Las secciones siguientes analizan los aspectos básicos de la puerta de enlace de VPN. Además, considere las [limitaciones de servicios de red](../azure-subscription-service-limits.md#networking-limits).

#### <a name="a-namesubnetsaabout-subnets"></a><a name="subnets"></a>Acerca de las subredes
Al crear conexiones, debe tener en cuenta los intervalos de subred. No puede tener intervalos de direcciones de subred superpuesta. Se habla de subred superpuesta cuando una red virtual o una ubicación local contiene el mismo espacio de direcciones que la otra ubicación. Esto significa que es necesario que los ingenieros de redes de sus redes locales definan un intervalo que puede usar para las subredes o el espacio de direcciones IP de Azure. Necesita espacio de direcciones que no esté siendo usado en la red local. 

También es importante evitar las subredes superpuestas cuando se trabaja con conexiones de red virtual a red virtual. Si las subredes se superponen y si existe una dirección IP en la red virtual de envío y en la red virtual de destino, se produce un error en las conexiones de red virtual a red virtual. Azure no puede enrutar los datos a la otra red virtual porque la dirección de destino forma parte de la red virtual de envío. 

Las puertas de enlace VPN requieren una subred específica denominada subred de puerta de enlace. Para que funcionen correctamente, todas las subredes de puerta de enlace se deben llamar GatewaySubnet. Asegúrese de no asignar un nombre distinto a la subred de puerta de enlace y no implemente máquinas virtuales ni ningún otro elemento en la subred de puerta de enlace. Consulte [Subredes de la puerta de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="a-namelocalaabout-local-network-gateways"></a><a name="local"></a>Acerca de las puertas de enlace de red local
La puerta de enlace de red local suele hacer referencia a la ubicación local. En el modelo de implementación clásica, la puerta de enlace de red local se conoce como un sitio de red local. Cuando configure una puerta de enlace de red local, proporciónele un nombre, especifique la dirección IP pública en el dispositivo VPN local y especifique los prefijos de dirección que están en la ubicación local. Azure examina los prefijos de dirección de destino para el tráfico de red, consulta la configuración que especificó para la puerta de enlace de red local y enruta los paquetes según corresponda. Puede modificar estos prefijos de dirección según sea necesario. Para más información, vea [Puertas de enlace de red](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="a-namegwtypeaabout-gateway-types"></a><a name="gwtype"></a>Acerca de los tipos de puerta de enlace
Seleccionar el tipo de puerta de enlace correcto para su topología es fundamental. Si selecciona el tipo incorrecto, la puerta de enlace no funcionará correctamente. El tipo de puerta de enlace especifica cómo se conecta la puerta de enlace y es un valor de configuración necesario para el modelo de implementación de Resource Manager.

Los tipos de puerta de enlace son los siguientes:

* VPN
* ExpressRoute

#### <a name="a-nameconnectiontypeaabout-connection-types"></a><a name="connectiontype"></a>Acerca de los tipos de conexión
Cada configuración requiere un tipo de conexión específico. Los tipos de conexión son los siguientes:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="a-namevpntypeaabout-vpn-types"></a><a name="vpntype"></a>Acerca de los tipos de VPN
Cada configuración requiere un tipo específico de VPN específico. Si combina dos configuraciones, como la creación de una conexión de sitio a sitio y una conexión de punto a sitio a la misma red virtual, debe usar un tipo de VPN que cumpla ambos requisitos de conexión.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Las tablas siguientes muestran el tipo de VPN según cómo se asigna a cada configuración de conexión. Asegúrese de que el tipo de VPN correspondiente a su puerta de enlace coincide con la configuración que desea crear. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="a-namedevicesavpn-devices-for-site-to-site-connections"></a><a name="devices"></a>Dispositivos VPN para conexiones de sitio a sitio
Para configurar una conexión de sitio a sitio, independientemente del modelo de implementación, necesita los siguientes elementos:

* Un dispositivo VPN compatible con puertas de enlace de VPN de Azure.
* Una dirección IP IPv4 orientada al público que no se encuentre detrás de NAT.

Debe tener experiencia en la configuración del dispositivo VPN o pedirle a alguien que pueda configurar el dispositivo para usted. Para obtener más información sobre dispositivos VPN, consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md). El artículo sobre dispositivos VPN contiene información sobre los dispositivos validados, requisitos de los dispositivos no validados y vínculos a los documentos sobre configuración de dispositivo, si se encuentran disponibles.

### <a name="a-nameforcedtunnelaconsider-forced-tunnel-routing"></a><a name="forcedtunnel"></a>Considerar el enrutamiento de túnel forzado
La tunelización forzada se puede configurar en la mayoría de las configuraciones. La tunelización forzada permite redirigir o forzar todo el tráfico vinculado a Internet de vuelta a su ubicación local a través de un túnel VPN de sitio a sitio para inspección y auditoría. Se trata de un requisito de seguridad crítico en la mayoría de las directivas de las empresas de TI. 

Sin la tunelización forzada, el tráfico vinculado a Internet desde las máquinas virtuales en Azure siempre atravesará desde la infraestructura de red de Azure directamente a Internet, sin la opción que permite inspeccionar o auditar el tráfico. Un acceso no autorizado a Internet puede provocar la divulgación de información u otros tipos de infracciones de seguridad.

**Diagrama de tunelización forzada**

![Diagrama de tunelización forzada de Azure VPN Gateway](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

Ambos modelos de implementación permiten configurar una conexión de tunelización forzada mediante distintas herramientas. Para obtener más información, vea la tabla siguiente. Esta tabla se actualiza cada vez que hay nuevos artículos, nuevos modelos de implementación y nuevas herramientas disponibles para esta configuración. Cuando aparezca un artículo, creamos un vínculo directo a él desde la tabla.

[!INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos [Preguntas más frecuentes sobre VPN Gateway](vpn-gateway-vpn-faq.md) e [Información sobre VPN Gateway](vpn-gateway-about-vpngateways.md) para obtener más información que pueda ayudarle con el diseño.

Para obtener más información acerca de la configuración de puerta de enlace específica, vea [Acerca de la configuración de puerta de enlace de VPN](vpn-gateway-about-vpn-gateway-settings.md).




<!--HONumber=Jan17_HO4-->


