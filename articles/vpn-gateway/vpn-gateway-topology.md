<properties 
   pageTitle="Topologías de conexión de puerta de enlace VPN | Microsoft Azure"
   description="Ver las topologías de conexión de puerta de enlace VPN y los modelos de implementación y herramientas de configuración disponibles."
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
   ms.date="07/13/2016"
   ms.author="cherylmc" />

# Topologías de conexión de la puerta de enlace de VPN de Azure

Este artículo muestra las topologías de línea de base de la conexión de la puerta de enlace de VPN. Puede utilizar los gráficos y las descripciones para ayudarle a seleccionar la topología de configuración que mejor se ajuste a sus requisitos. Aunque este artículo trata las topologías principales de línea de base, también se pueden crear topologías más complejas utilizando los diagramas como guía.

Cada topología contiene una tabla con el modelo de implementación para el que está disponible, las herramientas de implementación que puede usar para configurar cada topología y un vínculo directo a un artículo relacionado, si hubiera alguno disponible. Actualizamos las tablas con frecuencia a medida que nuevos artículos y herramientas de implementación están disponibles para su uso.

Si desea más información sobre las puertas de enlace de VPN, consulte [Información acerca las puertas de enlace de VPN](vpn-gateway-about-vpngateways.md).



## De sitio a sitio y multisitio

Una conexión de sitio a sitio es una conexión a través de un túnel VPN IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN o RRAS para Windows Server con ubicación local. Se pueden utilizar conexiones de sitio a sitio para las configuraciones híbridas y entre locales.


**Diagrama de S2S**

![Conexión S2S](./media/vpn-gateway-topology/site2site.png "De sitio a sitio")

Puede crear y configurar una o más conexiones VPN de S2S en sus redes locales si utiliza VPN basadas en enrutamiento de Azure. Este tipo de configuración se denomina con frecuencia, conexión "multisitio".
 

**Diagrama de multisitio**

![Conexión multisitio](./media/vpn-gateway-topology/multisite.png "Multisitio")


**Métodos y modelos de implementación disponibles**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## De red virtual a red virtual

La conexión de una red virtual a otra (VNet a VNet) es muy parecida a la conexión de una red virtual a la ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN de Azure para proporcionar un túnel seguro con IPsec/IKE. Las redes virtuales que se conecten pueden estar en regiones y suscripciones distintas. Incluso puede combinar la comunicación de red virtual a red virtual con configuraciones de varios sitios. Esto permite establecer topologías de red que combinen la conectividad entre entornos con la conectividad entre redes virtuales.

Actualmente, Azure tiene dos modos de implementación: Administración de servicios de Azure (conocido como clásico) y Azure Resource Manager. Si lleva ya algún tiempo usando Azure, probablemente tenga máquinas virtuales de Azure y roles de instancia que se ejecutan en una red virtual clásica, mientras que las máquinas virtuales y las instancias de rol más recientes se pueden estar ejecutando en una red virtual creada en ARM. Puede crear una conexión entre redes virtuales aunque estén en diferentes modelos de implementación.


**Diagrama de VNet2VNet**

![Conexión de red virtual a red virtual](./media/vpn-gateway-topology/vnet2vnet.png "De red virtual a red virtual")


**Métodos y modelos de implementación disponibles**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]



## Conexiones de sitio a sitio y de ExpressRoute coexistentes

ExpressRoute es una conexión dedicada directa con los servicios Microsoft, incluyendo Azure, desde la WAN, que no va a través de la red pública de Internet. El tráfico VPN de sitio a sitio viaja cifrado a través de la red pública de Internet. Tener la posibilidad de configurar las conexiones VPN de sitio a sitio y ExpressRoute para la misma red virtual tiene varias ventajas. Puede configurar una VPN de sitio a sitio como una ruta de acceso seguro de conmutación por error para ExressRoute, o bien usar la VPN de sitio a sitio para conectarse a sitios que no forman parte de su red, pero que están conectados a través de ExpressRoute.


**Diagrama de conexiones coexistentes**

![Coexistencia de conexión](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-site2site")


**Métodos y modelos de implementación disponibles**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## De punto a sitio

Una configuración punto a sitio permite crear una conexión segura a la red virtual desde un equipo cliente, de forma individual. Se establece una conexión VPN al iniciar la conexión desde el equipo cliente. Esta es una solución útil cuando desea conectarse a la red virtual desde una ubicación remota, como desde casa o desde una conferencia, o si solo tiene unos pocos clientes que necesitan conectarse a una red virtual.

Una conexión de punto a sitio es una conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros). Las conexiones punto a sitio no requieren un dispositivo VPN o una dirección IP pública para que funcionen.

**Diagrama de P2S**

![Conexión de punto a sitio](./media/vpn-gateway-topology/point2site.png "De punto a sitio")

**Métodos y modelos de implementación disponibles**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## Pasos siguientes

Le recomendamos familiarizarse con el contenido de los artículos [Información acerca de las puertas de enlace de VPN](vpn-gateway-about-vpngateways.md) y [Preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md) para comprender bien las puertas de enlace VPN antes de pasar al planeamiento y diseño de la conexión.





 

<!---HONumber=AcomDC_0713_2016-->