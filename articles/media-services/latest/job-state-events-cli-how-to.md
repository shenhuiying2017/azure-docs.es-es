---
title: Enrutamiento de eventos de Azure Media Services a un punto de conexión web personalizado | Microsoft Docs
description: Use Azure Event Grid para suscribirse al evento de cambio de estado del trabajo de Media Services.
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
ms.openlocfilehash: 2780438e715b6f6cb04d820c02d09f14e14b480f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Enrutamiento de eventos de Azure Media Services a un punto de conexión web personalizado

Azure Event Grid es un servicio de eventos para la nube. En este artículo, se usa la CLI de Azure para suscribirse a los eventos de cambio de estado del trabajo de Azure Media Services y desencadenar el evento para ver el resultado. 

Por lo general, los eventos se envían a un punto de conexión que responde ante ellos, como un webhook o Azure Function. En este tutorial se muestra cómo crear y establecer un webhook.

Al completar los pasos descritos en este artículo, verá que los datos del evento se han enviado a un punto de conexión.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](http://portal.azure.com) e inicie **CloudShell** para ejecutar comandos CLI, como se muestra en los siguientes pasos.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión que tiene. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Asegúrese de recordar los valores que usó para el nombre de la cuenta de Media Services, el nombre del almacenamiento y el nombre del recurso.

## <a name="enable-event-grid-resource-provider"></a>Habilitación del proveedor de recursos de Event Grid

Lo primero que debe hacer es asegurarse de tener habilitado el proveedor de recursos de Event Grid en la suscripción. 

En **Azure** Portal, haga lo siguiente:

1. Vaya a Suscripciones.
2. Seleccione su suscripción.
3. En Configuración, seleccione Proveedores de recursos.
4. Busque "EventGrid".
5. Asegúrese de que Event Grid esté registrado. Si no es así, presione el botón **Registrar**.  

## <a name="create-a-generic-azure-function-webhook"></a>Creación de un webhook genérico de Azure Functions 

### <a name="create-a-message-endpoint"></a>Creación de un punto de conexión de mensaje

Antes de suscribirse al artículo de Event Grid, cree un punto de conexión que recopile los mensajes para que pueda verlos.

Cree una función desencadenada por un webhook genérico tal como se describe en el artículo sobre el [webhook genérico](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). En este tutorial se usa el código **C#**.

Una vez creado el webhook, copie la dirección URL con un clic en el vínculo *Obtención de la dirección URL de la función* que se encuentra en la parte superior de la ventana de **Azure** Portal. No es necesaria la última parte de la dirección URL (*&clientID=default*).

![Creación de un webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Validación del webhook

Al registrar su propio punto de conexión de webhook con Event Grid, se le envía una solicitud POST con un código de validación simple con el fin de comprobar la propiedad del punto de conexión. La aplicación necesita responder devolviendo el código de validación. Event Grid no entrega eventos a los puntos de conexión de webhook que no hayan superado la validación. Para más información, vea [Event Grid security and authentication](https://docs.microsoft.com/azure/event-grid/security-authentication) (Seguridad y autenticación de Event Grid). En esta sección se definen dos partes que se deben definir para pasar la validación.

#### <a name="update-the-source-code"></a>Actualización del código fuente

Después de crear el webhook, el archivo **run.csx** aparece en el explorador. Reemplace el código predeterminado por el siguiente. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>Actualización del cuerpo de la solicitud de prueba

A la derecha de la ventana de **Azure** Portal hay dos pestañas: **Ver archivos** y **Prueba**. Seleccione la pestaña **Prueba**. En el campo **Cuerpo de la solicitud**, pegue el archivo JSON siguiente. Puede pegarlo tal cual, no es necesario que cambie ningún valor.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

Presione **Guardar y ejecutar** en la parte superior de la ventana.

![Cuerpo de la solicitud](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Registro para la suscripción de Event Grid 

Suscríbase a un artículo para indicar a Event Grid los eventos cuyo seguimiento desea realizar. En el ejemplo siguiente se realiza la suscripción a la cuenta de Media Services que creó y se pasa la dirección URL desde el webhook de Azure Functions que creó como el punto de conexión para la notificación de eventos. 

Reemplace `<event_subscription_name>` por un nombre único para la suscripción de eventos. En `<resource_group_name>` y `<ams_account_name>`, use los valores que creó anteriormente.  Para `<endpoint_URL>`, pegue la dirección URL del punto de conexión. Quite *&clientID=default* de la dirección URL. Al especificar un punto de conexión cuando se suscribe, Event Grid controla el enrutamiento de los eventos a ese punto de conexión. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

El valor del identificador de recurso de la cuenta de Media Services es similar a este:

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>Prueba de los ejemplos

Ejecute un trabajo de codificación. Por ejemplo, como se describe en el inicio rápido [Streaming de archivos de vídeo](stream-files-dotnet-quickstart.md).

Ha desencadenado el evento y Event Grid envió el mensaje al punto de conexión configurado durante la suscripción. Vaya al webhook que creó anteriormente. Haga clic en **Supervisar** y en **Actualizar**. Puede ver los eventos de cambios de estado del trabajo: "Queued", "Scheduled", "Processing", "Finished", "Error", "Canceled", "Canceling".  Para más información, consulte el artículo sobre los [esquemas de eventos de Media Services](media-services-event-schemas.md).

Por ejemplo: 

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![Prueba de los eventos](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si planea seguir trabajando con esta cuenta de almacenamiento y suscripción de eventos, no elimine los recursos creados en este artículo. Si no va a continuar, use el siguiente comando para eliminar los recursos creados en este artículo.

Sustituya `<resource_group_name>` por el nombre del grupo de recursos que ha creado.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Pasos siguientes

[Reacción a eventos](reacting-to-media-services-events.md)
