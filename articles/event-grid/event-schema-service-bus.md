---
title: Esquema de eventos de Service Bus de Azure Event Grid
description: Describe las propiedades que se proporcionan para los eventos de Service Bus con Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: article
ms.date: 02/21/2018
ms.author: babanisa
ms.openlocfilehash: 72780bff3807534efb456a9a7998f7d4de3c6f12
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Esquema de eventos de Azure Event Grid para Service Bus

En este artículo se proporcionan las propiedades y los esquemas de los eventos de Service Bus. Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Tipos de eventos disponibles

Service Bus emite los siguientes tipos de eventos:

| Tipo de evento | DESCRIPCIÓN |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Este evento se genera si hay mensajes activos en una cola o suscripción y no hay receptores a la escucha. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Este evento se genera si hay mensajes activos en una cola de mensajes fallidos y no hay receptores a la escucha. |

## <a name="example-event"></a>Evento de ejemplo

En el ejemplo siguiente se muestra el esquema de un mensajes activo con ningún evento de agentes de escucha:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

El esquema para un evento de cola de mensajes fallidos es similar:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propiedades de evento

Un evento tiene los siguientes datos de nivel superior:

| Propiedad | type | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| topic | string | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| subject | string | Ruta al asunto del evento definida por el anunciante. |
| eventType | string | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | string | La hora de generación del evento en función de la hora UTC del proveedor. |
| id | string | Identificador único para el evento |
| data | objeto | Datos de eventos de Blob Storage. |
| dataVersion | string | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | string | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

El objeto data tiene las siguientes propiedades:

| Propiedad | type | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| nameSpaceName | string | El espacio de nombres de Service Bus donde se encuentra el recurso. |
| requestUri | string | El URI en el que la cola específica o suscripción específicos emite el evento. |
| entityType | string | El tipo de entidad de Service Bus que emite eventos (cola o suscripción). |
| queueName | string | La cola con mensajes activos si se suscribe a una cola. El valor null si se usan temas o suscripciones. |
| topicName | string | El tema al que pertenece la suscripción de Service Bus con mensajes activos. El valor null si se usa una cola. |
| subscriptionName | string | La suscripción de Service Bus con mensajes activos. El valor null si se usa una cola. |

## <a name="next-steps"></a>pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).
* Para obtener más información sobre cómo usar Azure Event Grid con Service Bus, consulte [Introducción a la integración de Azure Service Bus en Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Pruebe a [recibir eventos de Service Bus con Functions o Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
