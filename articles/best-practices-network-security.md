---
title: Servicios en la nube de Microsoft y seguridad de red | Microsoft Docs
description: Obtenga información sobre las características clave disponibles en Azure para crear entornos de red seguros
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jonor;sivae

---
# Servicios en la nube de Microsoft y seguridad de red
Servicios en la nube de Microsoft ofrece servicios e infraestructura a gran escala, funcionalidades de calidad empresarial y numerosas opciones de conectividad híbrida. Los clientes pueden elegir tener acceso a estos servicios a través de Internet o con Azure ExpressRoute, que proporciona conectividad de red privada. La plataforma Microsoft Azure permite a los clientes ampliar su infraestructura en la nube sin problemas y crear arquitecturas de varios niveles. Además, otros fabricantes pueden habilitar funcionalidades mejoradas mediante servicios de seguridad y dispositivos virtuales. En este documento se proporciona una visión general de los problemas de seguridad y de arquitectura que los clientes deben tener en cuenta al usar Servicios en la nube de Microsoft con acceso a través de ExpressRoute. También se describe la creación de servicios más seguros en redes virtuales de Azure.

## Inicio rápido
El gráfico de lógica siguiente puede dirigirle a un ejemplo específico de las numerosas técnicas de seguridad disponibles con la plataforma Azure. Para obtener una referencia rápida, busque el ejemplo que mejor se adapte a su caso. Si necesita una explicación más completa, siga leyendo el documento. ![Diagrama de flujo de opciones de seguridad][0]

[Ejemplo 1: Creación de una red perimetral (también conocida como subred filtrada) para proteger las aplicaciones con grupos de seguridad de red (NSG).](#example-1-build-a-simple-dmz-with-nsgs)</br> [Ejemplo 2: Creación de una red perimetral para proteger las aplicaciones con un firewall y grupos de seguridad de red.](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br> [Ejemplo 3: Creación de una red perimetral para proteger las redes con un firewall, enrutamiento definido por el usuario (UDR) y grupo de seguridad de red (NSG).](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br> [Ejemplo 4: Incorporación de una conexión híbrida con una red privada virtual (VPN) de dispositivo virtual de sitio a sitio.](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br> [Ejemplo 5: Incorporación de una conexión híbrida con una VPN de puerta de enlace de Azure de sitio a sitio.](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br> [Ejemplo 6: Incorporación de una conexión híbrida con ExpressRoute.](#example-6-adding-a-hybrid-connection-with-expressroute)</br> Durante los próximos meses se agregarán a este documento ejemplos para incorporar conexiones entre redes virtuales, alta disponibilidad y encadenamiento de servicios.

## Protección de infraestructura y cumplimiento normativo de Microsoft
Microsoft ha adoptado una posición de liderazgo al apoyar las iniciativas de cumplimiento normativo requeridas por los clientes de empresa. Estas son algunas de las certificaciones de cumplimiento normativo para Azure: ![Notificaciones de cumplimiento de Azure][1]

Para obtener más detalles, consulte la información de cumplimiento normativo en el [Centro de confianza de Microsoft](https://azure.microsoft.com/support/trust-center/compliance/).

Microsoft tiene un enfoque integral para proteger la infraestructura en la nube necesaria para ejecutar servicios globales a gran escala. La infraestructura en la nube de Microsoft incluye hardware, software, redes y personal administrativo y operativo, así como centros de datos físicos.

![Características de seguridad de Azure][2]

Este enfoque proporciona una base más segura para que los clientes implementen los servicios en la nube de Microsoft. El siguiente paso es que los clientes diseñen y creen una arquitectura de seguridad para proteger estos servicios.

## Arquitecturas de seguridad tradicionales y redes perimetrales
Aunque Microsoft realiza importantes inversiones en la protección de la infraestructura en la nube, los clientes también deben proteger los servicios en la nube y los grupos de recursos. Un enfoque de varios niveles de seguridad proporciona la mejor defensa. Una zona de seguridad de red perimetral protege los recursos de red internos de una red que no sea de confianza. Una red perimetral hace referencia a los bordes o partes de la red que se encuentran entre Internet y la infraestructura de TI protegida de la empresa.

En las redes de empresa típicas, la infraestructura principal está considerablemente fortalecida en los perímetros, con varios niveles de dispositivos de seguridad. El límite de cada nivel consta de dispositivos y puntos de aplicación de directivas. Los dispositivos pueden incluir lo siguiente: firewalls, prevención de denegación de servicio distribuido (DDoS), sistemas de detección o prevención de intrusiones (IDS/IPS) y dispositivos de VPN. La aplicación de directivas puede adoptar la forma de directivas de firewall, listas de control de acceso (ACL) o enrutamiento específico. La primera línea de defensa de la red, que acepta directamente el tráfico entrante desde Internet, consta de una combinación de estos mecanismos para bloquear los ataques y el tráfico perjudicial mientras permite que las solicitudes legítimas avancen por la red. Este tráfico se enruta directamente a los recursos de la red perimetral. A continuación, ese recurso puede "hablar" con recursos más profundos de la red, pasando primero el siguiente límite para la validación. El nivel exterior se denomina red perimetral porque esta parte de la red está expuesta a Internet, normalmente con algún tipo de protección a ambos lados. En la siguiente ilustración se muestra un ejemplo de una sola red perimetral de subred en una red corporativa con dos límites de seguridad.

![Una red perimetral en una red corporativa][3]

Existen numerosas arquitecturas usadas para implementar una red perimetral, desde un simple equilibrador de carga ante una granja de servidores web hasta varias redes perimetrales de subred con distintos mecanismos en cada límite para bloquear el tráfico y proteger los niveles más profundos de la red corporativa. El modo de crear la red perimetral depende de las necesidades específicas de la organización y de su tolerancia general al riesgo.

Al desplazar los clientes las cargas de trabajo a nubes públicas, es fundamental admitir capacidades similares para la arquitectura de la red perimetral en Azure a fin de cumplir los requisitos de seguridad y cumplimiento normativo. En este documento se proporcionan instrucciones sobre cómo pueden crear los clientes un entorno de red seguro en Azure. Se centra en la red perimetral, pero también incluye una explicación completa de muchos aspectos relativos a la seguridad de red. Esta explicación se basa en las siguientes preguntas:

* ¿Cómo se puede crear una red perimetral en Azure?
* ¿Cuáles son algunas de las características de Azure disponibles para crear la red perimetral?
* ¿Cómo se pueden proteger las cargas de trabajo back-end?
* ¿Cómo se controlan las comunicaciones de Internet con las cargas de trabajo en Azure?
* ¿Cómo se pueden proteger las redes locales de las implementaciones en Azure?
* ¿Cuándo se deben usar las características de seguridad de Azure nativas frente a dispositivos o servicios de terceros?

En el diagrama siguiente se muestran los diferentes niveles de seguridad que Azure ofrece a los clientes. Estos niveles son nativos en la plataforma de Azure y características definidas por el cliente:

![Arquitectura de seguridad de Azure][4]

DDoS de Azure, entrante desde Internet, protege frente a ataques a gran escala contra Azure. El nivel siguiente son los puntos de conexión públicos definidos por el cliente, que se usan para determinar el tráfico que puede pasar a través del servicio en la nube a la red virtual. El aislamiento de la red virtual de Azure nativa garantiza un aislamiento completo de todas las demás redes y que el tráfico solo fluya a través de los métodos y las rutas de acceso configurados por el usuario. Estas rutas de acceso y métodos son el siguiente nivel, en el que se pueden usar NSG, UDR y los dispositivos virtuales de red para crear límites de seguridad y así proteger las implementaciones de aplicaciones en la red protegida.

En la siguiente sección se proporciona información general sobre las redes virtuales de Azure. Los clientes crean estas redes virtuales, que son a las que están conectadas las cargas de trabajo implementadas. Las redes virtuales constituyen la base de todas las características de seguridad de red necesarias para establecer una red perimetral con el fin de proteger las implementaciones de clientes en Azure.

## Información general sobre las redes virtuales de Azure
Antes de que el tráfico de Internet llegue a las redes virtuales de Azure, hay dos niveles de seguridad inherentes a la plataforma Azure:

1. **Protección de DDoS**: la protección de DDoS es un nivel de la red física de Azure que protege la plataforma Azure contra ataques a gran escala basados en Internet. Estos ataques usan varios nodos de "bot" en un intento de sobrecargar un servicio de Internet. Azure ofrece una sólida malla de protección de DDoS en toda la conectividad de Internet entrante. Este nivel de protección de DDoS no tiene ningún atributo que pueda configurar el usuario y no es accesible para el cliente. Esto protege a Azure como plataforma de los ataques a gran escala, pero no protegerá directamente las aplicaciones de cliente individuales. El cliente puede configurar niveles adicionales de resistencia contra un ataque localizado. Por ejemplo, si se ataca al cliente A con un ataque de DDoS a gran escala en un punto de conexión público, Azure bloqueará las conexiones a ese servicio. El cliente A puede conmutar a otra red virtual o a un punto de conexión de servicio no implicado en el ataque para restaurar el servicio. Se debe tener en cuenta que, aunque el cliente A podría verse afectado en ese punto de conexión, no se vería afectado ningún otro servicio fuera de ese punto de conexión. Además, otros clientes y servicios no verían impacto alguno de este ataque.
2. **Puntos de conexión de servicio**: los puntos de conexión permiten que los servicios en la nube o los grupos de recursos tengan expuestos los puertos y las direcciones IP de Internet públicas. El punto de conexión usará la traducción de direcciones de red (NAT) para enrutar el tráfico a la dirección interna y el puerto de la red virtual de Azure. Se trata de la ruta de acceso principal para que el tráfico externo pase a la red virtual. El usuario puede configurar los puntos de conexión de servicio para determinar qué tráfico se pasa, y cómo y dónde se traduce en la red virtual.

Una vez que el tráfico llega a la red virtual, entran en juego muchas características. Las redes virtuales de Azure son la base para que los clientes conecten sus cargas de trabajo, y en ellas se aplica la seguridad de nivel de red básica. Es una red privada (una superposición de red virtual) en Azure para clientes con las funciones y características siguientes:

* **Aislamiento del tráfico**: una red virtual es el límite de aislamiento del tráfico en la plataforma Azure. Las máquinas virtuales de una red virtual no se pueden comunicar directamente con las máquinas virtuales de otra red virtual, incluso si las dos redes virtuales las creó el mismo cliente. Se trata de una propiedad fundamental que garantiza que las máquinas virtuales del cliente y la comunicación sigan siendo privadas en una red virtual.
* **Topología de múltiples niveles**: las redes virtuales permiten a los clientes definir una topología de múltiples niveles al asignar subredes y designar espacios de direcciones independientes para distintos elementos o "niveles" de las cargas de trabajo. Estas agrupaciones lógicas y topologías permiten a los clientes definir una directiva de acceso diferente en función de los tipos de cargas de trabajo y controlar igualmente los flujos de tráfico entre los niveles.
* **Conectividad entre instalaciones locales**: los clientes pueden establecer conectividad entre instalaciones locales entre una red virtual y varios sitios locales u otras redes virtuales en Azure. Para ello, deben usar puertas de enlace de VPN de Azure o dispositivos virtuales de red de terceros. Azure es compatible con VPN de sitio a sitio (S2S) mediante los protocolos estándar IPsec/IKE y conectividad privada de ExpressRoute.
* **NSG** permite a los clientes crear reglas (ACL) con el nivel de granularidad deseado: interfaces de red, máquinas virtuales individuales o subredes virtuales. Los clientes pueden controlar el acceso al permitir o denegar la comunicación entre las cargas de trabajo dentro de una red virtual, desde sistemas de redes del cliente a través de conectividad entre locales o comunicación de Internet directa.
* **UDR** y **Reenvío IP** permiten a los clientes definir las rutas de comunicación entre distintos niveles dentro de una red virtual. Los clientes pueden implementar un firewall, IDS/IPS y otros dispositivos virtuales y enrutar el tráfico de red a través de estos dispositivos de seguridad para la aplicación de directivas de límites de seguridad, auditoría e inspección.
* **Dispositivos virtuales de red** en Azure Marketplace: hay dispositivos de seguridad, como firewalls, equilibradores de carga e IDS/IPS disponibles en Azure Marketplace y la Galería de imágenes de máquina virtual. Los clientes pueden implementar estos dispositivos en sus redes virtuales y, en concreto, en los límites de seguridad (incluidas las subredes de la red perimetral) para completar un entorno de red segura en múltiples niveles.

A continuación se muestra un ejemplo de cómo se puede construir una arquitectura de red perimetral en Azure con estas características y funcionalidades:

![Una red perimetral en una red virtual de Azure][5]

## Características y requisitos de una red perimetral
La red perimetral está diseñada para ser el front-end de la red y crear una interfaz directamente con la comunicación desde Internet. Los paquetes entrantes deben fluir a través de los dispositivos de seguridad, como el firewall, IDS e IPS, antes de llegar a los servidores back-end. Los paquetes enlazados a Internet desde las cargas de trabajo también pueden fluir a través de los dispositivos de seguridad de la red perimetral para fines de auditoría, inspección y aplicación de directivas antes de salir de la red. Además, la red perimetral puede hospedar puertas de enlace de VPN entre locales entre redes virtuales del cliente y redes locales.

### Características de una red perimetral
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
  * Los dispositivos de seguridad de los límites de la red perimetral accesibles tanto desde Internet como desde la red protegida (por ejemplo, los dos iconos de firewall que aparecen en la ilustración anterior) podrían ser en realidad un solo dispositivo virtual con reglas o interfaces diferenciadas para cada límite. (Es decir, un dispositivo dividido de forma lógica que administra la carga de los dos límites de la red perimetral).
* Otras prácticas y restricciones comunes:
  * Las cargas de trabajo no deben almacenar información crítica de la empresa.
  * El acceso y las actualizaciones de las configuraciones e implementaciones de red perimetral se limitan solo a los administradores autorizados.

### Requisitos de red perimetral
Para habilitar estas características, siga estas instrucciones relativas a los requisitos de red virtual para implementar una red perimetral correcta:

* **Arquitectura de subred:** especifique la red virtual de modo que se dedique una subred completa como red perimetral, separada de otras subredes en la misma red virtual. Esto garantiza que el tráfico entre la red perimetral y otros niveles de subred interna o privada fluya a través de un firewall o dispositivo virtual IDS/IPS en los límites de la subred con rutas definidas por el usuario.
* **NSG:** la subred de la red perimetral debe estar abierta para permitir la comunicación con Internet, pero esto no significa que los clientes puedan omitir los grupos de seguridad de red. Siga las recomendaciones de seguridad habituales para minimizar las superficies de red expuestas a Internet. Bloquee los intervalos de direcciones remotas que pueden acceder a las implementaciones o los protocolos de aplicación específicos y los puertos que estén abiertos. No obstante, en algunos casos, esto no siempre es posible. Por ejemplo, si los clientes tienen un sitio web externo en Azure, la red perimetral debe permitir las solicitudes web entrantes desde cualquier dirección IP pública, pero solo debe abrir los puertos de aplicación web TCP:80 y TCP:443.
* **Tabla de enrutamiento:** la subred de red perimetral en sí debe poder comunicarse directamente con Internet, pero no debe permitir la comunicación directa al back-end y desde él o en redes locales sin pasar por un firewall o dispositivo de seguridad.
* **Configuración del dispositivo de seguridad:** para enrutar e inspeccionar los paquetes entre la red perimetral y el resto de las redes protegidas, los dispositivos de seguridad, como dispositivos de firewall, IDS e IPS, pueden tener hosts múltiples. También pueden tener NIC independientes para la red perimetral y las subredes back-end. Las NIC de la red perimetral se comunican directamente desde y hacia Internet, con los correspondientes grupos de seguridad de red y tabla de enrutamiento de red perimetral. Las NIC que se conectan a las subredes back-end tienen grupos de seguridad de red y tablas de enrutamiento de las correspondientes subredes back-end más restringidos.
* **Funciones del dispositivo de seguridad:** los dispositivos de seguridad implementados en la red perimetral suelen realizar las siguientes funcionalidades:
  * Firewall: aplicar reglas de firewall o directivas de control de acceso para las solicitudes entrantes.
  * Detección y prevención de amenazas: detectar y mitigar los ataques malintencionados desde Internet.
  * Auditoría y registro: mantener registros detallados para auditoría y análisis.
  * Proxy inverso: redirigir las solicitudes entrantes a los servidores back-end correspondientes. Esto implica asignar y traducir las direcciones de destino en los dispositivos front-end, normalmente firewalls, a las direcciones de servidor back-end.
  * Proxy de reenvío: proporcionar NAT y realizar auditorías para comunicaciones iniciadas desde la red virtual hacia Internet.
  * Enrutador: reenviar tráfico entrante y entre subredes dentro de la red virtual.
  * Dispositivo VPN: actuar como puertas de enlace de VPN entre locales para conectividad VPN entre locales entre las redes locales del cliente y las redes virtuales de Azure.
  * Servidor VPN: aceptar clientes de VPN que se conectan a redes virtuales de Azure.

> [!TIP]
> Mantener separados los dos grupos siguientes: las personas autorizadas a acceder al engranaje de seguridad de la red perimetral y las personas autorizadas como administradores de desarrollo, implementación u operaciones de aplicaciones. Mantener estos grupos separados permite una separación de funciones y evita que una sola persona omita la seguridad de las aplicaciones y los controles de seguridad de red.
> 
> 

### Preguntas que se deben plantear al crear límites de red
En esta sección, salvo que se especifique de otro modo, el término "redes" hace referencia a las redes virtuales privadas de Azure creadas por un administrador de la suscripción. El término no hace referencia a las redes físicas subyacentes dentro de Azure.

Además, las redes virtuales de Azure a menudo se usan para extender las redes locales tradicionales. Es posible incorporar soluciones de redes híbridas de sitio a sitio o de ExpressRoute con arquitecturas de redes perimetrales. Esta es una consideración importante a la hora de crear límites de seguridad de red.

Es fundamental que conteste a las tres preguntas siguientes al crear una red con una red perimetral y varios límites de seguridad.

#### 1) ¿Cuántos límites se necesitan?
La primera decisión consiste en decidir cuántos límites de seguridad son necesarios en un escenario determinado:

* Un solo límite: uno en la red perimetral front-end entre la red virtual e Internet.
* Dos límites: uno en el lado de Internet de la red perimetral y otro entre la subred de la red perimetral y las subredes back-end de las redes virtuales de Azure.
* Tres límites: uno en el lado de Internet de la red perimetral, otro entre la red perimetral y las subredes back-end, y otro entre las subredes back-end y la red local.
* N límites: un número variable. En función de los requisitos de seguridad, puede aplicarse cualquier número de límites de seguridad en una red determinada.

El número y el tipo de límites necesarios variarán en función de la tolerancia al riesgo de la empresa y el escenario concreto que se implementa. Por lo general, suele ser una decisión tomada conjuntamente por varios grupos de una organización, que a menudo incluye un equipo de riesgos y cumplimiento, un equipo de redes y plataformas, y un equipo de desarrollo de aplicaciones. Las personas con conocimientos sobre seguridad, los datos implicados y las tecnologías que se van a usar deben participar en esta toma de decisiones a fin de garantizar la posición de seguridad adecuada para cada implementación.

> [!TIP]
> Use el menor número posible de límites que cumplan los requisitos de seguridad para una situación determinada. Cuanto mayor sea el número de límites, más difíciles pueden ser las operaciones y la solución de problemas, así como la sobrecarga de administración implicada en la administración de varias directivas de límites con el tiempo. Sin embargo, unos límites insuficientes aumentan el riesgo. Encontrar el equilibrio es fundamental.
> 
> 

![Red híbrida con tres límites de seguridad][6]

En la ilustración anterior se muestra una vista de alto nivel de una red con tres límites de seguridad. Los límites se encuentran entre la red perimetral e Internet, las subredes privadas de front-end y back-end de Azure, y la subred de back-end de Azure y la red corporativa local.

#### 2) ¿Dónde se encuentran los límites?
Una vez decidido el número de límites, la siguiente decisión es saber dónde se implementan. Generalmente, hay tres opciones:

* Uso de un servicio intermedio basado en Internet (por ejemplo, un firewall de aplicaciones web basado en la nube, que no se trata en este documento).
* Uso de características nativas o dispositivos virtuales de red en Azure.
* Uso de dispositivos físicos de la red local.

En redes únicamente de Azure, las opciones son características de Azure nativas (por ejemplo, equilibradores de carga de Azure) o dispositivos virtuales de red del ecosistema de socios enriquecido de Azure (por ejemplo, firewalls de punto de comprobación).

Si se necesita un límite entre Azure y una red local, los dispositivos de seguridad pueden residir en cualquier lado de la conexión (o en ambos). Por lo tanto, se debe tomar una decisión sobre la ubicación en la que se va a colocar un engranaje de seguridad.

En la ilustración anterior, los límites entre Internet y red perimetral y entre front-end y back-end están totalmente incluidos en Azure y deben ser características nativas de Azure o dispositivos virtuales de red. Los dispositivos de seguridad en el límite entre Azure (subred back-end) y la red corporativa pueden estar tanto en el lado de Azure como en el local o incluso una combinación de dispositivos en ambos lados. Pueden existir importantes ventajas y desventajas en cualquiera de las opciones, que se deben tener muy en cuenta.

Por ejemplo, el uso del engranaje de seguridad físico existente en el lado de la red local tiene la ventaja de que no se necesita ningún engranaje nuevo. Simplemente hay que volver a configurarlo. La desventaja es que todo el tráfico debe ir desde Azure a la red local para que lo pueda ver el engranaje de seguridad. Por lo tanto, el tráfico de Azure a Azure puede sufrir una latencia considerable y afectar a la experiencia de usuario y al rendimiento de las aplicaciones si se obligó a dirigirlo a la red local para la aplicación de directivas de seguridad.

#### 3) ¿Cómo se implementan los límites?
Cada límite de seguridad probablemente tendrá requisitos de capacidad diferentes (por ejemplo, IDS y reglas de firewall en el lado de Internet de la red perimetral, pero solo listas de control de acceso entre la red perimetral y la subred back-end). La decisión sobre los dispositivos que se van a usar depende de los requisitos de seguridad y del escenario. En la sección siguiente, los ejemplos 1, 2 y 3 describen algunas opciones que se podrían usar. La revisión de las características de red nativa de Azure y los dispositivos disponibles en Azure en el ecosistema de socios muestra las innumerables opciones disponibles para resolver prácticamente cualquier escenario.

Otro punto de decisión de implementación clave es cómo conectar la red local con Azure. ¿Debería usar la puerta de enlace virtual de Azure o un dispositivo virtual de red? Estas opciones se tratan con más detalle en la sección siguiente (ejemplos 4, 5 y 6).

Además, podría ser necesario el tráfico entre redes virtuales dentro de Azure. Estos escenarios se agregarán en una fecha posterior.

Cuando sepa las respuestas a las preguntas anteriores, la sección [Inicio rápido](#fast-start) le ayudará a identificar qué ejemplos son más adecuados para un escenario determinado.

## Ejemplos: Creación de límites de seguridad con redes virtuales de Azure
### Ejemplo 1: Creación de una red perimetral para proteger las aplicaciones con grupos de seguridad de red
[Volver a inicio rápido](#fast-start) | [Instrucciones de compilación detalladas para este ejemplo][Example1]

![Red perimetral de entrada con grupo de seguridad de red][7]

#### Descripción del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

* Dos servicios en la nube: "FrontEnd001" y "BackEnd001"
* Una red virtual, "CorpNetwork", con dos subredes: "FrontEnd" y "BackEnd"
* Un grupo de seguridad de red que se aplica a ambas subredes
* Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
* Dos servidores Windows que representan servidores back-end de aplicaciones ("AppVM01", "AppVM02")
* Un servidor Windows que representa un servidor DNS ("DNS01")

Para ver los scripts y una plantilla de Azure Resource Manager, consulte las [instrucciones de compilación detalladas][Example1].

#### Descripción del grupo de seguridad de red
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

Con estas reglas enlazadas a cada subred, si entrase una solicitud HTTP desde Internet al servidor web, se aplicarían las reglas 3 (permitir) y 5 (denegar). Sin embargo, dado que la regla 3 tiene una prioridad más alta, solo se aplicaría esta, y la regla 5 no entraría en juego. Por lo tanto, se permitiría la solicitud HTTP al servidor web. Si ese mismo tráfico estaba intentando ponerse en contacto con el servidor DNS01, se aplicaría primero la regla 5 (denegar) y no se permitiría que el tráfico pasase al servidor. La regla 6 (denegar) bloquea la subred front-end para impedirle comunicarse con la subred back-end (excepto en el caso del tráfico permitido en las reglas 1 y 4). Esto protege la red back-end en caso de que un atacante ponga en peligro la aplicación web en el front-end. El atacante tendría acceso limitado a la red back-end "protegida" (únicamente a los recursos expuestos en el servidor AppVM01).

Hay una regla de salida predeterminada que permite el tráfico saliente a Internet. En este ejemplo, permitimos el tráfico saliente y no modificamos las reglas de salida. Para bloquear el tráfico en ambas direcciones, es necesario el enrutamiento definido por el usuario (vea el ejemplo 3).

#### Conclusión
Se trata de una manera relativamente sencilla y directa de aislar la subred back-end del tráfico entrante. Para obtener más información, consulte las [instrucciones de compilación detalladas][Example1]. Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral con scripts de PowerShell.
* Creación de esta red perimetral con una plantilla de Azure Resource Manager.
* Descripciones detalladas de cada comando de grupo de seguridad de red.
* Escenarios de flujo de tráfico detallados, que muestran cómo se permite o se deniega el tráfico en cada nivel.

### Ejemplo 2: Creación de una red perimetral para proteger las aplicaciones con un firewall y grupos de seguridad de red [Volver a inicio rápido](#fast-start) | [Instrucciones de compilación detalladas para este ejemplo][Example2]
![Red perimetral de entrada con dispositivo virtual de red y grupo de seguridad de red][8]

#### Descripción del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

* Dos servicios en la nube: "FrontEnd001" y "BackEnd001"
* Una red virtual, "CorpNetwork", con dos subredes: "FrontEnd" y "BackEnd"
* Un grupo de seguridad de red que se aplica a ambas subredes
* Un dispositivo virtual de red, en este caso un firewall, conectado a la subred front-end
* Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
* Dos servidores Windows que representan servidores back-end de aplicaciones ("AppVM01", "AppVM02")
* Un servidor Windows que representa un servidor DNS ("DNS01")

Para ver los scripts y una plantilla de Azure Resource Manager, consulte las [instrucciones de compilación detalladas][Example2].

#### Descripción del grupo de seguridad de red
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

Con estas reglas enlazadas a cada subred, si entrase una solicitud HTTP desde Internet al firewall, se aplicarían las reglas 3 (permitir) y 5 (denegar). Sin embargo, dado que la regla 3 tiene una prioridad más alta, solo se aplicaría esta, y la regla 5 no entraría en juego. Por lo tanto, se permitiría la solicitud HTTP al firewall. Si ese mismo tráfico intentase ponerse en contacto con el servidor IIS01, incluso aunque esté en la subred front-end, se aplicaría la regla 5 (denegar) y no se permitiría que el tráfico pasara al servidor. La regla 6 (denegar) bloquea la subred front-end para impedirle comunicarse con la subred back-end (excepto en el caso del tráfico permitido en las reglas 1 y 4). Esto protege la red back-end en caso de que un atacante ponga en peligro la aplicación web en el front-end. El atacante tendría acceso limitado a la red back-end "protegida" (únicamente a los recursos expuestos en el servidor AppVM01).

Hay una regla de salida predeterminada que permite el tráfico saliente a Internet. En este ejemplo, permitimos el tráfico saliente y no modificamos las reglas de salida. Para bloquear el tráfico en ambas direcciones, es necesario el enrutamiento definido por el usuario (vea el ejemplo 3).

#### Descripción de reglas de firewall
En el firewall, deben crearse reglas de reenvío. Como en este ejemplo solo se enruta el tráfico entrante de Internet al firewall y después al servidor web, únicamente se necesita un regla de traducción de direcciones de red (NAT) de reenvío.

La regla de enrutamiento acepta cualquier dirección de origen entrante que alcance el firewall al intentar llegar a HTTP (puerto 80 o 443 para HTTPS). Se envía fuera de la interfaz local del firewall y se redirige al servidor web con la dirección IP de 10.0.1.5.

#### Conclusión
Se trata de una manera relativamente sencilla de proteger la aplicación con un firewall y de aislar la subred back-end del tráfico entrante. Para obtener más información, consulte las [instrucciones de compilación detalladas][Example2]. Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral con scripts de PowerShell.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Descripciones detalladas de cada comando de grupo de seguridad de red y regla de firewall.
* Escenarios de flujo de tráfico detallados, que muestran cómo se permite o se deniega el tráfico en cada nivel.

### Ejemplo 3: Creación de una red perimetral para proteger las redes con un firewall, UDR y NSG
[Volver a inicio rápido](#fast-start) | [Instrucciones de compilación detalladas para este ejemplo][Example3]

![Red perimetral bidireccional con dispositivo de red virtual, grupo de seguridad de red y enrutamiento definido por el usuario][9]

#### Descripción del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

* Tres servicios en la nube: “SecSvc001”, “FrontEnd001” y “BackEnd001”
* Una red virtual, "CorpNetwork", con tres subredes: "SecNet", "FrontEnd" y "BackEnd"
* Un dispositivo virtual de red, en este caso un firewall, conectado a la subred SecNet
* Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
* Dos servidores Windows que representan servidores back-end de aplicaciones ("AppVM01", "AppVM02")
* Un servidor Windows que representa un servidor DNS ("DNS01")

Para ver los scripts y una plantilla de Azure Resource Manager, consulte las [instrucciones de compilación detalladas][Example3].

#### Descripción de UDR
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

VNETLocal es siempre el prefijo de dirección definido de la red virtual para esa red específica (es decir, cambia de una red virtual a otra según cómo se defina cada red virtual específica). Las rutas de sistema restantes son estáticas y tienen el valor predeterminado indicado en la tabla.

En este ejemplo, se crean dos tablas de enrutamiento para las subredes front-end y back-end. Cada tabla se carga con rutas estáticas adecuadas para la subred indicada. Para este ejemplo, cada tabla tiene tres rutas que dirigen todo el tráfico (0.0.0.0/0) a través del firewall (salto siguiente = dirección IP del dispositivo virtual):

1. Tráfico de subred local sin próximo salto definido para que el tráfico de la subred local pase por alto el firewall.
2. Tráfico de red virtual con un próximo salto definido como firewall; esto invalida la regla predeterminada que permite que el tráfico de la red virtual local se enrute directamente.
3. Todo el tráfico restante (0/0) con un próximo salto definido como firewall.

> [!TIP]
> No disponer de la entrada de la subred local en el UDR hará que se interrumpan las comunicaciones de la subred local.
> 
> * En el ejemplo, 10.0.1.0/24 que está apuntando a VNETLocal resulta crítico ya que, de lo contrario, el paquete que sale del servidor web (10.0.1.4) con destino a otro servidor local (por ejemplo) 10.0.1.25 producirá un error ya que se enviará a NVA, que a su vez lo enviará a la subred y ésta lo reenviará nuevamente a NVA y así sucesivamente.
> * Las posibilidades de que se produzca un bucle de enrutamiento son normalmente mayores en aplicaciones con varias NIC que están conectadas directamente a cada subred con la que se comunican, lo cual suele suceder en el caso de las aplicaciones tradicionales locales.
> 
> 

Una vez creadas las tablas de enrutamiento, se enlazan a sus subredes. Una vez creada la tabla de enrutamiento de la subred front-end y enlazada a la subred, debe tener el siguiente aspecto:

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

#### Descripción de reenvío IP
El reenvío IP es una característica complementaria del enrutamiento definido por el usuario. Es una opción de configuración de los dispositivos virtuales que les permite recibir tráfico no dirigido específicamente al dispositivo y, después, reenviar ese tráfico a su destino final.

Por ejemplo, si el tráfico de AppVM01 realiza una solicitud al servidor DNS01, el enrutamiento definido por el usuario lo enrutará al firewall. Con el reenvío IP habilitado, el dispositivo (10.0.0.4) acepta el tráfico dirigido a DNS01 (10.0.2.4) y, a continuación, se reenvía a su destino final (10.0.2.4). Sin el reenvío IP habilitado en el firewall, el dispositivo no aceptaría el tráfico aunque la tabla de enrutamiento tuviese el firewall como próximo salto. Para usar un dispositivo virtual, es fundamental acordarse de habilitar el reenvío IP junto con el enrutamiento definido por el usuario.

#### Descripción del grupo de seguridad de red
En este ejemplo, se crea un grupo de seguridad de red y se carga después con una única regla. Este grupo se enlaza solo a las subredes front-end y back-end (no el SecNet). Mediante declaración se genera la siguiente regla:

* Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (todas las subredes).

Aunque en este ejemplo se usan grupos de seguridad de red, su principal objetivo es constituir un segundo nivel de defensa frente a errores de configuración manual. El objetivo es bloquear todo el tráfico entrante desde Internet a las subredes front-end o back-end. El tráfico solo debe fluir a través de la subred SecNet al firewall (y después, si procede, a las subredes front-end o back-end). Además, con las reglas de enrutamiento definido por el usuario vigentes, el tráfico que llegue a las subredes front-end o back-end se redirigirá al firewall (gracias al enrutamiento definido por el usuario). El firewall lo consideraría un flujo asimétrico y descartaría el tráfico saliente. Por lo tanto, hay tres niveles de seguridad que protegen las subredes:

* Ningún punto de conexión abierto en los servicios en la nube FrontEnd001 y BackEnd001.
* Grupos de seguridad de red que deniegan el tráfico de Internet.
* El firewall que elimina el tráfico asimétrico.

Un punto interesante sobre el grupo de seguridad de red de este ejemplo es que contiene solo una regla, que consiste en denegar el tráfico de Internet a toda la red virtual, incluida la subred de seguridad. Sin embargo, como el grupo de seguridad de red solo está enlazado a las subredes front-end y back-end, la regla no se procesa en el tráfico entrante a la subred de seguridad. Como resultado, el tráfico fluirá a la subred de seguridad.

#### Reglas de firewall
En el firewall, deben crearse reglas de reenvío. Dado que el firewall bloquea o reenvía todo el tráfico entrante, saliente y entre redes virtuales, se necesitan muchas reglas de firewall. Además, todo el tráfico entrante llegará a la dirección IP pública del servicio de seguridad (en puertos diferentes) para ser procesado por el firewall. El procedimiento recomendado es crear un diagrama de los flujos lógicos antes de configurar las subredes y las reglas de firewall para evitar las modificaciones más adelante. La siguiente ilustración es una vista lógica de las reglas de firewall para este ejemplo:

![Vista lógica de las reglas de firewall][10]

> [!NOTE]
> Según el dispositivo virtual de red usado, los puertos de administración varían. En este ejemplo se hace referencia a un firewall Barracuda NextGen que usa los puertos 22, 801 y 807. Consulte la documentación del proveedor del dispositivo para buscar los puertos exactos usados para la administración del dispositivo que se va a usar.
> 
> 

#### Descripción de reglas de firewall
En el diagrama lógico anterior, no se muestra la subred de seguridad. Esto se debe a que el firewall es el único recurso de la subred y este diagrama muestra las reglas de firewall y cómo permiten o deniegan lógicamente los flujos de tráfico, no la ruta enrutada real. Además, los puertos externos seleccionados para el tráfico RDP están en un intervalo más alto (8014-8026) y se han seleccionado para que se correspondan en cierto modo con los dos últimos octetos de la dirección IP local y facilitar así su legibilidad (por ejemplo, la dirección de servidor local 10.0.1.4 está asociada al puerto externo 8014). Sin embargo, podría usarse cualquier puerto superior que no planteara conflictos.

En este ejemplo, necesitamos siete tipos de reglas:

* Reglas externas (para el tráfico entrante):
  1. Regla de administración de firewall: esta regla de redirección de aplicación permite que el tráfico pase a los puertos de administración del dispositivo virtual de red.
  2. Reglas de RDP (para cada servidor Windows): estas cuatro reglas (una para cada servidor) permiten la administración de los servidores individuales a través de RDP. También se puede agrupar en una regla según las capacidades del dispositivo virtual de red que se use.
  3. Reglas de tráfico de aplicación: hay dos reglas de este tipo, la primera para el tráfico web front-end y la segunda para el tráfico back-end (por ejemplo, servidor web a capa de datos). La configuración de estas reglas depende de la arquitectura de red (donde están situados los servidores) y los flujos de tráfico (en qué dirección fluye el tráfico y qué puertos se usan).
     * La primera regla permite que el tráfico de aplicación real llegue al servidor de aplicaciones. Mientras que las demás reglas permiten la seguridad y la administración, las reglas de tráfico de aplicación son las que permiten a los usuarios o servicios externos acceder a las aplicaciones. En este ejemplo, hay un solo servidor web en el puerto 80. Por lo tanto, una sola regla de aplicación de firewall redirige el tráfico entrante a la dirección IP externa y a la dirección IP interna de los servidores web. La sesión de tráfico redirigido se traduciría a través de NAT al servidor interno.
     * La segunda regla es la regla back-end que permite que el servidor web se comunique con el servidor AppVM01 (no AppVM02) a través de cualquier puerto.
* Reglas internas (para el tráfico entre redes virtuales)
  1. Regla de saliente a Internet: esta regla permite que el tráfico de cualquier red pase a las redes seleccionadas. Normalmente, esta es una regla predeterminada que ya existe en el firewall, pero en estado deshabilitado. Esta regla debe habilitarse para este ejemplo.
  2. Regla DNS: esta regla permite que pase solo el tráfico DNS (puerto 53) al servidor DNS. Para este entorno se bloquea la mayor parte del tráfico de front-end a back-end. Esta regla permite específicamente DNS desde cualquier subred local.
  3. Regla de subred a subred: esta regla permite que cualquier servidor en la subred back-end conecte con cualquier servidor en la subred front-end (pero no a la inversa).
* Regla para notificaciones de error (para el tráfico que no cumple ninguna de las anteriores):
  1. Denegar todas las reglas de tráfico: esta debe ser siempre la última regla (en términos de prioridad) y, como tal, si un flujo de tráfico no coincide con ninguna de las reglas anteriores, esta regla lo descartará. Se trata de una regla predeterminada y suele estar activada. Generalmente no hace falta ninguna modificación.

> [!TIP]
> En la segunda regla de tráfico de aplicación, para simplificar el ejemplo, se permite cualquier puerto. En un escenario real, deben usarse el puerto y los intervalos de direcciones más específicos para reducir la superficie expuesta a ataques de esta regla.
> 
> 

Una vez creadas todas las reglas anteriores, es importante revisar la prioridad de cada una para asegurarse de que el tráfico se permitirá o se denegará según se desee. En este ejemplo, las reglas están en orden de prioridad.

#### Conclusión
Se trata de una manera más compleja pero más completa de proteger y aislar la red que los ejemplos anteriores. (En el ejemplo 2 solo se protege la aplicación y en el ejemplo 1 solo se aíslan las subredes). Este diseño permite supervisar el tráfico en ambas direcciones y protege no solo el servidor de aplicaciones de entrada, sino que aplica la directiva de seguridad de red a todos los servidores de esta red. Además, según el dispositivo que se usa, se pueden lograr el reconocimiento y la auditoría de todo el tráfico. Para más información, consulte las [instrucciones de compilación detalladas][Example3]. Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral de ejemplo con scripts de PowerShell.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Descripciones detalladas de cada enrutamiento definido por el usuario, comando de grupo de seguridad de red y regla de firewall.
* Escenarios de flujo de tráfico detallados, que muestran cómo se permite o se deniega el tráfico en cada nivel.

### Ejemplo 4: Incorporación de una conexión híbrida con una red privada virtual (VPN) de dispositivo virtual de sitio a sitio
[Volver a inicio rápido](#fast-start) | Instrucciones de compilación disponibles pronto

![Red perimetral con red híbrida conectada a un dispositivo virtual de red][11]

#### Descripción del entorno
Las redes híbridas que usan un dispositivo virtual de red (NVA) pueden agregarse a cualquiera de los tipos de red perimetral descritos en el ejemplo 1, 2 o 3.

Como se muestra en la ilustración anterior, se usa una conexión VPN a través de Internet (sitio a sitio) para conectar una red local a una red virtual de Azure a través de un dispositivo virtual de red.

> [!NOTE]
> Si usa ExpressRoute con la opción de configuración entre pares públicos de Azure habilitada, debe crear una ruta estática. Esta debe enrutar a la dirección IP de VPN del dispositivo de red virtual con Internet corporativo, y no con la WAN de ExpressRoute. La NAT requerida en la opción de configuración entre pares públicos de ExpressRoute de Azure puede interrumpir la sesión de VPN.
> 
> 

Una vez implementada la conexión VPN, el dispositivo virtual de red se convierte en el concentrador central para todas las redes y subredes. Las reglas de reenvío de firewall determinan qué flujos de tráfico se permiten, se traducen a través de NAT, se redirigen o se descartan (incluso para los flujos de tráfico entre la red local y Azure).

Los flujos de tráfico deben tenerse en cuenta cuidadosamente, ya que se pueden optimizar o degradar mediante este patrón de diseño en función del caso de uso específico.

Si usa el entorno creado en el ejemplo 3 e incorpora posteriormente una conexión de red híbrida de VPN de sitio a sitio, se producirá el siguiente diseño:

![Red perimetral con un dispositivo virtual de red conectada mediante una VPN de sitio a sitio][12]

El enrutador local, o cualquier otro dispositivo de red que sea compatible con el dispositivo virtual de red para VPN, sería el cliente VPN. Este dispositivo físico sería responsable de iniciar y mantener la conexión VPN con el dispositivo virtual de red.

Según la lógica del dispositivo virtual de red, la red parece estar constituida por cuatro "zonas de seguridad" independientes con las reglas del dispositivo virtual de red como director principal del tráfico entre estas zonas:

![Red lógica desde la perspectiva del dispositivo virtual de red][13]

#### Conclusión
La incorporación de una conexión de red híbrida VPN de sitio a sitio a una red virtual de Azure puede ampliar la red local a Azure de forma segura. Al usar una conexión VPN, el tráfico se cifra y se enruta a través de Internet. El dispositivo virtual de red de este ejemplo proporciona una ubicación central para aplicar y administrar la directiva de seguridad. Para obtener más información, consulte las instrucciones de compilación detalladas (próximamente). Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral de ejemplo con scripts de PowerShell.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Escenarios de flujo de tráfico detallados, que muestran cómo fluye el tráfico en este diseño.

### Ejemplo 5: Incorporación de una conexión híbrida con una VPN de puerta de enlace de Azure de sitio a sitio
[Volver a inicio rápido](#fast-start) | Instrucciones de compilación disponibles pronto

![Red perimetral con red híbrida conectada a una puerta de enlace][14]

#### Descripción del entorno
Las redes híbridas que usan una puerta de enlace de VPN de Azure se pueden agregar a cualquier tipo de red perimetral descrito en los ejemplos 1 y 2.

Como se muestra en la ilustración anterior, se usa una conexión VPN a través de Internet (de sitio a sitio) para conectar una red local a una red virtual de Azure a través de una puerta de enlace de VPN de Azure.

> [!NOTE]
> Si usa ExpressRoute con la opción de configuración entre pares públicos de Azure habilitada, debe crear una ruta estática. Esta debe enrutar a la dirección IP de VPN del dispositivo de red virtual con Internet corporativo, y no con la WAN de ExpressRoute. La NAT requerida en la opción de configuración entre pares públicos de ExpressRoute de Azure puede interrumpir la sesión de VPN.
> 
> 

En la siguiente ilustración se muestran los dos bordes de red de esta opción. En el primer borde, el dispositivo virtual de red y los grupos de seguridad de red controlan los flujos de tráfico para redes internas de Azure y entre Azure e Internet. El segundo borde es la puerta de enlace de VPN de Azure, que es un borde de red totalmente independiente y aislado entre la instalación local y Azure.

Los flujos de tráfico deben tenerse en cuenta cuidadosamente, ya que se pueden optimizar o degradar mediante este patrón de diseño en función del caso de uso específico.

Si usa el entorno creado en el ejemplo 1 e incorpora posteriormente una conexión de red híbrida de VPN de sitio a sitio, se producirá el siguiente diseño:

![Red perimetral con puerta de enlace conectada mediante una conexión de ExpressRoute][15]

#### Conclusión
La incorporación de una conexión de red híbrida VPN de sitio a sitio a una red virtual de Azure puede ampliar la red local a Azure de forma segura. Mediante una puerta de enlace nativa de VPN de Azure, el tráfico está cifrado con IPSec y se enruta a través de Internet. Asimismo, mediante la puerta de enlace de VPN de Azure, se proporciona una opción menos costosa (sin costes de licencias adicionales como ocurre con dispositivos virtuales de red de terceros). Donde resulta más económico es en el ejemplo 1, en el que no se usa ningún dispositivo virtual de red. Para obtener más información, consulte las instrucciones de compilación detalladas (próximamente). Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral de ejemplo con scripts de PowerShell.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Escenarios de flujo de tráfico detallados, que muestran cómo fluye el tráfico en este diseño.

### Ejemplo 6: Incorporación de una conexión híbrida con ExpressRoute
[Volver a inicio rápido](#fast-start) | Instrucciones de compilación disponibles pronto

![Red perimetral con red híbrida conectada a una puerta de enlace][16]

#### Descripción del entorno
Las redes híbridas que usan una conexión de configuración entre pares privados de ExpressRoute se pueden agregar a cualquier tipo de red perimetral descrito en el ejemplo 1 o 2.

Como se muestra en la ilustración anterior, la configuración entre pares privados de ExpressRoute proporciona una conexión directa entre la red local y la red virtual de Azure. El tráfico transita solo por la red del proveedor de servicio y la red de Microsoft Azure sin tocar nunca Internet.

> [!NOTE]
> Hay determinadas restricciones en el uso del enrutamiento definido por el usuario con ExpressRoute debido a la complejidad del enrutamiento dinámico que se usa en la puerta de enlace virtual de Azure. Se trata de las siguientes:
> 
> * El enrutamiento definido por el usuario no se debe aplicar a la subred de la puerta de enlace en la que está conectada la puerta de enlace virtual de Azure vinculada a ExpressRoute.
> * La puerta de enlace virtual de Azure vinculada a ExpressRoute no puede ser el dispositivo NextHop para otras subredes enlazadas con enrutamiento definido por el usuario.
> 
> <br />
> 
> [!TIP]
> Mediante ExpressRoute, se mantiene el tráfico de la red corporativa fuera de Internet para una mayor seguridad y se aumenta significativamente el rendimiento. Además, se permiten los contratos de nivel de servicio del proveedor de ExpressRoute. La puerta de enlace de Azure puede pasar a 2 Gbps con ExpressRoute, mientras que con la puerta de enlace de Azure de VPN de sitio a sitio, el rendimiento máximo es de 200 Mbps.
> 
> 

Como se muestra en el diagrama siguiente, con esta opción el entorno tiene ahora dos bordes de red. El dispositivo virtual de red y el grupo de seguridad de red controlan los flujos de tráfico para redes internas de Azure y entre Azure e Internet, mientras que la puerta de enlace es un borde de red totalmente independiente y aislado entre la instalación local y Azure.

Los flujos de tráfico deben tenerse en cuenta cuidadosamente, ya que se pueden optimizar o degradar mediante este patrón de diseño en función del caso de uso específico.

Si usa el entorno creado en el ejemplo 1 e incorpora posteriormente una conexión de red híbrida de ExpressRoute, se producirá el siguiente diseño:

![Red perimetral con puerta de enlace conectada mediante una conexión de ExpressRoute][17]

#### Conclusión
La incorporación de una conexión de red de configuración entre pares privados de ExpressRoute puede ampliar la red local a Azure de un modo seguro, con una latencia inferior y con un rendimiento mayor. Asimismo, mediante la puerta de enlace nativa de Azure, como en este ejemplo, se proporciona una opción menos costosa (sin necesidad de licencias adicionales como ocurre con dispositivos virtuales de red de terceros). Para obtener más información, consulte las instrucciones de compilación detalladas (próximamente). Estas instrucciones incluyen lo siguiente:

* Creación de esta red perimetral de ejemplo con scripts de PowerShell.
* Creación de este ejemplo con una plantilla de Azure Resource Manager.
* Escenarios de flujo de tráfico detallados, que muestran cómo fluye el tráfico en este diseño.

## Referencias
### Documentación y sitios web útiles
* Acceder a Azure con Azure Resource Manager:
* Acceso a Azure con PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](powershell-install-configure.md)
* Documentación de red virtual: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
* Documentación de grupo de seguridad de red: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](virtual-network/virtual-networks-nsg.md)
* Documentación de enrutamiento definido por el usuario: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](virtual-network/virtual-networks-udr-overview.md)
* Puertas de enlace virtuales de Azure: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
* VPN de sitio a sitio: [https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Documentación de ExpressRoute (asegúrese de consultar las secciones "Introducción" y "Procedimientos"): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Diagrama de flujo de opciones de seguridad"
[1]: ./media/best-practices-network-security/compliancebadges.png "Notificaciones de cumplimiento de Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Características de seguridad de Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Red perimetral en una red corporativa"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Arquitectura de seguridad de Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Red perimetral en una red virtual de Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Red híbrida con tres límites de seguridad"
[7]: ./media/best-practices-network-security/example1design.png "Red perimetral de entrada con grupo de seguridad de red"
[8]: ./media/best-practices-network-security/example2design.png "Red perimetral de entrada con dispositivo virtual de red y grupo de seguridad de red"
[9]: ./media/best-practices-network-security/example3design.png "Red perimetral bidireccional con un dispositivo de red virtual, grupos de seguridad de red y enrutamiento definido por el usuario"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Vista lógica de las reglas de firewall"
[11]: ./media/best-practices-network-security/example4designoptions.png "Red perimetral con red híbrida conectada a un dispositivo virtual de red"
[12]: ./media/best-practices-network-security/example4designs2s.png "Red perimetral con un dispositivo virtual de red conectada mediante una VPN de sitio a sitio"
[13]: ./media/best-practices-network-security/example4networklogical.png "Red lógica desde la perspectiva del dispositivo virtual de red"
[14]: ./media/best-practices-network-security/example5designoptions.png "Red perimetral con puerta de enlace de Azure conectada a red híbrida de sitio a sitio"
[15]: ./media/best-practices-network-security/example5designs2s.png "Red perimetral con puerta de enlace de Azure mediante VPN de sitio a sitio"
[16]: ./media/best-practices-network-security/example6designoptions.png "Red perimetral con puerta de enlace de Azure conectada a red híbrida de ExpressRoute"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "Red perimetral con puerta de enlace de Azure mediante una conexión de ExpressRoute"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

<!---HONumber=AcomDC_0921_2016-->