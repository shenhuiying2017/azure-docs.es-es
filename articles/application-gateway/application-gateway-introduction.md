---
title: ¿Qué es Azure Application Gateway?
description: Obtenga información acerca de cómo puede utilizar una puerta de enlace de aplicaciones de Azure para administrar el tráfico web a la aplicación.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.workload: infrastructure-services
ms.date: 4/9/2018
ms.author: victorh
ms.openlocfilehash: 3824eacb355c323a1850f6863ae2b99970c62cfb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151168"
---
# <a name="what-is-azure-application-gateway"></a>¿Qué es Azure Application Gateway?

Azure Application Gateway es un equilibrador de carga de tráfico web que permite administrar el tráfico a las aplicaciones web. 

Los equilibradores de carga tradicionales operan en la capa de transporte (OSI capa 4: TCP y UDP) y enrutan el tráfico en función de la dirección IP y puerto de origen a una dirección IP y puerto de destino. Pero con Application Gateway puede ser aun más específico. Por ejemplo, puede enrutar el tráfico en función de la dirección URL entrante. Por lo que si `/images` se encuentra en la dirección URL entrante, puede redirigir el tráfico a un conjunto específico de servidores (conocido como un grupo) configurados para imágenes. Si `/video` se encuentra en la dirección URL, el tráfico se enruta a otro grupo optimizado para vídeos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Este tipo de enrutamiento se conoce como equilibrio de carga de capa de aplicación (OSI capa 7). Azure Application Gateway puede hacer enrutamiento basado en dirección URL y mucho más. Las siguientes características se incluyen con Azure Application Gateway: 

## <a name="url-based-routing"></a>Enrutamiento basado en dirección URL

El enrutamiento basado en URL le permite enrutar el tráfico a los grupos de servidores back-end en función de las direcciones URL de la solicitud. Uno de los escenarios podría ser enrutar las solicitudes de diferentes tipos de contenido a otro grupo diferente.

Por ejemplo, las solicitudes para `http://contoso.com/video/*` se enrutan a VideoServerPool y para `http://contoso.com/images/*` se enrutan a ImageServerPool. DefaultServerPool se selecciona si ninguno de los patrones de ruta de acceso coincide.

## <a name="redirection"></a>Redireccionamiento

Un escenario común para muchas aplicaciones web es admitir el redireccionamiento automático de HTTP a HTTPS para asegurarse de que toda la comunicación entre la aplicación y sus usuarios se produce a través de una ruta de acceso cifrada. 

En el pasado, podría haber utilizado técnicas como la creación de un grupo dedicado cuya única finalidad sería redirigir las solicitudes que recibe de HTTP a HTTPS. Application Gateway admite la posibilidad de redirigir el tráfico en la puerta de enlace de aplicaciones. Esto simplifica la configuración de la aplicación, optimiza el uso de recursos y admite nuevos escenarios de redireccionamiento que incluyen el global y el redireccionamiento basado en la ruta de acceso. La compatibilidad del redireccionamiento de Application Gateway no se limita solo al redireccionamiento de HTTP a HTTPS. Se trata de un mecanismo de redireccionamiento genérico, por lo que puede redirigir desde y hacia cualquier puerto que defina mediante reglas. También admite la redirección a un sitio externo.

La compatibilidad con la redirección de Application Gateway ofrece las siguientes funcionalidades:

- Redireccionamiento global de un puerto a otro puerto en la puerta de enlace. Permite la redirección de HTTP a HTTPS en un sitio.
- Redirección basada en la ruta de acceso. Este tipo de redireccionamiento permite el redireccionamiento de HTTP a HTTPS solamente para un área de un sitio específico, por ejemplo un área de carro de la compra que se indica mediante `/cart/*`.
- Redireccionamiento a un sitio externo

## <a name="multiple-site-hosting"></a>Hospedaje de varios sitios

El hospedaje de varios sitios permite configurar más de un sitio web en la misma instancia de la puerta de enlace de aplicaciones. Esta característica permite configurar una topología más eficaz para las implementaciones al agregar hasta 20 sitios web en una puerta de enlace de aplicaciones. Cada sitio web se puede dirigir a su propio grupo. Por ejemplo, la puerta de enlace de aplicaciones puede atender el tráfico para `contoso.com` y `fabrikam.com` desde dos grupos de servidores denominados ContosoServerPool y FabrikamServerPool.

Las solicitudes para `http://contoso.com` se enrutan a ContosoServerPool y para `http://fabrikam.com` se enrutan a FabrikamServerPool.

De forma similar, dos subdominios del mismo dominio primario pueden hospedarse en la misma implementación de la puerta de enlace de aplicaciones. Ejemplos del uso de subdominios podrían incluir `http://blog.contoso.com` y `http://app.contoso.com` hospedados en una única implementación de la puerta de enlace de aplicaciones.

## <a name="session-affinity"></a>Afinidad de sesión

La característica de afinidad de sesión basada en cookies es útil cuando se quiere mantener una sesión de usuario en el mismo servidor. Mediante el uso de cookies administradas por la puerta de enlace, Application Gateway puede dirigir el tráfico posterior de una sesión de usuario hasta el mismo servidor para su procesamiento. Esto es importante en aquellos casos en los que se guarda el estado de la sesión de forma local en el servidor para una sesión de usuario.

## <a name="secure-sockets-layer-ssl-termination"></a>Terminación de la Capa de sockets seguros (SSL)

Application Gateway es compatible con la terminación SSL en la puerta de enlace; después, el tráfico fluye normalmente sin cifrar a los servidores back-end. Esta característica permite a los servidores web liberarse de la costosa sobrecarga de cifrado y descifrado. Sin embargo, en algunas ocasiones, la comunicación sin cifrar con los servidores no es una opción aceptable. Esto podría deberse a los requisitos de seguridad y cumplimiento normativo o bien a la posibilidad de que la aplicación solo acepte una conexión segura. Para tales aplicaciones, Application Gateway admite el cifrado SSL de un extremo a otro.

## <a name="web-application-firewall"></a>Firewall de aplicaciones web

Firewall de aplicaciones web (WAF) es una característica de Application Gateway que proporciona a las aplicaciones una protección centralizada contra vulnerabilidades de seguridad comunes. Firewall de aplicaciones web se basa en las reglas contenidas en el [conjunto de reglas básicas de OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 o 2.2.9. 

Las aplicaciones web son cada vez más los objetivos de ataques malintencionados que aprovechan vulnerabilidades comunes conocidas, como ataques por inyección de código SQL o ataques de scripts de sitios, por nombrar unos pocos. Impedir tales ataques en el código de aplicación puede ser un verdadero desafío y requerir tareas rigurosas de mantenimiento, aplicación de revisiones y supervisión en varias capas de la topología de aplicación. Un firewall de aplicaciones web centralizado facilita enormemente la administración de la seguridad y proporciona mayor protección a los administradores de la aplicación frente a amenazas o intrusiones. Las soluciones de WAF también pueden reaccionar más rápido ante una amenaza de la seguridad aplicando revisiones que aborden una vulnerabilidad conocida en una ubicación central en lugar de proteger cada una de las aplicaciones web por separado. Las puertas de enlace de aplicaciones existentes pueden transformarse rápidamente en puertas de enlace con un firewall de aplicaciones web habilitado.

## <a name="websocket-and-http2-traffic"></a>Tráfico de Websocket y HTTP/2

Application Gateway proporciona compatibilidad nativa con los protocolos Websocket y HTTP/2. No hay ninguna opción de configuración que permita al usuario habilitar o deshabilitar la compatibilidad con WebSocket. La compatibilidad con HTTP/2 se puede habilitar con Azure PowerShell.
 
Los protocolos WebSocket y HTTP/2 permiten una comunicación dúplex completa entre un servidor y un cliente a través de una conexión TCP de larga duración. Esto permite una comunicación más interactiva entre el servidor web y el cliente, que puede ser bidireccional sin necesidad de realizar sondeos como en las implementaciones basadas en HTTP. Estos protocolos tienen, a diferencia de HTTP, una sobrecarga reducida y pueden reutilizar la misma conexión TCP para varias solicitudes y respuestas, con lo que se utilizan los recursos de una manera más eficaz. Estos protocolos están diseñados para utilizarse a través de los puertos HTTP tradicionales 80 y 443.



## <a name="next-steps"></a>Pasos siguientes

Según los requisitos y el entorno, puede crear una puerta de enlace de aplicaciones de prueba mediante Azure Portal, Azure PowerShell o la CLI de Azure:

- [Guía de inicio rápido: Dirección del tráfico web con Azure Application Gateway: Azure Portal](quick-create-portal.md).
- [Guía de inicio rápido: Dirección del tráfico web con Azure Application Gateway: Azure PowerShell](quick-create-powershell.md).
- [Guía de inicio rápido: Dirección del tráfico web con Azure Application Gateway: CLI de Azure](quick-create-cli.md).
