---
title: "Recepción de eventos de Azure Event Grid en un punto de conexión de HTTP"
description: "Describe cómo validar un punto de conexión HTTP y luego recibir y deserializar eventos procedentes de Azure Event Grid."
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: article
ms.date: 02/16/2018
ms.author: babanisa
ms.openlocfilehash: 179f7c46186762eed2f7f8ac90620ac2fec9caf3
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="receive-events-to-an-http-endpoint"></a>Recepción de eventos en un punto de conexión de HTTP

En este artículo se describe cómo [validar un punto de conexión de HTTP](security-authentication.md#webhook-event-delivery) para recibir eventos de una suscripción a eventos y, a continuación, recibir y deserializar los eventos. En este artículo se utiliza una función de Azure para fines de demostración; sin embargo, se aplican los mismos conceptos con independencia de dónde se hospede la aplicación.

> [!NOTE]
> Se recomienda **encarecidamente** que use un [desencadenador de la cuadrícula de eventos](../azure-functions/functions-bindings-event-grid.md) al desencadenar una función de Azure con Event Grid. Aquí se usa un desencadenador de Webhook genérico con fines ilustrativos.

## <a name="prerequisites"></a>requisitos previos

* Necesitará una aplicación de función con una [función desencadenada por HTTP](../azure-functions/functions-create-generic-webhook-triggered-function.md).

## <a name="add-dependencies"></a>Adición de dependencias

Si está desarrollando en. NET, [agregue una dependencia](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) a la función para el `Microsoft.Azure.EventGrid` [paquete de NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Hay SDK para otros lenguajes disponibles a través de la referencia [SDK de publicación](./sdk-overview.md#publish-sdks). Estos paquetes contienen los modelos para los tipos de evento nativos, como `EventGridEvent`, `StorageBlobCreatedEventData` y `EventHubCaptureFileCreatedEventData`.

Para ello, haga clic en el vínculo "Ver archivos" de la función de Azure (panel más a la derecha en el portal de funciones de Azure) y cree un archivo denominado project.json. Agregue el siguiente contenido al archivo `project.json` y guárdelo:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "1.1.0-preview"
      }
    }
   }
}

```

![Paquete de NuGet agregado](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Validación de punto de conexión

Lo primero que queremos hacer es controlar eventos `Microsoft.EventGrid.SubscriptionValidationEvent`. Cada vez que se crea una nueva suscripción a eventos, Event Grid envía un evento de validación al punto de conexión con `validationCode` en la carga de datos. El punto de conexión debe reproducirlo en el cuerpo de la respuesta [para demostrar que el punto de conexión es válido y le pertenece](security-authentication.md#webhook-event-delivery). Si usa un [desencadenador de la cuadrícula de eventos](../azure-functions/functions-bindings-event-grid.md) en lugar de una función desencadenada por WebHook, la validación del punto de conexión se completa de manera automática.

Utilice el código siguiente para controlar la validación de la suscripción:

```csharp

using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{

    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript

var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};

```

### <a name="test-validation-response"></a>Prueba de respuesta de validación

Pruebe la función de la respuesta de validación pegando el evento de ejemplo en el campo de prueba para la función:

```json

[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]

```

Al hacer clic en Ejecutar, el resultado debería ser 200 OK y `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` en el cuerpo:

![respuesta de validación](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Control de eventos de Blob Storage

Ahora podemos ampliar la función para controlar `Microsoft.Storage.BlobCreated`:

```cs

using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript

var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Prueba del control de evento creado por blob

Pruebe la nueva funcionalidad de la función; para ello, coloque un [evento de Blob Storage](./event-schema-blob-storage.md#example-event) en el campo de prueba y ejecute:

```json

[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Debería ver el resultado de la dirección URL del blob en el registro de la función:

![Registro de salida](./media/receive-events/blob-event-response.png)

También puede probar esta salida en vivo mediante la creación de una cuenta de Blob Storage o una cuenta de almacenamiento de uso general V2 (GPv2), [la adición de una suscripción a eventos](../storage/blobs/storage-blob-event-quickstart.md) y la configuración del punto de conexión en la dirección URL de la función:

![dirección URL de la función](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Control de eventos personalizados

Por último, ampliemos una vez más la función para que también pueda controlar eventos personalizados. Agregamos una comprobación para nuestro propio evento `Contoso.Items.ItemReceived`. El código final debe tener el aspecto siguiente:

```cs

using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

class ContosoItemReceivedEventData
{
    public string id { get; set; }
    public string message { get; set; }
    public string time { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>();
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript

var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};

```

### <a name="test-custom-event-handling"></a>Prueba del control de eventos personalizados

Por último, pruebe que la función ampliada ahora pueda controlar el tipo de evento personalizado:

```json

[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]

```

También puede probar esta funcionalidad en vivo mediante el [envío de un evento personalizado con CURL desde Portal](./custom-event-quickstart-portal.md) o [la publicación en un tema personalizado](./post-to-custom-topic.md) con cualquier servicio o aplicación que pueda ejecutar POST en un punto de conexión, como [Postman](https://www.getpostman.com/). Cree un tema y una suscripción a eventos personalizados con el punto de conexión establecido como dirección URL de la función.

## <a name="next-steps"></a>pasos siguientes

* Explore los [SDK de Event Grid para administración y publicación](./sdk-overview.md).
* Obtenga información acerca de cómo [publicar en un tema personalizado](./post-to-custom-topic.md).
* Pruebe uno de los tutoriales detallados sobre Event Grid y funciones, como [el cambio de tamaño de imágenes cargadas a Blob Storage](resize-images-on-storage-blob-upload-event.md).
