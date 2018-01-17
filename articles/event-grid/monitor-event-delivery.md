---
title: Supervisar la entrega de mensajes de Azure Event Grid
description: "Describe cómo se supervisa la entrega de mensajes de Azure Event Grid."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/10/2018
ms.author: tomfitz
ms.openlocfilehash: 1552174589e91c370c3b85a9af7b5cc1106cbb90
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="monitor-event-grid-message-delivery"></a>Supervisar la entrega de mensajes de Event Grid 

En este artículo se describe cómo usar el portal para ver el estado de las entregas de eventos.

Event Grid ofrece entrega duradera. Entrega cada mensaje por lo menos una vez en cada suscripción. Los eventos se envían inmediatamente al webhook registrado de cada suscripción. Si un webhook no acusa recibo de un evento en los 60 segundos siguientes al primer intento, Event Grid reintenta la entrega del evento.

Para obtener información acerca de los reintentos y las entregas de eventos, consulte [Entrega y reintento de entrega de mensajes de Event Grid](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Métricas de entrega

El portal muestra las métricas para el estado de la entrega de mensajes de eventos.

Para los temas, las métricas son:

* **Publicación correcta**: el evento se envió correctamente al tema y se procesó con una respuesta 2xx.
* **Error al publicar**: el evento se envió al tema, pero se rechazó con un código de error.
* **Sin coincidencia**: el evento se publicó correctamente en el tema, pero no coincide con ninguna suscripción a eventos. Se descartó el evento.

Para las suscripciones, las métricas son:

* **Entrega realizada**: el evento se entregó correctamente al punto de conexión de la suscripción y se recibió una respuesta 2xx.
* **Entrega no realizada**: el evento se envió al punto de conexión de la suscripción, pero se recibió una respuesta 4xx o 5xx.
* **Expired Events (Eventos expirados)**: no se entregó el evento y se enviaron todos los reintentos. Se descartó el evento.
* **Matched Events (Eventos coincidentes)**: la suscripción a eventos coincide con el evento del tema.

## <a name="event-subscription-status"></a>Estado de la suscripción a eventos

Para ver las métricas de una suscripción a eventos, busque **Suscripciones de Event Grid** en los servicios disponibles y selecciónelo.

![Buscar suscripciones a eventos](./media/monitor-event-delivery/select-event-subscriptions.png)

Filtre por el tipo de evento, la suscripción y la ubicación. Seleccione **Métricas** para la suscripción que quiere consultar.

![Filtrar suscripciones a eventos](./media/monitor-event-delivery/filter-events.png)

Consulte las métricas para el tema del evento y la suscripción.

![Ver métricas de eventos](./media/monitor-event-delivery/subscription-metrics.png)

## <a name="custom-event-status"></a>Estado de evento personalizado

Si ha publicado un tema personalizado, puede consultar sus métricas. Seleccione el grupo de recursos que contiene el tema y seleccione el tema.

![Seleccionar tema personalizado](./media/monitor-event-delivery/select-custom-topic.png)

Consulte las métricas para el tema de eventos personalizado.

![Ver métricas de eventos](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>pasos siguientes

* Para obtener información acerca de los reintentos y las entregas de eventos, consulte [Entrega y reintento de entrega de mensajes de Event Grid](delivery-and-retry.md).
* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).
