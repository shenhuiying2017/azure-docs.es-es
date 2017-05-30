---
title: "Introducción a la arquitectura de procesamiento de mensajes de Azure Service Bus | Microsoft Docs"
description: "Describe la arquitectura de procesamiento de Retransmisión y mensajes del Bus de servicio de Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: ced46c64c1c105aa987759e05ab3680bc399f9a0
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017


---
# <a name="service-bus-architecture"></a>Arquitectura del Bus de servicio
En este artículo se describe la arquitectura de procesamiento de mensajes del Bus de servicio de Azure.

## <a name="service-bus-scale-units"></a>Unidades de escala del Bus de servicio
El Bus de servicio se organizada por *unidades de escala*. Una unidad de escalado es una unidad de implementación y contiene todos los componentes necesarios para ejecutar el servicio. Cada región implementa una o más unidades de escala del Bus de servicio.

Un espacio de nombres del Bus de servicio se asigna a una unidad de escalado. La unidad de escalado controla todos los tipos de entidades de Service Bus: retransmisiones y entidades de mensajería asincrónica (colas, temas, suscripciones). Una unidad de escalado del Bus de servicio consta de los siguientes componentes:

* **Un conjunto de nodos de puerta de enlace.** Los nodos de puerta de enlace autentican las solicitudes entrantes y controlan las solicitudes de retransmisión. Cada nodo de puerta de enlace tiene una dirección IP pública.
* **Un conjunto de nodos de agente de mensajería.** Los nodos de agente de mensajería procesan solicitudes relacionadas con entidades de mensajería.
* **Un almacén de puerta de enlace.** El almacén de puerta de enlace contiene los datos para todas las entidades que se definen en esta unidad de escalado. El almacén de puerta de enlace se implementa sobre una base de datos SQL de Azure.
* **Varios almacenes de mensajería.** Los almacenes de mensajería contienen los mensajes de todas las colas, los temas y las suscripciones que se definen en esta unidad de escalado. También contiene todos los datos de suscripción. A menos que estén habilitadas las [entidades de mensajería con particiones](service-bus-partitioning.md), se asigna una cola o un tema a un almacén de mensajería. Las suscripciones se almacenan en el mismo almacén de mensajería como su tema principal. Excepto en el caso de la [mensajería Premium](service-bus-premium-messaging.md)de Service Bus, los almacenes de mensajería se implementan sobre bases de datos SQL Azure.

## <a name="containers"></a>Contenedores
A cada entidad de mensajería se le asigna a un contenedor específico. Un contenedor es una construcción lógica que usa exactamente un almacén de mensajería para almacenar todos los datos relevantes para este contenedor. A cada contenedor se le asigna un nodo de agente de mensajería. Normalmente, hay más contenedores que nodos de agente de mensajería. Por tanto, cada nodo de agente de mensajería carga varios contenedores. La distribución de contenedores a un nodo de agente de mensajería se organiza de modo que todos los nodos de agente de mensajes se cargan por igual. Si cambia el modelo de carga(por ejemplo, uno de los contenedores se vuelve muy ocupado), o si un nodo de agente de mensajería deja de estar disponible temporalmente, los contenedores se redistribuyen entre los nodos de agente de mensajería.

## <a name="processing-of-incoming-messaging-requests"></a>Procesamiento de solicitudes entrantes de mensajería
Cuando un cliente envía una solicitud al Bus de servicio, el equilibrador de carga de Azure lo enruta a cualquiera de los nodos de puerta de enlace. El nodo de puerta de enlace autoriza la solicitud. Si la solicitud se refiere a una entidad de mensajería (cola, tema, suscripción), el nodo de la puerta de enlace busca la entidad en el almacén de puerta de enlace y determina en qué almacén de mensajería se encuentra la entidad. Después, busca qué nodo de agente de mensajería está dando servicio actualmente a este contenedor y envía la solicitud a ese nodo de agente de mensajería. El nodo de agente de mensajería procesa la solicitud y actualiza el estado de la entidad en el almacén del contenedor. El nodo de agente de mensajería envía entonces la respuesta al nodo de puerta de enlace, que envía a su vez una respuesta adecuada al cliente que emitió la solicitud original.

![Procesamiento de solicitudes entrantes de mensajería](./media/service-bus-architecture/ic690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Procesamiento de solicitudes entrantes de retransmisión
Cuando un cliente envía una solicitud a [Azure Relay](/azure/service-bus-relay/), Azure Load Balancer lo enruta a cualquiera de los nodos de puerta de enlace. Si la solicitud es una solicitud de escucha, el nodo de puerta de enlace crea una nueva retransmisión. Si la solicitud es una solicitud de conexión a una retransmisión específica, el nodo de puerta de enlace reenvía la solicitud de conexión al nodo de puerta de enlace que posee la retransmisión. El nodo de puerta de enlace que posee la retransmisión envía una solicitud de encuentro al cliente de escucha, solicitando al agente de escucha crear un canal temporal para el nodo de puerta de enlace que recibió la solicitud de conexión.

Cuando se establece la conexión de retransmisión, los clientes pueden intercambiar mensajes a través del nodo de puerta de enlace que se usa para el encuentro.

![Procesamiento de solicitudes entrantes de retransmisión WCF](./media/service-bus-architecture/ic690645.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha leído una introducción sobre la arquitectura de Service Bus, visite los siguientes vínculos para más información:

* [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
* [Introducción a Azure Relay](../service-bus-relay/relay-what-is-it.md)
* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* Una [solución de mensajería en cola mediante las colas de Service Bus](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)



