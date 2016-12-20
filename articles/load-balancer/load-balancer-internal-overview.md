---
title: "Información general sobre el equilibrador de carga interno | Microsoft Docs"
description: "Información general sobre el equilibrador de carga interno y sus características. Cómo funciona un equilibrador de carga en Azure y posibles escenarios para configurar extremos internos"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 177b01989ab2f3475cd853d89789b88e02d04be4
ms.openlocfilehash: 0194ffe54462e1a432ea10e69608e158f9cc54dc

---

# <a name="internal-load-balancer-overview"></a>Información general sobre el equilibrador de carga interno

A diferencia del equilibrador de carga accesible desde Internet, el equilibrador de carga interno (ILB) solo dirige el tráfico a los recursos dentro del servicio en la nube o mediante VPN para acceder la infraestructura de Azure. La infraestructura restringe el acceso a las direcciones IP virtuales de carga equilibrada de un servicio en la nube o una red virtual para que nunca se expongan directamente a un punto de conexión de Internet. Esto permite que las aplicaciones de línea de negocio (LOB) internas se ejecuten en Azure y se tenga acceso a ellas desde la nube o los recursos locales.

## <a name="why-you-may-need-an-internal-load-balancer"></a>Por qué es posible que sea necesario un equilibrador de carga interno

El equilibrio de carga interno (ILB) de Azure proporciona equilibrio de carga entre las máquinas virtuales que residen dentro de un servicio en la nube o en una red virtual con un ámbito regional. Para obtener información sobre el uso y la configuración de redes virtuales con un ámbito regional, consulte [Redes virtuales regionales](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) en el blog de Azure. Las redes virtuales existentes que se han configurado para un grupo de afinidad no pueden usar ILB.

ILB permite los siguientes tipos de equilibrio de carga:

* En un servicio en la nube, entre máquinas virtuales y un conjunto de máquinas virtuales que residen en el mismo servicio en la nube (consulte la Ilustración 1).
* En una red virtual, entre las máquinas virtuales de la red virtual y un conjunto de máquinas virtuales que residen en el mismo servicio en la nube de la red virtual (consulte la Ilustración 2).
* En una red virtual entre locales, entre los equipos locales y un conjunto de máquinas virtuales que residen en el mismo servicio en la nube de la red virtual (consulte la Ilustración 3).
* Aplicaciones de niveles múltiples accesibles desde Internet en las que los niveles back-end no son accesibles desde Internet, sino que requieren equilibrio de carga para el tráfico que procede del nivel accesible desde Internet.
* Equilibrio de carga para las aplicaciones LOB hospedadas en Azure sin requerir hardware ni software adicional de equilibrador de carga. Se incluyen los servidores locales del conjunto de equipos de cuyo tráfico se va a equilibrar la carga.

## <a name="internet-facing-multi-tier-applications"></a>Aplicaciones de niveles múltiples accesibles desde Internet

El nivel web tiene extremos accesibles desde Internet para clientes de Internet y forma parte de un conjunto con equilibrio de carga. El equilibrador de carga distribuye el tráfico que procede de los clientes web en el puerto TCP 443 (HTTPS) a los servidores web.

Los servidores de base de datos están detrás de un extremo ILB que los servidores web usan para el almacenamiento. Este es el extremo con equilibrio de carga del servicio de base de datos, de cuyo tráfico se va a equilibrar la carga entre los servidores de base de datos del conjunto ILB.

La imagen siguiente muestra la aplicación de niveles múltiples accesible desde Internet dentro del mismo servicio en la nube.

![Equilibrio de carga interno en un solo servicio en la nube](./media/load-balancer-internal-overview/IC736321.png)

Figura 1 - Una aplicación de niveles múltiples accesible desde Internet

Otro uso posible para una aplicación de niveles múltiples es cuando ILB se implementa en un servicio en la nube diferente al que consume el servicio para ILB.

Los servicios en la nube que usan la misma red virtual tendrán acceso al extremo ILB. En la imagen siguiente se muestran los servidores web front-end que se encuentran en un servicio en la nube diferente al del back-end de base de datos y que utilizan el punto de conexión ILB dentro de la misma red virtual.

![Equilibrio de carga interno entre servicios en la nube](./media/load-balancer-internal-overview/IC744147.png)

Figura 2 - Servidores front-end en un servicio en la nube diferente

## <a name="intranet-line-of-business-applications"></a>Aplicaciones de línea de negocio de Intranet

La carga del tráfico de los clientes de la red local se equilibra entre el conjunto de servidores LOB mediante una conexión VPN a la red de Azure.

El equipo cliente tendrá acceso a una dirección IP desde el servicio de VPN de Azure con VPN de sitio a punto. Permite el uso de la aplicación LOB hospedada detrás del punto de conexión ILB.

![Equilibrio de carga interno mediante una VPN de punto a sitio](./media/load-balancer-internal-overview/IC744148.png)

Figura 3 - Aplicaciones LOB hospedadas detrás del punto de conexión ILB

Otro escenario para LOB es tener una VPN de sitio a sitio a la red virtual donde está configurado el punto de conexión ILB. Esto permitirá que el tráfico de red local se enrute al punto de conexión ILB.

![Equilibrio de carga interno mediante una VPN de sitio a sitio](./media/load-balancer-internal-overview/IC744150.png)

Figura 4 - Tráfico de red local enrutado al punto de conexión ILB

## <a name="next-steps"></a>Pasos siguientes

[Compatibilidad de Azure Resource Manager con Azure Load Balancer](load-balancer-arm.md)

[Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md)

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO3-->


