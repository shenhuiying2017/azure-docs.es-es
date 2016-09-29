<properties
   pageTitle="Introducción a Puerta de enlace de aplicaciones | Microsoft Azure"
   description="Esta página proporciona información general sobre el servicio Puerta de enlace de aplicaciones para equilibrio de carga de nivel 7, incluidos los tamaños de puerta de enlace, el equilibrio de carga HTTP, la afinidad de sesión basada en cookies y la descarga SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace"/>

# Introducción a Puerta de enlace de aplicaciones

Application Gateway de Microsoft Azure ofrece un servicio de equilibrio de carga HTTP administrada de Azure de nivel 7. Por decirlo de una manera sencilla, funciona aceptando tráfico, con reglas que define esta solución y redirigiendo el tráfico a las instancias back-end apropiadas.

El equilibrio de carga de aplicaciones permite a los administradores de TI y desarrolladores crear reglas de enrutamiento para el tráfico de red basadas en el protocolo HTTP. El servicio Puerta de enlace de aplicaciones tiene alta disponibilidad y está muy limitado. Para información sobre el contrato de nivel de servicio y los precios, consulte las páginas [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/) y [Precios de Puerta de enlace de aplicaciones](https://azure.microsoft.com/pricing/details/application-gateway/).

En estos momentos, Application Gateway admite la entrega de aplicaciones de nivel 7 con las siguientes características:

- **Equilibrio de carga HTTP**: la característica principal de Application Gateway es proporcionar equilibrio de carga. El equilibrio de carga se realiza en el nivel 7 y se usa exclusivamente para el tráfico HTTP.
- **Afinidad de sesión basada en cookies**: esta característica es útil cuando se quiere mantener una sesión de usuario en el mismo back-end. Mediante el uso de cookies, Application Gateway puede dirigir el tráfico posterior desde una sesión de usuario al mismo back-end para procesarlo. Esta característica es importante en aquellos casos donde se guarda un elemento de forma local en el servidor back-end para una sesión de usuario.
- **[Descarga de Capa de sockets seguros (SSL)](application-gateway-ssl-arm.md)**: esta característica realiza la costosa tarea de descifrar el tráfico HTTPS desde los servidores web. Al terminar la conexión SSL en Application Gateway y reenviar la solicitud sin cifrar al servidor, el servidor web no tiene que realizar el descifrado. Application Gateway vuelve a cifrar la respuesta antes de enviarla al cliente. Esta característica es útil en escenarios donde el back-end se encuentra en la misma red virtual protegida que Application Gateway de Azure.
- **[Enrutamiento de contenido basado en direcciones URL](application-gateway-url-route-overview.md)**: esta característica ofrece la posibilidad de usar diferentes servidores back-end para tráfico distinto. El tráfico de una carpeta del servidor web o de una red CDN podría dirigirse a un back-end diferente, lo que reduce la carga innecesaria en back-ends que no entregan contenido específico.
- **[Enrutamiento multisitio](application-gateway-multi-site-overview.md)**: Application Gateway permite consolidar hasta 20 sitios web en una sola puerta de enlace de aplicaciones.
- **[Compatibilidad con WebSocket](application-gateway-websocket.md)**: otra gran característica de Application Gateway es la compatibilidad nativa con WebSocket.


## Equilibrio de carga de nivel 7 HTTP

La Puerta de enlace de aplicaciones aplica las reglas de enrutamiento al tráfico HTTP, ofreciendo un equilibrio de carga de nivel 7 (HTTP). Cuando se crea una puerta de enlace de aplicaciones, un punto de conexión (VIP) se asocia y usa como dirección IP pública para el tráfico de red de entrada. Azure ofrece equilibrio de carga de nivel 4 a través de la instancia de Azure Load Balancer operativa en el nivel de transporte (TCP/UDP), y la carga de todo el tráfico de red entrante se equilibra en el servicio Puerta de enlace de aplicaciones. La Puerta de enlace de aplicaciones enruta el tráfico HTTP en función de su configuración, independientemente de que se trate de una máquina virtual, un servicio en la nube, una aplicación web o una dirección IP externa.

El equilibrio de carga de nivel 7 HTTP es útil para:

- Aplicaciones que requieren solicitudes de la misma sesión de usuario o cliente para llegar a la misma máquina virtual back-end. Ejemplos de estas aplicaciones serían las aplicaciones de carro de la compra y los servidores de correo web.
- Aplicaciones que desean liberar a las granjas de servidores web de la sobrecarga de terminación SSL.
- Aplicaciones, como la red de entrega de contenido, que requieren que varias solicitudes HTTP en la misma conexión TCP de ejecución prolongada se enruten a servidores backend diferentes o su carga se equilibre entre estos.
- Aplicaciones que admiten el tráfico de WebSocket, pero todavía tienen un punto de conexión HTTP para tareas de supervisión.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Tamaños e instancias de puerta de enlace

Puerta de enlace de aplicaciones actualmente se ofrece en tres tamaños: pequeño, mediano y grande. Tamaños pequeños de instancia están pensados para escenarios de desarrollo y pruebas.

Puede crear hasta 50 puertas de enlace de aplicaciones por suscripción y cada una de esas puertas de enlace de aplicaciones puede tener un máximo de 10 instancias. El equilibrio de carga de Puerta de enlace de aplicaciones como servicio administrado de Azure permite el aprovisionamiento de un equilibrador de carga de nivel 7 detrás del equilibrador de carga de software de Azure.

En la tabla siguiente se muestra un promedio de rendimiento para cada instancia de puerta de enlace de aplicaciones:

| Respuesta de la página de back-end | Pequeña | Mediano | Grande|
|---|---|---|---|
| 6000 | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100 000 | 35 Mbps | 100 Mbps| 200 Mbps |

>[AZURE.NOTE] Se trata de valores aproximados para un rendimiento de puerta de enlace de aplicaciones. El rendimiento real depende de varios detalles del entorno, como el tamaño medio de página, la ubicación de las instancias de back-end y el tiempo de procesamiento para proporcionar una página.

## Supervisión del estado

Application Gateway de Azure supervisa el estado de las instancias back-end automáticamente mediante sondeos de estado básicos o personalizados. Para obtener más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md).

## Configuración y administración

Para su punto de conexión, Application Gateway puede tener una dirección IP pública, privada o ambas al configurarse. Application Gateway se configura dentro de una red virtual de su propia subred. La subred que se cree o utilice para la puerta de enlace de aplicaciones no puede contener otros tipos de recursos; en la subred solo se permiten otras puertas de enlace de aplicaciones.

Puede crear y administrar una puerta de enlace de aplicaciones mediante las API de REST, los cmdlets de PowerShell, la CLI de Azure o el [Portal de Azure](https://portal.azure.com/).

## Pasos siguientes

Ahora que ya conoce cómo funciona Puerta de enlace de aplicaciones, puede [crear una puerta de enlace de aplicaciones](application-gateway-create-gateway-portal.md) o bien puede [crear una descarga de SSL de Puerta de enlace de aplicaciones](application-gateway-ssl-arm.md) para equilibrar la carga de las conexiones HTTPS.

Para aprender a crear una puerta de enlace de aplicaciones mediante el enrutamiento de contenido basado en direcciones URL, vaya a [Creación de una puerta de enlace de aplicaciones mediante enrutamiento basado en direcciones URL](application-gateway-create-url-route-arm-ps.md) para obtener más información.

<!---HONumber=AcomDC_0914_2016-->