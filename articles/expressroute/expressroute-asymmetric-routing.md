<properties
   pageTitle="Enrutamiento asimétrico | Microsoft Azure"
   description="Este artículo le guía a través de los problemas a los que se puede enfrentar un cliente con enrutamiento asimétrico en su red cuando tiene varios vínculos a un destino."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="osamazia"/>

# Enrutamiento asimétrico con varias rutas de acceso de red

En este artículo se explica cómo puede el tráfico de reenvío y de retorno tomar distintas rutas cuando hay varias rutas de acceso entre el origen y el destino.

Para comprender el enrutamiento asimétrico, se deben entender dos conceptos. Uno es el impacto de tener varias rutas de acceso de red. Otro es el comportamiento de los dispositivos que mantienen el estado, como los firewalls. Estos dispositivos se denominan dispositivos con estado. Una combinación de estos dos factores crea escenarios en los que un dispositivo con estado descarta el tráfico, ya que no vería el tráfico originado por sí mismo.

## Varias rutas de acceso de red

Cuando una red empresarial solo tiene un vínculo a Internet a través de su proveedor de servicios de Internet, todo el tráfico hacia Internet y desde él va por la misma ruta de acceso. A menudo, las empresas adquieren varios circuitos, como rutas de acceso redundantes, para mejorar el tiempo de actividad de la red. En estos casos, es posible que el tráfico que va fuera de la red hacia Internet atraviese un vínculo mientras el tráfico de retorno venga por un vínculo diferente. Este fenómeno se conoce como enrutamiento asimétrico, en el que el tráfico en sentido inverso toma una ruta de acceso diferente al del flujo original.

![Routing3](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Aunque la descripción anterior es para Internet, se aplica también a otras combinaciones de varias rutas de acceso. Algunos ejemplos son: una ruta de acceso de Internet y una ruta de acceso privada al mismo destino, varias rutas de acceso privadas al mismo destino, etc.

Cada enrutador a lo largo del proceso desde el origen hasta el destino calcula la mejor ruta de acceso para alcanzar un destino en función del cálculo que realice de la mejor ruta a dicho destino. La determinación de la mejor ruta de acceso posible se basa en dos factores principales.

1.	El enrutamiento entre redes externas se realiza en función de un protocolo de enrutamiento llamado Border Gateway Protocol, comúnmente conocido como BGP. BGP toma anuncios de vecinos, ejecuta una serie de pasos con ellos para determinar la mejor ruta de acceso al destino y la instala en su tabla de enrutamiento.
2.	Longitud de la máscara de subred asociada a una ruta de acceso. Si se reciben varios anuncios para la misma dirección IP pero diferentes máscaras de subred, es preferible el anuncio con la máscara de subred más larga porque se considera una ruta más específica.

## Dispositivos con estado

Los enrutadores miran el encabezado IP del paquete para fines de enrutamiento. Sin embargo, hay dispositivos que miran de forma aún más profunda dentro del paquete. Normalmente estos dispositivos miran los encabezados de la capa 4 (TCP/UDP) o incluso de la capa 7 (capa de aplicación). Estos dispositivos son dispositivos de seguridad o dispositivos de optimización de ancho de banda. El firewall es un ejemplo común de dispositivos con estado. El firewall permite o rechaza un paquete mediante sus interfaces basadas en diversos campos, como protocolo, puerto TCP o UDP y encabezados de dirección URL. La inspección de este paquete coloca mucha carga de procesamiento en el dispositivo. Para mejorar el rendimiento, el firewall inspecciona el primer paquete de un flujo. Si se permite el paquete, la información de flujo se mantiene en su tabla de estado. Todos los paquetes posteriores relacionados con este flujo se permiten en función de la decisión inicial. Por lo tanto, cuando llega un paquete, que forma parte de un flujo existente, al firewall y este no tiene información de estado anterior, el firewall descarta este paquete.

## Enrutamiento asimétrico con ExpressRoute

Cuando se conecta a Microsoft a través de ExpressRoute, se producen los siguientes cambios en la red.

1.	Tiene varios vínculos con Microsoft. Un vínculo es la conexión existente de Internet y otro se realiza a través de ExpressRoute. Una parte del tráfico a Microsoft puede ir a través de Internet pero volver a través de ExpressRoute o viceversa.
2.	Recibe direcciones IP más específicas a través de ExpressRoute. Por lo tanto, el tráfico de la red a Microsoft para los servicios ofrecidos a través de ExpressRoute siempre da preferencia a ExpressRoute.

Para entender el impacto de lo indicado anteriormente, vamos a analizar algunos escenarios. Supongamos que solo tiene un circuito a Internet y consume todos los servicios de Microsoft a través de Internet. El tráfico de la red a Microsoft y de vuelta recorre el mismo vínculo de Internet y pasa a través del firewall. El firewall registra el flujo según ve el primer paquete y los paquetes de retorno se permiten si el flujo existe en la tabla de estado.

![Routing1](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)


Ahora, active ExpressRoute y consuma los servicios ofrecidos por Microsoft a través de ExpressRoute. Todos los demás servicios de Microsoft se consumen a través de Internet. Implemente un servidor de firewall independiente en su perímetro, que se conecta a ExpressRoute. Microsoft anunciará prefijos más específicos en la red a través de ExpressRoute para servicios específicos. La infraestructura de enrutamiento elegirá ExpressRoute como ruta de acceso preferida para estos prefijos. Si no va a anunciar direcciones IP públicas en Microsoft a través de ExpressRoute, Microsoft se comunicará con las direcciones IP públicas a través de Internet. Por lo tanto, al reenviar el tráfico de la red a Microsoft, se usará ExpressRoute mientras que el tráfico inverso desde Microsoft utilizará Internet. Cuando el firewall en el perímetro ve un paquete de respuesta para un flujo que no se encuentra en la tabla de estado, descartará el tráfico devuelto.

Si decide utilizar el mismo grupo NAT para ExpressRoute e Internet, verá problemas similares con los clientes en las direcciones IP privadas de la red. La solicitud de servicios como Windows Update pasará a través de Internet como direcciones IP porque estos servicios no se anuncian a través de ExpressRoute. Sin embargo, el tráfico de retorno volverá a través de ExpressRoute. Si Microsoft recibe una dirección IP con la misma máscara de subred de Internet y ExpressRoute, dará preferencia a ExpressRoute en lugar de a Internet. Si un firewall u otro dispositivo con estado en el perímetro de la red, orientado a ExpressRoute, no tiene ninguna información previa sobre el flujo, descartará los paquetes que pertenecen a ese flujo.

## Soluciones para el enrutamiento asimétrico

Hay dos formas de resolver el problema del enrutamiento asimétrico. Una es a través del enrutamiento y otra, a través de NAT basada en origen (SNAT).

1. Enrutamiento

    - Debe asegurarse de que las direcciones IP públicas se anuncian en los vínculos WAN adecuados. Por ejemplo, si desea utilizar Internet para el tráfico de autenticación y ExpressRoute para el tráfico de correo electrónico. En ese caso, no debe anunciar las direcciones IP públicas de AD FS a través de ExpressRoute. De forma similar, no debe exponer un servidor de AD FS local a direcciones IP recibidas a través de ExpressRoute. Las rutas recibidas a través de ExpressRoute son más específicas, por lo que ExpressRoute será la ruta preferida para el tráfico de autenticación a Microsoft. Esto provocará un enrutamiento asimétrico.

    - Si desea usar ExpressRoute para la autenticación, debe asegurarse de que va a anunciar direcciones IP públicas de AD FS a través de ExpressRoute sin NAT. De este modo, el tráfico procedente de Microsoft al servidor de AD FS local se realiza a través de ExpressRoute, mientras que el tráfico de retorno del cliente a Microsoft utilizará ExpressRoute ya que es preferible esta opción en lugar de Internet.

2. NAT basada en origen

	Otra manera de resolver los problemas de enrutamiento asimétrico es mediante NAT de origen (SNAT). Por ejemplo, si no ha anunciado la dirección IP pública del servidor SMTP local a través de ExpressRoute y prevé utilizar Internet para esta comunicación. Una solicitud originada de Microsoft a su servidor SMTP local recorre Internet. Realice una NAT de origen con la solicitud entrante a una dirección IP interna. El tráfico inverso desde el servidor SMTP pasará al firewall perimetral (que se usa para hacer NAT) en lugar de a ExpressRoute. De este modo el tráfico de retorno volverá a través de Internet.


![Routing2](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## Detección de enrutamiento asimétrico

Traceroute es la mejor manera de asegurarse de que el tráfico recorra la ruta de acceso prevista. Si espera que el tráfico del servidor SMTP local a Microsoft tome la ruta de acceso de Internet, realice un traceroute del servidor SMTP a Office 365. El resultado validará que el tráfico sale de hecho de la red hacia Internet y no hacia ExpressRoute.

<!---HONumber=AcomDC_0824_2016-->