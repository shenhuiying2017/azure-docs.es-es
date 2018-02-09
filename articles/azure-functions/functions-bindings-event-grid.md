---
title: Desencadenador de Event Grid para Azure Functions
description: "Comprenda cómo se pueden controlar eventos de Event Grid en Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: e1d623c831a912598db72ccd0242cf827c88ee6c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-trigger-for-azure-functions"></a>Desencadenador de Event Grid para Azure Functions

En este artículo se explica cómo controlar eventos de [Event Grid](../event-grid/overview.md) en Azure Functions.

Event Grid es un servicio de Azure que envía solicitudes HTTP para notificarle acerca de eventos que ocurre en los *publicadores*. Un publicador es el servicio o recurso que origina el evento. Por ejemplo, una cuenta de Azure Blob Storage es un publicador y una carga o eliminación de blobs es un evento. Algunos [servicios de Azure tienen compatibilidad integrada para publicar eventos en Event Grid](../event-grid/overview.md#event-publishers). 

Los *controladores* de eventos reciben y procesan eventos. Azure Functions es uno de los [servicios de Azure con compatibilidad integrada para controlar eventos de Event Grid](../event-grid/overview.md#event-handlers). En este artículo, aprenderá a usar un desencadenador de Event Grid para invocar una función cuando se recibe un evento de Event Grid.

Si lo prefiere, puede usar un desencadenador HTTP para controlar los eventos de Event Grid; consulte [Uso de un desencadenador HTTP como un desencadenador de Event Grid](#use-an-http-trigger-as-an-event-grid-trigger) más adelante en este artículo.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Ejemplo

Vea el ejemplo específico del lenguaje de un desencadenador de Event Grid:

* [C#](#c-example)
* [Script de C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

Para ver un ejemplo de un desencadenador HTTP, consulte [Cómo utilizar un desencadenador HTTP](#use-an-http-trigger-as-an-event-grid-trigger) más adelante en este artículo.

### <a name="c-example"></a>Ejemplo de C#

El ejemplo siguiente muestra una [función de C#](functions-dotnet-class-library.md) que registra algunos de los campos que son comunes a todos los eventos y a todos los datos específicos del evento.

```cs
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```

El atributo `EventGridTrigger` se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid).

### <a name="c-script-example"></a>Ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función registra algunos de los campos que son comunes a todos los eventos y a todos los datos específicos del evento.

Estos son los datos de enlace del archivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Este es el código de script de C#:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.Azure.WebJobs.Extensions.EventGrid"
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```

### <a name="javascript-example"></a>Ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función registra algunos de los campos que son comunes a todos los eventos y a todos los datos específicos del evento.

Estos son los datos de enlace del archivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Este es el código de JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>Atributos

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid).

A continuación, se muestra un atributo `EventGridTrigger` en una signatura de método:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] EventGridEvent eventGridEvent, TraceWriter log)
{
    ...
}
 ```

Para obtener un ejemplo completo, consulte [Ejemplo de C#](#c-example).

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json*. No hay parámetros de constructor ni propiedades que establecer en el atributo `EventGridTrigger`.

|Propiedad de function.json |DESCRIPCIÓN|
|---------|---------|----------------------|
| **type** | Requerida: se debe establecer en `eventGridTrigger`. |
| **dirección** | Requerida: se debe establecer en `in`. |
| **name** | Requerido: el nombre de la variable que se utiliza en el código de función para el parámetro que recibe los datos del evento. |

## <a name="usage"></a>Uso

Para las funciones C# y F #, declare el tipo de entrada del desencadenador para que sea `EventGridEvent` o un tipo personalizado. Para un tipo personalizado, el entorno en tiempo de ejecución de Functions intenta analizar el código JSON del evento para establecer las propiedades del objeto.

Para las funciones de JavaScript, el parámetro denominado por la propiedad `name` de *function.json* tiene una referencia al objeto de evento.

## <a name="event-schema"></a>Esquema del evento

Los datos para un evento de Event Grid se reciben como un objeto JSON en el cuerpo de una solicitud HTTP. El código JSON es similar al del siguiente ejemplo:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

El ejemplo que se muestra es una matriz de un elemento. Event Grid siempre envía una matriz y puede enviar más de un evento en la matriz. El entorno en tiempo de ejecución invoca la función una vez para cada elemento de matriz.

Las propiedades de nivel superior en los datos JSON del evento son las mismas entre todos los tipos de eventos, mientras que el contenido de la propiedad `data` es específico para cada tipo de evento. El ejemplo mostrado es para un evento de almacenamiento de blobs.

Para obtener una explicación de las propiedades comunes y específicas de eventos, consulte [Propiedades de evento](../event-grid/event-schema.md#event-properties) en la documentación de Event Grid.

El tipo `EventGridEvent` define solo las propiedades de nivel superior; la propiedad `Data` es un `JObject`. 

## <a name="create-a-subscription"></a>una suscripción

Para empezar a recibir solicitudes HTTP de Event Grid, cree una suscripción a Event Grid que especifique la dirección URL de punto de conexión que invoca la función.

### <a name="azure-portal"></a>Azure Portal

Para las funciones que desarrolle en Azure Portal con el desencadenador de Event Grid, seleccione **Agregar suscripción a Event Grid**.

![Creación de una suscripción en el portal](media/functions-bindings-event-grid/portal-sub-create.png)

Al seleccionar este vínculo, el portal abre la página **Crear suscripción de eventos** con la dirección URL del punto de conexión preinstalada.

![Dirección URL del punto de conexión automáticamente rellenada](media/functions-bindings-event-grid/endpoint-url.png)

Para obtener más información sobre cómo crear suscripciones mediante el Azure Portal, consulte [Creación de eventos personalizados: Azure Portal](../event-grid/custom-event-quickstart-portal.md) en la documentación de Event Grid.

### <a name="azure-cli"></a>Azure CLI

Para crear una suscripción mediante el uso de la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), utilice el comando [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create).

El comando requiere la dirección URL del punto de conexión que invoca la función. En el siguiente ejemplo se muestra el patrón de la dirección URL:

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

La clave de sistema es una clave de autorización que debe incluirse en la dirección URL del punto de conexión para desencadenador de Event Grid. En la siguiente sección se explica cómo obtener la clave del sistema.

En este ejemplo se suscribe a una cuenta de almacenamiento de blobs (con un marcador de posición para la clave del sistema):

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

Para más información sobre cómo crear una suscripción, consulte [la guía de inicio rápido sobre el almacenamiento de blobs](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) o cualquiera de las demás guías de inicio rápido de Event Grid.

### <a name="get-the-system-key"></a>Obtención de la clave del sistema

Puede obtener la clave del sistema mediante la siguiente API (HTTP GET):

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

Es una API de administración, por tanto requiere la [clave de administración](functions-bindings-http-webhook.md#authorization-keys). No confunda la clave del sistema (para invocar una función de desencadenador de Event Grid) con la clave de administración (para realizar tareas administrativas en la aplicación de función). Cuando se suscriba a un tema de Event Grid, asegúrese de usar la clave del sistema.

Este es un ejemplo de la respuesta que proporciona la clave del sistema:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Para más información, consulte [Claves de autorización](functions-bindings-http-webhook.md#authorization-keys) en el artículo de referencia del desencadenador HTTP. 

También puede enviar un HTTP PUT para especificar el mismo valor de la clave.

## <a name="local-testing-with-requestbin"></a>Pruebas locales con RequestBin

Para probar localmente un desencadenador de Event Grid, debe enviar las solicitudes HTTP de Event Grid desde su origen en la nube a la máquina local. Una manera de hacerlo es mediante la captura de solicitudes en línea y de reenviarlas manualmente a la máquina local:

2. [Cree un punto de conexión de RequestBin](#create-a-RequestBin-endpoint).
3. [Cree una suscripción a Event Grid](#create-an-event-grid-subscription) que envíe eventos al punto de conexión de RequestBin.
4. [Genere una solicitud](#generate-a-request) y copie el cuerpo de la solicitud desde el sitio de RequestBin.
5. [Envíe manualmente la solicitud](#manually-post-the-request) a la dirección URL de localhost de la función del desencadenador de Event Grid.

Cuando haya finalizado las pruebas, puede utilizar la misma suscripción para producción actualizando el punto de conexión. Utilice el comando [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) de la CLI de Azure.

### <a name="create-a-requestbin-endpoint"></a>Creación de un punto de conexión de RequestBin

RequestBin es una herramienta de código abierto que acepta solicitudes HTTP y muestra el cuerpo de la solicitud. La dirección URL http://requestb.in recibe un tratamiento especial de Azure Event Grid. Para facilitar las pruebas, Event Grid envía eventos a la dirección URL de RequestBin sin requerir una respuesta correcta a las solicitudes de validación de suscripción. Otras dos herramientas de pruebas reciben el mismo tratamiento: http://webhookinbox.com y http://hookbin.com.

RequestBin no está pensado para el uso de alto rendimiento. Si inserta más de un evento a la vez, puede que no vea todos los eventos en la herramienta.

Cree un punto de conexión.

![Creación de un punto de conexión de RequestBin](media/functions-bindings-event-grid/create-requestbin.png)

Copie la dirección URL del punto de conexión.

![Copia del punto de conexión de RequestBin](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Creación de una suscripción de Event Grid

Cree una suscripción a Event Grid del tipo que desee probar y asígnele el punto de conexión de RequestBin. Para más información sobre cómo crear una suscripción, consulte [Creación de una suscripción](#create-a-subscription) anteriormente en este artículo.

### <a name="generate-a-request"></a>Generación de una solicitud

Desencadene un evento que generará tráfico HTTP a su punto de conexión de RequestBin.  Por ejemplo, si crea una suscripción de almacenamiento de blobs, cargue o elimine un blob. Cuando aparece una solicitud en la página de RequestBin, copie el cuerpo de la solicitud.

La solicitud de validación de la suscripción se recibirá primero; ignore cualquier solicitud de validación y copie la solicitud del evento.

![Copia del cuerpo de la solicitud de RequestBin](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>Envío manual de la solicitud

Ejecute la función de Event Grid localmente.

Use una herramienta como [Postman](https://www.getpostman.com/) o [curl](https://curl.haxx.se/docs/httpscripting.html) para crear una solicitud HTTP POST:

* Establezca un encabezado `Content-Type: application/json`.
* Establezca un encabezado `aeg-event-type: Notification`.
* Pegue los datos de RequestBin en el cuerpo de la solicitud. 
* Envíe la dirección URL de la función del desencadenador de Event Grid con el patrón siguiente:

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={methodname}
``` 

El parámetro `functionName` debe ser el nombre del método, no el nombre especificado en el atributo `FunctionName`. Por esta razón, si tiene varias funciones en un proyecto, necesitan tener nombres de método únicos (no todos se pueden llamar `Run`) para desencadenadores de Event Grid de prueba local.

Las capturas de pantalla siguientes muestran los encabezados y el cuerpo de la solicitud en Postman:

![Encabezados en Postman](media/functions-bindings-event-grid/postman2.png)

![Cuerpo de la solicitud en Postman](media/functions-bindings-event-grid/postman.png)

La función de desencadenador de Event Grid ejecuta y muestra registros similares a los del ejemplo siguiente:

![Registros de la función de desencadenador de Event Grid de ejemplo](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Pruebas locales con ngrok

Otra forma de probar un desencadenador de Event Grid localmente consiste en automatizar la conexión HTTP entre Internet y su equipo de desarrollo. Puede hacerlo con una herramienta de código abierto denominada [ngrok](https://ngrok.com/):

3. [Cree un punto de conexión de ngrok](#create-an-ngrok-endpoint).
4. [Ejecute la función de desencadenador de Event Grid](#run-the-event-grid-trigger-function).
5. [Cree una suscripción a Event Grid](#create-a-subscription) que envíe eventos al punto de conexión de ngrok.
6. [Desencadene un evento](#trigger-an-event).

Cuando haya finalizado las pruebas, puede utilizar la misma suscripción para producción actualizando el punto de conexión. Utilice el comando [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) de la CLI de Azure.

### <a name="create-an-ngrok-endpoint"></a>Creación de punto de conexión de ngrok

Descargue *ngrok.exe* en [ngrok](https://ngrok.com/) y ejecútelo con el siguiente comando:

```
ngrok http -host-header=localhost 7071
```

Es necesario el parámetro -host-header porque el entorno en tiempo de ejecución de las funciones espera solicitudes de localhost cuando se ejecuta en localhost. 7071 es el número de puerto predeterminado cuando el entorno de tiempo de ejecución se ejecuta localmente.

El comando crea una salida similar a la siguiente:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Deberá usar la dirección URL https://{subdominio}.ngrok.io para su suscripción de Event Grid.

### <a name="run-the-event-grid-trigger-function"></a>Ejecución de la función de desencadenador de Event Grid

La dirección URL de ngrok no recibe un tratamiento especial por parte de Event Grid, por lo que su función debe ejecutarse localmente cuando se crea la suscripción. Si no es así, no se envía la respuesta de validación y se produce un error al crear la suscripción.

### <a name="create-a-subscription"></a>una suscripción

Cree una suscripción a Event Grid del tipo que desee probar y asígnele el punto de conexión de ngrok mediante el patrón siguiente:

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={methodname}
``` 

El parámetro `functionName` debe ser el nombre del método, no el nombre especificado en el atributo `FunctionName`. Por esta razón, si tiene varias funciones en un proyecto, necesitan tener nombres de método únicos (no todos se pueden llamar `Run`) para desencadenadores de Event Grid de prueba local.

A continuación se facilita un ejemplo mediante el uso de la CLI de Azure:

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Para más información sobre cómo crear una suscripción, consulte [Creación de una suscripción](#create-a-subscription) anteriormente en este artículo.

### <a name="trigger-an-event"></a>Desencadenar un evento

Desencadene un evento que generará tráfico HTTP a su punto de conexión de ngrok.  Por ejemplo, si crea una suscripción de almacenamiento de blobs, cargue o elimine un blob.

La función de desencadenador de Event Grid ejecuta y muestra registros similares a los del ejemplo siguiente:

![Registros de la función de desencadenador de Event Grid de ejemplo](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Uso de un desencadenador HTTP como un desencadenador de Event Grid

Los eventos de Event Grid se reciben como solicitudes HTTP, por lo que puede controlar los eventos mediante un desencadenador HTTP en lugar de un desencadenador de Event Grid. Una posible razón para hacerlo es tener más control sobre la dirección URL del punto de conexión que invoca la función. 

Si se usa un desencadenador HTTP, tiene que escribir código sobre lo que el desencadenador de Event Grid hace automáticamente:

* Envía una respuesta de validación a una [solicitud de validación de suscripción](../event-grid/security-authentication.md#webhook-event-delivery).
* Invoca la función una vez por elemento de la matriz de eventos contenida en el cuerpo de la solicitud.

El siguiente ejemplo de código C# para un desencadenador HTTP simula el comportamiento del desencadenador de Event Grid:

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

El siguiente ejemplo de código JavaScript para un desencadenador HTTP simula el comportamiento del desencadenador de Event Grid:

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: messages[0].data.validationCode }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

El código de control de eventos pasa al bucle a través de la matriz `messages`.

Para más información acerca de la dirección URL que se debe utilizar para invocar la función localmente o cuando se ejecuta en Azure, consulte la [documentación de referencia de enlaces del desencadenador HTTP](functions-bindings-http-webhook.md). 

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Más información sobre Event Grid](../event-grid/overview.md)
