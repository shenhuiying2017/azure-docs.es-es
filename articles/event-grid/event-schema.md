---
title: Esquema de eventos de Azure Event Grid
description: Describe las propiedades que se proporcionan para los eventos con Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: babanisa
ms.openlocfilehash: 9d1f0eed28a1c1c6776ddba89480adcedfc599a5
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="azure-event-grid-event-schema"></a>Esquema de eventos de Azure Event Grid

En este artículo se describen las propiedades y el esquema que están presentes para todos los eventos. Los eventos constan de un conjunto de cinco propiedades de cadena y un objeto de datos obligatorios. Las propiedades son comunes a todos los eventos de cualquier anunciante. El objeto de datos contiene propiedades específicas de cada anunciante. Para los temas de sistema, estas propiedades son específicas del proveedor de recursos, como Azure Storage o Azure Event Hubs.

Los eventos se envían a Azure Event Grid en una matriz que puede contener varios objetos de evento. Si hay un solo evento, la matriz tiene una longitud de 1. La matriz puede tener un tamaño total de hasta 1 MB. Cada evento de la matriz tiene 64 KB como máximo.

Puede encontrar el esquema JSON para el evento de Event Grid y cada carga de datos del publicador de Azure en el [almacén de esquemas de eventos](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Esquema del evento

En el ejemplo siguiente se muestran las propiedades que todos los publicadores de eventos usan:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Por ejemplo, el esquema publicado para un evento de Azure Blob Storage es:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Propiedades de evento

Todos los eventos contienen los mismos datos de nivel superior siguientes:

| Propiedad | type | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| topic | string | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| subject | string | Ruta al asunto del evento definida por el anunciante. |
| eventType | string | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | string | La hora de generación del evento en función de la hora UTC del proveedor. |
| id | string | Identificador único para el evento |
| data | objeto | Los datos del evento específicos del proveedor de recursos. |
| dataVersion | string | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | string | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

Para aprender acerca de las propiedades del objeto de datos, vea el origen del evento:

* [Suscripciones de Azure (operaciones de administración)](event-schema-subscriptions.md)
* [Blob storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [Service Bus](event-schema-service-bus.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Grupos de recursos (operaciones de administración)](event-schema-resource-groups.md)

Para temas personalizados, el publicador de eventos determina el objeto de datos. Los datos de nivel superior deben contener los mismos campos que los eventos estándar definidos por los recursos. Al publicar eventos de temas personalizados, debe considerar modelar el asunto de los eventos como ayuda para el enrutamiento y el filtrado.

## <a name="next-steps"></a>pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).
