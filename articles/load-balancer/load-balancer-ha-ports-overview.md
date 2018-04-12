---
title: Introducción a los puertos de alta disponibilidad en Azure | Microsoft Docs
description: Conozca el equilibrio de carga de los puertos de alta disponibilidad en una instancia interna de Load Balancer.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: f6e9dd09558a3485629d5b70dd8b68b292427b18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="high-availability-ports-overview"></a>Introducción a los puertos de alta disponibilidad

Azure Load Balancer Standard le permite equilibrar la carga de los flujos TCP y UDP en todos los puertos a la vez, cuando se usa una instancia interna de Load Balancer. 

Una regla de puertos de alta disponibilidad es una variante de una regla de equilibrio de carga, configurada en una instancia interna de Load Balancer estándar. Puede simplificar el modo en que usa Load Balancer y proporcionar una única regla para equilibrar la carga de todos los flujos TCP y UDP que llegan a todos los puertos de una instancia interna de Load Balancer estándar. La decisión de equilibrio de carga se toma por cada flujo. Esta decisión se toma en función de la conexión de tupla de cinco siguiente: dirección IP de origen, puerto de origen, dirección IP de destino, puerto de destino y protocolo.

La característica de puertos de alta disponibilidad le será de ayuda a la hora de usar escenarios críticos como, por ejemplo, aquellos que tengan alta disponibilidad y escalabilidad para dispositivos virtuales de red (NVA) que estén en redes virtuales. Asimismo, podrá serle de ayuda cuando necesite equilibrar la carga de un gran número de puertos. 

La característica de puertos de alta disponibilidad se configura al establecer los puertos de front-end y back-end en **0**y el protocolo en **Todos**. Es entonces cuando el recurso de Load Balancer interno equilibra todos los flujos TCP y UDP, independientemente del número de puerto.

## <a name="why-use-ha-ports"></a>¿Por qué usar puertos de alta disponibilidad?

### <a name="nva"></a>Dispositivos virtuales de red

Puede usar los dispositivos virtuales de red (NVA) para proteger la carga de trabajo de Azure frente a varios tipos de amenazas de seguridad. Cuando se usan los NVA en estos escenarios, deben ser confiables y de alta disponibilidad, y tener escalabilidad horizontal para la demanda.

Puede lograr estos objetivos si agrega instancias de NVA en el grupo de back-end de la instancia interna de Azure Load Balancer y configura una regla de Load Balancer en puertos de alta disponibilidad.

Los puertos de alta disponibilidad ofrecen varias ventajas para los escenarios de alta disponibilidad de NVA:
- Conmutación por error rápida para instancias en buen estado con sondeos de estado por instancia.
- Mayor rendimiento con escalado horizontal para *n* instancias activas.
- Escenarios *N*-activos y activo-pasivos.
- Eliminar la necesidad de usar soluciones complejas, como los nodos de Apache Zookeeper, para la supervisión de dispositivos.

En el diagrama siguiente se muestra una implementación de la red virtual de tipo hub-and-spoke. Los radios dirigen su tráfico a la red virtual del concentrador y a través de los NVA, antes de abandonar el espacio de confianza. Los NVA están detrás de una instancia interna de Load Balancer estándar con una configuración de puertos de alta disponibilidad. En consecuencia, se puede procesar y reenviar todo el tráfico.

![Diagrama de la red virtual de tipo hub-and-spoke con los NVA implementados en modo de alta disponibilidad.](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Si va a usar NVA, confirme con su proveedor cómo aprovechar mejor los puertos de alta disponibilidad y cuáles son los escenarios admitidos.

### <a name="load-balancing-large-numbers-of-ports"></a>Equilibrado de carga de un gran número de puertos

También puede usar puertos de alta disponibilidad para aplicaciones que requieran equilibrar la carga de un gran número de puertos. Igualmente, puede simplificar estos escenarios mediante una instancia interna de [Load Balancer estándar](load-balancer-standard-overview.md) con puertos de alta disponibilidad. Una regla de equilibrio de carga única reemplaza varias reglas individuales de equilibrio de carga: una por cada puerto.

## <a name="region-availability"></a>Disponibilidad en regiones

La característica de puertos de alta disponibilidad está disponible en todas las regiones globales de Azure.

## <a name="supported-configurations"></a>Configuraciones admitidas

### <a name="one-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Una configuración de puertos HA con una única dirección IP no flotante (sin Direct Server Return) en la instancia interna de Load Balancer estándar.

Esta es una configuración básica de puertos HA. La siguiente configuración le permite configurar el equilibrio de carga de puertos de alta disponibilidad en una única dirección IP de front-end:
- Al configurar Load Balancer estándar, active la casilla **Puertos HA** en la configuración de reglas de Load Balancer. 
- Asegúrese de que **IP flotante** está establecida en **Deshabilitado**.

Esta configuración no permite ninguna otra configuración de reglas de equilibrio de carga en el recurso actual de Load Balancer, ni ninguna otra configuración de recursos de Load Balancer interno para el conjunto dado de instancias de back-end.

Sin embargo, además de esta regla de puertos HA, puede configurar una instancia pública de Load Balancer estándar para las instancias de back-end.

### <a name="one-single-floating-ip-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Una configuración de puertos HA con una única dirección IP no flotante (Direct Server Return) en la instancia interna de Load Balancer estándar.

De igual forma, puede configurar Load Balancer para usar una regla de equilibrio de carga con **Puerto HA** con un único front-end y **Dirección IP flotante** establecida en **Habilitado**. 

Esta configuración le permite agregar más reglas de equilibrio de carga con IP flotante o una instancia pública de Load Balancer. Sin embargo, no puede usar una configuración de equilibrio de carga de puertos HA con IP flotante encima de esta configuración.

### <a name="multiple-ha-ports-configurations-on-the-internal-standard-load-balancer"></a>Varias configuraciones de puertos HA en la instancia interna de Load Balancer

Si su escenario requiere que configure más de un front-end de puertos HA para el mismo grupo de servidores back-end, puede conseguirlo de dos maneras: 
- Configure más de una dirección IP privada de front-end para un único recurso de una instancia interna de Load Balancer estándar.
- Configure varias reglas de equilibrio de carga, donde cada una tiene seleccionada una única dirección IP de font-end.
- Seleccione la opción **Puertos HA** y establezca **IP flotante** en **Habilitado** en todas las reglas de equilibrio de carga.

### <a name="internal-load-balancer-with-ha-ports--public-load-balancer-on-the-same-backend-instances"></a>Instancia interna de Load Balancer con puertos HA e instancia pública de Load Balancer en las mismas instancias de back-end

Puede configurar **un** recurso de una instancia pública de Load Balancer estándar para los recursos de back-end, junto con una única instancia interna de Load Balancer estándar con puertos HA.

>[!NOTE]
>Esta funcionalidad está disponible actualmente a través de plantillas de Azure Resource Manager, pero no mediante Azure Portal.

## <a name="limitations"></a>Limitaciones

- La configuración de puertos HA solo está disponible para la instancia interna de Load Balancer, aún no lo está para la instancia pública.

- No se admite una combinación de reglas de equilibrio de carga de puertos HA y puertos no HA.

- La característica de puertos HA no está disponible para IPv6.

- La simetría de flujo para escenarios de NVA solo es compatible con una NIC única. Consulte la descripción y el diagrama de los [dispositivos virtuales de red](#nva). Sin embargo, si una NAT de destino le sirve en su escenario, podría usarla para asegurarse de que la instancia interna de Load Balancer envía el tráfico de retorno a la misma NVA.


## <a name="next-steps"></a>Pasos siguientes

- [Configuración de puertos HA en una instancia interna de Load Balancer estándar](load-balancer-configure-ha-ports.md)
- [Más información sobre Load Balancer estándar](load-balancer-standard-overview.md)
