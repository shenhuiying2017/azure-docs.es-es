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
   ms.date="06/24/2015"
   ms.author="joaoma"/>

# Introducción técnica a la Puerta de enlace de aplicaciones 


Puerta de enlace de aplicaciones de Microsoft Azure es un servicio administrado de Azure similar a la Puerta de enlace de VPN de Azure. Puerta de enlace de aplicaciones proporciona una solución de equilibrio de carga HTTP administrada de Azure basada en el Enrutamiento de solicitud de aplicaciones (ARR) de IIS. El servicio de puerta de enlace de aplicaciones tiene alta disponibilidad y está muy medido. Para obtener información sobre el contrato de nivel de servicio y los precios, consulte las páginas [Contratos de nivel de servicio](http://azure.microsoft.com/support/legal/sla/) y [Precios](https://azure.microsoft.com/pricing/details/application-gateway/).

Puerta de enlace de aplicaciones actualmente admite la entrega de aplicación del nivel 7 para lo siguiente:

- Equilibrio de carga HTTP
- Afinidad de sesión basada en cookies
- Descarga SSL

![Puerta de enlace de aplicaciones](./media/application-gateway-introduction/appgateway1.png)

## Equilibrio de carga de nivel 7 HTTP
Azure proporciona equilibrio de carga de nivel 4 mediante un equilibrador de carga de software. Esto ocurre implícitamente para cada servicio en la nube que tenga una dirección IP virtual con equilibrio de carga (pública o interna). Sin embargo, hay muchas aplicaciones que pueden utilizar el equilibrio de carga basado en el nivel 7 (HTTP).


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

Creación de una puerta de enlace de aplicaciones. Vea [Creación de una puerta de enlace de aplicaciones](application-gateway-create-gateway.md).

Configuración de la descarga SSL. Consulte [Configuración de la descarga SSL con Puerta de enlace de aplicaciones](application-gateway-ssl.md).

<!---HONumber=July15_HO3-->