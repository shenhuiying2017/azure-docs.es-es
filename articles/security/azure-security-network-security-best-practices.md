---
title: Procedimientos recomendados de seguridad de la red de Azure | Microsoft Docs
description: "Este artículo proporciona un conjunto de procedimientos recomendados de seguridad de la red con funcionalidades de Azure integradas."
services: security
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3dee3411dadbca5e88951dec2ed1836d440423c4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="azure-network-security-best-practices"></a>Procedimientos recomendados de seguridad de la red de Azure
Microsoft Azure permite conectar máquinas virtuales y aplicaciones a otros dispositivos en red, colocándolos en instancias de Azure Virtual Network. Una instancia de Azure Virtual Network es una construcción que permite conectar tarjetas de interfaz de red virtual a una red virtual para posibilitar las comunicaciones basadas en TCP/IP entre dispositivos habilitados para red. Azure Virtual Machines conectadas a una instancia de Azure Virtual Network pueden conectarse a dispositivos en la misma instancia de Azure Virtual Network, distintas instancias de Azure Virtual Network, en Internet o, incluso, en sus propias redes locales.

En este artículo veremos un conjunto de procedimientos recomendados de seguridad de la red de Azure. Estos procedimientos recomendados se derivan de nuestra experiencia con las redes en Azure, y las experiencias de clientes como usted.

Para cada procedimiento recomendado, explicaremos:

* Qué es el procedimiento recomendado
* Por qué le conviene habilitar este procedimiento recomendado
* Cuál podría ser el resultado si no habilita el procedimiento recomendado
* Alternativas posibles al procedimiento recomendado
* Cómo aprender a habilitar el procedimiento recomendado

Este artículo de Procedimientos recomendados de seguridad de la red de Azure se basa en las funcionalidades y los conjuntos de características de la plataforma Azure existentes cuando se redactó. Las opiniones y las tecnologías cambian con el tiempo, por lo que se actualizará de forma periódica para reflejar esos cambios.

Los procedimientos recomendados de seguridad de la red de Azure descritos en este artículo incluyen:

* Segmentación lógica de subredes
* Control del comportamiento de enrutamiento
* Habilitación de la tunelización forzada
* Uso de aplicaciones de red virtual
* Implementación de redes perimetrales para zonas de seguridad
* Uso de vínculos WAN dedicados para evitar la exposición en Internet
* Optimización del rendimiento y el tiempo de actividad
* Uso del equilibrio de carga global
* Deshabilitación del acceso RDP a Azure Virtual Machines
* Habilitación de Azure Security Center
* Extensión de su centro de datos en Azure

## <a name="logically-segment-subnets"></a>Segmentación lógica de subredes
Las instancias de [Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) son similares a una LAN de la red local. La idea detrás de instancia de Azure Virtual Network es crear una sola red basada en espacios de direcciones IP privados en la que puede colocar todas las instancias de [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/). Los espacios de direcciones IP privados están en los intervalos de clase A (10.0.0.0/8), B (172.16.0.0/12) y C (192.168.0.0/16).

De modo similar a lo que lo se hace en local, debería segmentar el espacio de direcciones mayor en subredes. Puede usar los principios de subred basado en [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para crear las subredes.

El enrutamiento entre subredes se realizará automáticamente y no es necesario configurar manualmente las tablas de enrutamiento. Sin embargo, el valor predeterminado es que no hay ningún control de acceso a la red entre las subredes creadas en Azure Virtual Network. Para crear controles de acceso a la red entre subredes, debe poner algo entre las subredes.

Una de las cosas que puede usar para realizar esta tarea es un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) (NSG). Los NSG son dispositivos de inspección de paquetes con estado simple que utilizan el enfoque tupla 5 (IP de origen, puerto de origen, dirección IP de destino, puerto de destino y el protocolo de nivel 4) para crear reglas de permiso o denegación del tráfico de la red. Puede permitir o denegar el tráfico a y de una sola dirección IP, a y de varias direcciones IP o, incluso, a y de subredes enteras.

El uso de NSG para el control de acceso a la red entre subredes permite poner recursos que pertenecen a la misma zona de seguridad o función en sus propias subredes. Por ejemplo, imagine una aplicación sencilla de 3 niveles con un nivel de web, un nivel lógico de aplicación y un nivel de base de datos. Las máquinas virtuales que pertenecen a cada uno de estos niveles se colocan en sus propias subredes. A continuación, se usan los NSG para controlar el tráfico entre las subredes:

* Las máquinas virtuales del nivel de web solo pueden iniciar conexiones con las máquinas del nivel lógico de aplicación y aceptar conexiones de Internet.
* Las máquinas virtuales del nivel lógico de aplicación solo pueden iniciar conexiones con el nivel de base de datos y aceptar conexiones de la capa de web.
* Las máquinas virtuales de nivel de base de datos no pueden iniciar la conexión con nada fuera de su propia subred y solo pueden aceptar conexiones del nivel lógico de aplicación.

Para más información acerca de los grupos de seguridad de red y cómo puede usarlos para segmentar lógicamente las redes virtuales de Azure, lea el artículo [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Control del comportamiento de enrutamiento
Cuando coloca una máquina virtual en una instancia de Azure Virtual Network, observará que la máquina virtual puede conectarse a cualquier otra máquina virtual de la misma instancia de Azure Virtual Network, incluso si las otras máquinas virtuales están en subredes diferentes. El motivo es que hay una colección de rutas del sistema que están habilitadas de forma predeterminada y que permiten a este tipo de comunicación. Estas rutas predeterminadas permiten a las máquinas virtuales de la misma instancia de Azure Virtual Network iniciar conexiones entre sí y con Internet (solo para comunicaciones salientes a Internet).

Si bien las rutas del sistema predeterminadas son útiles para muchos escenarios de implementación, habrá veces en las que preferirá personalizar la configuración de enrutamiento para las implementaciones. Estas personalizaciones permiten configurar la dirección del próximo salto para que acceda a destinos específicos.

Se recomienda configurar rutas definidas por el usuario al implementar una aplicación de seguridad de la red virtual; en otro procedimiento recomendado, más adelante, se tratará este tema.

> [!NOTE]
> No son necesarias rutas definidas por el usuario y las rutas del sistema predeterminadas funcionan en la mayoría de lo casos.
>
>

Para más información sobre las rutas definidas por el usuario y cómo configurarlas, lea el artículo [¿Qué son las rutas definidas por el usuario y el reenvío IP?](../virtual-network/virtual-networks-udr-overview.md)

## <a name="enable-forced-tunneling"></a>Habilitación de la tunelización forzada
Para entender mejor la tunelización forzada, resulta útil entender qué es el "túnel dividido".
El ejemplo más común de túnel dividido se ve con conexiones VPN. Imagine que establece una conexión VPN de la habitación de hotel a su red corporativa. Esta conexión le permite conectarse a recursos de la red corporativa y todas las comunicaciones a estos pasan a través del túnel VPN.

¿Qué ocurre si desea conectarse a recursos de Internet? Cuando se habilita el túnel dividido, esas conexiones van directamente a Internet, no a través del túnel VPN. Algunos expertos en seguridad lo consideran un riesgo potencial y, por tanto, recomiendan deshabilitar el túnel dividido y que todas las conexiones, las destinadas a Internet y las destinadas a recursos corporativos, pasen a través del túnel VPN. La ventaja de hacerlo es que se fuerza que las conexiones a Internet pasen a través de los dispositivos de seguridad de la red corporativa, lo que no ocurriría si el cliente VPN se conectara a Internet fuera del túnel VPN.

Ahora volvamos a las máquinas virtuales en una instancia de Azure Virtual Network. Las rutas predeterminadas para una instancia de Azure Virtual Network permiten a las máquinas virtuales iniciar el tráfico a Internet. Esto también puede representar un riesgo de seguridad, ya que estas conexiones salientes podrían aumentar la superficie de ataque de una máquina virtual y los atacantes podrían aprovecharlo.
Por este motivo, se recomienda habilitar la tunelización forzada en las máquinas virtuales cuando tiene conectividad entre locales entre la instancia de Azure Virtual Network y su red local. Hablaremos acerca de conectividad entre entornos locales más adelante en este documento de procedimientos recomendados de redes de Azure.

Si no tiene ninguna conexión entre locales, asegúrese de que puede aprovechar los grupos de seguridad de red (descritos antes) o las aplicaciones de seguridad de la red virtual de Azure (descritas a continuación) para impedir las conexiones salientes a Internet desde sus Azure Virtual Machines.

Para más información sobre la tunelización forzada y cómo habilitarla, consulte el artículo [Configuración de tunelización forzada mediante PowerShell y Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Uso de aplicaciones de red virtual
Si bien los grupos de seguridad de red y el enrutamiento definido por el usuario pueden proporcionar un cierto grado de seguridad de la red en los niveles de red y de transporte del [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), habrá situaciones en la que deseará o necesitará habilitar la seguridad en los niveles altos de la pila. En tales situaciones, se recomienda implementar aplicaciones de seguridad de la red virtual proporcionadas por asociados de Azure.

Las aplicaciones de seguridad de la red de Azure pueden proporcionar niveles de seguridad mucho mejores que los proporcionados por los controles de nivel de red. Algunas de las funcionalidades de seguridad de la red proporcionadas por las aplicaciones de seguridad de la red virtual son:

* Firewalls
* Detección y prevención de intrusiones
* Administración de vulnerabilidades
* Control de aplicaciones
* Detección de anomalías basadas en la red
* Filtrado de web
* Antivirus
* Protección de redes de robots (botnets)

Si necesita un nivel de seguridad de la red mayor que el que se puede obtener con los controles de acceso al nivel de red, se recomienda investigar e implementar aplicaciones de seguridad de la red virtual de Azure.

Para aprender acerca de qué las aplicaciones de seguridad de la red virtual están disponibles y sus funcionalidades, visite [Azure Marketplace](https://azure.microsoft.com/marketplace/) y busque "seguridad" y "seguridad de la red".

## <a name="deploy-dmzs-for-security-zoning"></a>Implementación de redes perimetrales para zonas de seguridad
Una red perimetral es un segmento de red físico o lógico que está diseñado para proporcionar un nivel de seguridad adicional entre los recursos e Internet. El objetivo de la red perimetral es colocar dispositivos de control de acceso a la red especializados en el borde de la red perimetral, de forma que solo se permita al tráfico deseado pasar por el dispositivo de seguridad de la red y en la instancia de Azure Virtual Network.

Las redes perimetrales son útiles porque permiten centrar la administración, supervisión, registro y generación de informes sobre los dispositivos del control de acceso a la red en el borde de la instancia de Azure Virtual Network. Aquí normalmente habilitaría la prevención DDoS, los sistemas de prevención y detección de intrusiones (IDS/IPS), las reglas y directivas de firewall, el filtrado web, el antimalware de la red, etc. Los dispositivos de seguridad de la red se sitúan entre Internet y la instancia de Azure Virtual Network y tienen una interfaz en ambas redes.

Aunque este es el diseño básico de una red perimetral, existen muchos diseños diferentes, como configuración opuesta, con triple alojamiento, con múltiple alojamiento, etc.

Para todas las implementaciones de alta seguridad, se recomienda tener en cuenta la posibilidad de implementar una red perimetral para mejorar el nivel de seguridad de la red para los recursos de Azure.

Para más información acerca de las redes perimetrales y cómo implementarlas en Azure, consulte el artículo [Microsoft Cloud Services y seguridad de red](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Uso de vínculos WAN dedicados para evitar la exposición en Internet
Muchas organizaciones han elegido la ruta de TI híbrida. En la TI híbrida, algunos de los recursos de información de la compañía están en Azure, mientras que otros siguen siendo locales. En muchos casos, algunos componentes de un servicio se ejecutan en Azure, mientras que otros componentes siguen siendo locales.

En el escenario de TI híbrida, suele haber algún tipo de conectividad entre locales. Esta conectividad entre locales permite a la compañía conectar sus redes locales con redes virtuales de Azure. Hay dos soluciones de conectividad entre locales:

* VPN de sitio a sitio
* ExpressRoute

[VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md) representa una conexión privada virtual entre su red local y una instancia de Azure Virtual Network. Esta conexión tiene lugar a través de Internet y permite colocar la información en un "túnel" dentro de un vínculo cifrado entre la red y Azure. La VPN de sitio a sitio es una tecnología segura y madura que empresas de todos los tamaños han implementado durante décadas. El cifrado del túnel se realiza con el [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Si bien la VPN de sitio a sitio es una tecnología de confianza y establecida, el tráfico del túnel no atraviesa Internet. Además, el ancho de banda está relativamente limitado a un máximo de aproximadamente 200 Mbps.

Si necesita un nivel de seguridad o de rendimiento excepcional para las conexiones entre locales, se recomienda utilizar Azure ExpressRoute para su conectividad. ExpressRoute es un vínculo de WAN dedicada entre su ubicación local o un proveedor de hospedaje de Exchange. Se trata de una conexión de telecomunicaciones, los datos no viajan a través de Internet y, por tanto, no se exponen a los posibles riesgos inherentes a las comunicaciones de Internet.

Para más información sobre cómo funciona Azure ExpressRoute y cómo implementarlo, consulte el artículo [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optimización del rendimiento y el tiempo de actividad
La confidencialidad, la integridad y la disponibilidad (CIA) constituyen la triada del modelo de seguridad más influyente en la actualidad. La confidencialidad se refiere al cifrado y la privacidad, la integridad consiste en garantizar que personas no autorizadas no modifiquen los datos, y la disponibilidad consiste en asegurarse de que las personas autorizadas puedan acceder a la información para la que tengan autorización. Un error en cualquiera de estas áreas representa una posible infracción de seguridad.

La disponibilidad puede referirse al tiempo de actividad y el rendimiento. Si un servicio está inactivo, no puede accederse a la información. Si el rendimiento es tan bajo que no se pueden utilizar los datos, podemos considerar que los datos son inaccesibles. Por lo tanto, desde una perspectiva de seguridad, necesitamos hacer todo lo posible para asegurarnos de que nuestros servicios tienen un rendimiento y un tiempo de actividad óptimos.
Un método popular y eficaz usado para mejorar la disponibilidad y el rendimiento es usar el equilibrio de carga. El equilibrio de carga es un método para distribuir el tráfico de la red entre los servidores que forman parte de un servicio. Por ejemplo, si tiene servidores web front-end que forman parte de su servicio, puede usar el equilibrio de carga para distribuir el tráfico entre ellos.

Esta distribución del tráfico aumenta la disponibilidad, ya que si uno de los servidores web deja de estar disponible, el equilibrio de carga deja de enviarle tráfico y lo redirige a los servidores que aún están en línea. El equilibrio de carga también mejora el rendimiento, ya que la sobrecarga del procesador, la red y la memoria para atender a las solicitudes se distribuye entre todos los servidores con carga equilibrada.

Se recomienda usar el equilibrio de carga siempre que se pueda y, según sea adecuado para los servicios. Trataremos la idoneidad en las secciones siguientes; en el nivel de Azure Virtual Network, Azure proporciona tres opciones principales para el equilibrio de carga:

* Equilibrio de carga basado en HTTP
* Equilibrio de carga externo
* Equilibrio de carga interno

## <a name="http-based-load-balancing"></a>Equilibrio de carga basado en HTTP
El equilibrio de carga basado en HTTP toma las decisiones acerca de a qué servidor enviar las conexiones de acuerdo con las características del protocolo HTTP. Azure tiene un equilibrador de carga HTTP llamado Azure Application Gateway.

Se recomienda usar la Puerta de enlace de aplicaciones de Azure cuando hay:

* Aplicaciones que requieren solicitudes de la misma sesión de usuario o cliente para llegar a la misma máquina virtual back-end. Ejemplos de esto serían las aplicaciones de carro de la compra y los servidores de correo web.
* Aplicaciones que desean liberar las granjas de servidores web de la sobrecarga de la terminación SSL mediante la característica [Descarga SSL](https://f5.com/glossary/ssl-offloading) de Application Gateway.
* Aplicaciones, como la red de entrega de contenido, que requieren que varias solicitudes HTTP en la misma conexión TCP de ejecución prolongada se enruten a servidores backend diferentes o su carga se equilibre entre estos.

Para más información sobre cómo funciona Azure Application Gateway y el modo en que puede usarlo en las implementaciones, lea el artículo [Introducción a Application Gateway](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Equilibrio de carga externo
El equilibrio de carga externo tiene lugar cuando se realiza una carga equilibrada de las conexiones entrantes de Internet entre los servidores situados en una instancia de Azure Virtual Network. El equilibrio de carga externo de Azure puede proporcionar esta funcionalidad, y se recomienda utilizarlo cuando no se requieren sesiones persistentes o la descarga SSL.

A diferencia de equilibrio de carga basado en HTTP, el equilibrio de carga externo utiliza información en los niveles de red y transporte del modelo de redes OSI para tomar decisiones sobre en qué servidor equilibrar la carga de la conexión.

Recomendamos usar el equilibrio de carga externo siempre que tenga [aplicaciones sin estado](http://whatis.techtarget.com/definition/stateless-app) que aceptan solicitudes entrantes de Internet.

Para más información sobre cómo funciona el equilibrio de carga externo de Azure y cómo implementarlo, lea el artículo [Introducción a la creación de un equilibrio de carga orientado a Internet en Resource Manager con PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Equilibrio de carga interno
El equilibrio de carga interno es similar al equilibrio de carga externo y utiliza el mismo mecanismo para equilibrar las conexiones a los servidores tras ellas. La única diferencia es que, en este caso, el equilibrio de carga acepta conexiones procedentes de máquinas virtuales que no están en Internet. En la mayoría de los casos, las conexiones que se aceptan para el equilibrio de carga son iniciadas por dispositivos en una instancia de Azure Virtual Network.

Se recomienda utilizar el equilibrio de carga interno para escenarios que se benefician de esta funcionalidad, como cuando necesita equilibrar las conexiones a servidores SQL o servidores web internos.

Para más información sobre cómo funciona el equilibrio de carga interno de Azure y cómo se puede implementar, lea el artículo [Introducción a la creación de un equilibrio de carga orientado a Internet en Resource Manager con PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Uso del equilibrio de carga global
El proceso de la nube pública permite implementar globalmente aplicaciones distribuidas que tienen componentes ubicados en centros de datos de todo el mundo. Esto es posible en Microsoft Azure gracias a la presencia de un centro de datos global de Azure. A diferencia de la tecnologías de equilibrio de carga mencionadas anteriormente, el equilibrio de carga global permite que los servicios estén disponibles incluso aunque ninguno de los centros de datos esté disponible.

Puede obtener este tipo de equilibrio de carga global en Azure mediante [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/). El Administrador de tráfico permite equilibrar las conexiones a los servicios según la ubicación del usuario.

Por ejemplo, si el usuario realiza una solicitud a su servicio desde la Unión Europea, la conexión se dirige a sus servicios situados en un centro de datos de la Unión Europea. Esta parte del equilibrio de carga global del Administrador de tráfico ayuda a mejorar el rendimiento, ya que la conexión al centro de datos más cercano es más rápida que a los centros de datos que están lejos.

En relación con la disponibilidad, el equilibrio de carga global garantiza que el servicio esté disponible incluso si todo un centro de datos deja de estarlo.

Por ejemplo, si un centro de datos de Azure deja de estar disponible por motivos ambientales o debido a interrupciones (como errores en la red regional), las conexiones al servicio se vuelven a enrutar al centro de datos en línea más cercano. Este equilibrio de carga global se logra al aprovechar las directivas DNS que se pueden crear en el Administrador de tráfico.

Se recomienda usar el Administrador de tráfico para cualquier solución en la nube que desarrolle, tenga un ámbito ampliamente distribuido en varias regiones y requiera el tiempo de actividad más alto posible.

Para más información sobre Azure Traffic Manager y cómo implementarlo, lea el artículo [¿Qué es Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Deshabilitación del acceso RDP o SSH para Azure Virtual Machines
Es posible obtener acceso a Azure Virtual Machines mediante los protocolos [Protocolo de escritorio remoto](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) y [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Estos protocolos permiten administrar máquinas virtuales desde ubicaciones remotas y son estándar para calcular el centro de datos.

El posible problema de seguridad con el uso de estos protocolos a través de Internet es que los atacantes pueden utilizar diversas técnicas [por fuerza bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para obtener acceso a Azure Virtual Machines. Una vez que los atacantes obtienen acceso, pueden utilizar la máquina virtual como punto de inicio para poner en peligro otros equipos de Azure Virtual Network o incluso atacar dispositivos en red fuera de Azure.

Por este motivo, se recomienda deshabilitar el acceso directo de RDP y SSH a Azure Virtual Machines desde Internet. Cuando se deshabilita el acceso directo de RDP y SSH desde Internet, tiene otras opciones que puede utilizar para acceder a estas máquinas virtuales para la administración remota:

* VPN de punto a sitio
* VPN de sitio a sitio
* ExpressRoute

[VPN de punto a sitio](../vpn-gateway/vpn-gateway-point-to-site-create.md) es otro término para una conexión cliente/servidor de VPN con acceso remoto. Una VPN de punto a sitio permite a un solo usuario conectarse a una instancia de Azure Virtual Network a través de Internet. Una vez establecida la conexión de punto a sitio, el usuario podrá usar RDP o SSH para conectarse a cualquier máquina virtual situada en la instancia de Azure Virtual Network a la que el usuario se conectó mediante la VPN de punto a sitio. Esto supone que el usuario está autorizado para acceder a dichas máquinas virtuales.

La VPN de punto a sitio es más segura que las conexiones de RDP o SSH directas, ya que el usuario tiene que autenticarse dos veces antes de conectarse a una máquina virtual. En primer lugar, el usuario debe autenticar (y ser autorizado) para establecer la conexión VPN de sitio a punto; en segundo lugar, el usuario debe autenticar (y ser autorizado) para establecer la sesión RDP o SSH.

Una [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md) conecta toda una red a otra a través de Internet. Puede usar una VPN de sitio a sitio para conectar su red local a una instancia de Azure Virtual Network. Si va a implementar una VPN de sitio a sitio, los usuarios de la red local podrán conectarse a máquinas virtuales de la instancia de Azure Virtual Network mediante el protocolo RDP o SSH a través de la conexión VPN de sitio a sitio; no requiere que permita el acceso RDP o SSH directo a través de Internet.

También puede utilizar un vínculo WAN dedicado para ofrecer una funcionalidad similar a la VPN de sitio a sitio. Las principales diferencias son: 1. El vínculo WAN dedicado no recorre Internet, y 2. Los vínculos WAN dedicados suelen ser más estables y eficaces. Azure proporciona una solución de vínculo WAN dedicado mediante [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Habilitación de Azure Security Center
Azure Security Center ayuda a evitar, detectar y responder a amenazas, al tiempo que proporciona más visibilidad y control de la seguridad de los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

Azure Security Center le ayuda a optimizar y controlar la seguridad de la red al:

* Proporcionar recomendaciones de seguridad de la red
* Supervisar el estado de la configuración de seguridad de la red
* Alertar de las amenazas basadas en la red en los niveles de red y de punto de conexión

Recomendamos encarecidamente habilitar Azure Security Center para todas las implementaciones de Azure.

Para aprender más acerca de Azure Security Center y cómo habilitarlo para las implementaciones, lea el artículo [Introducción a Azure Security Center](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Extensión de su centro de datos en Azure de forma segura
Muchas organizaciones de TI desean expandirse en la nube en lugar de hacer que crezcan sus centros de datos locales. Esta expansión representa una extensión de la infraestructura de TI existente en la nube pública. Al aprovechar las opciones de conectividad entre locales, es posible tratar Azure Virtual Network como otra subred en la infraestructura de red local.

Sin embargo, hay mucho planeamiento y problemas de diseño que deben solucionarse antes. Esto es especialmente importante en el área de seguridad de la red. Una de las mejores formas de comprender cómo enfocar dicho diseño es ver un ejemplo.

Microsoft creó el [Datacenter Extension Reference Architecture Diagram](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) (Diagrama de arquitectura de referencia de extensión de centro de datos) y otro material auxiliar para ayudarle a comprender cuál sería la apariencia de una extensión del centro de datos. Esto proporciona un ejemplo de implementación de referencia que puede usar para planear y diseñar una extensión del centro de datos empresarial seguro en la nube. Se recomienda leer este documento para hacerse una idea de los componentes clave de una solución segura.

Para más información sobre cómo extender su centro de datos de forma segura en Azure, vea el vídeo [Extending Your Datacenter to Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA) (Extensión de su centro de datos a Microsoft Azure).
