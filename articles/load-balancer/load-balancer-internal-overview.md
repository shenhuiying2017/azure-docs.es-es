---
title: "Información general sobre el equilibrador de carga interno de Azure | Microsoft Docs"
description: "Cómo funciona un equilibrador de carga interno en Azure y escenarios para configurar los puntos de conexión internos."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 54e390dbdb07cb4c45c801b638099aa0dcc6db1a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="overview-of-azure-internal-load-balancer"></a>Información general sobre el equilibrador de carga interno de Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

El equilibrador de carga interno de Azure (ILB) solo dirige tráfico a los recursos que están dentro de un servicio de nube o que utilizan una VPN para acceder a la infraestructura de Azure. En este sentido, el ILB difiere de un equilibrador de carga accesible desde Internet. La infraestructura de Azure restringe el acceso a las direcciones de una IP virtual (VIP) con carga equilibrada de un servicio en la nube o una red virtual. Las direcciones de IP virtuales y las redes virtuales no se exponen nunca directamente a un punto de conexión de Internet. Las aplicaciones de línea de negocio internas se ejecutan en Azure y se accede a ellas desde Azure o desde recursos locales.

## <a name="why-you-might-need-an-internal-load-balancer"></a>Por qué puede resultarle útil un equilibrador de carga interno

El equilibrador de carga interno proporciona un equilibrio de carga entre las máquinas virtuales (VM) que residen en un servicio en la nube o en una red virtual con un ámbito regional. Para obtener información sobre redes virtuales con un ámbito regional, consulte [Redes virtuales regionales](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) en el blog de Azure. Las redes virtuales existentes configuradas para un grupo de afinidad no pueden usar ILB.

ILB permite los siguientes tipos de equilibrio de carga:

* En un servicio en la nube: equilibrio de carga entre las máquinas virtuales y un conjunto de máquinas virtuales que residen en el mismo servicio en la nube. Consulte este <a href="#figure1">ejemplo</a>.
* En una red virtual: equilibrio de carga entre las máquinas virtuales de la red virtual y un conjunto de máquinas virtuales que residen en el mismo servicio en la nube de la red virtual. Consulte este <a href="#figure2">ejemplo</a>.
* En una red virtual entre locales: equilibrio de carga entre los equipos locales y un conjunto de máquinas virtuales que residen en el mismo servicio en la nube de la red virtual. Consulte este <a href="#figure3">ejemplo</a>.
* En aplicaciones de varios niveles: equilibrio de carga para aplicaciones de varios niveles accesibles desde Internet, a cuyos niveles de back-end no se puede acceder desde Internet. Los niveles de back-end requieren un equilibrio de carga de tráfico en el nivel accesible desde Internet.
* En aplicaciones de línea de negocio: equilibrio de carga para las aplicaciones de línea de negocio hospedadas en Azure sin un hardware ni un software adicional al equilibrador de carga. Este escenario incluye servidores locales que se encuentran en el conjunto de equipos de cuyo tráfico se va a equilibrar la carga.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>Equilibrio de carga para aplicaciones de niveles múltiples accesibles desde Internet

El nivel web tiene puntos de conexión accesibles desde Internet para clientes de Internet y forma parte de un conjunto con equilibrio de carga. El ILB distribuye el tráfico que procede de los clientes web en el puerto TCP 443 (HTTPS) a los servidores web.

Los servidores de base de datos están detrás de un extremo ILB que los servidores web usan para el almacenamiento. El punto de conexión ILB es un punto de conexión para el equilibrio de carga de servicio de base de datos. La carga del tráfico se equilibra entre los servidores de base de datos en el conjunto del ILB.

En la imagen siguiente se muestra el equilibrio de carga interna de la aplicación de niveles múltiples accesible desde Internet dentro del mismo servicio en la nube.

<a name="figure1"></a>
![Aplicación de niveles múltiples accesible desde Internet](./media/load-balancer-internal-overview/IC736321.png)

Hay otro escenario disponible para las aplicaciones de varios niveles. El equilibrador de carga se implementa en un servicio en la nube diferente del que consume el servicio para el ILB.

Los servicios en la nube que usan la misma red virtual tienen acceso al punto de conexión del ILB. En la imagen siguiente se muestran los servidores web front-end que se encuentran en un servicio en la nube diferente al del back-end de base de datos. Los servidores front-end usan el punto de conexión del ILB dentro de la misma red virtual como back-end.

<a name="figure2"></a>
![Servidores front-end en un servicio en la nube diferente](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Equilibrio de carga para aplicaciones de línea de negocio de la intranet

La carga del tráfico de los clientes de la red local se equilibra en el conjunto de servidores de línea de negocio mediante una conexión VPN a la red de Azure.

El equipo del cliente puede acceder a una dirección IP desde el servicio de VPN de Azure con VPN de sitio a punto. La aplicación de línea de negocio se puede hospedar detrás del punto de conexión del ILB.

<a name="figure3"></a>
![Aplicación de línea de negocio hospedada detrás del punto de conexión del ILB](./media/load-balancer-internal-overview/IC744148.png)

Otro escenario para aplicaciones de línea de negocio es tener una VPN de sitio a sitio a la red virtual donde está configurado el punto de conexión del ILB. El tráfico de red local está enrutado al punto de conexión del ILB.

<a name="figure4"></a>
![Tráfico de red local enrutado al punto de conexión del ILB](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>Limitaciones

Las configuraciones del equilibrador de carga interno no son compatibles con SNAT. En este artículo, SNAT se refiere a escenarios que pasan por la traducción de direcciones de red de origen de enmascaramiento de puertos. Una máquina virtual en un grupo de equilibradores de carga debe alcanzar la dirección IP front-end del equilibrador de carga interno correspondiente. Cuando la carga del flujo es equilibrada en la máquina virtual que origina ese flujo, se producen errores de conexión. Los siguientes escenarios no son compatibles con el ILB. En su lugar, hay que usar un equilibrador de carga tipo proxy.

## <a name="next-steps"></a>Pasos siguientes

* [Compatibilidad de Azure Resource Manager con Azure Load Balancer](load-balancer-arm.md)
* [Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md)
* [Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)
* [Configuración del modo de distribución en el equilibrador de carga](load-balancer-distribution-mode.md)
* [Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
