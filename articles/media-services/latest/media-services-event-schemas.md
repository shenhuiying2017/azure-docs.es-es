---
title: Esquemas de Azure Event Grid para eventos de Media Services
description: Describe las propiedades que se proporcionan para los eventos de Media Services con Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782664"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Esquemas de Azure Event Grid para eventos de Media Services

En este artículo se proporcionan los esquemas y las propiedades para los eventos de Media Services.

## <a name="media-services-job-state-change-event"></a>Evento de cambio de estado del trabajo de Media Services

En esta sección se proporcionan las propiedades y el esquema para el evento de cambio de estado del trabajo de Media Services.  

### <a name="available-event-types"></a>Tipos de eventos disponibles

Un **trabajo** de Media Services emite los tipos de evento siguientes:

| Tipo de evento | DESCRIPCIÓN |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Se produce cuando cambia un estado del trabajo. |

### <a name="example-event"></a>Evento de ejemplo

En el ejemplo siguiente se muestra el esquema de un evento de estado de trabajo finalizado: 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>Propiedades de evento

Un evento tiene los siguientes datos de nivel superior:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| topic | string | Event Grid proporciona este valor. |
| subject | string | La ruta de acceso al recurso en el recurso de la cuenta de Media Services. |
| eventType | string | Uno de los tipos de eventos registrados para este origen de eventos. Por ejemplo, "Microsoft.Media.JobStateChange". |
| eventTime | string | La hora de generación del evento en función de la hora UTC del proveedor. |
| id | string | Identificador único para el evento |
| data | objeto | Datos del evento de Media Services. |
| dataVersion | string | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | string | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

El objeto data tiene las siguientes propiedades:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| previousState | string | El estado del trabajo antes del evento. |
| state | string | El nuevo estado del trabajo que se notifica en este evento. Por ejemplo, "En cola: el trabajo está a la espera de recursos" o "Programado: el trabajo está listo para empezar".|

Donde el estado del trabajo puede ser uno de estos valores: *Queued* (En cola), *Scheduled* (Programado), *Processing* (En proceso), *Finished* (Finalizado), *Error*, *Canceled* (Cancelado), *Canceling* (Cancelándose).

## <a name="next-steps"></a>Pasos siguientes

[Regístrese para los eventos de cambio de estado del trabajo](job-state-events-cli-how-to.md)
