<properties 
   pageTitle="Acerca de la conectividad segura entre locales de redes virtuales | Microsoft Azure"
   description="Obtenga más información acerca de los tipos de conexiones seguras entre locales para redes virtuales, como las conexiones de sitio a sitio, de punto a sitio y de ExpressRoute."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Acerca de la conectividad segura entre locales de redes virtuales

En este artículo se describen las distintas formas en que puede conectar el sitio local a una red virtual de Azure. Este artículo se aplica tanto al modelo de implementación del Administrador de recursos como al clásico. Si está buscando diagramas de conexión de Puerta de enlace de VPN, consulte [Topologías de conexión de la puerta de enlace de VPN de Azure](vpn-gateway-topology.md).

Hay tres opciones de conexión: de sitio a sitio, de punto a sitio y ExpressRoute. La opción que elija dependerá de varios factores, como, por ejemplo:


- ¿Qué tipo de rendimiento requiere la solución?
- ¿Desea comunicarse a través de la red pública de Internet mediante una VPN segura o a través de una conexión privada?
- ¿Dispone de una dirección IP pública?
- ¿Planea usar un dispositivo VPN? Si es así, ¿es compatible?
- ¿Va a conectar solo algunos equipos o desea establecer una conexión persistente para su sitio?
- ¿Qué tipo de puerta de enlace de VPN se necesita para la solución que desea crear?

La tabla siguiente puede ayudarle a decidir la mejor opción de conectividad para su solución.

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## Conexiones de sitio a sitio

Una VPN de sitio a sitio permite crear una conexión segura entre el sitio local y la red virtual. Para crear una conexión de sitio a sitio, debe configurar un dispositivo VPN que se encuentre en la red local para crear una conexión segura con la Puerta de enlace de VPN de Azure. Una vez creada la conexión, los recursos de la red local y los recursos ubicados en la red virtual pueden comunicarse de forma directa y segura. Las conexiones de sitio a sitio no exigen que se establezca una conexión independiente para cada equipo cliente en la red local para tener acceso a los recursos de la red virtual.

**Use una conexión de sitio a sitio cuando:**

- Desee crear una solución híbrida.
- Desee una conexión entre su ubicación local y la red virtual sin necesidad de configuraciones del lado cliente.
- Desee que una conexión sea persistente. 

**Requisitos**

- El dispositivo VPN local debe tener una dirección IP del tipo IPv4 accesible desde Internet. Ésta no puede estar detrás de un NAT.
- Debe tener un dispositivo VPN que sea compatible. Vea [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md). 
- El dispositivo VPN que use debe ser compatible con el tipo de puerta de enlace que se requiere para la solución. Consulte [Acerca de Puerta de enlace de VPN](vpn-gateway-about-vpngateways.md).
- La SKU de la puerta de enlace también afectará al rendimiento agregado. Vea [SKU de puertas de enlace](vpn-gateway-about-vpngateways.md#gwsku) para más información. 

**Métodos y modelos de implementación disponibles para S2S**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]


## Conexiones de punto a sitio

Una VPN de punto a sitio también permite crear una conexión segura con la red virtual. En una configuración de punto a sitio, la conexión se configura individualmente en cada equipo cliente que desee conectar a la red virtual. Las conexiones de punto a sitio no requieren ningún dispositivo VPN. Este tipo de conexión usa un cliente VPN que debe instalar en cada equipo cliente. La VPN se establece iniciando manualmente la conexión desde el equipo cliente local.

Las configuraciones de punto a sitio y de sitio a sitio pueden existir simultáneamente pero, a diferencia de las conexiones de sitio a sitio, las de punto a sitio no se pueden configurar al mismo tiempo que una conexión ExpressRoute en la misma red virtual.

**Use una conexión de punto a sitio cuando:**

- Solo desee configurar algunos clientes para conectarse a una red virtual.

- Desee conectarse a la red virtual desde una ubicación remota. Por ejemplo, al conectarse desde una cafetería o una sala de conferencias.

- Al disponer de una conexión de sitio a sitio, pero con algunos clientes que necesitan conectarse desde una ubicación remota.

- Al no tener acceso a un dispositivo VPN que cumpla los requisitos mínimos para una conexión de sitio a sitio.

- En caso de que no tenga acceso a una dirección IP del tipo IPv4 accesible desde Internet para el dispositivo VPN.

**Métodos y modelos de implementación disponibles para S2S**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## Conexiones de ExpressRoute

Azure ExpressRoute permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura de su entorno local o de un entorno de ubicación compartida. Las conexiones de ExpressRoute no se realizan a través de una conexión a Internet pública y ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales.

En algunos casos, el uso de conexiones de ExpressRoute para transferir datos entre dispositivos locales y Azure también puede aportar beneficios económicos importantes. Con ExpressRoute, se pueden establecer conexiones con Azure en una ubicación de ExpressRoute (utilidad de proveedor de intercambio) o se puede conectar directamente a Azure desde la red WAN existente (como una VPN MPLS) suministrada por un proveedor de servicios de red.

Para más información sobre ExpressRoute, consulte la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).


## Pasos siguientes

- Para más información acerca de Puerta de enlace de VPN, consulte [Acerca de Puerta de enlace de VPN](vpn-gateway-about-vpngateways.md), las [preguntas más frecuentes](vpn-gateway-vpn-faq.md) sobre Puerta de enlace de VPN y los artículos sobre [planeación y diseño](vpn-gateway-plan-design.md).

- Para más información sobre ExpressRoute, consulte la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md), las [preguntas más frecuentes](../expressroute/expressroute-faqs.md) y los [flujos de trabajo](../expressroute/expressroute-workflows.md).

<!---HONumber=AcomDC_0518_2016-->