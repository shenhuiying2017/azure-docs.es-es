---
title: Esquema de eventos de suscripción de Azure Event Grid
description: Describe las propiedades que se proporcionan para los eventos de suscripción con Azure Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 04bcc3018346e9cf162d26b0a23147c88e902e1e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301365"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Esquema de eventos de Azure Event Grid para las suscripciones

En este artículo se proporcionan las propiedades y los esquemas de los eventos de suscripción de Azure. Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md).

Las suscripciones y los grupos de recursos de Azure emiten los mismos tipos de eventos. Los tipos de eventos están relacionados con los cambios en los recursos. La diferencia principal es que los grupos de recursos emiten eventos para los recursos en el grupo de recursos y las suscripciones de Azure emiten eventos para los recursos a través de la suscripción.

## <a name="available-event-types"></a>Tipos de eventos disponibles

Las suscripciones de Azure emiten eventos de administración desde Azure Resource Manager, como cuando se crea una máquina virtual o se elimina una cuenta de almacenamiento.

| Tipo de evento | DESCRIPCIÓN |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Se genera cuando una operación de actualización o de creación de un recurso se realiza correctamente. |
| Microsoft.Resources.ResourceWriteFailure | Se genera cuando una operación de actualización o de creación de un recurso da error. |
| Microsoft.Resources.ResourceWriteCancel | Se genera cuando se cancela una operación de actualización o de creación de un recurso. |
| Microsoft.Resources.ResourceDeleteSuccess | Se genera cuando una operación de eliminación de un recurso tiene éxito. |
| Microsoft.Resources.ResourceDeleteFailure | Se genera cuando una operación de eliminación de un recurso da error. |
| Microsoft.Resources.ResourceDeleteCancel | Se genera cuando se cancela una operación de eliminación de un recurso. Este evento sucede cuando se cancela la implementación de una plantilla. |

## <a name="example-event"></a>Evento de ejemplo

En el ejemplo siguiente, se muestra el esquema de un evento de creación de un recurso: 

```json
[
  {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
      "dataVersion": "",
      "metadataVersion": "1"
  }
]
```

El esquema para un evento de eliminación de un recurso es similar:

```json
[{
  "topic":"/subscriptions/{subscription-id}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propiedades de evento

Un evento tiene los siguientes datos de nivel superior:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| topic | string | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| subject | string | Ruta al asunto del evento definida por el anunciante. |
| eventType | string | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | string | La hora de generación del evento en función de la hora UTC del proveedor. |
| id | string | Identificador único para el evento |
| data | objeto | Datos del evento de suscripción. |
| dataVersion | string | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | string | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

El objeto data tiene las siguientes propiedades:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| authorization | string | Autorización solicitada para la operación. |
| claims | string | Propiedades de las notificaciones. |
| correlationId | string | Identificador de operación para solucionar el problema. |
| httpRequest | string | Detalles de la operación. |
| resourceProvider | string | Proveedor de recursos que se realiza la operación. |
| resourceUri | string | URI del recurso en la operación. |
| operationName | string | Operación que se realizó. |
| status | string | Estado de la operación. |
| subscriptionId | string | Identificador de suscripción del recurso. |
| tenantId | string | Identificador de inquilino del recurso. |

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte el artículo de [introducción a Event Grid](overview.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).
