
<properties
   pageTitle="Información general sobre el equilibrador de carga interno | Microsoft Azure"
   description="Información general sobre el equilibrador de carga interno y sus características. Cómo funciona un equilibrador de carga en Azure y posibles escenarios para configurar extremos internos"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/25/2016"
   ms.author="sewhee" />


# Información general sobre el equilibrador de carga interno

A diferencia del equilibrador de carga con conexión a Internet, el equilibrador de carga interno (ILB) solo distribuye el tráfico a los recursos dentro del servicio en la nube o mediante VPN para acceder la infraestructura de Azure. La infraestructura restringe el acceso a las direcciones IP virtuales de carga equilibrada de un servicio en la nube o una red virtual para que nunca se expongan directamente a un punto de conexión de Internet. Esto permite que las aplicaciones de línea de negocio internas se ejecuten en Azure y se tenga acceso a ellas dentro de la nube o los recursos locales.

## Escenarios del equilibrador de carga interno

El equilibrio de carga interno (ILB) de Azure proporciona equilibrio de carga entre las máquinas virtuales que residen dentro de un servicio en la nube o en una red virtual con un ámbito regional. Para obtener información sobre el uso y la configuración de redes virtuales con un ámbito regional, consulte [Redes virtuales regionales](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) en el blog de Azure. Las redes virtuales existentes que se han configurado para un grupo de afinidad no pueden usar ILB.

ILB admite los siguientes escenarios:

- En un servicio en la nube, entre máquinas virtuales y un conjunto de máquinas virtuales que residen en el mismo servicio en la nube (consulte la Ilustración 1).
- En una red virtual, entre las máquinas virtuales de la red virtual y un conjunto de máquinas virtuales que residen en el mismo servicio en la nube de la red virtual (consulte la Ilustración 2).
- En una red virtual entre locales, entre los equipos locales y un conjunto de máquinas virtuales que residen en el mismo servicio en la nube de la red virtual (consulte la Ilustración 3).
- Aplicaciones de niveles múltiples accesibles desde Internet en las que los niveles back-end no son accesibles desde Internet, sino que requieren equilibrio de carga para el tráfico que procede del nivel accesible desde Internet.
- Equilibrio de carga para las aplicaciones de línea de negocio (LOB) hospedadas en Azure sin requerir hardware ni software adicional de equilibrador de carga. Se incluyen los servidores locales del conjunto de equipos de cuyo tráfico se va a equilibrar la carga.

## Aplicaciones de niveles múltiples accesibles desde Internet


El nivel web tiene extremos accesibles desde Internet para clientes de Internet y forma parte de un conjunto con equilibrio de carga. El equilibrador de carga distribuye el tráfico que procede de los clientes web en el puerto TCP 443 (HTTPS) a los servidores web.

Los servidores de base de datos están detrás de un extremo ILB que los servidores web usan para el almacenamiento. Este es el extremo con equilibrio de carga del servicio de base de datos, de cuyo tráfico se va a equilibrar la carga entre los servidores de base de datos del conjunto ILB.

La imagen siguiente describe la aplicación de niveles múltiples accesible desde Internet dentro del mismo servicio en la nube.

En la Ilustración 1

![Equilibrio de carga interno en un solo servicio en la nube](./media/load-balancer-internal-overview/IC736321.png)

Otro escenario posible para una aplicación de niveles múltiples es cuando ILB se implementa en un servicio en la nube diferente al que consume el servicio para ILB.

Los servicios en la nube que usan la misma red virtual tendrán acceso al extremo ILB.

En la imagen siguiente puede ver que los servidores web front-end se encuentran en un servicio en la nube diferente al del back-end de base de datos y que aprovechan el extremo ILB dentro de la misma red virtual.

Ilustración 2

![Equilibrio de carga interno entre servicios en la nube](./media/load-balancer-internal-overview/IC744147.png)

## Aplicaciones de línea de negocio (LOB) de Internet

La carga del tráfico de los clientes de la red local se equilibra entre el conjunto de servidores LOB mediante una conexión VPN a la red de Azure.

El equipo cliente tendrá acceso a una dirección IP desde el servicio VPN de Azure mediante una VPN de punto a sitio. Esto le permitirá usar la aplicación LOB hospedada detrás del extremo ILB.

Ilustración 3

![Equilibrio de carga interno mediante una VPN de punto a sitio](./media/load-balancer-internal-overview/IC744148.png)

Otro escenario para el LOB es tener una VPN de sitio a sitio a la red virtual donde está configurado el extremo ILB. Esto permitirá que el tráfico de red local se enrute al extremo ILB.

Ilustración 4

![Equilibrio de carga interno mediante una VPN de sitio a sitio](./media/load-balancer-internal-overview/IC744150.png)


## Pasos siguientes

[Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md)

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0831_2016-->