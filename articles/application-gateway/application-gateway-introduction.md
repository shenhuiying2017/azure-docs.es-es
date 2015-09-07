<properties 
   pageTitle="Introducción a Puerta de enlace de aplicaciones | Microsoft Azure"
	description="Esta página proporciona información general sobre el servicio Puerta de enlace de aplicaciones para equilibrio de carga de nivel 7, incluidos los tamaños de puerta de enlace, el equilibrio de carga HTTP, la afinidad de sesión basada en cookies y la descarga SSL."
	documentationCenter="na"
	services="application-gateway"
	authors="joaoma"
	manager="jdial"
	editor="tysonn"/>
<tags 
   ms.service="application-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/23/2015"
	ms.author="joaoma"/>

# ¿Qué es una Puerta de enlace de aplicaciones?


La Puerta de enlace de aplicaciones de Microsoft Azure ofrece una solución de equilibrio de carga HTTP administrada de Azure basada en el equilibrio de carga de nivel 7.

El equilibrio de carga de aplicaciones permite a los administradores de TI y desarrolladores crear reglas de enrutamiento para el tráfico de red basadas en HTTP. El servicio de puerta de enlace de aplicaciones tiene alta disponibilidad y está muy medido. Para obtener información sobre el contrato de nivel de servicio y los precios, consulte las páginas [Contratos de nivel de servicio](http://azure.microsoft.com/support/legal/sla/) y [Precios](https://azure.microsoft.com/pricing/details/application-gateway/).

Puerta de enlace de aplicaciones actualmente admite la entrega de aplicación del nivel 7 para lo siguiente:

- Equilibrio de carga HTTP
- Afinidad de sesión basada en cookies
- Descarga SSL

![Puerta de enlace de aplicaciones](./media/application-gateway-introduction/appgateway1.png)

## Equilibrio de carga de nivel 7 HTTP

Azure ofrece equilibrio de carga de nivel 4 a través del equilibrador de carga de Azure operativo en el nivel de transporte (TCP/UDP), y la carga de todo el tráfico de red entrante se equilibra en el servicio de la Puerta de enlace de aplicaciones. La Puerta de enlace de aplicaciones aplicará las reglas de enrutamiento al tráfico HTTP, ofreciendo un equilibrio de carga de nivel 7 (HTTP). Cuando se crea una puerta de enlace de aplicaciones, un extremo (VIP) se asociará y usará como dirección IP pública para el tráfico de red de entrada.

La Puerta de enlace de aplicaciones enrutará el tráfico HTTP en función de su configuración, independientemente de que se trate de una máquina virtual, un servicio en la nube, una aplicación web o una dirección IP externa.

En el diagrama siguiente se explica cómo fluye el tráfico para la Puerta de enlace de aplicaciones: ![Puerta de enlace de aplicaciones2](./media/application-gateway-introduction/appgateway2.png)

El equilibrio de carga de nivel 7 HTTP es útil para:


- Aplicaciones que requieren solicitudes de la misma sesión de usuario o cliente para llegar a la misma máquina virtual back-end. Ejemplos de esto serían las aplicaciones de carro de la compra y los servidores de correo web.
- Aplicaciones que desean liberar a las granjas de servidores web de la sobrecarga de terminación SSL.
- Aplicaciones, como la red CDN, que requieren que varias solicitudes HTTP en la misma conexión TCP de ejecución prolongada se enruten a servidores backend diferentes o su carga se equilibre entre estos.

## Tamaños e instancias de puerta de enlace

Puerta de enlace de aplicaciones actualmente se ofrece en 3 tamaños: pequeño, mediano y grande. Tamaños pequeños de instancia están pensados para escenarios de desarrollo y pruebas.

Puede crear hasta 10 puertas de enlace de aplicaciones por suscripción y cada una de esas puerta de enlace puede tener un máximo de 10 instancias. El equilibrio de carga de Puerta de enlace de aplicaciones como servicio administrado de Azure permite el aprovisionamiento de un equilibrador de carga de nivel 7 detrás del equilibrador de carga de software de Azure.

## Configuración y administración

Puede crear y administrar la puerta de enlace de aplicaciones mediante las API de REST y mediante el uso de cmdlets de PowerShell.

## Pasos siguientes

Creación de una puerta de enlace de aplicaciones. Consulte [Creación de una puerta de enlace de aplicaciones](application-gateway-create-gateway.md).

Configuración de la descarga SSL. Consulte [Configuración de la descarga SSL con Puerta de enlace de aplicaciones](application-gateway-ssl.md).

<!---HONumber=August15_HO9-->