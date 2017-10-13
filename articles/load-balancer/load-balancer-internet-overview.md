---
title: "Información general sobre el equilibrador de carga accesible desde Internet | Microsoft Docs"
description: "Información general sobre el equilibrador de carga accesible desde Internet y sus características. Cómo funciona un Equilibrador de carga de Azure mediante máquinas virtuales y servicios en la nube."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 529b37aa-a45c-41d1-8877-fee8cc1fa375
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5b9ffeadf6b1ffc4eaf4f49b85ba752c27da0e46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="internet-facing-load-balancer-overview"></a>Información general sobre el equilibrador de carga accesible desde Internet

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

El Equilibrador de carga de Azure asigna la dirección IP y el número de puerto públicos del tráfico entrante a la dirección IP y el número de puerto privados de la máquina virtual, y viceversa, para el tráfico de respuesta desde la máquina virtual. Las reglas de equilibrio de carga permiten distribuir tipos específicos de tráfico entre varias máquinas virtuales o servicios. Por ejemplo, puede extender la carga del tráfico de solicitudes web entre varios servidores web o roles web.

Para los servicios en la nube que contienen instancias de roles web o de roles de trabajo, puede definir un punto de conexión público en la definición de servicio (.csdef).

El archivo *servicedefinition.csdef* contiene la configuración del punto de conexión y, cuando tenga varias instancias de rol para una implementación de rol web o de trabajo, el equilibrador de carga se configurará para ello. La forma de agregar instancias a su implementación en la nube está cambiando el recuento de instancias en el archivo de configuración de servicio (.csfg).

## <a name="example-of-an-internet-facing-load-balancer"></a>Ejemplo de equilibrador de carga accesible desde Internet

En la siguiente ilustración, se muestra un punto de conexión de carga equilibrada para el tráfico web que se comparte entre tres máquinas virtuales en el puerto TCP público y privado de 80. Estas tres máquinas virtuales se encuentran en un conjunto con equilibrio de carga.

![ejemplo de equilibrador de carga público](./media/load-balancer-internet-overview/IC727496.png)

Figura 1: punto de conexión de carga equilibrada para tráfico web

Cuando los clientes de Internet envían solicitudes de página web a la dirección IP pública del servicio en la nube en el puerto TCP 80, Azure Load Balancer distribuye las solicitudes entre las tres máquinas virtuales del conjunto de carga equilibrada. Para más información sobre algoritmos del equilibrador de carga, consulte la [página de información general del equilibrador de carga](load-balancer-overview.md#load-balancer-features).

De forma predeterminada, Azure Load Balancer distribuye el tráfico de red equitativamente entre varias instancias de máquina virtual. También puede configurar la afinidad de la sesión. Para más información, consulte el [modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre el [equilibrador de carga interno](load-balancer-internal-overview.md) para saber qué equilibrador de carga se adapta mejor a su implementación en la nube.

También puede [empezar a crear un equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md) y configurar el tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento especifico del tráfico de red del equilibrador de carga.

Si la aplicación necesita mantener conexiones activas para servidores detrás de un equilibrador de carga, puede obtener más información acerca de la [configuración de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md). Le ayudará a conocer el comportamiento de conexión del tiempo de inactividad cuando se usa el Equilibrador de carga de Azure.
