<properties
   pageTitle="Introducción a Puerta de enlace de aplicaciones | Microsoft Azure"
   description="Esta página proporciona información general sobre el servicio Puerta de enlace de aplicaciones para equilibrio de carga de nivel 7, incluidos los tamaños de puerta de enlace, el equilibrio de carga HTTP, la afinidad de sesión basada en cookies y la descarga SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/09/2015"
   ms.author="joaoma"/>

# ¿Qué es una Puerta de enlace de aplicaciones?


La Puerta de enlace de aplicaciones de Microsoft Azure ofrece una solución de equilibrio de carga HTTP administrada de Azure basada en el equilibrio de carga de nivel 7.

El equilibrio de carga de aplicaciones permite a los administradores de TI y desarrolladores crear reglas de enrutamiento para el tráfico de red basadas en HTTP. El servicio Puerta de enlace de aplicaciones tiene alta disponibilidad y está muy limitado. Para información sobre el contrato de nivel de servicio y los precios, consulte las páginas [Contratos de nivel de servicio](http://azure.microsoft.com/support/legal/sla/) y [Precios](https://azure.microsoft.com/pricing/details/application-gateway/).

Puerta de enlace de aplicaciones actualmente admite la entrega de aplicación del nivel 7 para lo siguiente:

- Equilibrio de carga HTTP
- Afinidad de sesión basada en cookies
- Descarga de Capa de sockets seguros (SSL)

![Puerta de enlace de aplicaciones](./media/application-gateway-introduction/appgateway1.png)

El equilibrio de carga de nivel 7 HTTP es útil para:

- Aplicaciones que requieren solicitudes de la misma sesión de usuario o cliente para llegar a la misma máquina virtual back-end. Ejemplos de esto serían las aplicaciones de carro de la compra y los servidores de correo web.
- Aplicaciones que desean liberar a las granjas de servidores web de la sobrecarga de terminación SSL.
- Aplicaciones, como la red de entrega de contenido, que requieren que varias solicitudes HTTP en la misma conexión TCP de ejecución prolongada se enruten a servidores backend diferentes o su carga se equilibre entre estos.


## Tamaños e instancias de puerta de enlace

Puerta de enlace de aplicaciones actualmente se ofrece en tres tamaños: pequeño, mediano y grande. Tamaños pequeños de instancia están pensados para escenarios de desarrollo y pruebas.

Puede crear hasta 50 puertas de enlace de aplicaciones por suscripción y cada una de esas puertas de enlace de aplicaciones puede tener un máximo de 10 instancias. El equilibrio de carga de Puerta de enlace de aplicaciones como servicio administrado de Azure permite el aprovisionamiento de un equilibrador de carga de nivel 7 detrás del equilibrador de carga de software de Azure.

En la tabla siguiente se muestra un promedio de rendimiento para cada instancia de puerta de enlace de aplicaciones:


| Respuesta de la página de back-end | Pequeña | Mediano | Grande|
|---|---|---|---|
| 6K | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100 k | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE]Se trata de una guía aproximada para un rendimiento de puerta de enlace de aplicaciones. El rendimiento real depende de varios detalles del entorno, como el tamaño medio de página, la ubicación de las instancias de back-end y el tiempo de procesamiento para proporcionar una página.

## Supervisión del estado


La puerta de enlace de aplicaciones de Azure supervisa el estado de las instancias de back-end automáticamente. Para más información, vea [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md).

## Configuración y administración

Puede crear y administrar una puerta de enlace de aplicaciones mediante las API de REST y mediante el uso de cmdlets de PowerShell.



## Pasos siguientes

Creación de una puerta de enlace de aplicaciones. Vea [Create an application gateway](application-gateway-create-gateway.md) (Creación de una puerta de enlace de aplicaciones).

Configuración de la descarga SSL. Vea [Configure SSL offload with Application Gateway](application-gateway-ssl.md) (Configuración de la descarga SSL con Puerta de enlace de aplicaciones).

<!---HONumber=AcomDC_0114_2016-->