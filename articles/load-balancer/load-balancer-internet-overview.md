
---
title: Información general sobre el equilibrador de carga accesible desde Internet | Microsoft Docs
description: Información general sobre el equilibrador de carga accesible desde Internet y sus características. Cómo funciona un Equilibrador de carga de Azure mediante máquinas virtuales y servicios en la nube.
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2016
ms.author: sewhee

---
# Información general sobre el equilibrador de carga accesible desde Internet
El Equilibrador de carga de Azure asigna la dirección IP y el número de puerto públicos del tráfico entrante a la dirección IP y el número de puerto privados de la máquina virtual, y viceversa, para el tráfico de respuesta desde la máquina virtual. Las reglas de equilibrio de carga permiten distribuir tipos específicos de tráfico entre varias máquinas virtuales o servicios. Por ejemplo, puede extender la carga del tráfico de solicitudes web entre varios servidores web o roles web.

> [!NOTE]
> Azure Load Balancer proporcionará un tráfico de red de distribución hash entre varias instancias de máquina virtual con la configuración predeterminada (obtenga más información sobre la distribución hash en [características del equilibrador de carga](load-balancer-overview.md#load-balancer-features)). Si busca la afinidad de sesión, consulte [modo de distribución de equilibrador de carga](load-balancer-distribution-mode.md).
> 
> 

Para los servicios en la nube que contienen instancias de roles web o de roles de trabajo, puede definir un punto de conexión público en la definición de servicio (.csdef).

El archivo servicedefinition.csdef contendrá la configuración del extremo, y cuando tenga varias instancias de rol para una implementación de rol web o de trabajo, el equilibrador de carga se configurará para ello. La forma de agregar instancias a su implementación en la nube está cambiando el recuento de instancias en el archivo de configuración de servicio (.csfg).

En la siguiente ilustración se muestra un extremo con equilibrio de carga para el tráfico web cifrado que se comparte entre tres máquinas virtuales en el puerto TCP público y privado de 443. Estas tres máquinas virtuales se encuentran en un conjunto con equilibrio de carga.

![ejemplo de equilibrador de carga público](./media/load-balancer-internet-overview/IC727496.png))

Cuando los clientes de Internet envían solicitudes de página web a la dirección IP pública del servicio en la nube y el puerto TCP 443, Azure Load Balancer distribuye las solicitudes entre las tres máquinas virtuales del conjunto de carga equilibrada. Puede obtener más información sobre el algoritmo del equilibrador de carga en [Página de información general del equilibrador de carga](load-balancer-overview.md#load-balancer-features).

## Pasos siguientes
Después de obtener información sobre un equilibrador de carga con conexión a Internet, también puede leer sobre el [equilibrador de carga interno](load-balancer-internal-overview.md) para entender mejor qué equilibrador de carga se adapta mejor a su implementación en la nube.

También puede [empezar a crear un equilibrador de carga orientado a Internet](load-balancer-get-started-internet-arm-ps.md) y configurar el tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento especifico del tráfico de red del equilibrador de carga.

Si la aplicación necesita mantener conexiones activas para servidores detrás de un equilibrador de carga, puede obtener más información acerca de la [configuración de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md). Le ayudará a conocer el comportamiento de conexión del tiempo de inactividad cuando se usa el Equilibrador de carga de Azure.

<!---HONumber=AcomDC_0831_2016-->