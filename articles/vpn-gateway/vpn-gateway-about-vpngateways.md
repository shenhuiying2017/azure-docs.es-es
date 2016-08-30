<properties 
   pageTitle="Acerca de Puerta de enlace de VPN | Microsoft Azure"
   description="Obtenga información acerca de las conexiones de Puerta de enlace de VPN para redes virtuales de Azure."
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
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# Acerca de Puerta de enlace de VPN


Puerta de enlace de VPN es una colección de ajustes que se utilizan para enviar tráfico de red entre redes virtuales y ubicaciones locales. Puerta de enlace de VPN se utiliza para conexiones de sitio a sitio, de punto a sitio y ExpressRoute. También se usa para enviar el tráfico entre varias redes virtuales dentro de Azure (red virtual a red virtual).

Cada red virtual tiene una única puerta de enlace de red virtual. Para crear una conexión, agregue la puerta de enlace de red virtual a una red virtual y configure opciones adicionales para Puerta de enlace de VPN. En algunos casos, la conexión que se crea es una conexión VPN. En otros, la configuración no requiere una VPN. La colección de configuraciones se llama "Puerta de enlace de VPN" independientemente de si necesita o no una VPN para la conexión.

Cuando configure Puerta de enlace de VPN, las instrucciones que use dependen del modelo de implementación que usara para crear la red virtual. Por ejemplo, si creó la red virtual con el modelo de implementación clásica, use las instrucciones y directrices del modelo de implementación clásica para crear y configurar la puerta de enlace de VPN. Consulte [Descripción de los modelos de implementación clásica y de Resource Manager](../resource-manager-deployment-model.md) para más información.

Las secciones siguientes contienen tablas que muestran la siguiente información para la configuración:

- modelo de implementación disponible;
- herramientas de configuración disponibles;
- vínculos que llevan directamente a un artículo, si lo hay.


Use los gráficos y las descripciones como ayuda para seleccionar la topología de configuración que mejor se ajuste a sus requisitos. Los diagramas muestran las principales topologías de referencia, pero también se pueden crear configuraciones más complejas con los diagramas como guía. Cada configuración se basa en la configuración de Puerta de enlace de VPN que seleccione.

Como Puerta de enlace de VPN es una colección de configuraciones, puede configurar algunas de ellas con una herramienta y después cambiar a otra. Actualmente, no se pueden definir todas las configuraciones de puerta de enlace de VPN en el Portal de Azure. Las instrucciones de los artículos para cada configuración indican si se necesita una herramienta específica. Si está trabajando con el modelo de implementación clásica, podría trabajar en el Portal clásico o usar PowerShell ahora. Para más información sobre las configuraciones individuales disponibles, consulte [About VPN Gateway settings](vpn-gateway-about-vpn-gateway-settings.md) (Acerca de la configuración de Puerta de enlace de VPN).


## De sitio a sitio y multisitio

### De sitio a sitio

Una conexión de sitio a sitio (S2S) es una conexión a través de un túnel VPN IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada y que no se encuentre detrás de NAT. Se pueden utilizar conexiones S2S para las configuraciones híbridas y entre locales.

![Conexión S2S](./media/vpn-gateway-about-vpngateways/demos2s.png "De sitio a sitio")


### Multisitio

Puede crear y configurar una conexión VPN entre su red virtual y varias redes locales. Cuando trabaje con varias conexiones, debe usar un tipo de VPN basada en rutas (puerta de enlace dinámica para redes virtuales clásicas). Como una red virtual solo puede tener una puerta de enlace de red virtual, todas las conexiones a través de la puerta de enlace comparten el ancho de banda disponible. Este tipo de configuración se denomina con frecuencia, conexión "multisitio".
 

![Conexión multisitio](./media/vpn-gateway-about-vpngateways/demomulti.png "Multisitio")

### Modelos de implementación y métodos

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## De red virtual a red virtual

La conexión de una red virtual a otra es muy parecida a la conexión de una red virtual a una ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN de Azure para proporcionar un túnel seguro con IPsec/IKE. Incluso puede combinar la comunicación de red virtual a red virtual con configuraciones de varios sitios. Esto permite establecer topologías de red que combinen la conectividad entre entornos con la conectividad entre redes virtuales.

Las redes virtuales que conecta pueden:

- estar en la misma región o en distintas;
- pertenecer a la misma suscripción o a distintas;
- usar el mismo modelo de implementación o dos diferentes.



![Conexión de red virtual a red virtual](./media/vpn-gateway-about-vpngateways/demov2v.png "De red virtual a red virtual")



### Conexiones entre modelos de implementación

Actualmente, Azure tiene dos modelos de implementación: el clásico y el de Resource Manager. Si lleva un tiempo usando Azure, es probable que tenga máquinas virtuales de Azure y roles de instancia que se ejecuten en una red virtual clásica. Es posible que sus máquinas virtuales e instancias de roles más recientes se estén ejecutando en una red virtual creada en Resource Manager. Puede crear una conexión entre las redes virtuales para permitir que los recursos de una red virtual se comuniquen directamente con los recursos de otra.

### Emparejamiento de VNET

Es posible que pueda usar el emparejamiento de VNET para crear la conexión, siempre que la configuración de red virtual cumpla determinados requisitos. El emparejamiento de VNET no utiliza una puerta de enlace de red virtual. El [emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md) está actualmente en versión preliminar.


### Modelos de implementación y métodos

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## De punto a sitio

Una configuración de punto a sitio (P2S) permite crear una conexión segura a su red virtual desde un equipo cliente individual. P2S es una conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros). Las conexiones P2S no requieren un dispositivo VPN ni una dirección IP pública para funcionar. Para establecer la conexión VPN, iníciela desde el equipo cliente. Esta es una solución útil cuando desea conectarse a la red virtual desde una ubicación remota, como desde casa o desde una conferencia, o si solo tiene unos pocos clientes que necesitan conectarse a una red virtual.


![Conexión de punto a sitio](./media/vpn-gateway-about-vpngateways/demop2s.png "De punto a sitio")

### Modelos de implementación y métodos

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Para más información sobre ExpressRoute, vea la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).


## Conexiones de sitio a sitio y de ExpressRoute coexistentes

ExpressRoute es una conexión dedicada directa desde la WAN (no a través de Internet) a servicios Microsoft, incluido Azure. El tráfico VPN de sitio a sitio viaja cifrado a través de la red pública de Internet. Poder configurar las conexiones VPN de sitio a sitio y ExpressRoute para la misma red virtual tiene varias ventajas. Puede configurar una VPN de sitio a sitio como una ruta de acceso seguro de conmutación por error para ExpressRoute, o bien usar la VPN de sitio a sitio para conectarse a sitios que no forman parte de su red, pero que están conectados a través de ExpressRoute.


![Coexistencia de conexión](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### Modelos de implementación y métodos

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## Pasos siguientes

Consulte [Preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md) para más información sobre las puertas de enlace de VPN.

Conecte su ubicación local a una red virtual. Consulte [Creación de una conexión de sitio a sitio](vpn-gateway-howto-site-to-site-resource-manager-portal.md).





 

<!---HONumber=AcomDC_0824_2016-->