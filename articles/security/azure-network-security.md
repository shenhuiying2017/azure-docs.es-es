---
title: Azure Network Security | Microsoft Docs
description: "Obtenga información acerca de los servicios de computación en la nube que incluyen una amplia selección de instancias y servicios de proceso, que se pueden escalar tanto vertical como horizontalmente para satisfacer las necesidades de su aplicación o de su empresa."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2017
ms.author: TomSh
ms.openlocfilehash: 6ab59dd02391287a1effc0b51502bb7eb90db319
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-network-security"></a>Azure Network Security

Sabemos que la seguridad tiene la máxima prioridad en la nube y conocemos la importancia que tiene que buscar información exacta y a tiempo sobre la seguridad de Azure. Una de las mejores razones para usar Azure en sus aplicaciones y servicios es poder aprovechar su amplia gama de funcionalidades y herramientas de seguridad. Estas herramientas y funcionalidades permiten crear soluciones seguras en la plataforma Azure.

Microsoft Azure proporciona confidencialidad, integridad y disponibilidad para los datos del cliente, al mismo tiempo que hace posible una responsabilidad transparente. Para ayudarle a comprender mejor la colección de controles de seguridad de red que se implementa en Microsoft Azure desde la perspectiva del cliente, se ha escrito este artículo, "Azure Network Security", para proporcionar información completa sobre los controles de seguridad de red disponibles con Microsoft Azure.

Este documento está pensado para informarle sobre la amplia gama de controles de red que se pueden configurar para mejorar la seguridad de las soluciones que se implementan en Azure. Si está interesado en lo que hace Microsoft para proteger el tejido de red de la propia plataforma Azure, consulte la sección seguridad de Azure en el [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/azure-security) (Centro de confianza de Microsoft).

## <a name="azure-platform"></a>Plataforma Azure

Azure es una plataforma de servicios en la nube pública que admite una amplia selección de sistemas operativos, lenguajes de programación, plataformas, herramientas, bases de datos y dispositivos.  Puede ejecutar contenedores de Linux con integración de Docker, compilar aplicaciones con JavaScript, Python, .NET, PHP, Java, Node.js y crear back-ends para dispositivos iOS, Android y Windows. Los servicios en la nube de Azure admiten las mismas tecnologías en las que ya confían millones de desarrolladores y profesionales de TI.

Al crear en un proveedor de servicios en la nube pública, o migrar recurso de TI al mismo, está confiando en la capacidad de la organización para proteger sus aplicaciones y datos con los servicios y controles que proporcionan para administrar la seguridad de sus recursos en la nube.

La infraestructura de Azure está diseñada desde la instalación hasta las aplicaciones para hospedar millones de clientes simultáneamente, y proporciona una base de confianza en la que las empresas pueden satisfacer sus requisitos de seguridad. Además, Azure ofrece un amplio conjunto de opciones de seguridad configurables, así como la capacidad de controlarlas, por lo que puede personalizar la seguridad para satisfacer los requisitos exclusivos de las implementaciones de su organización.

## <a name="abstract"></a>Descripción breve

Servicios en la nube pública de Microsoft ofrece servicios e infraestructura a gran escala, funcionalidades de calidad empresarial y numerosas opciones de conectividad híbrida. Puede elegir tener acceso a estos servicios a través de Internet o con Azure ExpressRoute, que proporciona conectividad de red privada. La plataforma Microsoft Azure le permite ampliar su infraestructura en la nube sin problemas y crear arquitecturas de varios niveles. Además, otros fabricantes pueden habilitar funcionalidades mejoradas mediante servicios de seguridad y dispositivos virtuales.

Los servicios de red de Azure permiten maximizar la flexibilidad, disponibilidad, resistencia, seguridad e integridad por diseño. Estas notas del producto proporcionan detalles sobre las funciones de red de Azure e información acerca de cómo los clientes pueden usar las características de seguridad nativas de Azure para ayudar a proteger sus recursos de información.

Estas notas del producto están pensadas para:

- Administradores técnicos, administradores de red y desarrolladores que buscan soluciones de seguridad que estén disponibles y sean compatibles con Azure.

-   Expertos en la materia o ejecutivos de procesos empresariales que desean obtener una visión general de las tecnologías de red de Azure y de los servicios que son pertinentes en debates acerca de la seguridad de red en la nube pública de Azure.

## <a name="azure-networking-big-picture"></a>Panorama general de las redes de Azure
Microsoft Azure incluye una sólida infraestructura de red que respalda sus requisitos de conectividad de aplicaciones y servicios. Es posible la conectividad de red entre recursos ubicados en Azure, entre recursos locales y hospedados en Azure y entre Internet y Azure.

![Panorama general de las redes de Azure](media/azure-network-security/azure-network-security-fig-1.png)

La [infraestructura de red de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) le permite conectar de forma segura los recursos de Azure entre sí por medio de redes virtuales. Una red virtual es una representación de su propia red en la nube. Una red virtual es un aislamiento lógico de la red de nube de Azure dedicada a su suscripción. Puede conectar redes virtuales a las redes locales.

Azure es compatible con la conectividad de vínculo WAN dedicado a su red local y a una instancia de Azure Virtual Network con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). El vínculo entre Azure y su sitio utiliza una conexión dedicada que no va por la red pública de Internet. Si su aplicación de Azure se va a ejecutar en varios centros de datos, puede utilizar [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para enrutar las solicitudes de los usuarios de forma inteligente entre instancias de la aplicación. También puede enrutar tráfico a servicios que no se ejecuten en Azure si se puede obtener acceso a ellos desde Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Información empresarial sobre los componentes de red de Azure
Azure tiene muchos componentes de red que son importantes en cualquier debate sobre seguridad de red. Vamos a describir estos componentes de red y a centrarnos en los problemas de seguridad relacionados con ellos.

> [!Note]
> No se describen todos los aspectos sobre las redes de Azure: se analizan solo aquellos que se consideran importantes a la hora de planear y diseñar una infraestructura de red segura para los servicios y aplicaciones que implementa en Azure.

En este documento, se analizarán las siguientes funcionalidades empresariales de red de Azure:

-   Conectividad de red básica

-   Conectividad híbrida

-   Controles de seguridad

-   Validación de la red

### <a name="basic-network-connectivity"></a>Conectividad de red básica

El servicio [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) le permite conectar de forma segura los recursos de Azure entre sí por medio de redes virtuales. Una red virtual es una representación de su propia red en la nube. Una red virtual es un aislamiento lógico de la infraestructura de red de Azure dedicada a su suscripción. También se puede conectar redes virtuales entre sí y a las redes locales con VPN de sitio a sitio y dedicado [vínculos WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Conectividad de red básica](media/azure-network-security/azure-network-security-fig-2.png)

Teniendo en cuenta que usa máquinas virtuales para hospedar servidores en Azure, la cuestión es cómo se conectan esas máquinas virtuales a una red. La respuesta es que las máquinas virtuales se conectan a una instancia de [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Las instancias de Azure Virtual Network son similares a las redes virtuales que usa de forma local con sus propias soluciones de plataforma de virtualización, como Microsoft Hyper-V o VMware.

#### <a name="intra-vnet-connectivity"></a>Conectividad entre redes virtuales

Puede conectar redes virtuales entre sí, habilitar recursos conectados a cualquier red virtual para que se comuniquen entre sí a través de redes virtuales. Puede utilizar una o ambas de las siguientes opciones para conectar redes virtuales entre sí:

- **Emparejamiento:** permite que los recursos se conecten a distintas redes virtuales de Azure en la misma ubicación de Azure para comunicarse entre sí. El ancho de banda y la latencia en las redes virtuales son los mismos que si los recursos estuvieran conectados a la misma red virtual. Para más información sobre emparejamiento, consulte [Emparejamiento de redes virtuales de Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Emparejamiento](media/azure-network-security/azure-network-security-fig-3.png)

- **Conexión de red virtual a red virtual:** permite recursos conectados a la red virtual de Azure diferentes dentro de las mismas ubicaciones de Azure, o diferentes. A diferencia del emparejamiento, el ancho de banda es limitado entre las redes virtuales porque el tráfico debe pasar a través de una instancia de Azure VPN Gateway.

![Conexión de red virtual a red virtual](media/azure-network-security/azure-network-security-fig-4.png)


Para más información sobre cómo conectar redes virtuales con una conexión de red virtual a red virtual, consulte el artículo [Configuración de una conexión de red virtual a red virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Funcionalidades de Azure Virtual Network:

Como puede ver, una instancia de Azure Virtual Network proporciona máquinas virtuales para conectarse a la red de forma que puedan conectarse a otros recursos de red de una forma segura. Sin embargo, la conectividad básica es solo el principio. Las siguientes funcionalidades de Azure Virtual Network exponen características de seguridad de este servicio:

-   Aislamiento

-   Conectividad de Internet

-   Conectividad de recursos de Azure

-   Conectividad de red virtual

-   Conectividad local

-   Filtrado de tráfico

-   Enrutamiento

**Aislamiento**

Las redes virtuales están completamente [aisladas](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) entre sí. Puede crear redes virtuales independientes para el desarrollo, la prueba y la producción que usan los mismos bloques de direcciones de [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). Por el contrario, puede crear varias redes virtuales que usan diferentes bloques de direcciones CIDR y que conectan redes entre sí. También puede segmentar una red virtual en varias subredes.

Azure proporciona resolución de nombres interna para máquinas virtuales e instancias de rol de [Cloud Services](https://azure.microsoft.com/services/cloud-services/) conectadas a una red virtual. Si lo desea, puede configurar una red virtual para usar sus propios servidores DNS, en lugar de utilizar la resolución de nombres interna Azure.

Puede implementar varias redes virtuales dentro de cada [suscripción](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) y [región](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) de Azure. Cada red virtual está aislada de otras redes virtuales. Para cada red virtual, puede:

-   Especificar un espacio de direcciones IP privado personalizado mediante direcciones públicas y privadas (RFC 1918). Azure asigna recursos conectados a la red virtual a una dirección IP privada desde el espacio de direcciones que asigne.

-   Segmentar la red virtual en una o varias subredes y asignar una parte del espacio de direcciones de redes virtuales para cada subred.

-   Utilizar la resolución de nombres que proporciona Azure o especificar su propio servidor DNS para su uso por recursos conectados a una red virtual. Para más información sobre la resolución de nombres de las redes virtuales, lea el artículo [Resolución de nombres para las máquinas virtuales y servicios en la nube](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Conectividad de Internet**

Todas las [máquinas virtuales de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/) y las instancias de rol de Cloud Services conectadas a una red virtual tienen acceso a Internet, de forma predeterminada. También puede habilitar el acceso de entrada a recursos específicos, según sea necesario. Las máquinas virtuales y las instancias de rol de Cloud Services conectadas a una red virtual tienen acceso a Internet, de forma predeterminada. También puede habilitar el acceso entrante a recursos específicos, según sea necesario.

Todos los recursos conectados a una red virtual tienen conectividad saliente a Internet de forma predeterminada. La dirección IP privada del recurso es la dirección de red de origen traducida (SNAT) a una dirección IP pública por la infraestructura de Azure. Puede cambiar la conectividad predeterminada mediante la implementación del enrutamiento personalizado y del filtrado de tráfico. Para más información acerca de la conectividad de Internet saliente, consulte el artículo de [Comprender las conexiones salientes en Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para la comunicación entrante con los recursos de Azure desde Internet, o para la comunicación saliente a Internet sin SNAT, se debe asignar un recurso una dirección IP pública. Para más información sobre las direcciones IP públicas, consulte [Direcciones IP publicas](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Conectividad de recursos de Azure**

Los [recursos de Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) como Cloud Services y las máquinas virtuales se pueden conectar a la misma red virtual. Los recursos pueden conectarse entre sí mediante direcciones IP privadas, aunque estén en subredes diferentes. Azure proporciona el enrutamiento predeterminado entre subredes, redes virtuales y redes locales, por lo que no tendrá que configurar ni administrar rutas.

Se pueden conectar varios recursos de Azure a una red virtual, como máquinas virtuales (VM), Cloud Services, entornos de App Service y conjuntos de escalado de máquinas virtuales. Las máquinas virtuales se conectan a una subred dentro de una red virtual a través de una interfaz de red (NIC). Para más información sobre las NIC, consulte [Interfaces de red](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Conectividad de red virtual**

Las [redes virtuales](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) se pueden conectar entre sí, habilitando los recursos conectados a cualquier red virtual para que se comuniquen con cualquier recurso de cualquier red virtual.

Puede conectar redes virtuales entre sí, habilitar recursos conectados a cualquier red virtual para que se comuniquen entre sí a través de redes virtuales. Puede utilizar una o ambas de las siguientes opciones para conectar redes virtuales entre sí:

- **Emparejamiento:** permite que los recursos se conecten a distintas redes virtuales de Azure en la misma ubicación de Azure para comunicarse entre sí. El ancho de banda y la latencia en las redes virtuales son los mismos que si los recursos estuvieran conectados a la misma red virtual. Para más información sobre el emparejamiento, consulte [Emparejamiento de redes virtuales de Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Conexión de red virtual a red virtual:** permite recursos conectados a la red virtual de Azure diferentes dentro de las mismas ubicaciones de Azure, o diferentes. A diferencia del emparejamiento, el ancho de banda es limitado entre las redes virtuales porque el tráfico debe pasar a través de una instancia de Azure VPN Gateway. Más información sobre cómo conectar redes virtuales con una conexión de red virtual a red virtual. Para más información, lea el artículo [Configuración de una conexión de red virtual a red virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Conectividad local**

Las redes virtuales se pueden conectar a redes [locales](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) a través de conexiones de redes privadas entre la red y Azure, o a través de una conexión VPN de sitio a sitio a través de Internet.

Puede conectar su red local a una red virtual mediante cualquier combinación de las siguientes opciones:

- **Red privada virtual (VPN) de punto a sitio:** establecido entre un único equipo conectado a la red y a la red virtual. Este tipo de conexión es muy útil cuando se está comenzando con Azure, o para los desarrolladores, porque requiere poco o ningún cambio en la red existente. La conexión utiliza el protocolo SSTP para proporcionar comunicación cifrada a través de Internet entre el equipo y la red virtual. La latencia de una VPN de punto a sitio es imprevisible, ya que el tráfico atraviesa Internet.

- **VPN de sitio a sitio:** se establece entre el dispositivo VPN y una instancia de Azure VPN Gateway. Este tipo de conexión permite cualquier recurso local que autoriza a acceder a una red virtual. La conexión es una VPN de IPSec/IKE que proporciona comunicación cifrada por Internet entre el dispositivo local y la instancia de Azure VPN Gateway. La latencia de una conexión de sitio a sitio es imprevisible, ya que el tráfico atraviesa Internet.

- **Azure ExpressRoute:** establecida entre la red y Azure, a través de un asociado de ExpressRoute. Esta conexión es privada. El tráfico no atraviesa Internet. La latencia de una conexión ExpressRoute es predecible, ya que el tráfico no atraviesa Internet. Para más información acerca de todas las opciones de la conexión anterior, consulte [Diagramas de topología de conexión](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtrado de tráfico**

El [tráfico de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) de entrada y salida de las instancias de rol de Cloud Services y máquinas virtuales se puede filtrar por dirección IP y puerto de origen, dirección IP y puerto de destino, y por protocolo.

Puede filtrar el tráfico de red entre subredes mediante una o ambas de las siguientes opciones:

- **Grupos de seguridad de red:** cada grupo de seguridad de red puede contener varias reglas de seguridad entrante y saliente, que le permiten filtrar el tráfico por dirección IP, puerto y protocolo de origen y destino. Puede aplicar un grupo de seguridad de red a cada NIC de una máquina virtual. También puede aplicar un grupo de seguridad de red a la subred de una NIC u otro recurso de Azure, a la que está conectado. Consulte [Grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) para más información sobre estos.

- **Aplicaciones de red virtual:** una aplicación de red virtual es una máquina virtual que ejecuta software que realiza una función de red, como un firewall. Consulte una lista de dispositivos virtuales de red disponibles en Azure Marketplace. Los dispositivos virtuales de red también están disponibles para proporcionar la optimización de la WAN y otras funciones de tráfico de red. Los dispositivos virtuales de red normalmente se usan con rutas BGP o definidas por el usuario. También puede utilizar un dispositivo virtual de red para filtrar el tráfico entre redes virtuales.

**Enrutamiento**

Opcionalmente, puede invalidar el enrutamiento predeterminado de Azure mediante la configuración de sus propias rutas, o mediante el uso de rutas BGP a través de una puerta de enlace de red.

Azure crea tablas de rutas que permiten a los recursos conectados a una subred de cualquier red virtual se comuniquen entre sí, de forma predeterminada. Puede implementar una o ambas de las siguientes opciones para reemplazar las rutas predeterminadas que crea Azure:

- **Rutas definidas por el usuario:** puede crear tablas de rutas personalizadas con las rutas que controlan a dónde se enruta el tráfico para cada subred. Para aprender más sobre las rutas definidas por el usuario, consulte [Rutas definidas por el usuario](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Rutas BGP:** si conecta la red local a su red local mediante una conexión de Azure VPN Gateway o ExpressRoute, puede propagar las rutas BGP a sus redes virtuales.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Conectividad híbrida a Internet: Conexión a una red local
Puede conectar su red local a una red virtual mediante cualquier combinación de las siguientes opciones:

-   Conectividad de Internet

-   VPN de punto a sitio (VPN P2S)

-   VPN de sitio a sitio (VPN S2S)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Conectividad de Internet

Como sugiere su nombre, la conectividad de Internet permite que las cargas de trabajo sean accesibles desde Internet, haciendo que exponga diferentes puntos de conexión públicos a cargas de trabajo ubicadas dentro de la red virtual. Estas cargas de trabajo podrían exponerse mediante un [equilibrador de carga accesible desde Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) o simplemente asignando una dirección IP pública a la máquina virtual. De esta manera, es posible que cualquier elemento en Internet pueda tener acceso a esa máquina virtual, siempre y cuando el firewall del host, los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) y las [rutas definidas por el usuario](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) lo permitan.

En este escenario, puede exponer una aplicación que necesita que sea pública en Internet y hacer que sea posible conectarse a ella desde cualquier lugar, o desde ubicaciones específicas según la configuración de las cargas de trabajo.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>VPN de punto a sitio o de sitio a sitio
Estas dos pertenecen a la misma categoría. Ambas necesitan que la red virtual tenga una instancia de VPN Gateway y que pueda conectarse a ella mediante un cliente de VPN para la estación de trabajo como parte de la [configuración de punto a sitio](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) o bien mediante la configuración del [dispositivo VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) local para que pueda finalizar una VPN de sitio a sitio. De esta manera, los dispositivos locales pueden conectarse a los recursos de la red virtual.

Una configuración de punto a sitio (P2S) permite crear una conexión segura entre un equipo cliente individual y una red virtual. P2S es una conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros).

![VPN de punto a sitio](media/azure-network-security/azure-network-security-fig-5.png)

Las conexiones de punto a sitio son útiles cuando desea conectarse a su red virtual desde una ubicación remota, como desde casa o desde un centro de conferencias, o si tiene pocos clientes que necesiten conectarse a una red virtual.

Las conexiones P2S no requieren un dispositivo VPN ni una dirección IP pública. Para establecer la conexión VPN desde el equipo cliente. Por eso, no es recomendable la conexión de punto a sitio para conectarse a Azure si necesita una conexión persistente de muchos equipos y dispositivos locales a la red de Azure.

![VPN de sitio a sitio](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Para más información sobre las conexiones de punto a sitio, consulte [Preguntas frecuentes sobre la conexión de punto a sitio](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Se utiliza una conexión de puerta de enlace VPN de sitio a sitio para conectar su red local a una red virtual de Azure a través de un túnel VPN de IPsec/IKE (IKEv1 o IKEv2).

Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada. Esta conexión tiene lugar a través de Internet y permite colocar la información en un "túnel" dentro de un vínculo cifrado entre la red y Azure. La VPN de sitio a sitio es una tecnología segura y madura que empresas de todos los tamaños han implementado durante décadas. El cifrado del túnel se realiza con el [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Si bien la VPN de sitio a sitio es una tecnología de confianza y establecida, el tráfico del túnel no atraviesa Internet. Además, el ancho de banda está relativamente limitado a un máximo de aproximadamente 200 Mbps.

Si necesita un nivel de seguridad o de rendimiento excepcional para las conexiones entre locales, se recomienda utilizar Azure ExpressRoute para su conectividad. ExpressRoute es un vínculo de WAN dedicada entre su ubicación local o un proveedor de hospedaje de Exchange. Se trata de una conexión de telecomunicaciones, los datos no viajan a través de Internet y, por tanto, no se exponen a los posibles riesgos inherentes a las comunicaciones de Internet.

> [!Note]
> Para más información acerca de las puertas de enlace VPN, consulte [Acerca de VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Vínculo WAN dedicado
Microsoft Azure ExpressRoute le permite ampliar sus redes locales a Azure a través de una conexión privada y dedicada que facilita un proveedor de conectividad.

Las conexiones ExpressRoute no pasan por la red pública de Internet. Esto permite a las conexiones de ExpressRoute ofrecer más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones normales a través de Internet.

![ Vínculo WAN dedicado](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Para más información sobre cómo conectar la red a Microsoft mediante ExpressRoute, consulte [Modelos de conectividad de ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) e [Información técnica de ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Al igual que con las opciones de VPN de sitio a sitio, ExpressRoute también le permite conectarse a recursos que no tienen por qué estar necesariamente en una sola red virtual. De hecho, según la SKU, puede conectarse a 10 redes virtuales. Si tiene el [complemento premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), puede conectarse hasta a 100 redes virtuales, según el ancho de banda. Para más información sobre el aspecto de estos tipos de conexiones, consulte [Diagramas de topología de conexión](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Controles de seguridad
Una instancia de Azure Virtual Network proporciona una red lógica y segura que está aislada de otras redes virtuales y es compatible con muchos controles de seguridad que se usan en las redes locales. Los clientes pueden crear su propia estructura mediante: subredes (con sus propios intervalos privados de direcciones IP), configuración de tablas de rutas, grupos de seguridad de red, listas de control de acceso (ACL), puertas de enlace y aplicaciones virtuales para ejecutar sus cargas de trabajo en la nube.

Éstos son los controles de seguridad que puede usar en las instancias de Azure Virtual Network:

-   Controles de acceso a la red

-   Rutas definidas por el usuario

-   Aplicación de seguridad de red

-   Application Gateway

-   Firewall de aplicaciones web de Azure

-   Control de disponibilidad de red

#### <a name="network-access-controls"></a>Controles de acceso a la red
Aunque Azure Virtual Network es la piedra angular del modelo de redes de Azure y proporciona aislamiento y protección, los [grupos de seguridad de red (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) son la principal herramienta que se usa para aplicar y controlar las reglas de tráfico de red en el nivel de red.

![ Controles de acceso a la red](media/azure-network-security/azure-network-security-fig-8.png)


Puede controlar el acceso mediante el permiso o denegación de la comunicación entre las cargas de trabajo de una red virtual, desde sistemas de redes del cliente a través de conectividad entre locales o comunicación de Internet directa.

En el diagrama, las redes virtuales y los grupos de seguridad de red están situados en una capa concreta de la pila de seguridad general de Azure, en la que se pueden usar NSG, UDR y dispositivos virtuales de red para crear límites de seguridad y así proteger las implementaciones de aplicaciones en la red protegida.

Los grupos de seguridad de red usan una 5-tupla para evaluar el tráfico (y se utilizan en las reglas que configura para el NSG):

-   [Dirección IP de origen y destino](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Puerto de origen y destino](https://technet.microsoft.com/library/dd197515)

-   Protocolo: [Protocolo de control de transmisión (TCP)](https://technet.microsoft.com/library/cc940037.aspx) o [Protocolo de datagramas de usuario (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Esto significa que puede controlar el acceso entre una sola máquina virtual y un grupo de máquinas virtuales, o entre una sola máquina virtual y otra máquina virtual única, o entre subredes enteras. De nuevo, tenga en cuenta que esto es simplemente un filtrado de paquetes con estado, no una inspección de paquetes completa. No hay ninguna validación de protocolo ni funcionalidad IDS o IPS de nivel de red en un grupo de seguridad de red.

Un grupo de seguridad de red incluye algunas reglas integradas que debe tener en cuenta. Dichos componentes son:

-   **Permitir todo el tráfico dentro de una red virtual específica:** todas las máquinas virtuales de la misma instancia de Azure Virtual Network se pueden comunicar entre sí.

-   **Permitir el equilibrio de carga entrante de Azure:** Esta regla permite el tráfico desde cualquier dirección de origen a cualquier dirección de destino para el equilibrador de carga de Azure.

-   **Denegar todo el tráfico entrante:** Esta regla bloquea todo el tráfico procedente de Internet que haya permitido explícitamente.

-   **Permitir todo el tráfico saliente a Internet:** Esta regla permite a las máquinas virtuales iniciar conexiones a Internet. Si no desea que se inicien estas conexiones, debe crear una regla para bloquearlas o aplicar la tunelización forzada.

#### <a name="system-routes-and-user-defined-routes"></a>Rutas de sistema y rutas definidas por el usuario

Al agregar máquinas virtuales (VM) a una red virtual (VNet) en Azure, observará que las máquinas virtuales son capaces de comunicarse automáticamente con otras máquinas por toda la red. No es necesario que especifique una puerta de enlace, incluso si las máquinas virtuales están en subredes diferentes.

Cuando existe una conexión híbrida de Azure a su centro de datos, puede aplicar esto a la comunicación de las máquinas virtuales en la red pública e incluso a su red local.

![Rutas del sistema](media/azure-network-security/azure-network-security-fig-9.png)

Este flujo de comunicación es posible porque Azure usa varias rutas del sistema que definen cómo fluye el tráfico IP. Las rutas del sistema controlan el flujo de comunicación de los escenarios siguientes:

-   Desde dentro de la misma subred.

-   Desde una subred a otra dentro de una red virtual.

-   Desde máquinas virtuales a Internet.

-   Desde una red virtual a otra red virtual a través de una puerta de enlace de VPN.

-   Desde una red virtual a otra red virtual mediante emparejamiento de redes virtuales ([encadenamiento de servicios](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Desde una red virtual a su red local a través de una puerta de enlace de VPN.

Muchas empresas tienen requisitos estrictos de seguridad y conformidad que requieren la inspección local de todos los paquetes de redes para asegurar el cumplimiento de directivas específicas. Azure proporciona un mecanismo denominado [tunelización forzada](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) que enruta el tráfico desde las máquinas virtuales a una ubicación local mediante la creación de una ruta personalizada o mediante anuncios de [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) a través de ExpressRoute o VPN.

La tunelización forzada en Azure se configura a través de rutas definidas por el usuario (UDR) de redes virtuales. La redirección del tráfico a un sitio local se expresa como una ruta predeterminada a la puerta de enlace de VPN de Azure.

La sección siguiente muestra la limitación actual de la tabla de enrutamiento y las rutas de una red virtual de Azure:

-   Cada subred de la red virtual tiene una tabla de enrutamiento del sistema integrada. La tabla de enrutamiento del sistema tiene los siguientes tres grupos de rutas:

 -  **Rutas de redes virtuales locales** : directamente a las máquinas virtuales de destino en la misma red virtual.

 - **Rutas locales** : a la puerta de enlace de VPN.

 -  **Ruta predeterminada** : directamente a Internet. Los paquetes destinados a las direcciones IP privadas que no están cubiertos por las dos rutas anteriores se anularán.

-   Con la liberación de las rutas definidas por el usuario, puede crear una tabla de enrutamiento para agregar una ruta predeterminada y, después, asociar la tabla de enrutamiento a las subredes de la red virtual para habilitar la tunelización forzada en esas subredes.

-   Deberá establecer un "sitio predeterminado" entre los sitios locales entre entornos conectados a la red virtual.

-   La tunelización forzada debe asociarse a una red virtual que tiene una puerta de enlace de VPN de enrutamiento dinámico (no una puerta de enlace estática).

- La tunelización forzada ExpressRoute no se configura mediante este mecanismo, sino que se habilita mediante el anuncio de una ruta predeterminada a través de las sesiones de emparejamiento BGP de ExpressRoute.

> [!Note]
> Consulte la [Documentación de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para más información.

#### <a name="network-security-appliances"></a>Aplicaciones de seguridad de red
Si bien los grupos de seguridad de red y el enrutamiento definido por el usuario pueden proporcionar un cierto grado de seguridad de la red en los niveles de red y de transporte del [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), habrá situaciones en la que deseará o necesitará habilitar la seguridad en los niveles altos de la pila de redes. En tales situaciones, se recomienda implementar aplicaciones de seguridad de la red virtual proporcionadas por asociados de Azure.

![Aplicaciones de seguridad de red](./media/azure-network-security/azure-network-security-fig-10.png)

Las aplicaciones de seguridad de red Azure mejoran la seguridad y las funciones de red de la red virtual y están disponibles en numerosos proveedores a través de [Azure Marketplace](https://azuremarketplace.microsoft.com). Estas aplicaciones de seguridad virtual se pueden implementar para proporcionar:

-   Firewalls de alta disponibilidad

-   Prevención de intrusiones

-   Detección de intrusiones

-   Firewalls de aplicaciones web (WAF)

-   Optimización de WAN

-   Enrutamiento

-   Equilibrio de carga

-   VPN

-   Administración de certificados

-   Active Directory

-   Autenticación multifactor

#### <a name="application-gateway"></a>puerta de enlace de aplicaciones

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) es una aplicación virtual dedicada que proporciona un controlador de entrega de aplicaciones (ADC) como servicio.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Application Gateway también le permite optimizar el rendimiento y la disponibilidad de las granjas de servidores web traspasando la carga de la terminación SSL con mayor actividad de la CPU a la puerta de enlace de aplicaciones (Descarga de SSL). También proporciona otras funcionalidades de enrutamiento de capa 7 entre las que se incluyen:

-   Distribución round robin del tráfico entrante

-   Afinidad de sesión basada en cookies

-   Enrutamiento basado en URL

-   Capacidad de hospedar varios sitios web en una sola instancia de Application Gateway


También se proporciona un [firewall de aplicaciones web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) como parte de la instancia de Application Gateway. Esta proporciona protección a las aplicaciones web frente a vulnerabilidades web y vulnerabilidades de seguridad comunes. Application Gateway se puede configurar como una puerta de enlace orientada a Internet, una puerta de enlace solo para uso interno o una combinación de las dos.

El WAF de Application Gateway se puede ejecutar en modo de detección o de prevención. Un caso de uso común es el de los administradores que lo ejecutan en modo de detección para observar el tráfico en busca de patrones malintencionados. Si se detectan posibles vulnerabilidades de seguridad, con solo pasar al modo de prevención se bloquea el tráfico entrante sospechoso.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Además, el WAF de Application Gateway le ayuda a supervisar las aplicaciones web frente a ataques mediante un registro de WAF en tiempo real que está integrado en [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) y [Azure Security Center](https://azure.microsoft.com/services/security-center/) que permite realizar un seguimiento de las alertas de WAF y supervisar con facilidad las tendencias.

El registro con formato JSON va directamente a la cuenta de almacenamiento del cliente. Tiene un control total sobre estos registros y puede aplicar sus propias directivas de retención.

También puede usar estos registros en su propio sistema de análisis con [Integración de registro de Azure](https://aka.ms/AzLog). Los registros de WAF también se integran con [Operations Management Suite (OMS)](https://www.microsoft.com/cloud-platform/operations-management-suite) por lo que puede usar el análisis de registros de OMS para ejecutar consultas específicas sofisticadas.

#### <a name="azure-web-application-firewall-waf"></a>Firewall de aplicaciones web (WAF) de Azure

Las aplicaciones web se están convirtiendo en el objetivo principal de ataques maliciosos que se aprovechan de vulnerabilidades de seguridad conocidas como la inyección de código SQL, los ataques de scripts de sitios y otros ataques que aparecen en la lista [OWASP top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Impedir tales vulnerabilidades en la aplicación requiere tareas rigurosas de mantenimiento, aplicación de revisiones y supervisión en varias capas de la topología de aplicación.

 ![Firewall de aplicaciones web (WAF) de Azure](./media/azure-network-security/azure-network-security-fig-13.png)

Un [firewall de aplicaciones web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) centralizado puede proteger frente a ataques web y simplifica la administración de seguridad sin necesidad de realizar ningún cambio en las aplicaciones.

Las soluciones de WAF también pueden reaccionar más rápido ante una amenaza de la seguridad aplicando revisiones que aborden una vulnerabilidad conocida en una ubicación central en lugar de proteger cada una de las aplicaciones web por separado. Las puertas de enlace de aplicaciones existentes pueden transformarse rápidamente en puertas de enlace con un firewall de aplicaciones web habilitado.

#### <a name="network-availability-controls"></a>Controles de disponibilidad de red

Existen diversas opciones para distribuir el tráfico de red con Microsoft Azure. Estas opciones funcionan de manera diferente, ya que disponen de un conjunto de características distintas y son compatibles con escenarios distintos. Cada una se puede usar por separado, pero también se pueden combinar.

Estos son los controles de disponibilidad de red:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Azure Load balancer**

Proporciona rendimiento de red y alta disponibilidad para sus aplicaciones. Se trata de un equilibrador de carga de nivel 4 (TCP y UDP) que distribuye el tráfico entrante entre las instancias de servicio correctas de los servicios que se definen en un conjunto de carga equilibrada.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Azure Load Balancer puede configurarse para lo siguiente:

-   Equilibrar la carga del tráfico entrante de Internet entre las máquinas virtuales. Esta configuración se conoce como " [equilibrio de carga con conexión a Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)".

-   Equilibrar la carga del tráfico entre máquinas virtuales de una red virtual, entre máquinas virtuales de servicios en la nube o entre equipos locales y máquinas virtuales de una red virtual entre entornos locales. Esta configuración se conoce como " [equilibrio de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview)".

-   Reenviar el tráfico externo a una máquina virtual determinada.

Todos los recursos en la nube necesitan una dirección IP pública para ser accesibles desde Internet. La infraestructura de nube de Azure usa direcciones IP no enrutables para sus recursos. Asimismo, usa traducción de direcciones de red (NAT) con direcciones IP públicas para comunicarse con Internet.

 **Application Gateway**

 Application Gateway funciona en el nivel de aplicación (nivel 7 en la pila de referencia de red de OSI). Actúa como un servicio de proxy inverso, que termina la conexión de cliente y reenvía las solicitudes de vuelta a los puntos de conexión de back-end.

 **Traffic manager**

Microsoft Azure Traffic Manager permite controlar la distribución del tráfico de los usuarios para puntos de conexión de servicio en distintos centros de datos. Entre los puntos de conexión de servicio compatibles con Traffic Manager, se incluyen máquinas virtuales de Azure, Web Apps y servicios en la nube. También puede utilizar el Traffic Manager con puntos de conexión externos, que no forman parte de Azure.

Traffic Manager usa el sistema de nombres de dominio (DNS) para dirigir las solicitudes del cliente al punto de conexión más adecuado en función de un [método de enrutamiento del tráfico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) y el estado de los puntos de conexión. Traffic Manager proporciona una serie de métodos de enrutamiento del tráfico para satisfacer las necesidades de distintas aplicaciones, la [supervisión](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) del estado de los puntos de conexión y la conmutación automática por error. Traffic Manager es resistente a errores, incluidos los que afecten a toda una región de Azure.

Azure Traffic Manager permite controlar la distribución del tráfico a través de los puntos de conexión de la aplicación. Un punto de conexión es cualquier servicio accesible desde Internet hospedado dentro o fuera de Azure.

Traffic Manager ofrece dos ventajas principales:

-   La distribución del tráfico de acuerdo con uno o varios [métodos de enrutamiento de tráfico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   La [supervisión continua del estado de los puntos de conexión](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) y la conmutación automática por error en caso de error en estos.

Cuando un cliente intenta conectarse a un servicio, debe resolver primero el nombre DNS del servicio en una dirección IP. Luego, el cliente se conecta a esa dirección IP para tener acceso al servicio. Traffic Manager usa DNS para dirigir a los clientes a puntos de conexión específicos del servicio basados en las reglas del método de enrutamiento de tráfico. Los clientes se conectan directamente al punto de conexión seleccionado. Traffic Manager no es un proxy ni una puerta de enlace. Traffic Manager no ve el tráfico que circula entre el cliente y el servicio.

### <a name="azure-network-validation"></a>Validación de la red de Azure

La validación de la red de Azure consiste en asegurarse de que la red de Azure funciona tal y como está configurada y que se puede realizar la validación mediante los servicios y características disponibles para supervisar la red. Con Azure Network Watcher, puede tener acceso a una gran cantidad de funcionalidades de registro y diagnóstico que le ofrecen información para comprender el rendimiento de la red y su estado. Estas funcionalidades son accesibles a través del portal, Power Shell, CLI, API de Rest y SDK.

Con seguridad operativa de Azure, se hace referencia a los servicios, los controles y las características disponibles para los usuarios para proteger sus datos, aplicaciones y otros recursos en Microsoft Azure. La seguridad operativa de Azure se basa en una plataforma que incorpora el conocimiento adquirido a través de diversas funcionalidades exclusivas de Microsoft, incluido el ciclo de vida de desarrollo de seguridad (SDL) de Microsoft, el programa Microsoft Security Response Center y un conocimiento en profundidad del panorama de amenazas de ciberseguridad.

-   [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Administrador de recursos de Azure

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Las personas y los procesos que operan Microsoft Azure quizás son la característica de seguridad más importante de la plataforma. Esta sección describe las características de la infraestructura global de los centros de datos de Microsoft que le ayudan a mejorar y mantener la seguridad, la continuidad y la privacidad.

La infraestructura de la aplicación está constituida normalmente por varios componentes: quizás una máquina virtual, una cuenta de almacenamiento y una red virtual, o una aplicación web, una base de datos, un servidor de bases de datos y servicios de terceros. Estos componentes no se ven como entidades independientes, sino como partes de una sola entidad relacionadas e interdependientes. Desea implementarlos, administrarlos y supervisarlos como grupo. Azure Resource Manager permite trabajar con los recursos de la solución como un grupo.

Todos los recursos de la solución se pueden implementar, actualizar o eliminar en una sola operación coordinada. Para realizar la implementación se usa una plantilla, que puede funcionar en distintos entornos, como producción, pruebas y ensayo. Administrador de recursos proporciona funciones de seguridad, auditoría y etiquetado que le ayudan a administrar los recursos después de la implementación.

**Ventajas de usar Resource Manager**

Administrador de recursos ofrece varias ventajas:

-   Puede implementar, administrar y supervisar todos los recursos de la solución en grupo, en lugar de controlarlos individualmente.

-   Puede implementar la solución repetidamente a lo largo del ciclo de vida del desarrollo y tener la seguridad de que los recursos se implementan de forma coherente.

-   Puede administrar la infraestructura mediante plantillas declarativas en lugar de scripts.

-   Puede definir las dependencias entre recursos de modo que se implementen en el orden correcto.

-   Puede aplicar control de acceso a todos los servicios del grupo de recursos al integrarse de forma nativa Control de acceso basado en rol (RBAC) en la plataforma de administración.

-   Puede aplicar etiquetas a los recursos para organizar de manera lógica todos los recursos de la suscripción.

-   Puede aclarar la facturación de su organización viendo los costos de un grupo de recursos que compartan la misma etiqueta.

> [!Note]
> Resource Manager ofrece una nueva manera de implementar y administrar las soluciones. Si usó el anterior modelo de implementación y desea obtener más información sobre los cambios, consulte [Descripción de la implementación de Administrador de recursos y la implementación clásica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Registro y supervisión de red de Azure

Azure ofrece muchas herramientas para supervisar, evitar, detectar y responder a eventos de seguridad de red. Algunas de las herramientas más eficaces que tiene disponibles en esta área son:

-   Network Watcher

-   Supervisión en el nivel de recursos de red

-   Log Analytics

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): este servicio ofrece las características de supervisión basada en el escenario. Este servicio incluye captura de paquetes, próximo salto, comprobación de flujo de IP, vista de grupos de seguridad y registros de flujo de NSG. La supervisión en el nivel de escenario, ofrece una visión global de los recursos de la red que contrasta con la supervisión de recursos de red individuales.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Network Watcher es un servicio regional que permite supervisar y diagnosticar problemas en un nivel de escenario de red mediante Azure. Las herramientas de visualización y diagnóstico de red que incluye Network Watcher le ayudan a conocer, diagnosticar y obtener información acerca de cualquier red de Azure.

En la actualidad, Network Watcher dispone de las siguientes funcionalidades:

#### <a name="topology"></a>Topología

La [topología](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) devuelve un gráfico de recursos de red de una red virtual. El grafo traza la interconexión entre los recursos para representar la conectividad de red de un extremo a otro. En el portal, la topología devuelve los objetos de recursos por cada red virtual. Las relaciones se representan mediante líneas entre los recursos. No se mostrarán los recursos fuera de la región de Network Watcher, aunque estén en el grupo de recursos. Los recursos que se devuelven en la vista de portal son un subconjunto de los componentes de red que se representan. Para ver la lista completa de los recursos de red, puede usar [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) o [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Mientras los recursos devuelven la conexión entre ellos, se modelan con dos relaciones.

- **Contención**: Virtual Network contiene un subconjunto que incluye una NIC.

- **Asociación**: una NIC está asociada a una máquina virtual.

#### <a name="variable-packet-capture"></a>Captura de paquetes variable

La [captura de paquetes variable](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) de Network Watcher permite crear sesiones de captura de paquetes para realizar el seguimiento del tráfico de entrada y salida de una máquina virtual. La captura de paquetes ayuda a diagnosticar anomalías de la red, tanto de forma activa como reactiva. Otros usos son la recopilación de estadísticas de red, la obtención de información sobre las intrusiones de red y la depuración de las comunicaciones cliente-servidor, entre otros.

La captura de paquetes es una extensión de máquina virtual que se inicia de forma remota a través de Network Watcher. Esta funcionalidad reduce la carga de la ejecución manual de una captura de paquetes en el equipo deseado, lo que permite ahorrar tiempo. La captura de paquetes puede realizarse a través del portal, PowerShell, CLI o API de REST. Un ejemplo de cómo se puede activar la captura de paquetes es con las alertas de la máquina virtual.

#### <a name="ip-flow-verify"></a>Comprobación de flujo de IP

La [comprobación de flujos de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) permite comprobar si se permite o deniega un paquete hacia o desde una máquina virtual basada en la información de 5-tuplas. Esta información está formada por la dirección, el protocolo, la dirección IP local, la dirección IP remota, el puerto local y el puerto remoto. Si un grupo de seguridad deniega un paquete, se devuelve el nombre de la regla que lo deniega. Aunque se puede elegir cualquier dirección IP de origen o destino, esta característica ayuda a los administradores a diagnosticar rápidamente problemas de conectividad desde o hacia Internet y desde o hacia el entorno local.

La comprobación de flujos de IP tiene como destino una interfaz de red de una máquina virtual. El flujo de tráfico hacia o desde esa interfaz de red se comprueba en función de los valores configurados. Esta capacidad es útil para confirmar si una regla de un grupo de seguridad de red está bloqueando el tráfico de entrada o salida de una máquina virtual.

#### <a name="next-hop"></a>Próximo salto

Determina el [próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) para los paquetes que se enrutan en el tejido de red de Azure, lo que le permite diagnosticar cualquier ruta definida por el usuario que se haya configurado incorrectamente. El tráfico de una máquina virtual se envía a un destino en función de las rutas efectivas asociadas con una NIC. La funcionalidad Próximo salto obtiene el tipo de próximo salto y la dirección IP de un paquete de una máquina virtual y una NIC específicas. Esto sirve para determinar si el paquete se dirige hacia el destino o si el tráfico se está enviando a un agujero negro.

La funcionalidad Próximo salto también devuelve la tabla de ruta asociada con el próximo salto. Al consultar un próximo salto, si la ruta se define como una ruta definida por el usuario, se devolverá esa ruta. De lo contrario, la funcionalidad devolverá "Ruta de sistema".

#### <a name="security-group-view"></a>Vista de grupo de seguridad

Obtiene las reglas de seguridad eficaces que se aplican en una máquina virtual. Los grupos de seguridad de red pueden asociarse a un nivel de subred o a un nivel de NIC. Cuando se asocia a un nivel de subred, se aplica a todas las instancias de la máquina virtual en la subred. La [vista de grupos de seguridad](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) de red devuelve todos los grupos de seguridad de red así como las reglas que están asociadas a un nivel de subred y a la NIC para una máquina virtual que proporciona una perspectiva sobre la configuración. Además, se devuelven las reglas de seguridad vigentes para cada una de las NIC en una máquina virtual. Con la vista de grupos de seguridad de red, se puede evaluar una máquina virtual en busca de vulnerabilidades de red, como puertos abiertos. También se puede validar si el grupo de seguridad de red está funcionando según lo previsto en función de una [comparación entre las reglas de seguridad vigentes y configuradas](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Registro de flujo de NSG

 Los registros de flujo de los grupos de seguridad de red permiten capturar registros relacionados con el tráfico que están permitidos o no por las reglas de seguridad del grupo. El flujo se define mediante una información de 5-tuplas: IP de origen, IP de destino, puerto de origen, puerto de destino y protocolo.

Los [registros de flujos de grupos de seguridad de red](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) son una característica de Network Watcher que permite consultar información acerca del tráfico IP de entrada y de salida en un grupo de seguridad de red.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Solución de problemas de puerta de enlace de Virtual Network y de conexión

Network Watcher proporciona numerosas funcionalidades con relación a los recursos de red de Azure. Una de estas funcionalidades es la solución de problemas de recursos. Se puede llamar a la [solución de problemas de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) mediante PowerShell, la CLI o la API de REST. Cuando se llama a Network Watcher, este inspecciona el estado de una puerta de enlace de Virtual Network o de una conexión y devuelve sus conclusiones.

Esta sección le guiará por las diferentes tareas de administración que están actualmente disponibles para la solución de problemas de recursos.

-   [Solución de problemas de una puerta de enlace de Virtual Network](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Solución de problemas de una conexión](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Límites de suscripción de red

Los [límites de suscripción de red](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) proporcionan detalles del uso de cada uno de los recursos de red en una suscripción de una región en comparación con el número máximo de recursos disponibles.

#### <a name="configuring-diagnostics-log"></a>Configuración del registro de diagnóstico

Network Watcher proporciona una vista de los [registros de diagnóstico](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). Esta vista contiene todos los recursos de las redes que admiten el registro de diagnóstico. Desde esta vista, puede habilitar y deshabilitar los recursos de red de forma cómoda y rápida.

### <a name="network-resource-level-monitoring"></a>Supervisión en el nivel de recursos de red

Las siguientes características están disponibles para la supervisión en el nivel de recurso:

#### <a name="audit-log"></a>Registro de auditoría

Se registran las operaciones realizadas como parte de la configuración de redes. Estos registros de auditoría son esenciales para establecer diversas conformidades. Estos registros se pueden ver en Azure Portal o se pueden recuperar mediante herramientas de Microsoft como Power BI o herramientas de terceros. Los registros de auditoría están disponibles a través del portal, PowerShell, la CLI y la API de Rest.

> [!Note]
> Para más información sobre los registros de auditoría, consulte [Operaciones de auditoría con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Hay registros de auditoría disponibles para las operaciones realizadas en todos los recursos de red.


#### <a name="metrics"></a>Métricas

Las métricas son medidas de rendimiento y contadores recopilados durante un período de tiempo. Las métricas están disponibles actualmente para Application Gateway. Las métricas se pueden utilizar para desencadenar alertas basadas en umbrales. La puerta de enlace de aplicaciones de Azure supervisa de forma predeterminada el estado de todos los recursos de su grupo de back-end y elimina automáticamente del grupo los recursos que se considera que están en mal estado. Además, continúa supervisando las instancias en mal estado y las agrega de nuevo al grupo de back-end en buen estado, una vez que están disponibles y responden a los sondeos de estado. Application Gateway envía los sondeos de mantenimiento con el mismo puerto que se define en la configuración de HTTP de back-end. Esta configuración garantiza que el sondeo prueba el mismo puerto que utilizarían los clientes para conectarse al back-end.

> [!Note]
> Consulte [Diagnósticos de Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) para ver cómo se pueden utilizar las métricas para crear alertas.

#### <a name="diagnostic-logs"></a>Registros de diagnóstico

Los recursos de red crean eventos periódicos y espontáneos y estos se registran en las cuentas de almacenamiento, se envían a un centro de eventos o a Log Analytics. Estos registros proporcionan información acerca del estado de un recurso. Estos registros se pueden ver en herramientas como Power BI y Log Analytics. Para aprender a ver registros de diagnóstico, visite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Los registros de diagnóstico están disponibles para el [equilibrador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), las rutas y [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher proporciona una vista de los registros de diagnóstico. Esta vista contiene todos los recursos de las redes que admiten el registro de diagnóstico. Desde esta vista, puede habilitar y deshabilitar los recursos de red de forma cómoda y rápida.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) es un servicio de [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) que supervisa los entornos local y de nube para mantener su disponibilidad y rendimiento. Recopila los datos generados por los recursos en los entornos local y de nube y mediante otras herramientas de supervisión, para proporcionar análisis entre varios orígenes.

Log Analytics ofrece las siguientes soluciones para supervisar las redes:

-   Network Performance Monitor (NPM) (monitor de rendimiento de red)

-   Azure Application Gateway Analytics

-   Azure Network Security Group Analytics

#### <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM) (monitor de rendimiento de red)
La solución de administración [Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) supervisa el mantenimiento, la disponibilidad y accesibilidad de las redes.

Se utiliza para supervisar la conectividad entre:

-   nube pública y entorno local

-   centros de datos y ubicaciones de usuario (sucursales)

-   subredes que hospedan distintos niveles de una aplicación de varios niveles.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Azure Application Gateway Analytics en Log Analytics

Para Application Gateway se admiten los siguientes registros:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Las métricas siguientes son compatibles con Application Gateway:

-   Rendimiento de 5 minutos

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Azure Network Security Group Analytics en Log Analytics

Para los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) se admiten los siguientes registros:

- **NetworkSecurityGroupEvent:** contiene entradas para las que se aplican reglas de NSG a las máquinas virtuales y a los roles de instancia en función de la dirección MAC. El estado de estas reglas se recopila cada 60 segundos.

- **NetworkSecurityGroupRuleCounter:** contiene entradas para el número de veces que se aplica cada regla de NSG para denegar o permitir el tráfico.

## <a name="next-steps"></a>Pasos siguientes
Obtener más información acerca de la seguridad con la lectura de algunos de nuestros detallados temas de seguridad:

-   [Análisis del registro para grupos de seguridad de red (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Innovaciones en las redes que permiten controlar las interrupciones en la nube](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: El software de cambio de redes que incluye Microsoft Global Cloud](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Cómo crea Microsoft su red global rápida y confiable](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Impulso de la innovación en la red](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
