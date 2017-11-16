---
title: "Reacción ante eventos de Azure Blob Storage (versión preliminar) | Microsoft Docs"
description: Utilice Azure Event Grid para suscribirse a los eventos de Blob Storage.
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/25/2017
ms.topic: article
ms.service: storage
ms.openlocfilehash: a56e6026ed0c2c873030625fa7a9b35b92faf930
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="reacting-to-blob-storage-events-preview"></a>Reacción ante eventos de Blob Storage (versión preliminar)

Los eventos de Azure Blob Storage permiten a las aplicaciones reaccionar ante la creación y eliminación de blobs mediante modernas arquitecturas sin servidor y sin la necesidad de usar código complejo o servicios de sondeo costosos e ineficaces.  En su lugar, se insertan eventos a través de [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) a los suscriptores como [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), o incluso su propio agente de escucha http personalizado y solo paga por lo que utiliza.

Los escenarios habituales de los eventos de Blob Storage incluyen el procesamiento de imágenes o de vídeo, la indexación de búsqueda o cualquier flujo de trabajo orientado a archivos.  Cargas de archivos asincrónicas son una excelente elección para los eventos.  Cuando se realizan pocos cambios en el escenario, pero se requiere una respuesta inmediata, la arquitectura basada en eventos puede ser especialmente eficaz.

Actualmente Event Grid se encuentra en versión preliminar y está disponible para cuentas ubicadas en el ***Centro occidental de EE. UU.*** o en el ***Oeste de EE. UU. 2***.  En [Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado (versión preliminar)](storage-blob-event-quickstart.md), puede encontrar un ejemplo.

![Modelo de Event Grid](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Cuentas de Blob Storage
Los eventos de Blob Storage están disponibles en las [cuentas de Blob Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) (no en las cuentas de almacenamiento de uso general).  Una cuenta de Blob Storage es una cuenta de almacenamiento especializado para almacenar los datos no estructurados como blobs (objetos) en Azure Storage. Las cuentas de Blob Storage son similares a las cuentas de almacenamiento de uso general y comparten las excelentes características de rendimiento, escalabilidad, disponibilidad y durabilidad que se usan en la actualidad, incluida la coherencia total de la API con blobs en bloques y blobs en anexos. Para las aplicaciones que requieren solo Almacenamiento de blobs en bloque o en anexos, se recomienda utilizar cuentas de Almacenamiento de blobs.

## <a name="available-blob-storage-events"></a>Eventos de Blob Storage disponibles
Event Grid usa las [suscripciones a eventos](../../event-grid/concepts.md#event-subscriptions) para enrutar los mensajes de eventos a los suscriptores.  Las suscripciones a eventos de Blob Storage pueden incluir dos tipos de eventos:  

> |Nombre del evento|Descripción|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Se activa cuando un blob se crea o sustituye mediante las operaciones `PutBlob`, `PutBlockList` o `CopyBlob`|
> |`Microsoft.Storage.BlobDeleted`|Se activa cuando se elimina un blob mediante una operación `DeleteBlob`|

## <a name="event-schema"></a>Esquema de eventos
Los eventos de Blob Storage contienen toda la información necesaria para responder a cualquier cambio que se produzca en los datos.  Puede identificar un evento de Blob Storage porque la propiedad eventType comienza por "Microsoft.Storage".  
Puede encontrar información adicional acerca del uso de las propiedades de los eventos de Event Grid en [Esquema de eventos de Event Grid](../../event-grid/event-schema.md).  

> |Propiedad|Escriba|Descripción|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|cadena|Identificador completo de Azure Resource Manager de la cuenta de almacenamiento que emite el evento.|
> |subject|cadena|La ruta de acceso del recurso relativa al objeto que es el asunto del evento, con el mismo formato de Azure Resource Manager extendido que se usa para describir cuentas de almacenamiento, servicios y los contenedores de Azure RBAC.  Este formato incluye un nombre de blob que conservan las mayúsculas y minúsculas.|
> |eventTime|cadena|Fecha y hora en que se generó el evento, en formato ISO 8601|
> |eventType|cadena|“Microsoft.Storage.BlobCreated” o “Microsoft.Storage.BlobDeleted”|
> |Id|cadena|Identificador único de este evento|
> |data|objeto|Recopilación de datos de eventos específicos de Blob Storage|
> |data.contentType|cadena|El tipo de contenido del blob, como se devolvería en el encabezado Content-Type del blob|
> |data.contentLength|número|El tamaño del blob es un número entero que representa un número de bytes, como se devolverían en el encabezado Content-Length del blob.  Se envía con el evento BlobCreated, pero no con BlobDeleted.|
> |data.url|cadena|La dirección URL del objeto que es el asunto del evento|
> |data.eTag|cadena|El valor etag del objeto cuando se activa este evento.  No está disponible para el evento BlobDeleted.|
> |data.api|cadena|El nombre de la operación de API que desencadenó este evento.  En el caso de los eventos BlobCreated, este valor es "PutBlob", "PutBlockList" o "CopyBlob".  En el caso de los eventos BlobDeleted, este valor es "DeleteBlob".  Estos valores son los mismos nombres de API que se encuentran en los registros de diagnóstico de Azure Storage.  Consulte [Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Operaciones registradas y mensajes de estado).|
> |data.sequencer|cadena|Un valor de cadena opaco que representa la secuencia lógica de eventos para cualquier nombre de blob concreto.  Los usuarios pueden usar una comparación de cadenas estándar para conocer la secuencia relativa de dos eventos que estén en el mismo nombre de blob.|
> |data.requestId|cadena|Identificador de solicitud generado por el servicio para la operación de la API de Storage.  Se puede usar para establecer la correlación con los registros de diagnóstico de Azure Storage que usan el campo "request-id-header" en los registros y se devuelve cuando se inicia la llamada de la API en el encabezado "x-ms-request-id". Consulte [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato de registro de Storage Analytics).|
> |data.clientRequestId|cadena|Identificador de solicitud que proporciona el cliente para la operación de la API de Storage.  Se puede usar para establecer la correlación con los registros de diagnóstico de Azure Storage que usan el campo "client-request-id" en los registros y se puede proporcionar en las solicitudes de los clientes que usan el encabezado "x-ms-client-request-id". Consulte [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato de registro de Storage Analytics).|
> |data.storageDiagnostics|objeto|Datos de diagnóstico que, en ocasiones, incluye el servicio Azure Storage.  Cuando están presentes, los consumidores de eventos deben ignorarlos.|

Este es un ejemplo de un evento BlobCreated:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  }
}]

```

Para más información, consulte el [esquema de eventos de Blob Storage](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtrado de eventos
Las suscripciones de eventos de blobs se pueden filtrar en función del tipo de evento y por el nombre del contenedor y el nombre del blob del objeto que se creó o eliminó.  Los filtros de asunto de Event Grid funcionan según las coincidencias de "comienza por" y "termina en", con el fin de que los eventos cuyo asunto coincida se entreguen al suscriptor.
El asunto de los eventos de Blob Storage utiliza el formato:
```json
/blobServices/default/containers/<containername>/blobs/<blobname>
```
Para que coincida con todos los eventos de una cuenta de almacenamiento, los filtros de asunto se pueden dejar vacíos.

Para que coincida con eventos de blobs creados en un conjunto de contenedores que comparten un prefijo, utilice un filtro `subjectBeginsWith` como:
```json
/blobServices/default/containers/containerprefix
```
Para que coincida con eventos de blobs creados en un contenedor concreto, utilice un filtro `subjectBeginsWith` como:
```json
/blobServices/default/containers/containername/
```
Para que coincida con eventos de blobs creados en un contenedor concreto que comparten un prefijo de nombre de blob, utilice un filtro `subjectBeginsWith` como:
```json
/blobServices/default/containers/containername/blobs/blobprefix
```

Para que coincida con eventos de blobs creados en un contenedor concreto que comparten un sufijo de blob, utilice un filtro `subjectEndsWith` como ".log" o ".jpg"

Para más información, vea [Conceptos de Event Grid](../../event-grid/concepts.md#filters).

## <a name="practices-for-consuming-events"></a>Prácticas para consumir eventos
Las aplicaciones que controlan los eventos de Blob Storage deben seguir algunas prácticas recomendadas:
> [!div class="checklist"]
> * Dado que se pueden configurar varias suscripciones para enrutar eventos al mismo controlador de eventos, es importante no asumir que los eventos provienen de un origen determinado, sino comprobar el tema del mensaje para asegurarse de que proviene de la cuenta de almacenamiento esperable.
> * De igual forma, compruebe que eventType es uno de los que está preparado para procesar y no asuma que todos los eventos que reciba van a ser los tipos que espera.
> * Dado que los mensajes pueden llegar desordenados y con cierto retraso, utilice los campos de etag para saber si la información acerca de los objetos sigue estando actualizada.  Además, utilice los campos del secuenciador para conocer el orden de los eventos en cualquier objeto determinado.
> * Utilice el campo blobType para saber qué tipo de operaciones se permiten en el blob y qué tipos de bibliotecas de cliente se deben usar para acceder al blob.
> * Utilice el campo de dirección URL con los constructores `CloudBlockBlob` y `CloudAppendBlob` para acceder al blob.
> * Ignore los campos que no comprenda.  Esta práctica le ayudará a mantenerse resistente a las nuevas características que puedan agregarse en el futuro.


## <a name="next-steps"></a>Pasos siguientes

Obtenga más información acerca de Event Grid y pruebe los eventos de Blob Storage:

- [About Event Grid](../../event-grid/overview.md) (Acerca de Event Grid)
- [Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado (versión preliminar)](storage-blob-event-quickstart.md)
