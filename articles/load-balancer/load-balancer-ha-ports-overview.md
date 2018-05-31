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
ms.openlocfilehash: 53e09498324f802ce1839d262999657d37ee973b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169125"
---
# <a name="high-availability-ports-overview"></a>Introducción a los puertos de alta disponibilidad

Azure Load Balancer Estándar le ayuda a equilibrar la carga de los flujos TCP y UDP en todos los puertos a la vez cuando se usa un equilibrador de carga interno. 

Una regla de puertos de alta disponibilidad (HA) es una variante de una regla de equilibrio de carga configurada en una instancia interna de Load Balancer Estándar. Puede simplificar el uso de un equilibrador de carga si proporciona una única regla para equilibrar la carga de todos los flujos TCP y UDP que llegan a todos los puertos de una instancia interna de Load Balancer Estándar. La decisión de equilibrio de carga se toma por cada flujo. Esta decisión se toma en función de la conexión de cinco tuplas siguiente: dirección IP de origen, puerto de origen, dirección IP de destino, puerto de destino y protocolo.

La característica de puertos de alta disponibilidad ayuda a la hora de usar escenarios críticos como aquellos con alta disponibilidad y escalabilidad para dispositivos virtuales de red (NVA) que estén en redes virtuales. La característica también ayuda cuando hay que equilibrar la carga de un gran número de puertos. 

La característica de puertos de alta disponibilidad se configura al establecer los puertos de front-end y back-end en **0** y el protocolo en **Todos**. Es entonces cuando el equilibrador de carga interno equilibra todos los flujos TCP y UDP, independientemente del número de puerto.

## <a name="why-use-ha-ports"></a>¿Por qué usar puertos de alta disponibilidad?

### <a name="nva"></a>Dispositivos virtuales de red

Puede usar dispositivos virtuales de red (NVA) para proteger la carga de trabajo de Azure frente a varios tipos de amenazas de seguridad. Cuando se usan NVA en estos escenarios, deben ser confiables y de alta disponibilidad y escalar horizontalmente a petición.

Puede lograr estos objetivos si agrega instancias de NVA al grupo de back-end del equilibrador de carga interno y configura una regla de equilibrador de carga en puertos de alta disponibilidad.

En los escenarios de HA de NVA, los puertos de alta disponibilidad proporcionan las siguientes ventajas:
- Proporcionan conmutación por error rápida para instancias en buen estado con sondeos de estado por instancia
- Garantizan un mayor rendimiento con escalado horizontal para instancias *n*-activas
- Proporcionan escenarios *n*-activos y activo-pasivos
- Eliminan la necesidad de usar soluciones complejas, como los nodos de Apache ZooKeeper, para la supervisión de dispositivos

En el diagrama siguiente se muestra una implementación de la red virtual de tipo hub-and-spoke. Los radios dirigen su tráfico a la red virtual del concentrador y a través de los NVA, antes de abandonar el espacio de confianza. Los NVA están detrás de una instancia interna de Load Balancer estándar con una configuración de puertos de alta disponibilidad. En consecuencia, se puede procesar y reenviar todo el tráfico.

![Diagrama de la red virtual de tipo hub-and-spoke con los NVA implementados en modo de alta disponibilidad.](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Si va a usar NVA, confirme con su proveedor cómo aprovechar mejor los puertos de alta disponibilidad y cuáles son los escenarios admitidos.

### <a name="load-balancing-large-numbers-of-ports"></a>Equilibrado de carga de un gran número de puertos

También puede usar puertos de alta disponibilidad para aplicaciones que requieran equilibrar la carga de un gran número de puertos. Igualmente, puede simplificar estos escenarios mediante una instancia interna de [Load Balancer estándar](load-balancer-standard-overview.md) con puertos de alta disponibilidad. Una regla de equilibrio de carga única sustituye a varias reglas individuales de equilibrio de carga, una por cada puerto.

## <a name="region-availability"></a>Disponibilidad en regiones

La característica de puertos de alta disponibilidad está disponible en todas las regiones globales de Azure.

## <a name="supported-configurations"></a>Configuraciones admitidas

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Una configuración de puertos HA con una única dirección IP no flotante (sin Direct Server Return) en una instancia interna de Load Balancer Estándar

Esta es una configuración básica de puertos HA. Para configurar una regla de equilibrio de carga de puertos HA en una sola dirección IP de front-end, haga lo siguiente:
1. Al configurar Load Balancer Estándar, active la casilla **Puertos HA** en la configuración de reglas de equilibrador de carga.
2. En **IP flotante**, seleccione **Deshabilitado**.

Esta configuración no permite ninguna otra configuración de reglas de equilibrio de carga en el equilibrador de carga actual. Tampoco permite ninguna otra configuración del equilibrador de carga interno para el conjunto especificado de instancias de back-end.

Pero, además de esta regla de puertos HA, puede configurar una instancia pública de Load Balancer Estándar para las instancias de back-end.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Una configuración de puertos HA con una única dirección IP flotante (Direct Server Return) en una instancia interna de Load Balancer Estándar

De igual forma, puede configurar el equilibrador de carga para usar una regla de equilibrio de carga con **Puerto HA** con un único front-end si establece **IP flotante** en **Habilitado**. 

Esta configuración permite agregar más reglas de equilibrio de carga con IP flotante o un equilibrador de carga público. Pero no puede usar una configuración de equilibrio de carga de puertos HA con IP no flotante encima de esta configuración.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Varias configuraciones de puertos HA en una instancia interna de Load Balancer Estándar

Si el escenario requiere que configure más de un front-end de puertos HA para el mismo grupo de back-end, puede hacer lo siguiente: 
- Configure más de una dirección IP privada de front-end para un único recurso interno Load Balancer Standard.
- Configure varias reglas de equilibrio de carga, donde cada una tenga seleccionada una única dirección IP de front-end.
- Seleccione la opción **Puertos HA** y establezca **IP flotante** en **Habilitado** en todas las reglas de equilibrio de carga.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Un equilibrador de carga interno con puertos HA y un equilibrador de carga público en la misma instancia de back-end

Puede configurar *un* recurso público Load Balancer Estándar para los recursos de back-end, junto con una única instancia interna de Load Balancer Estándar con puertos HA.

>[!NOTE]
>Esta funcionalidad está disponible actualmente a través de plantillas de Azure Resource Manager, pero no mediante Azure Portal.

## <a name="limitations"></a>Limitaciones

- La configuración de puertos HA solo está disponible para equilibradores de carga internos. No está disponible para equilibradores de carga públicos.

- No se admite la combinación de una regla de equilibrio de carga de puertos HA y una regla de equilibrio de carga de puertos no HA.

- La característica de los puertos de alta disponibilidad no está disponible para IPv6.

- La simetría de flujo para escenarios de NVA solo es compatible con una NIC única. Consulte la descripción y el diagrama de los [dispositivos virtuales de red](#nva). Pero si una NAT de destino le sirve en su escenario, podría usarla para asegurarse de que el equilibrador de carga interno envía el tráfico de retorno a la misma NVA.


## <a name="next-steps"></a>Pasos siguientes

- [Configuración de los puertos de alta disponibilidad para un equilibrador de carga interno](load-balancer-configure-ha-ports.md)
- [Más información sobre Load Balancer estándar](load-balancer-standard-overview.md)
