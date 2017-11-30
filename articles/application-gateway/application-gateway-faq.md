---
title: "Preguntas más frecuentes sobre Azure Application Gateway | Microsoft Docs"
description: "Esta página proporciona respuestas a las preguntas más frecuentes acerca de Azure Application Gateway"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d54ee7ec-4d6b-4db7-8a17-6513fda7e392
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: f92af44df9863bbf48abb4afcf9b1505c843fadc
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Preguntas más frecuentes sobre Application Gateway

## <a name="general"></a>General

**P. ¿Qué es Application Gateway?**

Azure Application Gateway es un controlador de entrega de aplicaciones (ADC) como servicio que ofrece diversas funcionalidades de equilibrio de carga de nivel 7 para sus aplicaciones. Ofrece un servicio altamente disponible y escalable que está completamente administrado por Azure.

**P. ¿Qué características admite Application Gateway?**

Application Gateway admite la descarga de SSL y SSL de un extremo a otro, Firewall de aplicaciones web, afinidad de sesión basada en cookies, enrutamiento basado en ruta de dirección URL, alojamiento de varios sitios y muchas otras más. Para obtener una lista completa de las características admitidas, visite [Introducción a Application Gateway](application-gateway-introduction.md).

**P. ¿Cuál es la diferencia entre Application Gateway y Azure Load Balancer?**

Application Gateway es un equilibrador de carga de nivel 7, lo que significa que funciona solo tráfico web (HTTP/HTTPS/WebSocket). Admite funcionalidades como la terminación SSL, la afinidad de sesión basada en cookies y la distribución round robin para el tráfico de equilibrio de carga. Load Balancer equilibra la carga de tráfico en el nivel 4 (TCP/UDP).

**P. ¿Qué protocolos admite Application Gateway?**

Application Gateway admite HTTP, HTTPS y WebSocket.

**P. ¿Qué recursos son compatibles actualmente como parte del grupo de back-end?**

Los grupos de back-end pueden constar de NIC, conjuntos de escalado de máquinas virtuales, direcciones IP públicas e internas, nombres de dominio completos (FQDN) y servidores back-end multiinquilino como Azure Web Apps. Los miembros del grupo de back-end de Application Gateway no están asociados a un conjunto de disponibilidad. Los miembros de grupos de back-end pueden estar repartidos entre clústeres, centros de datos o fuera de Azure siempre y cuando dispongan de conectividad IP.

**P. ¿En qué regiones está disponible el servicio?**

Application Gateway está disponible en todas las regiones de Azure global. También está disponible en [Azure China](https://www.azure.cn/) y [Azure Government](https://azure.microsoft.com/en-us/overview/clouds/government/)

**P. ¿Se trata de una implementación dedicada para mi suscripción o compartida entre los clientes?**

Application Gateway es una implementación dedicada en su red virtual.

**P. ¿Se admite la redirección HTTP->HTTPS?**

Se admite el redireccionamiento. Visite [Introducción al redireccionamiento de Application Gateway](application-gateway-redirect-overview.md) para obtener más información.

**P. ¿En qué orden se procesan los agentes de escucha?**

Los agentes de escucha se procesan en el orden en que aparecen. Por este motivo, si un agente de escucha coincide con una solicitud entrante, se procesa primero.  Los agentes de escucha de varios sitios deben configurarse antes de un agente de escucha básico para asegurarse de que el tráfico se enrute al back-end correcto.

**P. ¿Dónde se encuentra la dirección IP y el DNS de Application Gateway?**

Cuando se usa una dirección IP pública como punto de conexión, esta información se puede encontrar en el recurso de la dirección IP pública o en la página de información general de Application Gateway en el portal. En el caso de direcciones IP internas, esta se puede encontrar en la página de información general.

**P. ¿Cambia la dirección IP o el DNS durante la vigencia de Application Gateway?**

La dirección IP virtual puede cambiar si la puerta de enlace se detiene y la inicia otro cliente. El DNS asociado con Application Gateway no cambia durante el ciclo de vida de la puerta de enlace. Por este motivo, se recomienda utilizar un alias CNAME y hacer que señale a la dirección DNS de Application Gateway.

**P. ¿Admite Application Gateway direcciones IP estáticas?**

No, Application Gateway no admite direcciones IP públicas estáticas, pero admite direcciones IP internas estáticas.

**P. ¿Admite Application Gateway varias direcciones IP públicas en la puerta de enlace?**

Solo se admite una dirección IP pública en una instancia de Application Gateway.

**P. ¿Admite Application Gateway encabezados x-forwarded-for?**

Sí, Application Gateway inserta encabezados x-forwarded-for, x-forwarded-proto y x-forwarded-port en la solicitud que se reenvía al back-end. El formato del encabezado x-forwarded-for es una lista separada por comas de IP:Port. Los valores válidos para x-forwarded-proto son http o https. X-forwarded-port especifica el puerto al que llegó la solicitud en Application Gateway.

**P. ¿Cuánto tiempo se tarda en implementar Application Gateway? ¿Sigue funcionando Application Gateway mientras se actualiza?**

Las nuevas implementaciones de Application Gateway pueden tardar hasta 20 minutos en aprovisionarse. Los cambios de tamaño y recuento de instancias no provocan interrupciones, y la puerta de enlace permanece activa durante este tiempo.

## <a name="configuration"></a>Configuración

**P. ¿Se implementa Application Gateway siempre en una red virtual?**

Sí, Application Gateway se implementa siempre en una red virtual. Esta subred solo puede contener instancias de Application Gateway.

**P. ¿Puede Application Gateway hablar con instancias fuera de su red virtual?**

Application Gateway puede comunicarse con instancias fuera de la red virtual en la que se encuentra siempre que haya conectividad IP. Si planea usar direcciones IP internas como miembros del grupo de back-end, necesitará [emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md) o [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**P. ¿Puedo implementar algo más en la subred en la que está Application Gateway?**

No, pero se pueden implementar otras puertas de enlace de aplicación en la subred.

**P. ¿Se admiten grupos de seguridad de red en la subred en la que está Application Gateway?**

Se admiten grupos de seguridad de red en la subred de Application Gateway con las restricciones siguientes:

* Se deben colocar excepciones para el tráfico entrante en los puertos 65503-65534 para que el estado del back-end funcione correctamente.

* No puede bloquearse la conectividad saliente de Internet.

* Se debe permitir el tráfico de la etiqueta AzureLoadBalancer.

**P. ¿Cuáles son los límites de Application Gateway? ¿Puedo aumentar estos límites?**

Visite [Límites de Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits) para ver los límites.

**P. ¿Puedo usar Application Gateway para tráfico externo e interno al mismo tiempo?**

Sí, Application Gateway admite una dirección IP interna y una dirección IP externa por cada instancia de Application Gateway.

**P. ¿Se admite el emparejamiento de VNet?**

Sí, se admite el emparejamiento de VNet y, además, es beneficioso para el tráfico de equilibrio de carga en otras redes virtuales.

**P. ¿Se puede comunicar con servidores locales cuando están conectados mediante ExpressRoute o túneles VPN?**

Sí, siempre que se permita el tráfico.

**P. ¿Se puede tener un grupo de back-end que preste servicio a muchas aplicaciones en puertos diferentes?**

Se admite la arquitectura de microservicios. Necesitaría varias opciones de configuración de http configuradas para realizar sondeos en puertos diferentes.

**P. ¿Admiten los sondeos personalizados caracteres comodín o regex en los datos de respuesta?**

Los sondeos personalizados no admiten caracteres comodín o regex en los datos de respuesta. 

**P. ¿Cómo se procesan las reglas?**

Las reglas se procesan en el orden en que están configuradas. Se recomienda que las reglas de varios sitios se configuren antes de las reglas básicas para reducir la posibilidad de que el tráfico se enrute al back-end inadecuado, ya que la regla básica coincidiría con el tráfico basado en el puerto antes de que se evalúe la regla de varios sitios.

**P. ¿Cómo se procesan las reglas?**

Las reglas se procesan en el orden en que se crearon. Se recomienda configurar las reglas multisitio antes que las reglas básicas. Al configurar primero los agentes de escucha multisitio, esta configuración reduce la probabilidad de que el tráfico se enrute al servidor no apropiado. Este problema de enrutamiento puede ocurrir cuando la regla básica debería coincidir con tráfico basado en puerto antes que la regla multisitio que se va a evaluar.

**P. ¿Qué significa el campo Host de los sondeos personalizados?**

El campo Host especifica el nombre al que enviar el sondeo. Solo se puede aplicar cuando se ha configurado un entorno multisitio en Application Gateway; de lo contrario hay que usar '127.0.0.1'. Este valor es diferente del nombre de host de máquina virtual y está en formato \<protocolo\>://\<host\>:\<puerto\>\<ruta de acceso\>.

**P. ¿Puedo permitir a Application Gateway el acceso a algunas direcciones IP de origen?**

Este escenario puede hacerse mediante el uso de grupos de seguridad de red en la subred de Application Gateway. Las siguientes restricciones se deben colocar en la subred en el orden de prioridad indicado:

* Permitir el tráfico entrante de la IP o intervalo IP de origen.

* Permitir las solicitudes entrantes de todos los orígenes a los puertos 65503-65534 para la [comunicación del estado del back-end](application-gateway-diagnostics.md).

* Permitir sondeos entrantes de Azure Load Balancer (con la etiqueta AzureLoadBalancer) y el tráfico de red virtual entrante (con la etiqueta VirtualNetwork) en el [NSG](../virtual-network/virtual-networks-nsg.md).

* Bloquear todo el tráfico entrante restante con una regla Denegar todo.

* Permitir el tráfico saliente a Internet para todos los destinos.

## <a name="performance"></a>Rendimiento

**P. ¿Cómo admite Application Gateway la alta disponibilidad y la escalabilidad?**

Application Gateway admite escenarios de alta disponibilidad cuando tiene dos o más instancias implementadas. Azure distribuye estas instancias entre dominios de actualización y de errores para asegurarse de que todas las instancias no produzcan un error al mismo tiempo. Application Gateway admite la escalabilidad mediante la adición de varias instancias de la misma puerta de enlace para compartir la carga.

**P. ¿Cómo se puede lograr el escenario de recuperación ante desastres a través de centros de datos con Application Gateway?**

Los clientes pueden usar Traffic Manager para distribuir el tráfico a través de varias instancias de Application Gateway en distintos centros de datos.

**P. ¿Se admite el escalado automático?**

No, pero Application Gateway tiene una métrica de rendimiento que se puede utilizar para mostrar una alerta cuando se alcanza un determinado umbral. Agregar instancias manualmente o cambiar el tamaño no reinicia la puerta de enlace y no afecta al tráfico existente.

**P. ¿Provoca el escalado o reducción vertical algún tiempo de inactividad?**

No hay ningún tiempo de inactividad, las instancias se distribuyen entre varios dominios de actualización y dominios de error.

**P. ¿Puedo cambiar el tamaño de la instancia de mediano a grande sin que haya una interrupción?**

Sí, Azure distribuye las instancias entre los dominios de actualización y de errores para asegurarse de que todas las instancias no produzcan un error al mismo tiempo. Application Gateway admite el escalado mediante la adición de varias instancias de la misma puerta de enlace para compartir la carga.

## <a name="ssl-configuration"></a>Configuración de SSL

**P. ¿Qué certificados se admiten en Application Gateway?**

Se admiten los certificados autofirmados, los certificados de entidad emisora de certificados y los certificados comodín. No se admiten los certificados de validación extendida (EV).

**P. ¿Cuáles son los conjuntos de cifrado actuales que admite Application Gateway?**

Los siguientes son los conjuntos de cifrado actuales que admite Application Gateway. Para aprender a personalizar opciones de SSL, visite [Configuración de versiones de directivas SSL y conjuntos de cifrado en Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

**P. ¿Admite Application Gateway también el recifrado del tráfico dirigido al back-end?**

Sí, Application Gateway admite la descarga de SSL y SSL de extremo a extremo, lo cual permite volver a cifrar el tráfico dirigido al back-end.

**P. ¿Se puede configurar la directiva SSL para controlar las versiones del protocolo SSL?**

Sí, puede configurar Application Gateway para denegar TLS1.0, TLS1.1 y TLS1.2. SSL 2.0 y 3.0 ya están deshabilitados de forma predeterminada y no se pueden configurar.

**P. ¿Puedo configurar los conjuntos de cifrado y el orden de las directivas?**

Sí, se admite la [configuración de conjuntos de cifrado](application-gateway-ssl-policy-overview.md). Al definir una directiva personalizada, se debe habilitar al menos uno de los siguientes conjuntos de cifrado. Application Gateway usa SHA256 para la administración de back-end.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**P. ¿Cuántos certificados SSL se admiten?**

Se admiten hasta 20 certificados SSL.

**P. ¿Cuántos certificados de autenticación de recifrado de back-end se admiten?**

Se admiten hasta 10 certificados de autenticación, con 5 como valor predeterminado.

**P. ¿Se integra Application Gateway con Azure Key Vault de forma nativa?**

No, no se integra con Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Firewall de aplicaciones web (WAF): Configuración

**P. ¿Ofrece la SKU de WAF todas las características disponibles con la SKU estándar?**

Sí, WAF admite todas las características de la SKU estándar.

**P. ¿Qué versión de CRS admite Application Gateway?**

Application Gateway admite CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) y CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**P. ¿Cómo se supervisa el WAF?**

WAF se supervisa a través del registro de diagnóstico. Puede encontrar más información sobre el registro de diagnóstico en [Métricas y registro de diagnóstico de Application Gateway](application-gateway-diagnostics.md)

**P. ¿Bloquea el modo de detección el tráfico?**

No, el modo de detección solo registra el tráfico, que desencadenó una regla de WAF.

**P. ¿Cómo se personalizan las reglas de WAF?**

Sí, las reglas de WAF son personalizables. Para obtener más información sobre cómo personalizarlas, visite [Personalización de reglas y grupos de reglas de WAF](application-gateway-customize-waf-rules-portal.md).

**P. ¿Qué reglas están disponibles actualmente?**

WAF actualmente admite CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) y [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), que proporcionan una seguridad de línea de base frente a la mayoría de las 10 principales vulnerabilidades identificadas por Open Web Application Security Project (OWASP) y que se describen en [OWASP top 10 Vulnerabilities](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013) (Las 10 principales vulnerabilidades de OWASP).

* Protección contra la inyección de código SQL

* Protección contra scripts entre sitios

* Protección contra ataques web comunes, como inyección de comandos, contrabando de solicitudes HTTP, división de respuestas HTTP y ataque remoto de inclusión de archivos

* Protección contra infracciones del protocolo HTTP

* Protección contra anomalías del protocolo HTTP, como la falta de agentes de usuario de host y encabezados de aceptación

* Prevención contra bots, rastreadores y escáneres

 * Detección de errores de configuración comunes en aplicaciones (es decir, Apache, IIS, etc.)

**P. ¿Admite también WAF la prevención DDoS?**

No, WAF no ofrece prevención DDoS.

## <a name="diagnostics-and-logging"></a>Diagnósticos y registro

**P. ¿Qué tipos de registros están disponibles con Application Gateway?**

Hay tres registros disponibles para Application Gateway. Para más información sobre estos registros y otras funcionalidades de diagnóstico, visite [Mantenimiento del back-end, registro de diagnóstico y métricas de Application Gateway](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog**: este registro de acceso contiene todas las solicitudes enviadas al front-end de Application Gateway. Los datos incluyen la dirección IP del autor de la llamada, la dirección URL solicitada, la latencia de la respuesta, el código de devolución y los bytes de entrada y salida. El registro de acceso se recopila cada 300 segundos. Este registro contiene un registro por cada instancia de Application Gateway.
- **ApplicationGatewayPerformanceLog**: este registro de rendimiento captura la información de rendimiento de cada instancia, incluida la cantidad total de solicitudes atendidas, el rendimiento en bytes, la cantidad de solicitudes con error y la cantidad de instancias back-end completadas correcta e incorrectamente.
- **ApplicationGatewayFirewallLog**: este registro de firewall contiene las solicitudes que se registran con el modo de detección o prevención de una puerta de enlace de aplicaciones que está configurada con el firewall de aplicaciones web.

**P. ¿Cómo se puede saber si mis miembros del grupo de back-end están en buenas condiciones?**

Puede usar el cmdlet de PowerShell `Get-AzureRmApplicationGatewayBackendHealth` o comprobar el estado a través del portal visitando [Diagnósticos de Application Gateway](application-gateway-diagnostics.md)

**P. ¿Qué es la directiva de retención en los registros de diagnóstico?**

Los registros de diagnóstico fluyen hacia la cuenta de almacenamiento de los clientes y estos pueden establecer la directiva de retención según sus preferencias. Los registros de diagnóstico también se pueden enviar a un centro de eventos o a Log Analytics. Visite [Diagnósticos de Application Gateway](application-gateway-diagnostics.md) para más información.

**P. ¿Cómo se pueden obtener los registros de auditoría de Application Gateway?**

Los registros de auditoría están disponibles para Application Gateway. En el portal, haga clic en **Registro de actividad** en la hoja de menú de una instancia de Application Gateway para tener acceso al registro de auditoría. 

**P. ¿Se pueden establecer alertas con Application Gateway?**

Sí, Application Gateway admite alertas, y estas se configuran a partir de las métricas.  Application Gateway tiene actualmente una métrica de "rendimiento", que se puede configurar para que genere una alerta. Para más información sobre las alertas, visite [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**P. El estado de back-end devuelve un estado desconocido, ¿que puede haberlo provocado?**

La razón más común es que el acceso al servidor está bloqueado por un NSG o DNS personalizado. Visite [Estado de back-end, registros de diagnóstico y métricas de Application Gateway](application-gateway-diagnostics.md) para más información.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Application Gateway, visite [Introducción a Application Gateway](application-gateway-introduction.md).
