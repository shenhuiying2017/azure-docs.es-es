---
title: Esquema de eventos de Event Hubs de Azure Event Grid
description: Describe las propiedades que se proporcionan para los eventos de Event Hubs con Azure Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9fdc8816d8db88d4f1fd7b6ce722b7d2763eeaeb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
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
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>Propiedades de evento

Un evento tiene los siguientes datos de nivel superior:

| Propiedad | type | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| topic | cadena | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| subject | cadena | Ruta al asunto del evento definida por el anunciante. |
| eventType | cadena | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | cadena | La hora de generación del evento en función de la hora UTC del proveedor. |
| id | cadena | Identificador único para el evento |
| data | objeto | Datos del evento de Event Hubs. |
| dataVersion | cadena | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | cadena | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

El objeto data tiene las siguientes propiedades:

| Propiedad | type | DESCRIPCIÓN |
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

## <a name="next-steps"></a>pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).
* Para obtener información sobre el control de los eventos de Event Hubs, vea [Transmitir macrodatos a un almacenamiento de datos](event-grid-event-hubs-integration.md).