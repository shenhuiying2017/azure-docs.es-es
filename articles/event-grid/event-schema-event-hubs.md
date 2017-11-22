---
title: Esquema de eventos de Event Hubs de Azure Event Grid
description: Describe las propiedades que se proporcionan para los eventos de Event Hubs con Azure Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: tomfitz
ms.openlocfilehash: 80959ee589a1cfcf317a98c3bafd7f92c796fc2d
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Esquema de eventos de Azure Event Grid para Event Hubs

En este artículo se proporcionan las propiedades y los esquemas de los eventos de Event Hubs. Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md).

### <a name="available-event-types"></a>Tipos de eventos disponibles

Event Hubs emite el tipo de evento **Microsoft.EventHub.CaptureFileCreated** cuando se crea un archivo de captura.

## <a name="example-event"></a>Evento de ejemplo

Este evento de ejemplo muestra el esquema de un evento de Event Hubs que se genera cuando la característica de captura almacena un archivo: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="event-properties"></a>Propiedades de evento

Un evento tiene los siguientes datos de nivel superior:

| Propiedad | Escriba | Descripción |
| -------- | ---- | ----------- |
| topic | cadena | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. |
| subject | cadena | Ruta al asunto del evento definida por el anunciante. |
| eventType | cadena | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | cadena | La hora de generación del evento en función de la hora UTC del proveedor. |
| id | cadena | Identificador único para el evento |
| data | objeto | Datos del evento de Event Hubs. |

El objeto data tiene las siguientes propiedades:

| Propiedad | Escriba | Descripción |
| -------- | ---- | ----------- |
| fileUrl | cadena | Ruta de acceso al archivo de captura. |
| fileType | cadena | Tipo del archivo de captura. |
| partitionId | cadena | Identificador de partición. |
| sizeInBytes | integer | Tamaño del archivo. |
| eventCount | integer | Número de eventos en el archivo. |
| firstSequenceNumber | integer | Menor número de secuencia de la cola. |
| lastSequenceNumber | integer | Último número de secuencia de la cola. |
| firstEnqueueTime | cadena | Primera vez desde la cola. |
| lastEnqueueTime | cadena | Última vez desde la cola. |

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).
* Para obtener información sobre el control de los eventos de Event Hubs, vea [Transmitir macrodatos a un almacenamiento de datos](event-grid-event-hubs-integration.md).