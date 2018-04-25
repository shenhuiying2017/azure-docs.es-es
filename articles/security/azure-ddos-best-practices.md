---
title: Procedimientos recomendados y arquitectura de referencia para Azure DDoS Protection | Microsoft Docs
description: Aprenda a usar datos de registro para obtener un conocimiento más profundo sobre su aplicación.
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2018
ms.author: barclayn
ms.openlocfilehash: fea235ed057517840515f1ad0543dba1b1c46dd0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Procedimientos recomendados y arquitectura de referencia para Azure DDoS Protection

Este documento está dirigido a los responsables de decisiones de TI y el personal de seguridad. Se presupone estar familiarizado con Azure, las redes y la seguridad.

Diseñar para crear resistencia frente a los ataques de denegación de servicio distribuidos (DDoS) requiere planear y diseñar para una amplia variedad de modos de error. Este documento proporciona los procedimientos recomendados para diseñar aplicaciones en Azure resistentes a los ataques DDoS.

## <a name="types-of-attacks"></a>Tipos de ataques

DDoS es un tipo de ataque que se usa con la intención de agotar los recursos de la aplicación. El objetivo es afectar a la disponibilidad de la aplicación y a la capacidad para controlar solicitudes legítimas. Los ataques son cada vez más sofisticados y mayor en tamaño e impacto. Los ataques DDoS pueden ir dirigidos a cualquier punto de conexión que esté públicamente accesible a través de Internet.

Azure proporciona protección continua contra los ataques de DDoS. Esta protección se integra en la plataforma de Azure de forma predeterminada y sin costo adicional. Además de la protección contra DDoS que proporciona la plataforma, también tenemos una oferta nueva llamada "[Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/)", que proporciona funcionalidades avanzadas de mitigación de DDoS frente a ataques de red, y que se ajusta automáticamente para proteger sus recursos de Azure. La protección se puede habilitar fácilmente durante la creación de nuevas redes virtuales. También puede realizarse después de la creación inicial y no requiere ningún cambio de aplicación o recurso.

![](media/azure-ddos-best-practices/image1.png)

A grandes rasgos, los ataques de DDoS se pueden clasificar en tres categorías diferentes:

### <a name="volumetric-attacks"></a>Ataques volumétricos

Los ataques volumétricos son los tipos más comunes de ataques de DDoS. Los ataques volumétricos son ataques por fuerza bruta dirigidos a los niveles de red y transporte. Intentan agotar recursos tales como los vínculos de red. Estos ataques suelen utilizar varios sistemas infectados para desbordar el nivel de red con una gran cantidad de tráfico aparentemente legítimo. En este tipo de red, se utilizan distintos protocolos de nivel de red, como el Protocolo de mensajes de control de Internet (ICMP), el Protocolo de datagramas de usuario (UDP) y el Protocolo de control de transmisión (TCP).

Los ataques de DDoS dirigidos al nivel de red más comunes son desbordamiento SYN de TCP, eco ICMP, desbordamiento UDP, DNS y amplificación de NTP. Este tipo de ataque se puede utilizar no solo para interrumpir el servicio, sino también como cortina de humo para ocultar una intrusión en la red con fines más perversos y malintencionados. Un ejemplo de un ataque volumétrico reciente es la [vulnerabilidad de seguridad Memcached](https://www.wired.com/story/github-ddos-memcached/) que afectó a GitHub. Este ataque se dirigió al puerto UDP 11211 y generó un volumen de ataque de 1,35 Tb/s.

### <a name="protocol-attacks"></a>Ataques a protocolos

Los ataques a protocolos tienen como objetivo los protocolos de aplicación. Intentan consumir todos los recursos disponibles en los dispositivos de infraestructura, tales como firewalls, servidores de aplicaciones y equilibradores de carga. Los ataques a protocolos utilizan paquetes que tienen un formato incorrecto o que contienen anomalías de protocolo. Estos ataques envían un gran número de solicitudes de apertura a servidores y otros dispositivos de comunicación, y esperan una respuesta de paquete. Los intentos del sistema de destino por responder a las solicitudes de apertura llegan a causar el bloqueo del sistema de destino.

El ejemplo más común de un ataque DDoS basado en protocolo es el desbordamiento SYN de TCP. En este ataque, se dirige una sucesión de solicitudes SYN de TCP hacia un destino y puede usarse para sobrecargarlo. El objetivo es lograr que el destino deje de responder. La interrupción de Dyn de 2016, aparte de ser un ataque de nivel de aplicación, también contenía desbordamientos SYN de TCP dirigidas al puerto 53 de los servidores DNS de Dyn.

### <a name="resource-attacks"></a>Ataques a recursos

Loa ataques a recursos tienen como objetivo el nivel de aplicación. Activan los procesos back-end en un esfuerzo por sobrecargar el sistema de destino. Los ataques a recursos generan un tráfico abusivo que parece normal pero que transporta consultas que consumen mucha CPU al servidor. El volumen de tráfico necesario para agotar los recursos es comparativamente menor que en otros tipos de ataques. El tráfico de un ataque a recursos no se distingue del tráfico legítimo, por lo que resulta difícil de detectar. Los ataques a recursos más comunes se encuentran en los servicios HTTP/HTTPS y DNS.

## <a name="shared-responsibility-in-the-cloud"></a>Responsabilidad compartida en la nube

Se necesita una estrategia detallada de defensa para luchar contra los tipos y la sofisticación de los ataques, que cada vez son mayores. La seguridad es una responsabilidad compartida entre el cliente y Microsoft. Microsoft se refiere a esto como un [modelo de responsabilidad compartida](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). La siguiente ilustración muestra esta división de la responsabilidad.

![](media/azure-ddos-best-practices/image2.png)

Es conveniente que los clientes de Azure revisen nuestros procedimientos recomendados y creen aplicaciones globalmente distribuidas diseñadas y probadas para resistir a los errores.

## <a name="fundamental-best-practices"></a>Procedimientos recomendados fundamentales

Los ataques de DDoS van en aumento. En la siguiente sección se proporcionan instrucciones preceptivas para crear servicios resistentes a los ataques de DDoS en Azure.

### <a name="design-for-security"></a>Diseño para seguridad

Los clientes deben asegurar la seguridad durante todo el ciclo de vida de una aplicación, desde su diseño e implementación hasta la implementación y las operaciones. Las aplicaciones pueden tener errores que permitan que un volumen relativamente bajo de solicitudes usen una cantidad excesiva de recursos y produzcan una interrupción del servicio. Para proteger un servicio que se ejecuta en Microsoft Azure, los clientes deben conocer bien la arquitectura de su aplicación y deben centrarse en los [cinco pilares de la calidad del software](https://docs.microsoft.com/azure/architecture/guide/pillars).
Los clientes deben conocer los volúmenes de tráfico típicos, el modelo de conectividad entre la aplicación y otras aplicaciones, y los puntos de conexión del servicio expuestos a la red de Internet pública.

Además, es de vital importancia garantizar que una aplicación sea lo suficientemente resistente para tratar con un ataque de denegación de servicio dirigida a la propia aplicación. La seguridad y la privacidad están integradas en la plataforma de Azure, comenzando por el [ciclo de vida del desarrollo de la seguridad (SDL)](https://www.microsoft.com/sdl/default.aspx). El SDL aborda la seguridad en cada fase de desarrollo y se asegura de que Azure se actualice continuamente para que sea aún más seguro.

### <a name="design-for-scalability"></a>Diseño para escalabilidad

La escalabilidad es la capacidad de un sistema para controlar el aumento de la carga. Los clientes deben diseñar sus aplicaciones para que puedan [escalar horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para satisfacer la demanda de una carga mayor, específicamente en un caso de ataques de DDoS. Si la aplicación depende de una única instancia de un servicio, crea un único punto de error. El aprovisionamiento de varias instancias mejora tanto la resistencia como la escalabilidad.

Para [Azure App Service](../app-service/app-service-value-prop-what-is.md), seleccione un [Plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) que ofrezca varias instancias. Para Azure Cloud Services, configure cada uno de los roles para utilizar [varias instancias](../cloud-services/cloud-services-choose-me.md). En el caso de [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), asegúrese de que la arquitectura de las máquinas virtuales incluya más de una máquina virtual y que cada una de ellas se incluya en un [conjunto de disponibilidad](../virtual-machines/virtual-machines-windows-manage-availability.md). Se recomienda usar [conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para contar con funcionalidades de escalado automático.

### <a name="defense-in-depth"></a>Defensa en profundidad

La idea que subyace a la defensa en profundidad es administrar los riesgos con diversas estrategias defensivas. Disponer en niveles la defensa de la seguridad en una aplicación reduce las probabilidades de éxito de un ataque. Se recomienda que los clientes implementen diseños seguros para sus aplicaciones con las funcionalidades integradas de la plataforma de Azure.

Por ejemplo, el riesgo de ataques aumenta con el tamaño o el área expuesta de la aplicación. Se recomienda reducir el área expuesta mediante la creación de listas de permitidos para cerrar el espacio de direcciones IP y los puertos de escucha expuestos que no son necesarios en los equilibradores de carga ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[puerta de enlace de aplicación](../application-gateway/application-gateway-create-probe-portal.md)) o mediante [grupos de seguridad de red (NSG)](../virtual-network/virtual-networks-nsg.md).
Puede usar [etiquetas de servicio](/virtual-network/security-overview.md) y [grupos de seguridad de la aplicación](/virtual-network/security-overview.md) para minimizar la complejidad de la creación de reglas de seguridad y configurar la seguridad de la red como una extensión natural de la estructura de una aplicación.

Los clientes deben implementar los servicios de Azure en una [red virtual](../virtual-network/virtual-networks-overview.md) siempre que sea posible. Esto permite que los recursos del servicio se comuniquen mediante direcciones IP privadas. De forma predeterminada, el tráfico de los servicios Azure desde una red virtual usa direcciones IP públicas como direcciones IP de origen. Con los [puntos de conexión de servicio](../virtual-network/virtual-network-service-endpoints-overview.md), el tráfico del servicio cambia para usar direcciones privadas de red virtual como direcciones IP de origen al acceder al servicio de Azure desde una red virtual.

Con frecuencia vemos ataques a los recursos locales de un cliente, además de los recursos en Azure. Si conecta un entorno local a Azure, se recomienda que reducir al mínimo la exposición de los recursos locales a la red de Internet pública. Para usar las funcionalidades de escalado y protección contra DDoS de Azure puede implementar entidades públicas bien conocidas en Azure. Como estas entidades de acceso público suelen ser destinatarias de ataques de DDoS, al colocarlas en Azure se reduce el impacto en los recursos locales.

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Azure tiene dos ofertas de servicio de DDoS que proporcionan protección frente a ataques de la red (niveles 3 y 4): DDoS Protection Basic y DDoS Protection Standard. 

### <a name="azure-ddos-basic-protection"></a>Azure DDoS Protection Basic

DDoS Protection Basic se integra en Azure de forma predeterminada y sin costo adicional. En su conjunto, el tamaño y la capacidad de la red de implementación global de Azure proporciona una defensa contra los ataques al nivel de red más comunes mediante la supervisión constante del tráfico y la mitigación en tiempo real. DDoS Protection Basic no requiere ningún cambio en la configuración de usuarios o aplicaciones. Todos los servicios de Azure, incluidos servicios de PaaS como Azure DNS, están protegidos por DDoS Protection Basic.

![](media/azure-ddos-best-practices/image3.png)

Azure DDoS Protection Basic consta de componentes de hardware y software. El plano de control de software decide cuándo, dónde y qué tipo de tráfico debe derivarse a los dispositivos de hardware que analizan y quitan el tráfico de ataque. El plano de control toma esta decisión basándose en una *directiva* de DDoS Protection en toda la infraestructura, que se establece estáticamente y se aplica universalmente a todos los clientes de Azure.

Por ejemplo, la directiva de DDoS Protection especifica a qué volumen de tráfico debe *activarse* la protección (es decir, cuándo debe enrutarse el tráfico del inquilino a los dispositivos de limpieza) y, después, cómo deben *mitigar* el ataque los dispositivos de limpieza.

El objetivo del servicio Azure DDoS Protection Basic es proteger la infraestructura y la plataforma de Azure. Reduce el tráfico cuando supera una tasa que sea tan significativa que podría afectar a varios clientes en un entorno multiinquilino. No proporciona alertas ni directivas personalizadas por cliente.

### <a name="azure-ddos-standard-protection"></a>Azure DDoS Protection Standard

DDoS Protection Standard proporciona características mejoradas de mitigación frente a DDoS y se ajusta automáticamente para proteger los recursos de Azure específicos en una red virtual. La protección se puede habilitar fácilmente en cualquier red virtual nueva o existente y no requiere cambios en las aplicaciones ni los recursos. Tiene varias ventajas sobre el servicio básico, incluidos el registro, las alertas y la telemetría. A continuación se enumeran los principales elementos diferenciadores del servicio Azure DDoS Protection Standard.

#### <a name="adaptive-realtime-tuning"></a>Ajuste adaptable en tiempo real

El servicio Azure DDoS Protection Basic lo ayuda a proteger a los clientes, pero solo los protege para evitar que afecten a otros clientes. Por ejemplo, si un servicio se aprovisiona para un volumen normal de tráfico de entrada legítimo que es menor que la *tasa desencadenante* de la directiva de DDoS Protection para toda la infraestructura, un ataque de DDoS en los recursos de ese cliente podría pasar desapercibido. Por lo general, la naturaleza compleja de los ataques recientes (por ejemplo, DDoS multivector), así como los comportamientos de los inquilinos, específicos de cada aplicación, hacen necesarias directivas de protección personalizadas para cada cliente.
Esto se logra mediante dos perspectivas:

1. Aprendizaje automático de los patrones de tráfico de los niveles 3 y 4 (por IP) de cada cliente.
2. Reducción de los falsos positivos porque el tamaño de Azure le permite absorber una cantidad importante de tráfico.

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>Telemetría, alertas y supervisión de DDoS Protection

Con DDoS Protection Standard, exponemos datos de telemetría detallados mediante [Azure Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) mientras dura un ataque de DDoS. Las alertas se pueden configurar para cualquiera de las métricas de Azure Monitor que expone DDoS Protection. El registro se puede integrar con Splunk (Azure Event Hubs), Azure Log Analytics y Azure Storage para realizar análisis avanzados con la interfaz de Azure Monitor Diagnostics.

##### <a name="ddos-mitigation-policies"></a>Directivas de mitigación de DDoS

En Azure Portal, haga clic en **Monitor** \> **Métricas**. En la pantalla **Métricas**, seleccione el grupo de recursos, el tipo de recurso de Dirección IP pública y la dirección IP pública de Azure. Las métricas de DDoS estarán visibles en el panel de métricas disponibles.

DDoS Protection Standard aplica **tres directivas de mitigación de ajuste automático (TCP SYN, TCP y UDP)** a cada dirección IP pública del recurso protegido, en la red virtual que tiene habilitado DDoS Protection. Para ver los umbrales de la directiva, seleccione la métrica "**Inbound packets to trigger DDoS mitigation**" (Paquetes de entrada para desencadenar la mitigación de DDoS).

![](media/azure-ddos-best-practices/image7.png)

Los umbrales de las directivas se configuran automáticamente con nuestro sistema de generación de perfiles de tráfico de red basado en aprendizaje automático. La mitigación de DDoS se produce para una dirección IP que está siendo atacada solo cuando se supera el umbral de la directiva.

##### <a name="under-ddos-attack"></a>Bajo ataque de DDoS

Si la dirección IP pública está siendo atacada, el valor de la métrica "Under DDoS attack or not" (Bajo ataque de DDoS o no) cambia a 1 cuando aplicamos la mitigación al tráfico de ataque.

![](media/azure-ddos-best-practices/image8.png)

Se recomienda configurar una alerta en esta métrica para recibir una notificación cuando se esté aplicando una mitigación de DDoS en su dirección IP pública.

Para más información, consulte [Administración de Azure DDoS Protection Standard mediante Azure Portal](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="resource-attacks"></a>Ataques a recursos

En el caso de los ataques a recursos en el nivel de aplicación, los clientes deben configurar un firewall de aplicaciones web (WAF) para ayudar a proteger las aplicaciones web. WAF inspecciona el tráfico web entrante para bloquear las inyecciones de SQL, los scripts entre sitios, DDoS y otros ataques al nivel 7. Azure ofrece [WAF como una característica de Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md) para ofrecer una protección centralizada de las aplicaciones web contra las vulnerabilidades de seguridad comunes. En [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1) encontrará ofertas de WAF de partners de Azure que podrían ser más adecuadas para sus necesidades.

Incluso los firewalls de aplicaciones web son susceptibles de sufrir ataques de agotamiento del estado y volumétricos. Es muy recomendable activar DDoS Protection Standard en la red virtual del WAF para impedir los ataques a protocolos y volumétricos. Para más información, consulte la sección sobre la arquitectura de referencia.

### <a name="protection-planning"></a>Planeamiento de la protección

El planeamiento y la preparación son cruciales para entender cómo se comportará un sistema durante un ataque de DDoS. Este planeamiento y preparación le servirán también para diseñar un plan de respuesta para la administración de incidentes.

Los clientes deben asegurarse de que DDoS Protection Standard esté habilitado en la red virtual de los puntos de conexión accesibles desde Internet. La configuración de alertas de DDoS contribuye a vigilar constantemente los posibles ataques contra su infraestructura. Los clientes deben supervisar sus aplicaciones de forma independiente. Deben conocer el comportamiento normal de la aplicación. Deben tomarse medidas si la aplicación no se comporta como está previsto durante un ataque de DDoS.

#### <a name="ddos-attacks-orchestration"></a>Orquestación de ataques de DDoS

El procedimiento recomendado es realizar simulaciones periódicas para probar las suposiciones acerca de cómo responderán los servicios a un ataque, incluso antes de que se produzca. Durante las pruebas, compruebe que los servicios y las aplicaciones continúan funcionando según lo previsto y que no hay ninguna interrupción en la experiencia del usuario final. Identifique las carencias desde la perspectiva de la tecnología y de los procesos, e incorpórelas en la estrategia de respuesta a DDoS. Una recomendación general es realizar dichas pruebas en entornos de ensayo o durante las horas de poca actividad para reducir el impacto en el entorno de producción.

Nos hemos asociado con [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para crear una interfaz en la que los clientes de Azure puedan generar tráfico destinado a los puntos de conexión públicos que tengan habilitado el servicio DDoS Protection con fines de simulación. La simulación de [BreakPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) le permitirá:

- Comprobar en qué medida Microsoft Azure DDoS Protection protege sus recursos de Azure frente a ataques de DDoS.

- Optimizar el proceso de respuesta a incidentes durante el ataque de DDoS.

- Documentar el cumplimiento normativo de DDoS.

- Enseñar a los equipos de seguridad de red.

La ciberseguridad es una batalla constante que requiere una innovación continua en la defensa. Azure DDoS Protection Standard es una innovadora oferta que proporciona a los clientes una solución eficaz para mitigar los ataques de DDoS cada vez más complejos.

## <a name="components-of-a-ddos-response-strategy"></a>Componentes de una estrategia de respuesta a DDoS

En la mayoría de los casos, cuando un ataque de DDoS tiene como objetivo los recursos de Azure, la intervención necesaria por parte del usuario final es mínima. No obstante, incorporar la mitigación de DDoS como parte de la estrategia de respuesta a incidentes de la organización garantizará un impacto mínimo en la continuidad del negocio.

### <a name="microsoft-threat-intelligence"></a>Información sobre amenazas de Microsoft

Microsoft cuenta con una extensa red de información sobre amenazas que usa los conocimientos colectivos de una amplia comunidad de seguridad para dar soporte a los servicios en línea de Microsoft, los partners de Microsoft y las relaciones dentro de la comunidad de seguridad de Internet. Como importante proveedor de infraestructura, Microsoft recibe alertas tempranas acerca de las amenazas, toma la información sobre amenazas obtenida de otros servicios en línea de Microsoft y de la base internacional de clientes de Microsoft. Toda la información sobre amenazas de Microsoft se incorpora a los productos de Azure DDoS Protection.

Además, la unidad de delitos digitales de Microsoft realiza estrategias ofensivas contra redes de robots (botnets), un origen habitual de comando y control de ataques de DDoS.

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Evaluación del riesgo de los recursos de Azure

Es fundamental conocer el alcance del riesgo de un ataque de DDoS de forma continuada. Los clientes deben preguntarse periódicamente: ¿qué nuevos recursos de Azure están disponibles públicamente y necesitan protección? ¿Hay un único punto de error en el servicio? ¿Cómo pueden aislarse los servicios para limitar el impacto de un ataque, manteniendo al mismo tiempo los servicios disponibles para los clientes legítimos? ¿Hay redes virtuales en las que DDoS Protection Standard debería estar habilitado y no lo está? ¿Estás mis servicios activos o activos con conmutación por error en varias regiones?

### <a name="customer-ddos-response-team"></a>Equipo de respuesta a DDoS del cliente

Crear un equipo de respuesta a DDoS es un paso clave para garantizar una respuesta rápida y eficaz a un ataque. Los clientes deben identificar los contactos de la organización que supervisarán el planeamiento y la ejecución. El equipo de respuesta a DDoS debe conocer a fondo el servicio Azure DDoS Protection Standard y debe ser capaz de identificar y mitigar un ataque para coordinar los diversos clientes internos y externos, incluido el equipo de soporte técnico de Microsoft, según sea necesario.

Microsoft recomienda al equipo de respuesta a DDoS realizar ejercicios de planeamiento y simulación, incluidas pruebas de escalado, como rutina habitual de la disponibilidad del servicio y el planeamiento de la continuidad. 

### <a name="during-an-attack"></a>Durante un ataque

Azure DDoS Protection Standard identificará y mitigará los ataques de DDoS sin intervención del usuario. Para recibir una notificación cuando haya una mitigación activa en una dirección IP pública protegida, también puede [configurar una alerta](../virtual-network/ddos-protection-manage-portal.md) en la métrica "Under DDoS attack or not" (Bajo ataque de DDoS o no). Si es necesario, puede revisar y crear más alertas para otras métricas de DDoS para entender la escala del ataque, el tráfico que se va a quitar, etc.

#### <a name="when-to-contact-microsoft-support"></a>Cuándo ponerse en contacto con el soporte técnico de Microsoft

- Si, durante un ataque de DDoS, encuentra que la degradación del rendimiento del recurso protegido es grave o el recurso no está disponible.

- Si cree que el servicio DDoS Protection no se comporta según lo previsto. El servicio DDoS Protection solo iniciará la mitigación si se cumplen los siguientes criterios:

    - El valor de la métrica "Policy to trigger DDoS mitigation (TCP/TCP SYN/UDP)" [Directiva para desencadenar la mitigación de DDoS (TCP/TCP SYN/UDP)] es menor que el tráfico recibido en el recurso de IP pública protegido.

- Si sabe que se va a producir un evento viral planeado que darán lugar a un aumento significativo en el tráfico de red.

- Si alguien ha amenazado con iniciar un ataque de DDoS contra sus recursos.

En caso de problemas que afecten a aspectos críticos de su empresa, cree una [incidencia de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de gravedad A.

### <a name="post-attack-steps"></a>Pasos posteriores al ataque

Siempre es una buena estrategia hacer una autopsia después de un ataque y reajustar la estrategia de respuesta a DDoS según sea necesario. Puntos que se deben tener en cuenta:

- ¿Se produjo una interrupción en el servicio o en la experiencia del usuario final debido a la falta de una arquitectura escalable?

- ¿Qué aplicaciones o servicios sufrieron más?

- ¿Fue eficaz la estrategia de respuesta a DDoS y cómo se podría mejorar aún más?

Si sospecha que está sufriendo un ataque de DDoS, notifíquelo a través de los canales de soporte técnico de Azure normales.

## <a name="ddos-protection-reference-architectures"></a>Arquitecturas de referencia de DDoS Protection

DDoS Protection Standard se ha diseñado [para los servicios que se implementan en una red virtual.](../virtual-network/virtual-network-for-azure-services.md) Para otros servicios, se aplicará el servicio predeterminado DDoS Protection Basic. Las arquitecturas de referencia específicas que se describen a continuación se organizan por escenarios, y los patrones de la arquitectura se agrupan juntos.

### <a name="virtual-machine-windowslinux-workloads"></a>Cargas de trabajo de máquina virtual (Windows o Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplicación que se ejecuta en máquinas virtuales con equilibrio de carga

En esta arquitectura de referencia se muestra un conjunto de prácticas demostradas para ejecutar varias máquinas virtuales con Windows en un conjunto de escalado detrás de un equilibrador de carga, para mejorar la disponibilidad y escalabilidad. Esta arquitectura puede usarse para cargas de trabajo sin estado, como un servidor web.

![](media/azure-ddos-best-practices/image9.png)

En esta arquitectura, una carga de trabajo se distribuye entre varias instancias de máquina virtual. Hay una única dirección IP pública, y el tráfico de Internet se distribuye a las máquinas virtuales con un equilibrador de carga. DDoS Protection Standard está habilitado en la red virtual del equilibrador de carga de Azure (Internet) que tiene asociada la dirección IP pública.

El equilibrador de carga distribuye las solicitudes entrantes de Internet a las instancias de máquina virtual. Los conjuntos de escalado de máquinas virtuales permiten reducir o escalar horizontalmente el número de máquinas virtuales de forma manual, o bien automáticamente en función de reglas predefinidas. Esto es importante si el recurso está sufriendo un ataque de DDoS. Consulte este [artículo](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) para obtener más información sobre esta arquitectura de referencia.

#### <a name="applications-running-on-windows-n-tier"></a>Aplicaciones que se ejecutan en una arquitectura de n niveles de Windows

Hay muchas maneras de implementar una arquitectura de n niveles. En el diagrama se muestra una aplicación web típica de tres niveles. Esta arquitectura se basa en [Ejecución de máquinas virtuales de carga equilibrada para escalabilidad y disponibilidad](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Los niveles Web y Business usan máquinas virtuales de carga equilibrada.

![](media/azure-ddos-best-practices/image10.png)

En la arquitectura anterior, DDoS Protection Standard está habilitado en la red virtual. Todas las direcciones IP públicas de la red virtual obtendrán protección contra DDoS en los niveles 3 y 4. Para la protección del nivel 7, debe implementarse Application Gateway en la SKU de WAF. Consulte [este](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier) artículo para obtener más información acerca de esta arquitectura de referencia.

#### <a name="paas-web-application"></a>Aplicación web PaaS

Esta arquitectura de referencia muestra la ejecución de una aplicación de Azure App Service en una única región. Esta arquitectura muestra un conjunto de prácticas demostradas para una aplicación web que usa [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) y [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
La región en espera se configura para escenarios de conmutación por error.

![](media/azure-ddos-best-practices/image11.png)

Traffic Manager enruta las solicitudes entrantes a Application Gateway en una de las regiones. Durante las operaciones normales, enruta las solicitudes a Application Gateway en la región activa. Si esa región deja de estar disponible, Traffic Manager conmuta por error a Application Gateway en la región en espera.

Todo el tráfico de Internet con destino a la aplicación web se enruta a la [dirección IP pública de Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) a través de Traffic Manager. En este escenario, App Service (Web Apps) no es directamente accesible desde el exterior y está protegido por Application Gateway. Se recomienda configurar la SKU de WAF de Application Gateway (modo de prevención) para protegerse contra ataques de nivel 7 (Socket Web, HTTP o HTTPS). Además, las aplicaciones web están configuradas para [aceptar tráfico solo desde la dirección IP de Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).

Consulte [este](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) artículo para obtener más información acerca de esta arquitectura de referencia.

### <a name="mitigation-for-non-web-paas-services"></a>Mitigación para servicios de PaaS que no son web

#### <a name="hdinsight-on-azure"></a>HDInsight en Azure

Esta arquitectura de referencia muestra cómo configurar DDoS Protection Standard para un [clúster de HDInsight](https://docs.microsoft.com/azure/hdinsight/) en Azure. Debe asegurarse de que el clúster de HDInsight esté vinculado a una red virtual y que DDoS Protection esté habilitado en esa red virtual.

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

En esta arquitectura, el tráfico de Internet con destino al clúster de HDInsight se enruta a la dirección IP pública asociada con el equilibrador de carga de la puerta de enlace de HDInsight. El equilibrador de carga de la puerta de enlace envía el tráfico directamente a los nodos principales o los nodos de trabajo. Como DDoS Protection Standard está habilitado en la red virtual de HDInsight, todas las direcciones IP públicas de la red virtual obtendrán protección contra DDoS en los niveles 3 y 4. La arquitectura de referencia anterior puede combinarse con arquitecturas de referencia de n niveles o múltiples regiones.

Para más información acerca de esta arquitectura de referencia, consulte [Extensión de HDInsight con Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="azure-api-management"></a>Azure API Management

Esta arquitectura de referencia protege el punto de conexión público del recurso [API Management](../api-management/api-management-key-concepts.md) que publica las API a los clientes externos a la organización. APIM debe implementarse en una red virtual externa para habilitar la protección contra DDoS.

![](media/azure-ddos-best-practices/image15.png)

Con una red virtual externa, la puerta de enlace de API Management y el portal para desarrolladores son accesibles públicamente desde Internet con un equilibrador de carga público. En esta arquitectura, DDoS Protection Standard está habilitado en la red virtual externa a la red virtual de APIM. El tráfico se enruta desde internet a la dirección IP pública de APIM, que está protegida frente a ataques de red de nivel 3 y 4. Para protegerse frente a ataques de nivel 7 (HTTP/HTTPS), puede configurar Application Gateway en modo WAF.

Consulte [aquí](../virtual-network/virtual-network-for-azure-services.md) la lista de servicios adicionales que se implementan en una red virtual y que pueden configurarse para DDoS Protection Standard. DDoS Protection Standard solo admite recursos de Azure Resource Manager. *Implementación de entorno de Application Service Environment (ASE) insertado en una red virtual con una dirección IP pública no se admite de forma nativa*. Para más información acerca de cómo proteger un entorno ASE, consulte esta sección.

## <a name="next-steps"></a>Pasos siguientes

* [Página del producto Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)

* [Blog de Azure DDoS Protection](http://aka.ms/ddosblog)

* [Documentación de Azure DDoS Protection](../virtual-network/ddos-protection-overview.md)
