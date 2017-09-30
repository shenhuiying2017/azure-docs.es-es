---
title: Esquema de eventos de Azure Event Grid
description: Describe las propiedades que se proporcionan para los eventos con Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: a61357b6ba75566e0ad4d3300cc602333ece0563
ms.contentlocale: es-es
ms.lasthandoff: 09/20/2017

---

# <a name="azure-event-grid-event-schema"></a>Esquema de eventos de Azure Event Grid

En este artículo se proporcionan las propiedades y los esquemas de los eventos. Los eventos constan de un conjunto de cinco propiedades de cadena y un objeto de datos obligatorios. Las propiedades son comunes a todos los eventos de cualquier anunciante. El objeto de datos contiene propiedades específicas de cada anunciante. Para los temas de sistema, estas propiedades son específicas del proveedor de recursos, como Azure Storage o Azure Event Hubs.

Los eventos se envían a Azure Event Grid en una matriz que puede contener varios objetos de evento. Si hay un solo evento, la matriz tiene una longitud de 1. La matriz puede tener un tamaño total de hasta 1 MB. Cada evento de la matriz tiene 64 KB como máximo.
 
## <a name="event-properties"></a>Propiedades de evento

Todos los eventos contendrán los siguientes datos de nivel superior iguales:

| Propiedad | Escriba | Descripción |
| -------- | ---- | ----------- |
| topic | cadena | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. |
| subject | cadena | Ruta al asunto del evento definida por el anunciante. |
| eventType | cadena | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | cadena | La hora de generación del evento en función de la hora UTC del proveedor. |
| id | cadena | Identificador único para el evento |
| data | objeto | Los datos del evento específicos del proveedor de recursos. |

## <a name="available-event-sources"></a>Orígenes de eventos disponibles

Los siguientes orígenes de eventos publican eventos para el consumo a través de Event Grid:

* Grupos de recursos (operaciones de administración)
* Suscripciones de Azure (operaciones de administración)
* Centros de eventos
* Temas personalizados

## <a name="azure-subscriptions"></a>Suscripciones de Azure

Las suscripciones de Azure ahora pueden emitir eventos de administración de Azure Resource Manager, como cuando se crea una máquina virtual o se elimina una cuenta de almacenamiento.

### <a name="available-event-types"></a>Tipos de eventos disponibles

- **Microsoft.Resources.ResourceWriteSuccess**: se genera cuando se realiza una operación de creación o actualización de recursos correctamente.  
- **Microsoft.Resources.ResourceWriteFailure**: se genera cuando se produce un error en una operación de creación o actualización de recursos.  
- **Microsoft.Resources.ResourceWriteCancel**: se genera cuando se cancela una operación de creación o actualización de recursos.  
- **Microsoft.Resources.ResourceDeleteSuccess**: se genera cuando se elimina un recurso correctamente.  
- **Microsoft.Resources.ResourceDeleteFailure**: se genera cuando se produce un error al eliminar una operación de creación o actualización de recursos.  
- **Microsoft.Resources.ResourceDeleteCancel**: se genera cuando se cancela una operación de eliminación de recursos. Esto sucede cuando se cancela la implementación de una plantilla.

### <a name="example-event-schema"></a>Esquema de evento de ejemplo

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
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="resource-groups"></a>Grupos de recursos

Los grupos de recursos ahora pueden emitir eventos de administración de Azure Resource Manager, como cuando se crea una máquina virtual o se elimina una cuenta de almacenamiento.

### <a name="available-event-types"></a>Tipos de eventos disponibles

- **Microsoft.Resources.ResourceWriteSuccess**: se genera cuando se realiza una operación de creación o actualización de recursos correctamente.  
- **Microsoft.Resources.ResourceWriteFailure**: se genera cuando se produce un error en una operación de creación o actualización de recursos.  
- **Microsoft.Resources.ResourceWriteCancel**: se genera cuando se cancela una operación de creación o actualización de recursos.  
- **Microsoft.Resources.ResourceDeleteSuccess**: se genera cuando se elimina un recurso correctamente.  
- **Microsoft.Resources.ResourceDeleteFailure**: se genera cuando se produce un error al eliminar una operación de creación o actualización de recursos.  
- **Microsoft.Resources.ResourceDeleteCancel**: se genera cuando se cancela una operación de eliminación de recursos. Esto sucede cuando se cancela la implementación de una plantilla.

### <a name="example-event"></a>Evento de ejemplo

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="event-hubs"></a>Event Hubs

A día de hoy, los eventos de Event Hubs solo se generan cuando se envía automáticamente un archivo al almacenamiento mediante la característica Captura.

### <a name="available-event-types"></a>Tipos de eventos disponibles

- **Microsoft.EventHub.CaptureFileCreated**: se genera cuando se crea un archivo de captura.

### <a name="example-event"></a>Evento de ejemplo

Este evento de ejemplo muestra el esquema de un evento de Event Hubs que se genera cuando la característica Captura almacena un archivo: 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```


## <a name="azure-blob-storage"></a>Azure Blob Storage

>[!IMPORTANT]
>Para usar eventos de Blob Storage, debe estar registrado para la versión preliminar de eventos de Blob Storage. Para más información acerca del programa de la versión preliminar, consulte los [eventos de Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview#join-the-preview).  


### <a name="available-event-types"></a>Tipos de eventos disponibles

- **Microsoft.Storage.BlobCreated**: se genera cuando se crea un blob.
- **Microsoft.Storage.BlobDeleted**: se genera cuando se elimina un blob.

### <a name="example-event"></a>Evento de ejemplo

Este evento de ejemplo muestra el esquema de un evento de almacenamiento que se genera cuando se crea un blob: 

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
    }
  }
]
```




## <a name="custom-topics"></a>Temas personalizados

La carga de datos de los eventos personalizados la define el usuario y puede ser cualquier objeto con formato JSON correcto. Los datos de nivel superior deben contener los mismos campos que los eventos estándar definidos por los recursos. Al publicar eventos de temas personalizados, debe considerar modelar el asunto de los eventos como ayuda para el enrutamiento y el filtrado.

### <a name="example-event"></a>Evento de ejemplo

En el ejemplo siguiente se muestra un evento de tema personalizado:
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte el artículo de [introducción a Event Grid](overview.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).

