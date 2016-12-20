---
title: "Información general sobre el equilibrador de carga accesible desde Internet | Microsoft Docs"
description: "Información general sobre el equilibrador de carga accesible desde Internet y sus características. Cómo funciona un Equilibrador de carga de Azure mediante máquinas virtuales y servicios en la nube."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 529b37aa-a45c-41d1-8877-fee8cc1fa375
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: bec4f89556a2daa41e19b0ecb2ab9bbbed849107
ms.openlocfilehash: fb3f7c08c5f3a76e4bd28f3519ab72e8b636b93c

---

# <a name="internet-facing-load-balancer-overview"></a>Información general sobre el equilibrador de carga accesible desde Internet

El Equilibrador de carga de Azure asigna la dirección IP y el número de puerto públicos del tráfico entrante a la dirección IP y el número de puerto privados de la máquina virtual, y viceversa, para el tráfico de respuesta desde la máquina virtual. Las reglas de equilibrio de carga permiten distribuir tipos específicos de tráfico entre varias máquinas virtuales o servicios. Por ejemplo, puede extender la carga del tráfico de solicitudes web entre varios servidores web o roles web.

Para los servicios en la nube que contienen instancias de roles web o de roles de trabajo, puede definir un punto de conexión público en la definición de servicio (.csdef).

El archivo *servicedefinition.csdef* contiene la configuración del punto de conexión y, cuando tenga varias instancias de rol para una implementación de rol web o de trabajo, el equilibrador de carga se configurará para ello. La forma de agregar instancias a su implementación en la nube está cambiando el recuento de instancias en el archivo de configuración de servicio (.csfg).

En la siguiente ilustración se muestra un extremo con equilibrio de carga para el tráfico web cifrado que se comparte entre tres máquinas virtuales en el puerto TCP público y privado de 443. Estas tres máquinas virtuales se encuentran en un conjunto con equilibrio de carga.

![ejemplo de equilibrador de carga público](./media/load-balancer-internet-overview/IC727496.png))

Figura 1 - Punto de conexión de equilibrio de carga para tráfico web cifrado

Cuando los clientes de Internet envían solicitudes de página web a la dirección IP pública del servicio en la nube y el puerto TCP 443, Azure Load Balancer distribuye las solicitudes entre las tres máquinas virtuales del conjunto de carga equilibrada. Para más información sobre algoritmos del equilibrador de carga, consulte la [página de información general del equilibrador de carga](load-balancer-overview.md#load-balancer-features).

De forma predeterminada, Azure Load Balancer distribuye el tráfico de red equitativamente entre varias instancias de máquina virtual. También puede configurar la afinidad de la sesión. Para más información, consulte el [modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre el [equilibrador de carga interno](load-balancer-internal-overview.md) para saber qué equilibrador de carga se adapta mejor a su implementación en la nube.

También puede [empezar a crear un equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md) y configurar el tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento especifico del tráfico de red del equilibrador de carga.

Si la aplicación necesita mantener conexiones activas para servidores detrás de un equilibrador de carga, puede obtener más información acerca de la [configuración de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md). Le ayudará a conocer el comportamiento de conexión del tiempo de inactividad cuando se usa el Equilibrador de carga de Azure.



<!--HONumber=Nov16_HO3-->


