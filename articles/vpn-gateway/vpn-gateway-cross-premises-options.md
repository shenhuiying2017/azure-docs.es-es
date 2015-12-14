<properties 
   pageTitle="Acerca de la conectividad segura entre locales de redes virtuales | Microsoft Azure"
   description="Obtenga más información acerca de los tipos de conexiones seguras entre locales de redes virtuales, como las conexiones de punto a sitio, de sitio a sitio y de ExpressRoute."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/01/2015"
   ms.author="cherylmc" />

# Acerca de la conectividad segura entre locales de redes virtuales

Si quiere conectar los sitios locales de forma segura a una red virtual, existen tres opciones: [de sitio a sitio](#site-to-site-connections), [de punto a sitio](#point-to-site-connections) y [ExpressRoute](#expressroute-connections).

La opción que elija dependerá de varios factores, como, por ejemplo:


- ¿Qué tipo de rendimiento requiere la solución?
- ¿Desea comunicarse a través de la red pública de Internet mediante una VPN segura o a través de una conexión privada?
- ¿Dispone de una dirección IP pública?
- ¿Planea usar un dispositivo VPN? Si es así, ¿es compatible?
- ¿Va a conectar solo algunos equipos o desea establecer una conexión persistente para su sitio?
- ¿Qué tipo de puerta de enlace de VPN se necesita para la solución que desea crear?

La tabla siguiente puede ayudarle a decidir la mejor opción de conectividad para su solución.


| - | **De punto a sitio** | **De sitio a sitio** | **ExpressRoute** | |------------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------| | **Servicios compatibles con Azure** | Servicios en la nube y máquinas virtuales | Servicios en la nube y máquinas virtuales | [Lista de servicios](../expressroute/expressroute-faqs.md#supported-services) | | **Anchos de banda habituales** | Normalmente < 100 Mbps agregados | Normalmente < 100 Mbps agregados | 50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps | | **Protocolos admitidos** | Protocolo de túnel de sockets seguros (SSTP) | IPsec | Conexión directa a través de VLAN, tecnologías VPN de NSP (MPLS, VPLS, etc.) | | **Enrutamiento** | Basado en rutas (dinámico) | Se admite basado en directivas (enrutamiento estático) y basado en rutas (VPN de enrutamiento dinámico) | BGP | | **Resistencia de la conexión** | activa-pasiva | activa-pasiva | activa-activa | | **Caso de uso habitual** | Creación de prototipos, escenarios de desarrollo, pruebas o laboratorio para servicios en la nube y máquinas virtuales | Escenarios de desarrollo, pruebas o laboratorio y cargas de trabajo de producción de pequeña escala para servicios en la nube y máquinas virtuales | Acceso a todos los servicios de Azure (lista validada), cargas de trabajo críticas y de clase empresarial, copia de seguridad, macrodatos, Azure como un sitio DR | | **Contrato de nivel de servicio** | [Contrato de nivel de servicio](https://azure.microsoft.com/support/legal/sla/) | [Contrato de nivel de servicio](https://azure.microsoft.com/support/legal/sla/) | [Contrato de nivel de servicio](https://azure.microsoft.com/support/legal/sla/) | | **Precios** | [Precios](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [Precios](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [Precios](http://azure.microsoft.com/pricing/details/expressroute/) | | **Documentación técnica** | [Documentación de la puerta de enlace de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Documentación de la puerta de enlace de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Documentación de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) | | **Preguntas más frecuentes ** | [Preguntas más frecuentes de puerta de la enlace de VPN](vpn-gateway-vpn-faq.md) | [Preguntas más frecuentes de puerta de la enlace de VPN](vpn-gateway-vpn-faq.md) | [Preguntas más frecuentes de ExpressRoute](../expressroute/expressroute-faqs.md) |


## Conexiones de sitio a sitio

Una VPN de sitio a sitio permite crear una conexión segura entre el sitio local y la red virtual. Para crear una conexión de sitio a sitio, debe configurar un dispositivo VPN que se encuentre en la red local para crear una conexión segura con la puerta de enlace de VPN de Azure. Una vez creada la conexión, los recursos de la red local y los recursos ubicados en la red virtual pueden comunicarse de forma directa y segura. Las conexiones de sitio a sitio no exigen que se establezca una conexión independiente para cada equipo cliente en la red local para tener acceso a los recursos de la red virtual.

**Use una conexión de sitio a sitio cuando:**

- Desee crear una solución híbrida.
- Desee una conexión entre su ubicación local y la red virtual sin necesidad de configuraciones del lado cliente.
- Desee que una conexión sea persistente. 

**Requisitos**

- El dispositivo VPN local debe tener una dirección IP del tipo IPv4 accesible desde Internet. Ésta no puede estar detrás de un NAT.
- Debe tener un dispositivo VPN que sea compatible. Consulte [Acerca de los dispositivos VPN](http://go.microsoft.com/fwlink/p/?LinkID=615099). 
- El dispositivo VPN que use debe ser compatible con el tipo de puerta de enlace que se requiere para la solución. Consulte [Acerca de las puertas de enlace de VPN](vpn-gateway-about-vpngateways.md).
- La SKU de la puerta de enlace también afectará al rendimiento agregado. Consulte [SKU de puertas de enlace](vpn-gateway-about-vpngateways.md#gateway-skus) para más información. 

Para obtener información sobre cómo configurar una conexión de puerta de enlace de VPN de sitio a sitio con el Portal de Azure clásico y el modelo de implementación clásico, vea [Configurar una red virtual con una conexión VPN de sitio a sitio](vpn-gateway-site-to-site-create.md). Para obtener información sobre cómo configurar una VPN de sitio a sitio con el modelo de implementación de Administrador de recursos, vea [Crear una red virtual con una conexión VPN de sitio a sitio](vpn-gateway-create-site-to-site-rm-powershell.md).


## Conexiones de punto a sitio

Una VPN de punto a sitio también permite crear una conexión segura con la red virtual. En una configuración de punto a sitio, la conexión se configura individualmente en cada equipo cliente que desee conectar a la red virtual. Las conexiones de punto a sitio no requieren ningún dispositivo VPN. Este tipo de conexión usa un cliente VPN que debe instalar en cada equipo cliente. La VPN se establece iniciando manualmente la conexión desde el equipo cliente local.

Si bien las configuraciones de punto a sitio y de sitio a sitio pueden existir simultáneamente, las conexiones de punto a sitio no se pueden configurar al mismo tiempo que una conexión ExpressRoute en la misma red virtual.

**Use una conexión de punto a sitio cuando:**

- Solo desee configurar algunos clientes para conectarse a una red virtual.

- Desee conectarse a la red virtual desde una ubicación remota. Por ejemplo, al conectarse desde una cafetería o una sala de conferencias.

- Al disponer de una conexión de sitio a sitio, pero con algunos clientes que necesitan conectarse desde una ubicación remota.

- Al no tener acceso a un dispositivo VPN que cumpla los requisitos mínimos para una conexión de sitio a sitio.

- En caso de que no tenga acceso a una dirección IP del tipo IPv4 accesible desde Internet para el dispositivo VPN.

Para más información sobre cómo configurar una conexión de punto a sitio, consulte [Configurar una conexión VPN de punto a sitio con una red virtual](vpn-gateway-point-to-site-create.md).

## Conexiones de ExpressRoute

Azure ExpressRoute permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura de su entorno local o de un entorno de ubicación compartida. Las conexiones de ExpressRoute no se realizan a través de una conexión a Internet pública y ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales.

En algunos casos, el uso de conexiones de ExpressRoute para transferir datos entre dispositivos locales y Azure también puede aportar beneficios económicos importantes. Con ExpressRoute, se pueden establecer conexiones con Azure en una ubicación de ExpressRoute (utilidad de proveedor de intercambio) o se puede conectar directamente a Azure desde la red WAN existente (como una VPN MPLS) suministrada por un proveedor de servicios de red.

Para más información sobre ExpressRoute, consulte la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).


## Pasos siguientes

Consulte las [Preguntas más frecuentes sobre ExpressRoute](../expressroute/expressroute-faqs.md) y las [Preguntas más frecuentes de la puerta de enlace de VPN](vpn-gateway-vpn-faq.md) para más información.

<!---HONumber=AcomDC_1203_2015-->