<properties
   pageTitle="Migración de la VPN de sitio a sitio a ExpressRoute"
   description="Este artículo le ayudará a migrar la VPN de sitio a sitio a ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />
   
# Migración de conexión: de VPN de sitio a sitio a ExpressRoute

Los clientes de Azure pueden conectarse a su red virtual de dos maneras: ExpressRoute y VPN de sitio a sitio. La conexión VPN de sitio a sitio va a través de Internet público. IPsec se utiliza para cifrar el tráfico de red. ExpressRoute es una conexión privada a Azure. Puede conectarse a Azure a través de un [proveedor de Exchange (EXP)](expressroute-exchange-providers.md) o un [proveedor de servicios de red (NSP)](expressroute-network-service-providers.md).

Si ya tiene una conexión VPN de sitio a sitio en la red virtual, puede seguir los pasos siguientes para migrarla y conectarse a través de ExpressRoute.

1) Inicie sesión en el Portal de Azure.

2) En el panel de navegación, haga clic en **REDES** y haga clic en la red virtual que tiene la conexión VPN.

3) Haga clic en **PANEL**. En la parte inferior de la página, haga clic en **ELIMINAR PUERTA DE ENLACE** y, a continuación, haga clic en **SÍ**.

  Este paso quitó la puerta de enlace VPN de sitio a sitio.

(4) Después de que el portal muestre "NO SE HA CREADO LA PUERTA DE ENLACE" para la red virtual, haga clic en la página **CONFIGURAR**.

(a) Haga clic en la casilla "Usar ExpressRoute".

(b) Cambie el CIDR para la subred de puerta de enlace a /28.

![Uso compartido de la suscripción](./media/expressroute-s2s-er-migration/expressroute-s2s-er.png)

5) En la parte inferior de la página, haga clic en **GUARDAR** y, a continuación, haga clic en SÍ.

6) Haga clic en **PANEL**. En la parte inferior de la página, haga clic en **CREAR PUERTA DE ENLACE** y, a continuación, haga clic en **SÍ**.

 En este paso se crea la puerta de enlace de ExpressRoute.
 
Cuando se crea la nueva puerta de enlace, la red virtual está lista para conectarse a un circuito ExpressRoute. Todas las máquinas virtuales en la red virtual están en funcionamiento durante todo el proceso. No es necesario moverlas o cerrarlas.

Vaya al último paso de la configuración ExpressRoute a través del tutorial de [proveedores de Exchange](expressroute-configuring-exps.md)/[proveedores de servicios de red](expressroute-configuring-nsps.md) para vincular la red virtual al circuito ExpressRoute.



<!--HONumber=54-->