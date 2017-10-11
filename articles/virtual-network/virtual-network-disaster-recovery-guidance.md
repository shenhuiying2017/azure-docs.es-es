---
title: "Qué hacer si se produce una interrupción del servicio de Azure que afecta a instancias de Azure Virtual Network | Microsoft Docs"
description: "Obtenga información acerca de qué hacer si se produce una interrupción de un servicio de Azure que afecta a las redes virtuales de Azure."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 4e125406d2e798138c45e3fbbf61a610afab69fc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="virtual-network--business-continuity"></a>Red virtual: continuidad del negocio
## <a name="overview"></a>Información general
Una red virtual (VNet) es una representación lógica de su red en la nube. Permite definir su propio espacio de direcciones IP privadas y segmentar la red en subredes. Las redes virtuales actúan como un límite de confianza para hospedar los recursos de proceso, como Máquinas virtuales y Servicios en la nube (roles web y de trabajo) de Azure. Una red virtual permite la comunicación IP privada directa entre los recursos hospedados en ella. También se puede vincular una red virtual a una red local a través de una de las opciones híbridas, como Puerta de enlace de VPN o ExpressRoute.

Una red virtual se crea dentro del ámbito de una región. Puede crear redes virtuales con el mismo espacio de direcciones en dos regiones diferentes (es decir, Este de EE y Oeste de EE. UU.), pero no se pueden conectar entre sí directamente. 

## <a name="business-continuity"></a>Continuidad del negocio
La aplicación puede sufrir diferentes formas de interrupción. Por ejemplo, es posible que el servicio se corte por completo en una región determinada como consecuencia de un desastre natural o que se produzca un desastre parcial debido a un error en varios dispositivos o servicios. La repercusión en el servicio de red virtual es diferente en cada una de estas situaciones.

**P: ¿Qué se debe hacer en caso de interrupción en una toda una región (es decir, si el servicio se corta por completo debido a un desastre natural)? ¿Qué ocurre con las redes virtuales hospedadas en la región?**

R: La red virtual y los recursos de la región afectada permanecen inaccesibles durante el tiempo en que dure la interrupción del servicio.

![Diagrama de red virtual simple](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: ¿Qué se puede hacer para volver a crear la misma red virtual en una región distinta?**

R: Una red virtual es un recurso bastante ligero. Puede invocar las API de Azure para crear una red virtual con el mismo espacio de direcciones en una región distinta. Para volver a crear el mismo entorno que estaba presente en la región afectada, tendrá que realizar llamadas de API para volver a implementar los Servicios en la nube (roles web y de trabajo) y las Máquinas virtuales que tenía. También tendrá que poner en marcha una Puerta de enlace de VPN y conectarse a la red local si tiene conectividad local (como en una implementación híbrida).

Las instrucciones para crear una red virtual se encuentran [aquí](virtual-networks-create-vnet-arm-pportal.md). 

**P: ¿Es posible volver a crear una réplica de una red virtual de una región determinada en otra región de antemano?**

R: Sí, puede crear dos redes virtuales con el mismo espacio de direcciones IP privadas y los recursos en dos regiones diferentes antes de que suceda algo. Si el cliente hospedaba los servicios orientados a Internet en la red virtual, podría haber configurado el Traffic Manager para distribuir geográficamente el tráfico en la región que está activa. Sin embargo, un cliente no puede conectar dos redes virtuales con el mismo espacio de direcciones con la red local, ya que esto causaría problemas de enrutamiento. Si se produjera un desastre y la pérdida de una red virtual en una región, el cliente podría conectar la otra red virtual en la región disponible con el mismo espacio de direcciones con la red local.

Las instrucciones para crear una red virtual se encuentran [aquí](virtual-networks-create-vnet-arm-pportal.md).

