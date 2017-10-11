---
title: Redes de Azure | Microsoft Docs
description: "Obtenga información sobre servicios de redes y funcionalidades de Azure ."
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 7ed018c8c9759bc497c5fea129257486f6128531
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-networking"></a>Redes de Azure

Azure proporciona una variedad de funcionalidades de red que se pueden usar conjuntamente o por separado. Haga clic en cualquiera de las siguientes funcionalidades principales para obtener más información acerca de ellas:
- [Conectividad entre recursos de Azure](#connectivity): conecte juntos los recursos de Azure en una red virtual, privada y segura en la nube.
- [Conectividad a Internet](#internet-connectivity): comunique los recursos a y desde Azure a través de Internet.
- [Conectividad local](#on-premises-connectivity): conecte una red local a los recursos de Azure a través de una red privada virtual (VPN), a través de Internet o a través de una conexión dedicada a Azure.
- [Equilibrio de carga y dirección del tráfico](#load-balancing): equilibre la carga del tráfico en los servidores de la misma ubicación y dirija el tráfico a los servidores de ubicaciones diferentes.
- [Seguridad](#security): filtre el tráfico de red entre subredes de la red o máquinas virtuales individuales.
- [Enrutamiento](#routing): utilice el enrutamiento predeterminado o controle completamente el enrutamiento entre los recursos locales y de Azure.
- [Manejabilidad](#manageability): supervise y administre los recursos de red de Azure.
- [Herramientas de implementación y configuración](#tools): utilice un portal basado en web o herramientas de línea de comandos multiplataforma para implementar y configurar recursos de red.

## <a name="Connectivity"></a>Conectividad entre recursos de Azure

Los recursos de Azure como Virtual Machines, Cloud Services, conjuntos de escalado de máquinas virtuales y Azure App Service Environment pueden comunicarse de forma privada entre sí a través de una red virtual de Azure. Una red virtual es un aislamiento lógico de la nube de Azure dedicada a su [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Puede implementar varias redes virtuales dentro de cada suscripción y [región](https://azure.microsoft.com/regions) de Azure. Cada red virtual está aislada de otras redes virtuales. Para cada red virtual, puede:

- Especificar un espacio de direcciones IP privado personalizado mediante direcciones públicas y privadas (RFC 1918). Azure asigna recursos conectados a la red virtual a una dirección IP privada desde el espacio de direcciones que asigne.
- Segmentar la red virtual en una o varias subredes y asignar una parte del espacio de direcciones de redes virtuales para cada subred.
- Utilizar la resolución de nombres que proporciona Azure o especificar su propio servidor DNS para su uso por recursos conectados a una red virtual.

Para más información acerca del servicio Azure Virtual Network, consulte el artículo [Introducción a Virtual Network](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json). Puede conectar redes virtuales entre sí, habilitar recursos conectados a cualquier red virtual para que se comuniquen entre sí a través de redes virtuales. Puede utilizar una o ambas de las siguientes opciones para conectar redes virtuales entre sí:

- **Emparejamiento:** permite que los recursos se conecten a distintas redes virtuales de Azure en la misma región de Azure para comunicarse entre sí. El ancho de banda y la latencia en las redes virtuales son los mismos que si los recursos estuvieran conectados a la misma red virtual. Para aprender más sobre emparejamiento, consulte el artículo [Introducción al emparejamiento de redes virtuales](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **VPN Gateway:** permite que los recursos se conecten a distintas redes virtuales de Azure en distintas regiones de Azure para comunicarse entre sí. El tráfico entre redes virtuales fluye a través de una instancia de Azure VPN Gateway. El ancho de banda entre redes virtuales está limitado al ancho de banda de la puerta de enlace. Para más información sobre cómo conectar redes virtuales con una instancia de VPN Gateway, consulte el artículo [Configuración de una conexión de red virtual a red virtual entre regiones](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="internet-connectivity"></a>Conectividad de Internet

Todos los recursos de Azure conectados a una red virtual tienen conectividad saliente a Internet de forma predeterminada. La dirección IP privada del recurso es la dirección de red de origen traducida (SNAT) a una dirección IP pública por la infraestructura de Azure. Para más información acerca de la conectividad de Internet saliente, consulte el artículo de [Comprender las conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json).

Para la comunicación entrante con los recursos de Azure desde Internet, o para la comunicación saliente a Internet sin SNAT, se debe asignar un recurso una dirección IP pública. Para más información sobre las direcciones IP públicas, lea el artículo [Direcciones IP publicas](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="on-premises-connectivity"></a>Conectividad local

Se puede acceder a los recursos de la red virtual de forma segura a través de una conexión VPN o de una conexión directa privada. Para enviar tráfico de red entre su red virtual de Azure y la red local, es preciso crear una puerta de enlace de red virtual. Puede configurar las opciones para la puerta de enlace y crear el tipo de conexión que desee, VPN o ExpressRoute.

Puede conectar su red local a una red virtual mediante cualquier combinación de las siguientes opciones:

**Punto a sitio (VPN a través de SSTP)**

La siguiente imagen muestra conexiones de sitio de punto a sitio independientes entre varios equipos y una red virtual:

![De punto a sitio](./media/networking-overview/point-to-site.png)

Esta conexión se establece entre un solo equipo y una red virtual. Este tipo de conexión es muy útil cuando se está comenzando con Azure, o para los desarrolladores, porque requiere poco o ningún cambio en la red existente. También es adecuada cuando se conecta desde una ubicación remota, como una conferencia o el domicilio. A menudo se asocian conexiones punto a sitio con una conexión de sitio a sitio a través de la misma puerta de enlace de red virtual. La conexión utiliza el protocolo SSTP para proporcionar comunicación cifrada a través de Internet entre el equipo y la red virtual. La latencia de una VPN de sitio a punto es imprevisible, ya que el tráfico atraviesa Internet.

**Sitio a sitio (túnel VPN de IPsec/IKE)**

![De sitio a sitio](./media/networking-overview/site-to-site.png)

Esta conexión se establece entre el dispositivo VPN local y una instancia de Azure VPN Gateway. Este tipo de conexión permite cualquier recurso local que autoriza a acceder a la red virtual. La conexión es una VPN de IPSec/IKE que proporciona comunicación cifrada por Internet entre el dispositivo local y la instancia de Azure VPN Gateway. Puede conectar varios sitios locales a la misma instancia VPN Gateway. El dispositivo VPN local en cada sitio debe tener una dirección IP pública conectada externamente que no está detrás de un dispositivo NAT. La latencia de una conexión de sitio a sitio es imprevisible, ya que el tráfico atraviesa Internet.

**ExpressRoute (conexión privada dedicada)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Este tipo de conexión se establece entre la red y Azure, a través de un asociado de ExpressRoute. Esta conexión es privada. El tráfico no atraviesa Internet. La latencia de una conexión ExpressRoute es predecible, ya que el tráfico no atraviesa Internet. ExpressRoute puede combinarse con una conexión de sitio a sitio.

Para más información acerca de todas las opciones de la conexión anterior, consulte el artículo [Diagramas de topología de conexión](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="load-balancing"></a>Equilibrio de carga y dirección del tráfico

Microsoft Azure proporciona varios servicios para administrar cómo se distribuye el tráfico de red y se equilibra la carga. Puede usar cualquiera de las siguientes funcionalidades por separado o conjuntamente:

**Equilibrio de carga de DNS**

El servicio Azure Traffic Manager proporciona equilibrio de carga de DNS global. Traffic Manager responde a los clientes con la dirección IP de un punto de conexión correcto, basado en uno de los métodos de enrutamiento siguientes:
- **Geográfico:** Los clientes se dirigen a puntos de conexión concretos (Azure, externo o anidado) en función de la ubicación geográfica de la que parta su consulta de DNS. Este método permite escenarios donde es importante conocer la región geográfica del cliente, así como el enrutamiento a partir de ella. Algunos ejemplos incluyen cumplir los mandatos de soberanía de datos, la localización del contenido y de la experiencia del usuario, y la medición del tráfico de otras regiones.
- **Rendimiento:** la dirección IP devuelta al cliente es la "más cercana" al cliente. El punto de conexión "más cercano" no es necesariamente el más cercano según la medición de la distancia geográfica. En vez de eso, este método determina cuál es el punto de conexión más cercano mediante la medición de la latencia de red. Traffic Manager mantiene una tabla de latencia de Internet que realiza un seguimiento del tiempo que se invierte en la ida y la vuelta entre intervalos de direcciones IP y cada centro de datos de Azure.
- **Prioridad:** el tráfico se dirige al punto de conexión principal (prioridad más alta). Si el punto de conexión primario no está disponible, Traffic Manager enruta el tráfico al segundo punto de conexión. Si los puntos de conexión principal y secundario no están disponibles, el tráfico pasa al tercero, y así sucesivamente. La disponibilidad del punto de conexión se basa en el estado configurado (habilitado o deshabilitado) y en la supervisión continuada del punto de conexión.
- **Método round-robin ponderado:** para cada consulta, Traffic Manager elegirá aleatoriamente un punto de conexión disponible. La probabilidad de elegir un punto de conexión se basa en los pesos asignados a todos los puntos de conexión disponibles. Usar el mismo peso en todos los puntos de conexión dará como resultado una distribución de tráfico uniforme. El uso de pesos mayores o menores en puntos de conexión específicos hace que esos puntos de conexión se devuelvan con mayor o menor frecuencia en las respuestas de DNS.

En la siguiente imagen muestra una solicitud para una aplicación web que se dirige a un punto de conexión de la aplicación web. Los puntos de conexión también pueden ser otros servicios de Azure como Virtual Machines y Cloud Services.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

El cliente se conecta directamente a ese punto de conexión. Azure Traffic Manager detecta cuándo un punto de conexión es incorrecto y redirige a los clientes a otra instancia correcta. Para más información acerca de Traffic Manager, consulte el artículo [Introducción a Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

**Equilibrio de carga de la aplicación**

El servicio Azure Application Gateway proporciona un controlador de entrega de aplicaciones (ADC) como servicio. Application Gateway ofrece diversas funcionalidades de equilibrio de carga de nivel 7 (HTTP/HTTPS) para sus aplicaciones, incluido un firewall de aplicación web para proteger las aplicaciones web de amenazas y ataques. Application Gateway también permite optimizar la productividad de las granjas de servidores web traspasando la carga de la terminación SSL con mayor actividad de la CPU a la puerta de enlace de aplicaciones. 

Otras funcionalidades de enrutamiento de nivel 7 son la distribución round robin del tráfico entrante, la afinidad de sesiones basada en cookies, el enrutamiento basado en ruta de acceso de URL y la posibilidad de hospedar varios sitios web tras una única instancia de Application Gateway. Application Gateway puede configurarse como una puerta de enlace accesible desde Internet, una puerta de enlace solo para uso interno o una combinación de las dos. Application Gateway está completamente administrada por Azure, es escalable y tiene una elevada disponibilidad. Cuenta con un amplio conjunto de funcionalidades de diagnóstico y registro, lo que facilita su administración. Para más información sobre Application Gateway, consulte el artículo [Introducción a Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json).

La siguiente imagen muestra el enrutamiento basado en la ruta de la URL con Application Gateway:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Equilibrio de carga de red**

Azure Load Balancer proporciona equilibrio de carga de nivel 4 con latencia baja y rendimiento alto para todos los protocolos UDP y TCP. Administra conexiones entrantes y salientes. Puede configurar los puntos de conexión de carga equilibrada públicos e internos. Puede definir reglas para asignar conexiones entrantes a destinos de grupo de back-end con opciones de sondeo de estado TCP y HTTP para administrar la disponibilidad del servicio. Para más información sobre Load Balancer, consulte el artículo [Introducción a Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

La siguiente imagen muestra una aplicación de niveles múltiples con conexión a Internet que utiliza los equilibradores de carga externos e internos:

![Equilibrador de carga](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Seguridad

Puede filtrar el tráfico hacia y desde los recursos de Azure mediante las siguientes opciones:

- **Red:** puede implementar grupos de seguridad de red de Azure (NSG) para filtrar el tráfico entrante y saliente en los recursos de Azure. Cada grupo de seguridad de red contiene una o varias reglas de entrada y salida. Cada regla especifica las direcciones IP de origen, las direcciones IP de destino, el puerto y el protocolo con el que se filtra el tráfico. Los grupos de seguridad de red pueden aplicarse a subredes individuales y a máquinas virtuales individuales. Para más información sobre los grupos de seguridad de red, consulte el artículo [Introducción a los grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Aplicación:** al usar una instancia de Application Gateway con el firewall de aplicación web, puede proteger las aplicaciones web de amenazas y ataques. Algunos ejemplos comunes son ataques de inyección de código SQL, scripting entre sitios y encabezados con formato incorrecto. La puerta de enlace de aplicaciones filtra este tráfico y le impide llegar a los servidores web. Es posible configurar qué reglas desea que estén activadas. Se proporciona la capacidad de configurar las directivas de negociación de SSL para permitir que determinadas directivas se deshabiliten. Para más información sobre el servidor de aplicaciones web, consulte el artículo [Firewall de aplicaciones web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

Si necesita una funcionalidad de red que no proporciona Azure, o va a usar aplicaciones de red que se utilizan en local, puede implementar los productos en máquinas virtuales y conectarlos a la red virtual. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) contiene varias máquinas virtuales diferentes configuradas previamente con aplicaciones de red que es posible que esté utilizando. Estas máquinas virtuales configuradas previamente se conocen normalmente como aplicaciones virtuales de red (NVA). Las aplicaciones virtuales de red están disponibles con aplicaciones como el firewall y la optimización de WAN.

## <a name="routing"></a>Enrutamiento

Azure crea tablas de rutas predeterminadas que permiten a los recursos conectados a una subred de cualquier red virtual comunicarse entre sí. Puede implementar uno o ambos de los tipos siguientes de rutas para reemplazar las rutas predeterminadas que crea Azure:
- **Definidas por el usuario:** puede crear tablas de rutas personalizadas con las rutas que controlan a dónde se enruta el tráfico para cada subred. Para aprender más sobre las rutas definidas por el usuario, lea el artículo [Rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Border Gateway Protocol (BGP):** si conecta la red local a su red local mediante una conexión de Azure VPN Gateway o ExpressRoute, puede propagar las rutas BGP a sus redes virtuales. BGP es el protocolo de enrutamiento estándar usado habitualmente en Internet para intercambiar información de enrutamiento y disponibilidad entre dos o más redes. Cuando se utiliza en el contexto de instancias de Azure Virtual Network, BGP permite que instancias de Azure VPN Gateway y los dispositivos VPN locales, denominados vecinos o pares BGP, intercambien "rutas" que comunicarán a ambas puertas de enlace la disponibilidad y la posibilidad de que dichos prefijos pasen a través de las puertas de enlace o los enrutadores implicados. BGP también puede permitir el enrutamiento del tránsito entre varias redes mediante la propagación de las rutas que una puerta de enlace de BGP aprende de un par BGP a todos los demás pares BGP. Para más información sobre BGP, consulte el artículo [Información general de BGP con Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="manageability"></a>Manejabilidad

Azure proporciona las siguientes herramientas para supervisar y administrar las redes:
- **Registros de actividad:** todos los recursos de Azure tienen registros de actividad que proporcionan información acerca de las operaciones que llevan a cabo, el estado de las operaciones y quién inició la operación. Para más información sobre los registros de actividad, consulte el artículo [Información general sobre el registro de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Registros de diagnóstico:** los recursos de red crean eventos periódicos y espontáneos y estos se registran en las cuentas de almacenamiento de Azure, se envían a un centro de eventos de Azure o a Azure Log Analytics. Estos registros de diagnóstico proporcionan información acerca del estado de un recurso. Los registros de diagnóstico se proporcionan para Load Balancer (con conexión a Internet), los grupos de seguridad de red, las rutas y Application Gateway. Para más información acerca de los registros de diagnóstico, consulte el artículo [Introducción a los registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Métricas:** las métricas son medidas de rendimiento y contadores recopilados durante un período de tiempo en los recursos. Las métricas se pueden utilizar para desencadenar alertas basadas en umbrales. Las métricas están disponibles actualmente en Application Gateway. Para más información sobre las métricas, consulte el artículo de [introducción a las métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Solución de problemas:** se puede acceder directamente a la información para solucionar problemas en Azure Portal. Esta información ayuda a diagnosticar los problemas comunes con ExpressRoute, VPN Gateway, Application Gateway, registros de seguridad de red, rutas, DNS, Load Balancer y Traffic Manager.
- **Control de acceso basado en rol (RBAC):** controle quién puede crear y administrar recursos de red con el control de acceso basado en rol (RBAC). Obtenga más información sobre RBAC en el artículo [Introducción al control de acceso basado en roles en Azure Portal](../active-directory/role-based-access-control-what-is.md?toc=%2fazure%2fnetworking%2ftoc.json). 
- **Captura de paquetes:** el servicio Azure Network Watcher proporciona la capacidad de ejecutar una captura de paquetes en una máquina virtual a través de una extensión en la máquina virtual. La funcionalidad está disponible para máquinas virtuales Linux y Windows. Para más información sobre la captura de paquetes, consulte el artículo [Introducción a la captura de paquetes](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Comprobar flujos de IP:** Network Watcher le permite comprobar los flujos de IP entre una máquina virtual de Azure y un recurso remoto para determinar si los paquetes se permiten o deniegan. Esta funcionalidad proporciona a los administradores la capacidad de diagnosticar rápidamente problemas de conectividad. Para más información sobre cómo comprobar los flujos de IP, consulte el artículo [Introducción a la comprobación de flujo de IP de Azure Network Watcher](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Solucionar problemas de conectividad VPN:** la funcionalidad del solucionador de problemas de VPN de Network Watcher proporciona la capacidad de consultar una conexión o puerta de enlace y comprobar el estado de los recursos. Para más información sobre la solución de problemas de conexiones VPN, consulte la [Introducción a la solución de problemas de conectividad VPN](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Ver la topología de red:** consulte una representación gráfica de los recursos de red en una red virtual con Network Watcher. Para más información sobre cómo ver la topología de red, consulte el artículo [Introducción a la topología](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="tools"></a>Herramientas de implementación y configuración

Puede implementar y configurar los recursos de red de Azure con cualquiera de las siguientes herramientas:

- **Azure Portal:** una interfaz gráfica de usuario que se ejecuta en un explorador. Abra el [Azure Portal](http://portal.azure.com).
- **Azure PowerShell:** herramientas de línea de comandos para la administración de Azure desde equipos Windows. Para más información acerca de Azure PowerShell, consulte la [Introducción a Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json).
- **Interfaz de la línea de comandos (CLI) de Azure:** herramientas de la línea de comandos para administrar Azure desde equipos Linux, macOS o Windows. Para más información acerca de la CLI de Azure, consulte la [Introducción a la CLI de Azure](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json).
- **Plantillas de Azure Resource Manager:** un archivo (en formato JSON) que define la infraestructura y la configuración de una solución de Azure. Mediante una plantilla, puede implementar la solución repetidamente a lo largo del ciclo de vida y tener la seguridad de que los recursos se implementan de forma coherente. Para más información sobre la creación de plantillas, consulte [los procedimientos recomendados para la creación de plantillas](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json). Las plantillas pueden implementarse con Azure Portal, CLI o PowerShell. Para empezar a trabajar con plantillas de forma inmediata, implemente una de las muchas plantillas preconfiguradas en la biblioteca [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/?term=network). 

## <a name="pricing"></a>Precios

Algunos de los servicios de red de Azure tienen un cargo, mientras que otros son gratis. Consulte las páginas de precios de [Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network), [VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway), [Application Gateway](https://azure.microsoft.com/en-us/pricing/details/application-gateway/), [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer), [Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) y [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) para más información.

## <a name="next-steps"></a>Pasos siguientes

- Cree su primera red virtual y conecte algunas máquinas virtuales a ella, siguiendo los pasos descritos en el artículo [Creación de su primera red virtual](../virtual-network/virtual-network-get-started-vnet-subnet.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Conecte su equipo a una red virtual siguiendo los pasos descritos en el artículo [Configuración de una conexión de punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Equilibre la carga de tráfico de Internet en los servidores públicos siguiendo los pasos descritos en el artículo [Creación de un equilibrador de carga con conexión a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
