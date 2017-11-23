---
title: Conceptos y requisitos de seguridad de red en Azure| Microsoft Docs
description: " Este artículo permite comprender de forma sencilla la oferta de Microsoft Azure en el área de la seguridad de red. Proporcionamos explicaciones básicas de los conceptos y requisitos fundamentales de la seguridad de red, así como información sobre lo que puede ofrecer Azure en cada una de estas áreas. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: cefc15e7df0dabd9229196d0175dcf6546a6ebce
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="azure-network-security-overview"></a>Azure Network Security Overview (Información general sobre Azure Network Security)
Microsoft Azure incluye una sólida infraestructura de red que respalda sus requisitos de conectividad de aplicaciones y servicios. Es posible la conectividad de red entre recursos ubicados en Azure, entre recursos locales y hospedados en Azure y entre Internet y Azure.

El objetivo de este artículo es que pueda comprender de forma más fácil lo que ofrece Microsoft Azure en el área de la seguridad de red. Aquí ofrecemos una explicación básica de los conceptos y requisitos fundamentales de la seguridad de red. También proporcionamos información sobre lo que Azure ofrece en cada una de estas áreas, así como vínculos para ayudarle a obtener una comprensión más profunda de las áreas de interés.

Este artículo de información general sobre la seguridad de red en Azure se centra en las siguientes áreas:

* Redes de Azure
* Control de acceso de red
* Protección del acceso remoto y la conectividad local
* Disponibilidad
* Resolución de nombres
* Arquitectura de red perimetral (DMZ)
* Detección de amenazas y supervisión


## <a name="azure-networking"></a>Redes de Azure
Las máquinas virtuales necesita conectividad de red. Para satisfacer este requisito, es necesario que Azure Virtual Machines esté conectado a una instancia de Azure Virtual Network. Azure Virtual Network es una construcción lógica creada encima del tejido de red físico de Azure. Cada instancia lógica de Azure Virtual Network está aislada de todas las demás redes virtuales de Azure. Esto contribuye a garantizar que otros clientes de Microsoft Azure no puedan acceder al tráfico de red de sus implementaciones.

Más información:

* [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Control de acceso de red
El control de acceso de red es el acto de limitar la conectividad entre subredes o dispositivos específicos dentro de una instancia de Azure Virtual Network. El objetivo del control de acceso de red es limitar el acceso a las máquinas virtuales y los servicios a los usuarios y dispositivos aprobados. Los controles de acceso se basan en decisiones de permiso o denegación a y desde la máquina virtual o el servicio.

Azure admite varios tipos de controles de acceso de red, como:

* Control de capa de red
* Control de ruta y tunelización forzada
* Dispositivos de seguridad de red virtual

### <a name="network-layer-control"></a>Control de capa de red
Toda implementación segura requiere alguna medida de control del acceso a la red. El objetivo del control de acceso de red es restringir la comunicación de la máquina virtual con los sistemas necesarios y que se bloqueen otros intentos de comunicación.

Si necesita un control de acceso de nivel de red básico (basado en la dirección IP y los protocolos TCP o UDP), puede usar entonces grupos de seguridad de red. Un grupo de seguridad de red (NSG) es un firewall de filtrado de paquetes básico con estado que le permite controlar el acceso basado en una [5-tupla](https://www.techopedia.com/definition/28190/5-tuple). Los NSG no proporcionan inspección de nivel de aplicación ni controles de acceso autenticados.

Más información:

* [Grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Control de ruta y tunelización forzada
La posibilidad de controlar el comportamiento de enrutamiento en Azure Virtual Network es una funcionalidad crítica del control de acceso y la seguridad de red. Si el enrutamiento no está configurado correctamente, las aplicaciones y los servicios hospedados en la máquina virtual podrían conectarse a dispositivos no autorizados, incluidos los sistemas que son propiedad de atacantes potenciales u operados por ellos.

Las redes de Azure ofrecen la posibilidad de personalizar el comportamiento de enrutamiento del tráfico de red en Azure Virtual Network. Por ejemplo, puede modificar las entradas de tabla de enrutamiento predeterminadas en la instancia de Azure Virtual Network. El control del comportamiento de enrutamiento ayuda a garantizar que todo el tráfico procedente de un determinado dispositivo o grupo de dispositivos entra o sale de la red virtual a través de una ubicación específica.

Por ejemplo, suponga que tiene un dispositivo de seguridad de red virtual en la instancia de Azure Virtual Network. Quiere asegurarse de que todo el tráfico que entra y sale de la instancia de Azure Virtual Network pase por el dispositivo de seguridad virtual. Para ello, puede configurar [rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md) en Azure.

[tunelización forzada](https://www.petri.com/azure-forced-tunneling) es un mecanismo que puede usar para tener la seguridad de que no se permite que sus servicios inicien una conexión con dispositivos en Internet. Tenga en cuenta que este proceso no es lo mismo que aceptar conexiones entrantes y luego responder a ellas. En este caso, los servidores web front-end tienen que responder a las solicitudes de los hosts de Internet, así que se permite que el tráfico cuyo origen es Internet entre en estos servidores web y que dichos servidores respondan.

Lo que no quiere es permitir que un servidor web front-end inicie una solicitud saliente. Estas solicitudes pueden representar un riesgo para la seguridad porque estas conexiones podrían usarse para descargar software malintencionado. Incluso si quiere que estos servidores front-end inicien solicitudes salientes a Internet, puede que quiera obligarles a que pasen por los proxies web locales para así aprovechar el filtrado y el registro de direcciones URL.

En su lugar, y para evitar esto, querrá usar la tunelización forzada. Cuando se habilita la tunelización forzada, todas las conexiones a Internet tienen que pasar a la fuerza por la puerta de enlace local. Puede configurar la tunelización forzada aprovechando las rutas definidas por el usuario.

Más información:

* [¿Qué son las rutas definidas por el usuario y el reenvío IP?](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Dispositivos de seguridad de red virtual
Aunque los grupos de seguridad de red, las rutas definidas por el usuario y la tunelización forzada proporcionan un nivel de seguridad en las capas de red y transporte del [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), habrá ocasiones en las que desee habilitar la seguridad en niveles más altos que la red.

Por ejemplo, sus requisitos de seguridad podrían incluir:

* Autenticación y autorización antes de permitir el acceso a la aplicación
* Detección de intrusiones y respuesta a estas
* Inspección de la capa de aplicación para comprobar la existencia de protocolos de alto nivel
* Filtrado para direcciones URL
* Antimalware y antivirus de nivel de red
* Protección contra robots
* Control de acceso a las aplicaciones
* Protección adicional de DDoS (encima de la protección de DDoS proporcionada por el mismo tejido de Azure)

Puede tener acceso a estas características de seguridad de red mejoradas mediante el uso de una solución de socio de Azure. Para encontrar las soluciones de seguridad de red de asociados más actuales de Azure, visite [Azure Marketplace](https://azure.microsoft.com/marketplace/) y busque "seguridad" y "seguridad de la red".

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acceso remoto seguro y conectividad entre entornos
La instalación, la configuración y la administración de los recursos de Azure se han de realizar de forma remota. Además, puede que quiera implementar soluciones de [TI híbrida](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) que tengan componentes locales y en la nube pública de Azure. Estos escenarios requieren acceso remoto seguro.

Las redes de Azure admiten los siguientes escenarios de acceso remoto seguro:

* Conexión de estaciones de trabajo individuales a una instancia de Azure Virtual Network
* Conexión de la red local a una instancia de Azure Virtual Network con una VPN
* Conexión de la red local a Azure Virtual Network con un vínculo WAN dedicado
* Conexión de redes virtuales de Azure entre sí

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Conexión de estaciones de trabajo individuales a una instancia de Azure Virtual Network
Puede haber ocasiones en que quiera que determinados desarrolladores o miembros del personal de operaciones administren máquinas virtuales y servicios en Azure. Por ejemplo, quizás necesite acceso a una máquina virtual en una instancia de Azure Virtual Network y la directiva de seguridad no permite acceso remoto RDP o SSH a máquinas virtuales individuales. En este caso, puede usar una conexión VPN de punto a sitio.

La conexión VPN de punto a sitio emplea el protocolo [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) para permitirle configurar una conexión privada y segura entre el usuario y la instancia de Azure Virtual Network. Después de que se ha establecido la conexión VPN, el usuario podrá usar el protocolo RDP o SSH mediante el vínculo VPN para conectarse a cualquier máquina virtual de Azure Virtual Network (suponiendo que el usuario pueda autenticarse y esté autorizado).

Más información:

* [Configuración de una conexión punto a sitio a una red virtual mediante PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Conexión de la red local a una instancia de Azure Virtual Network con una VPN
Quizás quiera conectar la red corporativa completa, o algunas de sus partes, a una instancia de Azure Virtual Network. Esto es habitual en escenarios de TI híbridos donde las empresas [amplían su centro de datos local a Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). En muchos casos, las empresas hospedarán partes de un servicio en Azure y partes en el entorno local, como cuando una solución incluye servidores web front-end en Azure y bases de datos de back-end en local. Estos tipos de conexiones "entre locales" también hacen que la administración de recursos ubicados en Azure sea más segura y permiten escenarios como la ampliación de controladores de dominio de Active Directory a Azure.

Una manera de lograr esto es usar una [VPN de sitio a sitio](https://www.techopedia.com/definition/30747/site-to-site-vpn). La diferencia entre una VPN de sitio a sitio y una VPN de punto a sitio es que la segunda conecta un único dispositivo a Azure Virtual Network, mientras que la primera conecta una red entera (por ejemplo, la red local) a Azure Virtual Network. Las VPN de sitio a sitio a una instancia de Azure Virtual Network emplean el protocolo VPN de modo de túnel IPsec de alta seguridad.

Más información:

* 
            [Creación de una red virtual con una conexión VPN de sitio a sitio mediante Azure Portal y Azure Resource Manager](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Planeamiento y diseño de puerta de enlace de VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Conexión de la red local a una instancia de Azure Virtual Network con un vínculo WAN dedicado
Las conexiones VPN de punto a sitio y de sitio a sitio son eficaces para permitir la conectividad entre locales. Sin embargo, algunas organizaciones consideran que presentan las siguientes desventajas:

* Las conexiones VPN mueven los datos a través de Internet; como consecuencia, estas conexiones se exponen a potenciales problemas de seguridad relacionados con el movimiento de los datos a través de una red pública. Además, no se puede garantizar la confiabilidad y disponibilidad de las conexiones a Internet.
* Las conexiones VPN a instancias de Azure Virtual Networks se pueden considerar de ancho de banda limitado en algunas aplicaciones y para algunos fines, ya que agotan el límite en torno a 200 Mbps.

Las organizaciones que necesitan el más alto nivel de seguridad y disponibilidad para sus conexiones entre locales, suelen usar vínculos WAN dedicados para la conexión a sitios remotos. Azure ofrece la posibilidad de usar un vínculo WAN dedicado que se puede emplear para conectar la red local a una instancia de Azure Virtual Network. Para ello se utiliza ExpressRoute de Azure.

Más información:

* [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Conexión de redes virtuales de Azure entre sí
Es posible utilizar varias instancias de Azure Virtual Networks en sus implementaciones. Hay muchas razones para hacer esto. Quizás quiera simplificar la administración, o sus motivos podrían estar relacionados con la seguridad. Con independencia de los motivos o razones para colocar los recursos en redes virtuales de Azure diferentes, puede haber ocasiones en que quiera que los recursos de cada una de las redes se conecten entre sí.

Una opción sería conectar los servicios de una instancia de Azure Virtual Network con los servicios de otra creando un "bucle de retroceso" a través de Internet. La conexión comenzaría en una instancia de Azure Virtual Network, pasaría por Internet y volvería a la red virtual de destino. Esta opción expone la conexión a los problemas de seguridad inherentes a toda comunicación basada en Internet.

Una opción mejor podría ser crear una VPN de sitio a sitio entre redes virtuales de Azure. Esta VPN usa el mismo protocolo de [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) que la conexión VPN de sitio a sitio entre locales mencionada anteriormente.

La ventaja de utilizar una VPN de sitio a sitio entre redes virtuales de Azure es que la conexión VPN se establece a través del tejido de red de Azure y no mediante la conexión a través de Internet. Esto proporciona un nivel adicional de seguridad en comparación con las VPN de sitio a sitio que se conectan a través de Internet.

Más información:

* [Configuración de una conexión entre dos redes virtuales mediante Azure Resource Manager y PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilidad
La disponibilidad es un componente clave de cualquier programa de seguridad. Si los usuarios y los sistemas no pueden acceder a lo que necesitan a través de la red, el servicio puede considerarse en peligro. Azure ofrece tecnologías de red que admiten los siguientes mecanismos de alta disponibilidad:

* Equilibrio de carga basado en HTTP
* Equilibrio de carga de nivel de red
* Equilibrio de carga global

El equilibrio de carga es un mecanismo diseñado para distribuir equitativamente las conexiones entre varios dispositivos. Los objetivos del equilibrio de carga son:

* Aumentar la disponibilidad: al equilibrar la carga de las conexiones entre varios dispositivos, si uno o varios de los dispositivos dejan de estar disponibles, los servicios que se ejecutan en el resto de dispositivos en línea pueden seguir suministrando el contenido desde el servicio.
* Aumentar el rendimiento: cuando se equilibra la carga de las conexiones entre varios dispositivos, un solo dispositivo no tiene que carga el saldo conexiones en varios dispositivos, un único dispositivo no tiene que asumir toda la carga del procesador. En su lugar, las demandas de procesamiento y memoria para el suministro de contenido se reparten entre varios dispositivos.

### <a name="http-based-load-balancing"></a>Equilibrio de carga basado en HTTP
Las organizaciones que ejecutan servicios web, desean a menudo tener un equilibrador de carga HTTP delante de esos servicios con el fin de ayudar a garantizar unos niveles adecuados de rendimiento y alta disponibilidad. A diferencia de los equilibradores de carga de red tradicionales, las decisiones sobre el equilibrio de carga tomadas por los equilibradores de carga HTTP se basan en las características del protocolo HTTP, no en las de los protocolos de capa de transporte y red.

Con el fin de proporcionar equilibrio de carga HTTP para los servicios web, Azure ofrece la Puerta de enlace de aplicaciones. La Puerta de enlace de aplicaciones de Azure admite:

* Equilibrio de carga HTTP: las decisiones sobre el equilibrio de carga se toman en función de características especiales del protocolo HTTP.
* Afinidad de sesión basada en cookies: esta funcionalidad garantiza que las conexiones establecidas con uno de los servidores detrás del equilibrador de carga permanecen intactas entre el cliente y el servidor. Esto asegura la estabilidad de las transacciones.
* Descarga SSL: cuando se establece una conexión de cliente con el equilibrador de carga, esa sesión entre el cliente y el equilibrador de carga se cifra mediante el protocolo HTTPS (SSL/). Sin embargo, para aumentar el rendimiento, tiene la opción de hacer que la conexión entre el equilibrador de carga y el servidor web que está detrás use el protocolo HTTP (sin cifrar). Esto se conoce como "descarga de SSL" porque los servidores web que hay detrás del equilibrador de carga no experimentan la sobrecarga del procesador implicada en el cifrado y, por tanto, deberían poder atender las solicitudes con mayor rapidez.
* Enrutamiento de contenido basado en URL: esta característica hace posible que el equilibrador de carga tome decisiones sobre dónde reenviar las conexiones en función de la dirección URL de destino. Este método proporciona mucha más flexibilidad que las soluciones que toman decisiones sobre el equilibrio de carga según las direcciones IP.

Más información:

* [Introducción a Puerta de enlace de aplicaciones](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Equilibrio de carga de nivel de red
A diferencia del equilibrio de carga basado en HTTP, el equilibrio de carga de nivel de red toma las decisiones en función de la dirección IP y el número de puerto (TCP o UDP).
Puede beneficiarse del equilibrio de carga de nivel de red en Azure con Azure Load Balancer. Algunas características importantes de Azure Load Balancer son:

* Equilibrio de carga de nivel de red basado en la dirección IP y el número de puerto.
* Compatibilidad con cualquier protocolo de capa de aplicación.
* Equilibra la carga en máquinas virtuales e instancias de rol de servicios en la nube de Azure.
* Puede utilizarse en aplicaciones y máquinas virtuales accesibles desde Internet (equilibrio de carga externo) y no accesibles desde Internet (equilibrio de carga interno).
* Supervisión de puntos de conexión, que se utiliza para determinar si alguno de los servicios que hay detrás del equilibrador de carga ha dejado de estar disponible.

Más información:

* [Equilibrador de carga accesible desde Internet entre varias máquinas virtuales o servicios](../load-balancer/load-balancer-internet-overview.md)
* [Información general sobre el equilibrador de carga interno](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Equilibrio de carga global
Algunas organizaciones querrán que el más alto de disponibilidad posible. Una manera de lograr este objetivo es hospedar aplicaciones en centros de datos distribuidos globalmente. Cuando una aplicación está hospedada en centros de datos repartidos por todo el mundo, una región geopolítica entera puede dejar de estar disponible pero la aplicación seguir funcionando.

Además de las ventajas de disponibilidad que se obtienen al hospedar las aplicaciones en centros de datos distribuidos globalmente, también puede obtener ventajas de rendimiento. Estas ventajas de rendimiento se pueden obtener mediante un mecanismo que dirige las solicitudes del servicio al centro de datos más cercano al dispositivo que realiza la solicitud.

El equilibrio de carga global puede proporcionarle ambas ventajas. En Azure, puede conseguir los beneficios del equilibrio de carga global mediante el Administrador de tráfico de Azure.

Más información:

* [¿Qué es el Administrador de tráfico?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Resolución de nombres
La resolución de nombres es una función crítica para todos los servicios hospedados en Azure. Desde una perspectiva de la seguridad, poner en peligro esta función puede dar lugar a que un atacante redirija las solicitudes de sus sitios al sitio de dicho individuo. Proteger la resolución de nombres es un requisito de todos los servicios hospedados en la nube.

Hay dos tipos de resolución de nombres que debe abordar:

* Resolución de nombres interna: la resolución de nombres interna la usan los servicios en la instancia de Azure Virtual Network, las redes locales o ambas. Los nombres usados para la resolución de nombres interna no son accesibles a través de Internet. Para lograr una seguridad óptima, es importante que el esquema de la resolución de nombres interna no sea accesible a usuarios externos.
* Resolución de nombres externa: la resolución de nombres externa la utilizan las personas y los dispositivos fuera de las redes locales y virtuales de Azure. Son los nombres que son visibles en Internet y se usan para dirigir la conexión a los servicios basados en la nube.

Para la resolución de nombres interna, tiene dos opciones:

* Un servidor DNS de Azure Virtual Network: cuando se crea una nueva instancia de Azure Virtual Network, un servidor DNS se crea automáticamente. Este servidor DNS puede resolver los nombres de los equipos ubicados en esa instancia de Azure Virtual Network. Dicho servidor no es configurable y está administrado por el administrador de tejido de Azure, lo que hace que sea una solución de resolución de nombres segura.
* Traer su propio servidor DNS: tiene la opción de colocar un servidor DNS de su elección en Azure Virtual Network. Podría ser un servidor DNS integrado de Active Directory o una solución de servidor DNS dedicada proporcionada por un socio de Azure, que puede obtener en Azure Marketplace.

Más información:

* [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md)
* [Administración de servidores DNS usados por una red virtual](../virtual-network/virtual-network-manage-network.md#dns-servers)

Para la resolución DNS externa, tiene dos opciones:

* Hospedar su propio servidor DNS externo en el entorno local
* Hospedar su propio servidor DNS externo con un proveedor de servicios

Muchas organizaciones de gran tamaño hospedarán sus propios servidores DNS en el entorno local. Pueden hacerlo porque tienen la experiencia en redes y la presencia global para ello.

En la mayoría de los casos, es mejor hospedar los servicios de resolución de nombres DNS con un proveedor de servicios. Estos proveedores de servicios cuentan con la experiencia en redes y la presencia global para garantizar una disponibilidad muy alta de los servicios de resolución de nombres. La disponibilidad es esencial para los servicios DNS, ya que si se produce un error en los servicios de resolución de nombres, nadie podrá establecer comunicación con los servicios accesibles desde Internet.

Azure proporciona una solución DNS externa de alta disponibilidad y elevado rendimiento en forma de DNS de Azure. Esta solución de resolución de nombres externa aprovecha la infraestructura de DNS de Azure en todo el mundo. Permite hospedar un dominio en Azure con las mismas credenciales, API, herramientas y facturación que los demás servicios de Azure. Al ser parte de Azure, también hereda los fuertes controles de seguridad incorporados en la plataforma.

Más información:

* [Introducción a DNS de Azure](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Arquitectura de red perimetral (DMZ)
Muchas organizaciones empresariales usan una red perimetral para segmentar sus redes y crear una zona de protección entre Internet y sus servicios. La parte perimetral de la red se considera una zona de baja seguridad y ningún recurso de alto valor se coloca en ese segmento de red. Lo normal es que vea dispositivos de seguridad de red que tienen una interfaz de red en el segmento de red perimetral y otra interfaz de red conectada a una red que tiene máquinas virtuales y servicios que aceptan conexiones entrantes de Internet.

Existen algunas variaciones en el diseño de una red perimetral, y la decisión de implementar una y de qué tipo, si es que decide usar una, se basa en sus requisitos de seguridad de la red.

Más información:

* [Cloud Services de Microsoft y seguridad de red](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Detección de amenazas y supervisión

Azure proporciona funcionalidades para ayudarle en esta área clave con la detección temprana, la supervisión y la capacidad de recopilar y revisar el tráfico de red.

### <a name="azure-network-watcher"></a>Azure Network Watcher
Azure Network Watcher incluye un gran número de funcionalidades que le ayudarán a solucionar problemas, así como a proporcionar un nuevo conjunto completo de herramientas que le ayudarán a identificar los problemas de seguridad.

[La vista de grupo de seguridad ](/network-watcher/network-watcher-security-group-view-overview.md) ayuda con el cumplimiento de seguridad y auditoría de las máquinas virtuales y puede usarse para realizar auditorías mediante programación al comparar las directivas de las líneas de base definidas por su organización con las reglas vigentes para cada una de las máquinas virtuales. Esto puede ayudarle a identificar cualquier cambio en la configuración.

La [captura de paquetes](/network-watcher/network-watcher-packet-capture-overview.md) le permite capturar el tráfico de red hacia y desde la máquina virtual. Además de permitirle recopilar las estadísticas de la red y con la solución de problemas de captura de paquetes de problemas de la aplicación puede ser muy útil en la investigación de las intrusiones de red. También puede usar esta funcionalidad junto con Azure Functions para iniciar las capturas de red en respuesta a alertas específicas de Azure.

Para más información sobre Azure Network Watcher y cómo comenzar a probar algunas de las funciones en los laboratorios, eche un vistazo a la [introducción sobre supervisión de Azure Network Watcher](/network-watcher/network-watcher-monitoring-overview.md)

>[!NOTE]
Azure Network Watcher sigue en versión preliminar pública y puede no tener el mismo nivel de disponibilidad y confiabilidad que los servicios que están en versión de disponibilidad general. Puede que algunas características no se admitan, que tengan funcionalidades limitadas y que no estén disponibles en todas las ubicaciones de Azure. Para obtener las notificaciones más recientes sobre la disponibilidad y el estado de este servicio, consulte la página de [actualizaciones de Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center
Security Center ayuda a evitar amenazas y a detectar y responder a estas, y proporciona una mayor visibilidad y control sobre la seguridad de sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio conjunto de soluciones de seguridad.

Azure Security Center le ayuda a optimizar y controlar la seguridad de la red al:

* Proporcionar recomendaciones de seguridad de la red
* Supervisar el estado de la configuración de seguridad de la red
* Alertar de las amenazas basadas en la red en los niveles de red y de punto de conexión

Más información:

* [Introducción al Centro de seguridad de Azure](../security-center/security-center-intro.md)


### <a name="logging"></a>Registro
El registro en el nivel de red es una función clave en cualquier escenario de seguridad de red. En Azure, puede registrar la información obtenida de los grupos de seguridad de red a fin de obtener información de registro de nivel de red. Con el registro de NSG, obtiene información de:

* [Registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md): estos registros se utilizan para ver todas las operaciones enviadas a las suscripciones de Azure. Estos registros de auditoría están habilitados de forma predeterminada y se pueden ver en el Portal de Azure. Anteriormente se les llamaba "Registros de auditoría" o "Registros operativos".
* Los registros de eventos: estos registros proporcionan información sobre las reglas NSG que se aplicaron.
* Registros de contador: estos registros le permiten saber cuántas veces se aplica cada regla de grupos de seguridad de red para denegar o permitir el tráfico.

También puede usar [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), una eficaz herramienta de visualización de datos, para ver y analizar estos registros.

Más información:

* [Análisis del registro para grupos de seguridad de red (NSG)](../virtual-network/virtual-network-nsg-manage-log.md)
