---
title: Entrega y reintento de entrega de Azure Event Grid
description: Describe cómo Azure Event Grid entrega eventos y cómo administra los mensajes no entregados.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 27ad4351bdcf9d7d655b19343dcf41d334f079f9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega y reintento de entrega de mensajes de Event Grid 

En este artículo se describe cómo Azure Event Grid administra los eventos cuando no se confirma la entrega.

Event Grid ofrece entrega duradera. Entrega cada mensaje por lo menos una vez en cada suscripción. Los eventos se envían inmediatamente al webhook registrado de cada suscripción. Si un webhook no acusa recibo de un evento en los 60 segundos siguientes al primer intento, Event Grid reintenta la entrega del evento. 

Actualmente, Event Grid envía cada evento individualmente a los suscriptores. El suscriptor recibe una matriz con un solo evento.

## <a name="message-delivery-status"></a>Estado de entrega de mensajes

Event Grid usa códigos de respuesta HTTP para acusar recibo de eventos. 

### <a name="success-codes"></a>Códigos de éxito

Los siguientes códigos de respuesta HTTP response indican que un evento se ha entregado correctamente en el webhook. Event Grid considera la entrega finalizada.

- 200 OK
- 202 - Aceptado

### <a name="failure-codes"></a>Códigos de error

Los siguientes códigos de respuesta HTTP indican que el intento de entrega de un evento no se ha realizado. 

- 400 - Solicitud incorrecta
- 401 No autorizado
- 404 No encontrado
- 408 - Tiempo de espera de solicitud
- 414 - URI de solicitud demasiado largo
- Error de servidor interno 500
- Servicio no disponible 503
- Tiempo de espera de puerta de enlace 504

Si Event Grid recibe un error que indica que el punto de conexión no está disponible, intenta enviar de nuevo el evento. 

## <a name="retry-intervals-and-duration"></a>Intervalos de reintento y duración

Event Grid usa una directiva de reintentos de retroceso exponencial para la entrega de eventos. Si el webhook no responde o devuelve un código de error, Event Grid reintenta la entrega según la siguiente programación:

1. 10 segundos
2. 30 segundos
3. 1 minuto
4. 5 minutos
5. 10 minutos
6. 30 minutos
7. 1 hora

Event Grid agrega una pequeña selección aleatoria a todos los intervalos de reintento. Después de una hora, la entrega del evento se vuelve a intentar cada una hora.

De forma predeterminada, Azure Event Grid expira todos los eventos que no se entregan en 24 horas.

## <a name="next-steps"></a>Pasos siguientes

* Para ver el estado de las entregas de eventos, consulte [Supervisar la entrega de mensajes de Event Grid](monitor-event-delivery.md).
* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).