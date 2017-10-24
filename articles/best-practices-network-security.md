---
title: Procedimientos recomendados de seguridad de la red de Azure | Microsoft Docs
description: "Obtenga información sobre las características clave disponibles en Azure para crear entornos de red seguros"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: fb5e399d4ab02a7f2805cc280b213bf5b44f6993
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-cloud-services-and-network-security"></a>Servicios en la nube de Microsoft y seguridad de red
Servicios en la nube de Microsoft ofrece servicios e infraestructura a gran escala, funcionalidades de calidad empresarial y numerosas opciones de conectividad híbrida. Los clientes pueden elegir tener acceso a estos servicios a través de Internet o con Azure ExpressRoute, que proporciona conectividad de red privada. La plataforma Microsoft Azure permite a los clientes ampliar su infraestructura en la nube sin problemas y crear arquitecturas de varios niveles. Además, otros fabricantes pueden habilitar funcionalidades mejoradas mediante servicios de seguridad y dispositivos virtuales. En este documento se proporciona una visión general de los problemas de seguridad y de arquitectura que los clientes deben tener en cuenta al usar Servicios en la nube de Microsoft con acceso a través de ExpressRoute. También se describe la creación de servicios más seguros en redes virtuales de Azure.

## <a name="fast-start"></a>Inicio rápido
El gráfico de lógica siguiente puede dirigirle a un ejemplo específico de las numerosas técnicas de seguridad disponibles con la plataforma Azure. Para obtener una referencia rápida, busque el ejemplo que mejor se adapte a su caso. Si necesita una explicación más amplia, siga leyendo el documento.
[![0]][0]

[Ejemplo 1: Creación de una red perimetral (también conocida como subred filtrada) para proteger las aplicaciones con grupos de seguridad de red (NSG).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Ejemplo 2: Creación de una red perimetral para proteger las aplicaciones con un firewall y grupos de seguridad de red.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Ejemplo 3: Creación de una red perimetral para proteger las redes con un firewall, enrutamiento definido por el usuario (UDR) y grupo de seguridad de red (NSG).](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Ejemplo 4: Incorporación de una conexión híbrida con una red privada virtual (VPN) de dispositivo virtual de sitio a sitio.](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Ejemplo 5: Incorporación de una conexión híbrida con una instancia de Azure VPN Gateway de sitio a sitio.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Ejemplo 6: Incorporación de una conexión híbrida con ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Durante los próximos meses se agregarán a este documento ejemplos para incorporar conexiones entre redes virtuales, alta disponibilidad y encadenamiento de servicios.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Protección de infraestructura y cumplimiento normativo de Microsoft
Para ayudar a las organizaciones a cumplir los requisitos nacionales, regionales y específicos del sector que rigen la recopilación y el uso de los datos personales, Microsoft ofrece más de 40 certificaciones y atestaciones. El conjunto más completo de cualquier proveedor de servicios en la nube.

Para obtener más información, consulte la información de cumplimiento normativo en el [Centro de confianza de Microsoft][TrustCenter].

Microsoft tiene un enfoque integral para proteger la infraestructura en la nube necesaria para ejecutar servicios globales a gran escala. La infraestructura en la nube de Microsoft incluye hardware, software, redes y personal administrativo y operativo, así como centros de datos físicos.

![2]

Este enfoque proporciona una base más segura para que los clientes implementen los servicios en la nube de Microsoft. El siguiente paso es que los clientes diseñen y creen una arquitectura de seguridad para proteger estos servicios.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Arquitecturas de seguridad tradicionales y redes perimetrales
Aunque Microsoft realiza importantes inversiones en la protección de la infraestructura en la nube, los clientes también deben proteger los servicios en la nube y los grupos de recursos. Un enfoque de varios niveles de seguridad proporciona la mejor defensa. Una zona de seguridad de red perimetral protege los recursos de red internos de una red que no sea de confianza. Una red perimetral hace referencia a los bordes o partes de la red que se encuentran entre Internet y la infraestructura de TI protegida de la empresa.

En las redes de empresa típicas, la infraestructura principal está considerablemente fortalecida en los perímetros, con varios niveles de dispositivos de seguridad. El límite de cada nivel consta de dispositivos y puntos de aplicación de directivas. Cada nivel puede incluir una combinación de los siguientes dispositivos de seguridad de red: firewalls, prevención de denegación de servicio (DoS), sistemas de protección o detección de intrusiones (IDS/IPS) y dispositivos de VPN. La aplicación de directivas puede adoptar la forma de directivas de firewall, listas de control de acceso (ACL) o enrutamiento específico. La primera línea de defensa de la red, que acepta directamente el tráfico entrante desde Internet, consta de una combinación de estos mecanismos para bloquear los ataques y el tráfico perjudicial mientras permite que las solicitudes legítimas avancen por la red. Este tráfico se enruta directamente a los recursos de la red perimetral. A continuación, ese recurso puede "hablar" con recursos más profundos de la red, pasando primero el siguiente límite para la validación. El nivel exterior se denomina red perimetral porque esta parte de la red está expuesta a Internet, normalmente con algún tipo de protección a ambos lados. En la siguiente ilustración se muestra un ejemplo de una sola red perimetral de subred en una red corporativa con dos límites de seguridad.

![3]

Existen numerosas arquitecturas que se utilizan para implementar una red perimetral. Estas arquitecturas pueden ir de un simple equilibrador de carga a una red perimetral de varias subredes con distintos mecanismos en cada límite para bloquear el tráfico y proteger los niveles inferiores de la red corporativa. El modo de crear la red perimetral depende de las necesidades específicas de la organización y de su tolerancia general al riesgo.

Al desplazar los clientes las cargas de trabajo a nubes públicas, es fundamental admitir capacidades similares para la arquitectura de la red perimetral en Azure a fin de cumplir los requisitos de seguridad y cumplimiento normativo. En este documento se proporcionan instrucciones sobre cómo pueden crear los clientes un entorno de red seguro en Azure. Se centra en la red perimetral, pero también incluye una explicación completa de muchos aspectos relativos a la seguridad de red. Esta explicación se basa en las siguientes preguntas:

* ¿Cómo se puede crear una red perimetral en Azure?
* ¿Cuáles son algunas de las características de Azure disponibles para crear la red perimetral?
* ¿Cómo se pueden proteger las cargas de trabajo back-end?
* ¿Cómo se controlan las comunicaciones de Internet con las cargas de trabajo en Azure?
* ¿Cómo se pueden proteger las redes locales de las implementaciones en Azure?
* ¿Cuándo se deben usar las características de seguridad de Azure nativas frente a dispositivos o servicios de terceros?

En el diagrama siguiente se muestran los diferentes niveles de seguridad que Azure ofrece a los clientes. Estos niveles son nativos en la plataforma de Azure y características definidas por el cliente:

![4]

DDoS de Azure, entrante desde Internet, protege frente a ataques a gran escala contra Azure. El nivel siguiente son las direcciones de IP (puntos de conexión) públicas definidas por el cliente, que se usan para determinar el tráfico que puede pasar a través del servicio en la nube a la red virtual. El aislamiento de la red virtual de Azure nativa garantiza un aislamiento completo de todas las demás redes y que el tráfico solo fluya a través de los métodos y las rutas de acceso configurados por el usuario. Estas rutas de acceso y métodos son el siguiente nivel, en el que se pueden usar NSG, UDR y los dispositivos virtuales de red para crear límites de seguridad y así proteger las implementaciones de aplicaciones en la red protegida.

En la siguiente sección se proporciona información general sobre las redes virtuales de Azure. Los clientes crean estas redes virtuales, que son a las que están conectadas las cargas de trabajo implementadas. Las redes virtuales constituyen la base de todas las características de seguridad de red necesarias para establecer una red perimetral con el fin de proteger las implementaciones de clientes en Azure.

## <a name="overview-of-azure-virtual-networks"></a>Información general sobre las redes virtuales de Azure
Antes de que el tráfico de Internet llegue a las redes virtuales de Azure, hay dos niveles de seguridad inherentes a la plataforma Azure:

1.    **Protección de DDoS**: la protección de DDoS es un nivel de la red física de Azure que protege la plataforma Azure contra ataques a gran escala basados en Internet. Estos ataques usan varios nodos de "bot" en un intento de sobrecargar un servicio de Internet. Azure cuenta con una malla de protección frente a DDoS sólida en toda la conectividad de entrada, salida y entre regiones de Azure. Este nivel de protección de DDoS no tiene ningún atributo que pueda configurar el usuario y no es accesible para el cliente. El nivel de protección frente a DDoS protege a Azure, como plataforma, de ataques a gran escala. Además, supervisa el tráfico saliente y el que se produce entre las distintas regiones de Azure. Al usar dispositivos virtuales de red en la red virtual, el cliente puede configurar niveles adicionales de resistencia contra un ataque a menor escala que no active la protección de nivel de plataforma. Un ejemplo de DDoS en acción: si se ataca una dirección IP conectada a Internet con una DDoS a gran escala, Azure detectaría los orígenes de los ataques y se desharía del tráfico infractor antes de que este llegara a su destino. En casi todos los casos, el punto de conexión atacado no se ve afectado por el ataque. En los casos poco frecuentes en que un punto de conexión se ve afectado, el tráfico de los demás puntos de conexión no se vería afectado, únicamente el del punto de conexión objetivo del ataque. Por lo tanto, otros clientes y servicios no sufrirían impacto alguno de este ataque. Resulta esencial tener en cuenta que DDoS de Azure solo busca ataques a gran escala. Es posible que su servicio específico se vea desbordado antes de que se superen los umbrales de protección de nivel de la plataforma. Por ejemplo, un ataque de DDoS podría desconectar un sitio web de un solo servidor IIS A0 antes de que la protección frente a DDoS de nivel de plataforma de Azure registrara una amenaza.

2.  **Direcciones IP públicas**: las direcciones IP públicas (habilitadas mediante los puntos de conexión de servicio, las direcciones IP públicas, Application Gateway y otras características de Azure que presenten una dirección IP pública conectada a Internet enrutada a su recurso) permiten a los servicios en la nube o grupos de recursos exponer los puertos y las direcciones IP de Internet públicas. El punto de conexión usa la traducción de direcciones de red (NAT) para enrutar el tráfico a la dirección interna y el puerto de la red virtual de Azure. Esta ruta es la vía principal para que el tráfico externo pase a la red virtual. Las direcciones IP públicas se pueden configurar para determinar qué tráfico se pasa, y cómo y dónde se traduce en la red virtual.

Una vez que el tráfico llega a la red virtual, entran en juego muchas características. Las redes virtuales de Azure son la base para que los clientes conecten sus cargas de trabajo, y en ellas se aplica la seguridad de nivel de red básica. Es una red privada (una superposición de red virtual) en Azure para clientes con las funciones y características siguientes:

* **Aislamiento del tráfico**: una red virtual es el límite de aislamiento del tráfico en la plataforma Azure. Las máquinas virtuales de una red virtual no se pueden comunicar directamente con las máquinas virtuales de otra red virtual, incluso si las dos redes virtuales las creó el mismo cliente. El aislamiento consiste en una propiedad fundamental que garantiza que las máquinas virtuales del cliente y la comunicación sigan siendo privadas en una red virtual.

>[!NOTE]
>El aislamiento del tráfico solo hace referencia al tráfico *entrante* en la red virtual. De forma predeterminada, se permite el tráfico saliente de la red virtual a Internet, pero puede evitarse mediante NSG, si así lo desea.
>
>

* **Topología de múltiples niveles**: las redes virtuales permiten a los clientes definir una topología de múltiples niveles al asignar subredes y designar espacios de direcciones independientes para distintos elementos o "niveles" de las cargas de trabajo. Estas agrupaciones lógicas y topologías permiten a los clientes definir una directiva de acceso diferente en función de los tipos de cargas de trabajo y controlar igualmente los flujos de tráfico entre los niveles.
* **Conectividad entre instalaciones locales**: los clientes pueden establecer conectividad entre instalaciones locales entre una red virtual y varios sitios locales u otras redes virtuales en Azure. Para crear una conexión, los clientes pueden usar el emparejamiento de red virtual, instancias de Azure VPN Gateway, dispositivos virtuales de red de terceros o ExpressRoute. Azure es compatible con VPN de sitio a sitio (S2S) mediante los protocolos estándar IPsec/IKE y conectividad privada de ExpressRoute.
* **NSG** permite a los clientes crear reglas (ACL) con el nivel de granularidad deseado: interfaces de red, máquinas virtuales individuales o subredes virtuales. Los clientes pueden controlar el acceso al permitir o denegar la comunicación entre las cargas de trabajo dentro de una red virtual, desde sistemas de redes del cliente a través de conectividad entre locales o comunicación de Internet directa.
* **UDR** y **Reenvío IP** permiten a los clientes definir las rutas de comunicación entre distintos niveles dentro de una red virtual. Los clientes pueden implementar un firewall, IDS/IPS y otros dispositivos virtuales y enrutar el tráfico de red a través de estos dispositivos de seguridad para la aplicación de directivas de límites de seguridad, auditoría e inspección.
* **Dispositivos virtuales de red** en Azure Marketplace: hay dispositivos de seguridad, como firewalls, equilibradores de carga e IDS/IPS disponibles en Azure Marketplace y la Galería de imágenes de máquina virtual. Los clientes pueden implementar estos dispositivos en sus redes virtuales y, en concreto, en los límites de seguridad (incluidas las subredes de la red perimetral) para completar un entorno de red segura en múltiples niveles.

En el siguiente diagrama se muestra un ejemplo de cómo se puede construir una arquitectura de red perimetral en Azure con estas características y funcionalidades:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Características y requisitos de una red perimetral
La red perimetral es el front-end de la red y crea una interfaz directamente con la comunicación desde Internet. Los paquetes entrantes deben fluir a través de los dispositivos de seguridad, como el firewall, IDS e IPS, antes de llegar a los servidores back-end. Los paquetes enlazados a Internet desde las cargas de trabajo también pueden fluir a través de los dispositivos de seguridad de la red perimetral para fines de auditoría, inspección y aplicación de directivas antes de salir de la red. Además, la red perimetral puede hospedar puertas de enlace de VPN entre locales entre redes virtuales del cliente y redes locales.

### <a name="perimeter-network-characteristics"></a>Características de una red perimetral
Haciendo referencia a la ilustración anterior, algunas de las características de una buena red perimetral son las siguientes:

* Accesible desde Internet:
  * La propia subred de red perimetral es accesible desde Internet y se comunica directamente con Internet.
  * Las direcciones IP públicas, las direcciones IP virtuales (VIP) y los puntos de conexión de servicio pasan tráfico de Internet a la red y los dispositivos front-end.
  * El tráfico entrante de Internet pasa a través de dispositivos de seguridad antes de otros recursos de la red front-end.
  * Si está habilitada la seguridad saliente, el tráfico pasa a través de dispositivos de seguridad, como último paso, antes de pasar a Internet.
* Red protegida:
  * No hay una ruta directa desde Internet a la infraestructura central.
  * Los canales hacia la infraestructura central deben atravesar dispositivos de seguridad, como NSG, firewalls o dispositivos VPN.
  * Otros dispositivos no deben tender puentes entre Internet y la infraestructura central.
  * Los dispositivos de seguridad de los límites de la red perimetral accesibles tanto desde Internet como desde la red protegida (por ejemplo, los dos iconos de firewall que aparecen en la ilustración anterior) podrían ser en realidad un solo dispositivo virtual con reglas o interfaces diferenciadas para cada límite. Por ejemplo, un dispositivo físico dividido de forma lógica que administra la carga de los dos límites de la red perimetral.
* Otras prácticas y restricciones comunes:
  * Las cargas de trabajo no deben almacenar información crítica de la empresa.
  * El acceso y las actualizaciones de las configuraciones e implementaciones de red perimetral se limitan solo a los administradores autorizados.

### <a name="perimeter-network-requirements"></a>Requisitos de red perimetral
Para habilitar estas características, siga estas instrucciones relativas a los requisitos de red virtual para implementar una red perimetral correcta:

* **Arquitectura de subred:** especifique la red virtual de modo que se dedique una subred completa como red perimetral, separada de otras subredes en la misma red virtual. Esta separación garantiza que el tráfico entre la red perimetral y otros niveles de subred interna o privada fluya a través de un firewall o dispositivo virtual IDS/IPS.  Se requieren rutas definidas por el usuario las subredes limítrofes para reenviar este tráfico al dispositivo virtual.
* **NSG:** la subred de la red perimetral debe estar abierta para permitir la comunicación con Internet, pero esto no significa que los clientes puedan omitir los grupos de seguridad de red. Siga las recomendaciones de seguridad habituales para minimizar las superficies de red expuestas a Internet. Bloquee los intervalos de direcciones remotas que pueden acceder a las implementaciones o los protocolos de aplicación específicos y los puertos que estén abiertos. Sin embargo, es posible que se den circunstancias en las que no sea posible realizar un bloqueo completo. Por ejemplo, si los clientes tienen un sitio web externo en Azure, la red perimetral debe permitir las solicitudes web entrantes desde cualquier dirección IP pública, pero solo debe abrir los siguientes puertos de aplicación web: TCP en el puerto 80 y TCP en el puerto 443.
* **Tabla de enrutamiento:** la subred de red perimetral en sí debe poder comunicarse directamente con Internet, pero no debe permitir la comunicación directa al back-end y desde él o en redes locales sin pasar por un firewall o dispositivo de seguridad.
* **Configuración del dispositivo de seguridad:** para enrutar e inspeccionar los paquetes entre la red perimetral y el resto de las redes protegidas, los dispositivos de seguridad, como dispositivos de firewall, IDS e IPS, pueden tener hosts múltiples. También pueden tener NIC independientes para la red perimetral y las subredes back-end. Las NIC de la red perimetral se comunican directamente desde y hacia Internet, con los correspondientes grupos de seguridad de red y tabla de enrutamiento de red perimetral. Las NIC que se conectan a las subredes back-end tienen grupos de seguridad de red y tablas de enrutamiento de las correspondientes subredes back-end más restringidos.
* **Funciones del dispositivo de seguridad:** los dispositivos de seguridad implementados en la red perimetral suelen realizar las siguientes funcionalidades:
  * Firewall: aplicar reglas de firewall o directivas de control de acceso para las solicitudes entrantes.
  * Detección y prevención de amenazas: detectar y mitigar los ataques malintencionados desde Internet.
  * Auditoría y registro: mantener registros detallados para auditoría y análisis.
  * Proxy inverso: redirigir las solicitudes entrantes a los servidores back-end correspondientes. Esta redirección implica asignar y traducir las direcciones de destino en los dispositivos front-end, normalmente firewalls, a las direcciones de servidor back-end.
  * Proxy de reenvío: proporcionar NAT y realizar auditorías para comunicaciones iniciadas desde la red virtual hacia Internet.
  * Enrutador: reenviar tráfico entrante y entre subredes dentro de la red virtual.
  * Dispositivo VPN: actuar como puertas de enlace de VPN entre locales para conectividad VPN entre locales entre las redes locales del cliente y las redes virtuales de Azure.
  * Servidor VPN: aceptar clientes de VPN que se conectan a redes virtuales de Azure.

> [!TIP]
> Mantener separados los dos grupos siguientes: las personas autorizadas a acceder al engranaje de seguridad de la red perimetral y las personas autorizadas como administradores de desarrollo, implementación u operaciones de aplicaciones. Mantener estos grupos separados permite una separación de funciones y evita que una sola persona omita la seguridad de las aplicaciones y los controles de seguridad de red.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Preguntas que se deben plantear al crear límites de red
En esta sección, salvo que se especifique de otro modo, el término "redes" hace referencia a las redes virtuales privadas de Azure creadas por un administrador de la suscripción. El término no hace referencia a las redes físicas subyacentes dentro de Azure.

Además, las redes virtuales de Azure a menudo se usan para extender las redes locales tradicionales. Es posible incorporar soluciones de redes híbridas de sitio a sitio o de ExpressRoute con arquitecturas de redes perimetrales. Este vínculo híbrido es una consideración importante a la hora de crear límites de seguridad de red.

Es fundamental que conteste a las tres preguntas siguientes al crear una red con una red perimetral y varios límites de seguridad.

#### <a name="1-how-many-boundaries-are-needed"></a>1) ¿Cuántos límites se necesitan?
La primera decisión consiste en decidir cuántos límites de seguridad son necesarios en un escenario determinado:

* Un solo límite: uno en la red perimetral front-end entre la red virtual e Internet.
* Dos límites: uno en el lado de Internet de la red perimetral y otro entre la subred de la red perimetral y las subredes back-end de las redes virtuales de Azure.
* Tres límites: uno en el lado de Internet de la red perimetral, otro entre la red perimetral y las subredes back-end, y otro entre las subredes back-end y la red local.
* N límites: un número variable. En función de los requisitos de seguridad, no hay ninguna restricción en lo relativo al número de límites de seguridad en una red determinada.

El número y el tipo de límites necesarios varían en función de la tolerancia al riesgo de la empresa y el escenario concreto que se implementa. Esta decisión suelen tomarla conjuntamente varios grupos de una organización, que a menudo incluye un equipo de riesgos y cumplimiento, un equipo de redes y plataformas, y un equipo de desarrollo de aplicaciones. Las personas con conocimientos sobre seguridad, los datos implicados y las tecnologías que se van a usar deben participar en esta toma de decisiones a fin de garantizar la posición de seguridad adecuada para cada implementación.

> [!TIP]
> Use el menor número posible de límites que cumplan los requisitos de seguridad para una situación determinada. Cuanto mayor sea el número de límites, más difíciles pueden ser las operaciones y la solución de problemas, así como la sobrecarga de administración implicada en la administración de varias directivas de límites con el tiempo. Sin embargo, unos límites insuficientes aumentan el riesgo. Encontrar el equilibrio es fundamental.
>
>

![6]

En la ilustración anterior se muestra una vista de alto nivel de una red con tres límites de seguridad. Los límites se encuentran entre la red perimetral e Internet, las subredes privadas de front-end y back-end de Azure, y la subred de back-end de Azure y la red corporativa local.

#### <a name="2-where-are-the-boundaries-located"></a>2) ¿Dónde se encuentran los límites?
Una vez decidido el número de límites, la siguiente decisión es saber dónde se implementan. Generalmente, hay tres opciones:

* Uso de un servicio intermedio basado en Internet (por ejemplo, un firewall de aplicaciones web basado en la nube, que no se trata en este documento).
* Uso de características nativas o dispositivos virtuales de red en Azure.
* Uso de dispositivos físicos de la red local.

En redes únicamente de Azure, las opciones son características de Azure nativas (por ejemplo, equilibradores de carga de Azure) o dispositivos virtuales de red del ecosistema de socios enriquecido de Azure (por ejemplo, firewalls de punto de comprobación).

Si se necesita un límite entre Azure y una red local, los dispositivos de seguridad pueden residir en cualquier lado de la conexión (o en ambos). Por lo tanto, se debe tomar una decisión sobre la ubicación en la que se va a colocar un engranaje de seguridad.

En la ilustración anterior, los límites entre Internet y red perimetral y entre front-end y back-end están totalmente incluidos en Azure y deben ser características nativas de Azure o dispositivos virtuales de red. Los dispositivos de seguridad en el límite entre Azure (subred back-end) y la red corporativa pueden estar tanto en el lado de Azure como en el local o incluso una combinación de dispositivos en ambos lados. Pueden existir importantes ventajas y desventajas en cualquiera de las opciones, que se deben tener muy en cuenta.

Por ejemplo, el uso del engranaje de seguridad físico existente en el lado de la red local tiene la ventaja de que no se necesita ningún engranaje nuevo. Simplemente hay que volver a configurarlo. La desventaja es que todo el tráfico debe ir desde Azure a la red local para que lo pueda ver el engranaje de seguridad. Por lo tanto, el tráfico de Azure a Azure puede sufrir una latencia considerable y afectar a la experiencia de usuario y al rendimiento de las aplicaciones si se obligó a dirigirlo a la red local para la aplicación de directivas de seguridad.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) ¿Cómo se implementan los límites?
Cada límite de seguridad probablemente tendrá requisitos de capacidad diferentes (por ejemplo, IDS y reglas de firewall en el lado de Internet de la red perimetral, pero solo listas de control de acceso entre la red perimetral y la subred back-end). La decisión sobre qué dispositivo (o qué número de dispositivos) se va a usar depende de los requisitos de seguridad y del escenario. En la sección siguiente, los ejemplos 1, 2 y 3 describen algunas opciones que se podrían usar. La revisión de las características de red nativa de Azure y los dispositivos disponibles en Azure en el ecosistema de socios muestra las innumerables opciones disponibles para resolver prácticamente cualquier escenario.

Otro punto de decisión de implementación clave es cómo conectar la red local con Azure. ¿Debería usar la puerta de enlace virtual de Azure o un dispositivo virtual de red? Estas opciones se tratan con más detalle en la sección siguiente (ejemplos 4, 5 y 6).

Además, podría ser necesario el tráfico entre redes virtuales dentro de Azure. Estos escenarios se agregarán en el futuro.

Cuando sepa las respuestas a las preguntas anteriores, la sección [Inicio rápido](#fast-start) le ayudará a identificar qué ejemplos son más adecuados para un escenario determinado.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Ejemplos: Creación de límites de seguridad con redes virtuales de Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Ejemplo 1: Creación de una red perimetral para proteger las aplicaciones con grupos de seguridad de red
[Volver a inicio rápido](#fast-start) | [Instrucciones de compilación detalladas para este ejemplo][Example1]

[![7]][7]

#### <a name="environment-description"></a>Descripción del entorno
En este ejemplo hay una suscripción que contiene los siguientes recursos:

- Un único grupo de recursos
- Una red virtual solo en la nube con dos subredes: "FrontEnd" y "BackEnd"
- Un grupo de seguridad de red que se aplica a ambas subredes
- Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
- Dos servidores Windows que representan servidores back-end de aplicaciones ("AppVM01", "AppVM02")
- Un servidor Windows que representa un servidor DNS ("DNS01")
- Una dirección IP pública asociada al servidor de aplicaciones web

Para ver los scripts y una plantilla de Azure Resource Manager, consulte las [instrucciones de compilación detalladas][Example1].

#### <a name="nsg-description"></a>Descripción del grupo de seguridad de red
En este ejemplo, se crea un grupo de seguridad de red y se cargan después seis reglas.

> [!TIP]
> Por lo general, debe crear primero las reglas específicas de "Permitir" y después las reglas de "Denegar" más genéricas. La prioridad asignada indica qué reglas se evalúan primero. Una vez que se encuentra el tráfico para aplicar a una regla específica, no se evalúa ninguna otra regla. Se pueden aplicar reglas de grupo de seguridad de red en cualquier dirección, entrante o saliente (desde la perspectiva de la subred).
>
>

De forma declarativa, se compilan las reglas siguientes para el tráfico entrante:

1. Se permite el tráfico DNS interno (puerto 53).
2. Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier máquina virtual.
3. Se permite el tráfico HTTP (puerto 80) desde Internet al servidor web (IIS01).
4. Se permite cualquier tráfico (todos los puertos) desde IIS01 a AppVM1.
5. Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (ambas subredes).
6. Se deniega todo el tráfico (todos los puertos) desde la subred front-end a la subred back-end.

Con estas reglas enlazadas a cada subred, si entrase una solicitud HTTP desde Internet al servidor web, se aplicarían las reglas 3 (permitir) y 5 (denegar). Sin embargo, dado que la regla 3 tiene una prioridad más alta, solo se aplicaría esta, y la regla 5 no entraría en juego. Por lo tanto, se permitiría la solicitud HTTP al servidor web. Si ese mismo tráfico estaba intentando ponerse en contacto con el servidor DNS01, se aplicaría primero la regla 5 (denegar) y no se permitiría que el tráfico pasase al servidor. La regla 6 (denegar) bloquea la subred front-end para impedirle comunicarse con la subred back-end (excepto en el caso del tráfico permitido en las reglas 1 y 4). Este conjunto de reglas protege la red back-end en caso de que un atacante ponga en peligro la aplicación web en el front-end. El atacante tendría acceso limitado a la red back-end "protegida" (únicamente a los recursos expuestos en el servidor AppVM01).

Hay una regla de salida predeterminada que permite el tráfico saliente a Internet. En este ejemplo, permitimos el tráfico saliente y no modificamos las reglas de salida. Para bloquear el tráfico en ambas direcciones, es necesario el enrutamiento definido por el usuario (vea el ejemplo 3).

#### <a name="conclusion"></a>Conclusión
Se trata de una manera relativamente sencilla y directa de aislar la subred back-end del tráfico entrante. Para obtener más información, consulte las [instrucciones de compilación detalladas][Example1]. Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral con scripts de PowerShell clásicos.
* Creación de esta red perimetral con una plantilla de Azure Resource Manager.
* Descripciones detalladas de cada comando de grupo de seguridad de red.
* Escenarios de flujo de tráfico detallados, que muestran cómo se permite o se deniega el tráfico en cada nivel.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Ejemplo 2: Creación de una red perimetral para proteger las aplicaciones con un firewall y grupos de seguridad de red.
[Volver a inicio rápido](#fast-start) | [Instrucciones de compilación detalladas para este ejemplo][Example2]

[![8]][8]

#### <a name="environment-description"></a>Descripción del entorno
En este ejemplo hay una suscripción que contiene los siguientes recursos:

* Un único grupo de recursos
* Una red virtual solo en la nube con dos subredes: "FrontEnd" y "BackEnd"
* Un grupo de seguridad de red que se aplica a ambas subredes
* Un dispositivo virtual de red, en este caso un firewall, conectado a la subred front-end
* Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
* Dos servidores Windows que representan servidores back-end de aplicaciones ("AppVM01", "AppVM02")
* Un servidor Windows que representa un servidor DNS ("DNS01")

Para ver los scripts y una plantilla de Azure Resource Manager, consulte las [instrucciones de compilación detalladas][Example2].

#### <a name="nsg-description"></a>Descripción del grupo de seguridad de red
En este ejemplo, se crea un grupo de seguridad de red y se cargan después seis reglas.

> [!TIP]
> Por lo general, debe crear primero las reglas específicas de "Permitir" y después las reglas de "Denegar" más genéricas. La prioridad asignada indica qué reglas se evalúan primero. Una vez que se encuentra el tráfico para aplicar a una regla específica, no se evalúa ninguna otra regla. Se pueden aplicar reglas de grupo de seguridad de red en cualquier dirección, entrante o saliente (desde la perspectiva de la subred).
>
>

De forma declarativa, se compilan las reglas siguientes para el tráfico entrante:

1. Se permite el tráfico DNS interno (puerto 53).
2. Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier máquina virtual.
3. Se permite cualquier tráfico de Internet (todos los puertos) al dispositivo de red virtual (firewall).
4. Se permite cualquier tráfico (todos los puertos) desde IIS01 a AppVM1.
5. Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (ambas subredes).
6. Se deniega todo el tráfico (todos los puertos) desde la subred front-end a la subred back-end.

Con estas reglas enlazadas a cada subred, si entrase una solicitud HTTP desde Internet al firewall, se aplicarían las reglas 3 (permitir) y 5 (denegar). Sin embargo, dado que la regla 3 tiene una prioridad más alta, solo se aplicaría esta, y la regla 5 no entraría en juego. Por lo tanto, se permitiría la solicitud HTTP al firewall. Si ese mismo tráfico intentase ponerse en contacto con el servidor IIS01, incluso aunque esté en la subred front-end, se aplicaría la regla 5 (denegar) y no se permitiría que el tráfico pasara al servidor. La regla 6 (denegar) bloquea la subred front-end para impedirle comunicarse con la subred back-end (excepto en el caso del tráfico permitido en las reglas 1 y 4). Este conjunto de reglas protege la red back-end en caso de que un atacante ponga en peligro la aplicación web en el front-end. El atacante tendría acceso limitado a la red back-end "protegida" (únicamente a los recursos expuestos en el servidor AppVM01).

Hay una regla de salida predeterminada que permite el tráfico saliente a Internet. En este ejemplo, permitimos el tráfico saliente y no modificamos las reglas de salida. Para bloquear el tráfico en ambas direcciones, es necesario el enrutamiento definido por el usuario (vea el ejemplo 3).

#### <a name="firewall-rule-description"></a>Descripción de reglas de firewall
En el firewall, deben crearse reglas de reenvío. Como en este ejemplo solo se enruta el tráfico entrante de Internet al firewall y después al servidor web, únicamente se necesita un regla de traducción de direcciones de red (NAT) de reenvío.

La regla de enrutamiento acepta cualquier dirección de origen entrante que alcance el firewall al intentar llegar a HTTP (puerto 80 o 443 para HTTPS). Se envía fuera de la interfaz local del firewall y se redirige al servidor web con la dirección IP de 10.0.1.5.

#### <a name="conclusion"></a>Conclusión
Este ejemplo es una manera relativamente sencilla de proteger la aplicación con un firewall y de aislar la subred back-end del tráfico entrante. Para obtener más información, consulte las [instrucciones de compilación detalladas][Example2]. Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral con scripts de PowerShell clásicos.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Descripciones detalladas de cada comando de grupo de seguridad de red y regla de firewall.
* Escenarios de flujo de tráfico detallados, que muestran cómo se permite o se deniega el tráfico en cada nivel.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Ejemplo 3: Creación de una red perimetral para proteger las redes con un firewall, UDR y NSG
[Volver a inicio rápido](#fast-start) | [Instrucciones de compilación detalladas para este ejemplo][Example3]

[![9]][9]

#### <a name="environment-description"></a>Descripción del entorno
En este ejemplo hay una suscripción que contiene los siguientes recursos:

* Un único grupo de recursos
* Una red virtual con tres subredes: "SecNet", "FrontEnd" y "BackEnd"
* Un dispositivo virtual de red, en este caso un firewall, conectado a la subred SecNet
* Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
* Dos servidores Windows que representan servidores back-end de aplicaciones ("AppVM01", "AppVM02")
* Un servidor Windows que representa un servidor DNS ("DNS01")

Para ver los scripts y una plantilla de Azure Resource Manager, consulte las [instrucciones de compilación detalladas][Example3].

#### <a name="udr-description"></a>Descripción de UDR
De forma predeterminada, las siguientes rutas de sistema se definen como:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal es siempre uno o varios prefijos de dirección definidos que conforman la red virtual para esa red específica (es decir, cambia de una red virtual a otra según cómo se defina cada red virtual específica). Las rutas de sistema restantes son estáticas y tienen el valor predeterminado indicado en la tabla.

En este ejemplo, se crean dos tablas de enrutamiento para las subredes front-end y back-end. Cada tabla se carga con rutas estáticas adecuadas para la subred indicada. En este ejemplo, cada tabla tiene tres rutas que dirigen todo el tráfico (0.0.0.0/0) a través del firewall (salto siguiente = dirección IP del dispositivo virtual):

1. Tráfico de subred local sin próximo salto definido para que el tráfico de la subred local pase por alto el firewall.
2. Tráfico de red virtual con un próximo salto definido como el firewall. Este próximo salto anula la regla predeterminada que permite al tráfico de la red virtual local enrutarse directamente.
3. Todo el tráfico restante (0/0) con un próximo salto definido como firewall.

> [!TIP]
> No disponer de la entrada de la subred local en el UDR hará que se interrumpan las comunicaciones de la subred local.
>
> * En nuestro ejemplo, es crucial que 10.0.1.0/24 apunte a VNETLocal. Sin ello, un paquete que salga del servidor web (10.0.1.4) cuyo destino sea otro servidor local (por ejemplo, 10.0.1.25) generará un error, ya que se enviará al dispositivo virtual de red. Este último lo enviará a la subred y esta lo reenviará al dispositivo virtual de red en un bucle infinito.
> * La probabilidad de que se produzca un bucle de enrutamiento suele ser mayor en aplicaciones con varias NIC conectadas a subredes independientes, que suele ser un rasgo habitual en dispositivos locales tradicionales.
>
>

Una vez creadas las tablas de enrutamiento, se deben enlazar a sus subredes. Una vez creada la tabla de enrutamiento de la subred front-end y enlazada a la subred, debe tener el siguiente aspecto:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> El enrutamiento definido por el usuario se puede aplicar ahora a la subred de puerta de enlace en la que está conectado el circuito de ExpressRoute.
>
> En los ejemplos 3 y 4 se muestra cómo habilitar la red perimetral con ExpressRoute o redes de sitio a sitio.
>
>

#### <a name="ip-forwarding-description"></a>Descripción de reenvío IP
El reenvío IP es una característica complementaria del enrutamiento definido por el usuario. El reenvío IP es una opción de configuración de los dispositivos virtuales que les permite recibir tráfico no dirigido específicamente al dispositivo y, después, reenviar ese tráfico a su destino final.

Por ejemplo, si AppVM01 realiza una solicitud al servidor DNS01, el enrutamiento definido por el usuario enrutará este tráfico al firewall. Con el reenvío IP habilitado, el dispositivo (10.0.0.4) acepta el tráfico dirigido a DNS01 (10.0.2.4) y, a continuación, se reenvía a su destino final (10.0.2.4). Sin el reenvío IP habilitado en el firewall, el dispositivo no aceptaría el tráfico aunque la tabla de enrutamiento tuviese el firewall como próximo salto. Para usar un dispositivo virtual, es fundamental acordarse de habilitar el reenvío IP junto con el enrutamiento definido por el usuario.

#### <a name="nsg-description"></a>Descripción del grupo de seguridad de red
En este ejemplo, se crea un grupo de seguridad de red y se carga después con una única regla. Este grupo se enlaza solo a las subredes front-end y back-end (no el SecNet). Mediante declaración se genera la siguiente regla:

* Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (todas las subredes).

Aunque en este ejemplo se usan grupos de seguridad de red, su principal objetivo es constituir un segundo nivel de defensa frente a errores de configuración manual. El objetivo es bloquear todo el tráfico entrante desde Internet a las subredes front-end o back-end. El tráfico solo debe fluir a través de la subred SecNet al firewall (y después, si procede, a las subredes front-end o back-end). Además, con las reglas de enrutamiento definido por el usuario vigentes, el tráfico que llegue a las subredes front-end o back-end se redirigirá al firewall (gracias al enrutamiento definido por el usuario). El firewall consideraría este tráfico un flujo asimétrico y descartaría el tráfico saliente. Por lo tanto, hay tres niveles de seguridad que protegen las subredes:

* No hay ninguna dirección IP pública en ningún NIC de FrontEnd o BackEnd.
* Grupos de seguridad de red que deniegan el tráfico de Internet.
* El firewall que elimina el tráfico asimétrico.

Un punto interesante sobre el grupo de seguridad de red de este ejemplo es que contiene solo una regla, que consiste en denegar el tráfico de Internet a toda la red virtual, incluida la subred de seguridad. Sin embargo, como el grupo de seguridad de red solo está enlazado a las subredes front-end y back-end, la regla no se procesa en el tráfico entrante a la subred de seguridad. Como resultado, el tráfico fluye a la subred de seguridad.

#### <a name="firewall-rules"></a>Reglas de firewall
En el firewall, deben crearse reglas de reenvío. Dado que el firewall bloquea o reenvía todo el tráfico entrante, saliente y entre redes virtuales, se necesitan muchas reglas de firewall. Además, todo el tráfico entrante llegará a la dirección IP pública del servicio de seguridad (en puertos diferentes) para ser procesado por el firewall. El procedimiento recomendado es crear un diagrama de los flujos lógicos antes de configurar las subredes y las reglas de firewall para evitar las modificaciones más adelante. La siguiente ilustración es una vista lógica de las reglas de firewall para este ejemplo:

![10]

> [!NOTE]
> Según el dispositivo virtual de red usado, los puertos de administración varían. En este ejemplo se hace referencia a un firewall Barracuda NextGen que usa los puertos 22, 801 y 807. Consulte la documentación del proveedor del dispositivo para buscar los puertos exactos usados para la administración del dispositivo que se va a usar.
>
>

#### <a name="firewall-rules-description"></a>Descripción de reglas de firewall
En el diagrama lógico anterior, no se muestra la subred de seguridad porque el servidor de seguridad es el único recurso en esa subred. En el diagrama se muestran las reglas de firewall y cómo permitir o denegar de forma lógica flujos de tráfico, no la propia ruta de acceso enrutada. Además, los puertos externos seleccionados para el tráfico RDP están en un intervalo más alto (8014-8026) y se han seleccionado para que se correspondan en cierto modo con los dos últimos octetos de la dirección IP local y facilitar así su legibilidad (por ejemplo, la dirección de servidor local 10.0.1.4 está asociada al puerto externo 8014). Sin embargo, podría usarse cualquier puerto superior que no planteara conflictos.

En este ejemplo, necesitamos siete tipos de reglas:

* Reglas externas (para el tráfico entrante):
  1. Regla de administración de firewall: esta regla de redirección de aplicación permite que el tráfico pase a los puertos de administración del dispositivo virtual de red.
  2. Reglas de RDP (para cada servidor Windows): estas cuatro reglas (una para cada servidor) permiten la administración de los servidores individuales a través de RDP. Las cuatro reglas de RDP también se pueden agrupar en una regla según las características del dispositivo virtual de red que se use.
  3. Reglas de tráfico de aplicación: hay dos reglas de este tipo, la primera para el tráfico web front-end y la segunda para el tráfico back-end (por ejemplo, servidor web a capa de datos). La configuración de estas reglas depende de la arquitectura de red (donde están situados los servidores) y los flujos de tráfico (en qué dirección fluye el tráfico y qué puertos se usan).
     * La primera regla permite que el tráfico de aplicación real llegue al servidor de aplicaciones. Mientras que las demás reglas permiten la seguridad y la administración, las reglas de tráfico de aplicación son las que permiten a los usuarios o servicios externos acceder a las aplicaciones. En este ejemplo, hay un solo servidor web en el puerto 80. Por lo tanto, una sola regla de aplicación de firewall redirige el tráfico entrante a la dirección IP externa y a la dirección IP interna de los servidores web. La sesión de tráfico redirigido se traduciría a través de NAT al servidor interno.
     * La segunda regla es la regla back-end que permite que el servidor web se comunique con el servidor AppVM01 (no AppVM02) a través de cualquier puerto.
* Reglas internas (para el tráfico entre redes virtuales)
  1. Regla de saliente a Internet: esta regla permite que el tráfico de cualquier red pase a las redes seleccionadas. Normalmente, esta es una regla predeterminada que ya existe en el firewall, pero en estado deshabilitado. Esta regla debe habilitarse para este ejemplo.
  2. Regla DNS: esta regla permite que pase solo el tráfico DNS (puerto 53) al servidor DNS. Para este entorno, se bloquea la mayor parte del tráfico de front-end a back-end. Esta regla permite específicamente DNS desde cualquier subred local.
  3. Regla de subred a subred: esta regla permite que cualquier servidor en la subred back-end conecte con cualquier servidor en la subred front-end (pero no a la inversa).
* Regla para notificaciones de error (para el tráfico que no cumple ninguna de las anteriores):
  1. Denegar todas las reglas de tráfico: esta regla de denegación debe ser siempre la última regla (en términos de prioridad) y, como tal, si un flujo de tráfico no coincide con ninguna de las reglas anteriores, esta regla lo descartará. Esta regla es la predeterminada y, por lo general, está establecida y activa. Normalmente, no se necesita ninguna modificación a esta regla.

> [!TIP]
> En la segunda regla de tráfico de aplicación, para simplificar el ejemplo, se permite cualquier puerto. En un escenario real, deben usarse el puerto y los intervalos de direcciones más específicos para reducir la superficie expuesta a ataques de esta regla.
>
>

Una vez creadas las reglas anteriores, es importante revisar la prioridad de cada una para asegurarse de que el tráfico se permitirá o se denegará según se desee. En este ejemplo, las reglas están en orden de prioridad.

#### <a name="conclusion"></a>Conclusión
Este ejemplo consiste en una manera más compleja pero más completa de proteger y aislar la red que los ejemplos anteriores. (En el ejemplo 2 solo se protege la aplicación y en el ejemplo 1 solo se aíslan las subredes). Este diseño permite supervisar el tráfico en ambas direcciones y protege no solo el servidor de aplicaciones de entrada, sino que aplica la directiva de seguridad de red a todos los servidores de esta red. Además, según el dispositivo que se usa, se pueden lograr el reconocimiento y la auditoría de todo el tráfico. Para obtener más información, consulte las [instrucciones de compilación detalladas][Example3]. Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral de ejemplo con scripts de PowerShell clásicos.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Descripciones detalladas de cada enrutamiento definido por el usuario, comando de grupo de seguridad de red y regla de firewall.
* Escenarios de flujo de tráfico detallados, que muestran cómo se permite o se deniega el tráfico en cada nivel.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Ejemplo 4: Incorporación de una conexión híbrida con una VPN de dispositivo virtual de sitio a sitio
[Volver a inicio rápido](#fast-start) | Instrucciones de compilación disponibles pronto

[![11]][11]

#### <a name="environment-description"></a>Descripción del entorno
Las redes híbridas que usan un dispositivo virtual de red (NVA) pueden agregarse a cualquiera de los tipos de red perimetral descritos en el ejemplo 1, 2 o 3.

Como se muestra en la ilustración anterior, se usa una conexión VPN a través de Internet (sitio a sitio) para conectar una red local a una red virtual de Azure a través de un dispositivo virtual de red.

> [!NOTE]
> Si usa ExpressRoute con la opción de configuración entre pares públicos de Azure habilitada, debe crear una ruta estática. Esta ruta estática debe enrutar a la dirección IP de VPN del dispositivo de red virtual con Internet corporativo, y no con la conexión de ExpressRoute. La NAT requerida en la opción de configuración entre pares públicos de ExpressRoute de Azure puede interrumpir la sesión de VPN.
>
>

Una vez implementada la conexión VPN, el dispositivo virtual de red se convierte en el concentrador central para todas las redes y subredes. Las reglas de reenvío de firewall determinan qué flujos de tráfico se permiten, se traducen a través de NAT, se redirigen o se descartan (incluso para los flujos de tráfico entre la red local y Azure).

Los flujos de tráfico deben tenerse en cuenta cuidadosamente, ya que se pueden optimizar o degradar mediante este patrón de diseño en función del caso de uso específico.

Si usa el entorno creado en el ejemplo 3 e incorpora posteriormente una conexión de red híbrida de VPN de sitio a sitio, se producirá el siguiente diseño:

[![12]][12]

El enrutador local, o cualquier otro dispositivo de red que sea compatible con el dispositivo virtual de red para VPN, sería el cliente VPN. Este dispositivo físico sería responsable de iniciar y mantener la conexión VPN con el dispositivo virtual de red.

Según la lógica del dispositivo virtual de red, la red parece estar constituida por cuatro "zonas de seguridad" independientes con las reglas del dispositivo virtual de red como director principal del tráfico entre estas zonas:

![13]

#### <a name="conclusion"></a>Conclusión
La incorporación de una conexión de red híbrida VPN de sitio a sitio a una red virtual de Azure puede ampliar la red local a Azure de forma segura. Al usar una conexión VPN, el tráfico se cifra y se enruta a través de Internet. El dispositivo virtual de red de este ejemplo proporciona una ubicación central para aplicar y administrar la directiva de seguridad. Para obtener más información, consulte las instrucciones de compilación detalladas (próximamente). Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral de ejemplo con scripts de PowerShell.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Escenarios de flujo de tráfico detallados, que muestran cómo fluye el tráfico en este diseño.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Ejemplo 5: Incorporación de una conexión híbrida con una VPN de puerta de enlace de Azure de sitio a sitio
[Volver a inicio rápido](#fast-start) | Instrucciones de compilación disponibles pronto

[![14]][14]

#### <a name="environment-description"></a>Descripción del entorno
Las redes híbridas que usan una puerta de enlace de VPN de Azure se pueden agregar a cualquier tipo de red perimetral descrito en los ejemplos 1 y 2.

Como se muestra en la ilustración anterior, se usa una conexión VPN a través de Internet (de sitio a sitio) para conectar una red local a una red virtual de Azure a través de una puerta de enlace de VPN de Azure.

> [!NOTE]
> Si usa ExpressRoute con la opción de configuración entre pares públicos de Azure habilitada, debe crear una ruta estática. Esta debe enrutar a la dirección IP de VPN del dispositivo de red virtual con Internet corporativo, y no con la conexión de ExpressRoute. La NAT requerida en la opción de configuración entre pares públicos de ExpressRoute de Azure puede interrumpir la sesión de VPN.
>
>

En la siguiente ilustración se muestran los dos bordes de red de este ejemplo. En el primer borde, el dispositivo virtual de red y los grupos de seguridad de red controlan los flujos de tráfico para redes internas de Azure y entre Azure e Internet. El segundo borde es la puerta de enlace de VPN de Azure, que es un borde de red independiente y aislado entre la instalación local y Azure.

Los flujos de tráfico deben tenerse en cuenta cuidadosamente, ya que se pueden optimizar o degradar mediante este patrón de diseño en función del caso de uso específico.

Si usa el entorno creado en el ejemplo 1 e incorpora posteriormente una conexión de red híbrida de VPN de sitio a sitio, se producirá el siguiente diseño:

[![15]][15]

#### <a name="conclusion"></a>Conclusión
La incorporación de una conexión de red híbrida VPN de sitio a sitio a una red virtual de Azure puede ampliar la red local a Azure de forma segura. Mediante una puerta de enlace nativa de VPN de Azure, el tráfico está cifrado con IPSec y se enruta a través de Internet. Asimismo, mediante la puerta de enlace de VPN de Azure, se proporciona una opción menos costosa (sin costos de licencias adicionales como ocurre con dispositivos virtuales de red de terceros). Esta opción resulta más económica en el ejemplo 1, en el que no se usa ningún dispositivo virtual de red. Para obtener más información, consulte las instrucciones de compilación detalladas (próximamente). Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral de ejemplo con scripts de PowerShell.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Escenarios de flujo de tráfico detallados, que muestran cómo fluye el tráfico en este diseño.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Ejemplo 6: Incorporación de una conexión híbrida con ExpressRoute.
[Volver a inicio rápido](#fast-start) | Instrucciones de compilación disponibles pronto

[![16]][16]

#### <a name="environment-description"></a>Descripción del entorno
Las redes híbridas que usan una conexión de configuración entre pares privados de ExpressRoute se pueden agregar a cualquier tipo de red perimetral descrito en el ejemplo 1 o 2.

Como se muestra en la ilustración anterior, la configuración entre pares privados de ExpressRoute proporciona una conexión directa entre la red local y la red virtual de Azure. El tráfico transita solo por la red del proveedor de servicio y la red de Microsoft Azure sin tocar nunca Internet.

> [!TIP]
> Al usar ExpressRoute, se mantiene el tráfico de la red corporativa fuera de Internet. Además, se permiten los contratos de nivel de servicio del proveedor de ExpressRoute. La puerta de enlace de Azure puede pasar a 10 Gbps con ExpressRoute, mientras que con la puerta de enlace de Azure de VPN de sitio a sitio, el rendimiento máximo es de 200 Mbps.
>
>

Como se muestra en el diagrama siguiente, con esta opción el entorno tiene ahora dos bordes de red. El dispositivo virtual de red y el grupo de seguridad de red controlan los flujos de tráfico para redes internas de Azure y entre Azure e Internet, mientras que la puerta de enlace es un borde de red independiente y aislado entre la instalación local y Azure.

Los flujos de tráfico deben tenerse en cuenta cuidadosamente, ya que se pueden optimizar o degradar mediante este patrón de diseño en función del caso de uso específico.

Si usa el entorno creado en el ejemplo 1 e incorpora posteriormente una conexión de red híbrida de ExpressRoute, se producirá el siguiente diseño:

[![17]][17]

#### <a name="conclusion"></a>Conclusión
La incorporación de una conexión de red de configuración entre pares privados de ExpressRoute puede ampliar la red local a Azure de un modo seguro, con una latencia inferior y con un rendimiento mayor. Asimismo, mediante la puerta de enlace nativa de Azure, como en este ejemplo, se proporciona una opción menos costosa (sin necesidad de licencias adicionales como ocurre con dispositivos virtuales de red de terceros). Para obtener más información, consulte las instrucciones de compilación detalladas (próximamente). Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral de ejemplo con scripts de PowerShell.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Escenarios de flujo de tráfico detallados, que muestran cómo fluye el tráfico en este diseño.

## <a name="references"></a>Referencias
### <a name="helpful-websites-and-documentation"></a>Documentación y sitios web útiles
* Acceder a Azure con Azure Resource Manager:
* Acceder a Azure con PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Documentación de redes virtuales: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Documentación del grupo de seguridad de red: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/virtual-networks-nsg.md)
* Documentación del enrutamiento definido por el usuario: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Puertas de enlace virtuales de Azure: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Redes VPN de sitio a sitio: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Documentación de ExpressRoute (asegúrese de consultar las secciones "Introducción" y "Procedimientos"): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Diagrama de flujo de opciones de seguridad"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Características de seguridad de Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Red perimetral en una red corporativa"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Arquitectura de seguridad de Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Red perimetral en una instancia de Azure Virtual Network"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Red híbrida con tres límites de seguridad"
[7]: ./media/best-practices-network-security/example1design.png "Red perimetral de entrada con grupo de seguridad de red"
[8]: ./media/best-practices-network-security/example2design.png "Red perimetral de entrada con dispositivo virtual de red y grupo de seguridad de red"
[9]: ./media/best-practices-network-security/example3design.png "Red perimetral bidireccional con un dispositivo de red virtual, grupos de seguridad de red y enrutamiento definido por el usuario"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Vista lógica de las reglas de firewall"
[11]: ./media/best-practices-network-security/example3designoptions.png "Red perimetral con red híbrida conectada a un dispositivo virtual de red"
[12]: ./media/best-practices-network-security/example4designs2s.png "Red perimetral con un dispositivo virtual de red conectada mediante una VPN de sitio a sitio"
[13]: ./media/best-practices-network-security/example4networklogical.png "Red lógica desde la perspectiva del dispositivo virtual de red"
[14]: ./media/best-practices-network-security/example5designoptions.png "Red perimetral con puerta de enlace de Azure conectada a red híbrida de sitio a sitio"
[15]: ./media/best-practices-network-security/example5designs2s.png "Red perimetral con puerta de enlace de Azure mediante VPN de sitio a sitio"
[16]: ./media/best-practices-network-security/example6designoptions.png "Red perimetral con puerta de enlace de Azure conectada a red híbrida de ExpressRoute"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "Red perimetral con puerta de enlace de Azure mediante una conexión de ExpressRoute"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
