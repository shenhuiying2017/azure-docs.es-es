---
title: Eventos personalizados para Azure Event Grid con PowerShell | Microsoft Docs
description: Use Azure Event Grid y PowerShell para publicar un tema y suscribirse a ese evento.
services: event-grid
keywords: 
author: tfitzmac
ms.author: tomfitz
ms.date: 01/30/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 24366df54fa4fc32ebbff7c1303183707dea17c6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="create-and-route-custom-events-with-azure-powershell-and-event-grid"></a>Creación y enrutamiento de eventos personalizados con Azure PowerShell y Event Grid

Azure Event Grid es un servicio de eventos para la nube. En este artículo, se usa Azure PowerShell para crear un tema personalizado, suscribirse al tema y desencadenar el evento para ver el resultado. Por lo general, los eventos se envían a un punto de conexión que responde al evento, como un webhook o Azure Function. Sin embargo, para simplificar las cosas, en este artículo los eventos se envían a una dirección URL que simplemente recopila los mensajes. Debe crear esta dirección URL mediante herramientas de terceros desde [RequestBin](https://requestb.in/) o [Hookbin](https://hookbin.com/).

>[!NOTE]
>Tenga en cuenta que **RequestBin** y **Hookbin** no están diseñados para el uso de alto rendimiento. El uso de estas herramientas es meramente ilustrativo. Si inserta más de un evento a la vez, puede que no vea todos los eventos en la herramienta.

Cuando haya terminado, verá que los datos del evento se han enviado a un punto de conexión.

![Datos de evento](./media/custom-event-quickstart-powershell/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Para este artículo es necesario ejecutar la versión más reciente de Azure PowerShell. Si necesita instalarlas o actualizarlas, vea [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Los temas de Event Grid son recursos de Azure y se deben colocar en un grupo de recursos de Azure. El grupo de recursos de Azure es una colección lógica en la que se implementan y administran los recursos de Azure.

Cree un grupo de recursos con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

En el ejemplo siguiente, se crea un grupo de recursos denominado *gridResourceGroup* en la ubicación *westus2*.

```powershell
New-AzureRmResourceGroup -Name gridResourceGroup -Location westus2
```

## <a name="create-a-custom-topic"></a>Creación de un tema personalizado

Un tema proporciona un punto de conexión definido por el usuario en el que se registran los eventos. En el ejemplo siguiente se crea el tema en el grupo de recursos. Reemplace `<topic_name>` por un nombre único para el tema. El nombre del tema debe ser único porque se representa mediante una entrada DNS.

```powershell
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location westus2 -Name <topic_name>
```

## <a name="create-a-message-endpoint"></a>Creación de un punto de conexión de mensaje

Antes de suscribirse al tema, vamos a crear el punto de conexión para el mensaje de evento. En lugar de escribir código para responder al evento, vamos a crear un punto de conexión que recopile los mensajes para que pueda verlos. RequestBin y Hookbin son herramientas de terceros que permiten crear un punto de conexión y ver las solicitudes enviadas a este. Vaya a [RequestBin](https://requestb.in/) y haga clic en **Create a RequestBin** (Crear un elemento de RequestBin), o visite [Hookbin](https://hookbin.com/) y haga clic en **Crear un nuevo punto de conexión**.  Copie la dirección URL de la ubicación, la necesitará para suscribirse al tema.

## <a name="subscribe-to-a-topic"></a>Suscripción a un tema

Suscríbase a un tema para indicar a Event Grid los eventos cuyo seguimiento desea realizar. En el ejemplo siguiente se suscribirá al tema que ha creado y pasará la dirección URL de RequestBin o Hookbin como punto de conexión en la notificación de eventos. Reemplace `<event_subscription_name>` por un nombre único para la suscripción y `<endpoint_URL>` por el valor de la sección anterior. Al especificar un punto de conexión cuando se suscribe, Event Grid controla el enrutamiento de los eventos a ese punto de conexión. En `<topic_name>`, use el valor que creó anteriormente.

```powershell
New-AzureRmEventGridSubscription -EventSubscriptionName <event_subscription_name> -Endpoint <endpoint_URL> -ResourceGroupName gridResourceGroup -TopicName <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>Envío de un evento al tema

Ahora, vamos a desencadenar un evento para ver cómo Event Grid distribuye el mensaje al punto de conexión. En primer lugar, vamos a obtener la dirección URL y la clave del tema. De nuevo, use el nombre de su tema en `<topic_name>`.

```powershell
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>
```

Para simplificar las cosas, en este artículo hemos configurado datos de evento de ejemplo para enviar al tema. Normalmente, una aplicación o servicio de Azure enviaría los datos del evento. El ejemplo siguiente utiliza una tabla hash para construir los datos del evento `htbody` y, a continuación, convertirlos en el objeto de carga JSON `$body` con un formato correcto:

```powershell
$eventID = Get-Random 99999

#Date format should be SortableDateTimePattern (ISO 8601)
$eventDate = Get-Date -Format s

#Construct body using Hashtable
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/motorcycles"
    eventTime= $eventDate   
    data= @{
        make="Ducati"
        model="Monster"
    }
    dataVersion="1.0"
}

#Use ConvertTo-Json to convert event body from Hashtable to JSON Object
#Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
$body = "["+(ConvertTo-Json $htbody)+"]"
```

En `$body` verá el evento completo. El elemento `data` del archivo JSON es la carga del evento. En este campo, puede usar cualquier archivo JSON bien formado. También puede usar el campo de asunto para realizar enrutamiento y filtrado avanzados.

Ahora, envíe el evento al tema.

```powershell
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Ha desencadenado el evento y Event Grid envió el mensaje al punto de conexión configurado durante la suscripción. Vaya a la dirección URL del punto de conexión que creó anteriormente. O bien, haga clic en Actualizar en el explorador abierto. Verá el evento que se acaba de enviar.

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2018-01-25T15:58:13",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si piensa seguir trabajando con este evento, no limpie los recursos creados en este artículo. Si no va a continuar, use el siguiente comando para eliminar los recursos creados en este artículo.

```powershell
Remove-AzureRmResourceGroup -Name gridResourceGroup
```

## <a name="next-steps"></a>pasos siguientes

Ahora que sabe cómo crear suscripciones a temas y eventos, aprenda más sobre cómo Event Grid puede ayudarle:

- [About Event Grid](overview.md) (Acerca de Event Grid)
- [Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps)
- [Transmisión de macrodatos a un almacén de datos](event-grid-event-hubs-integration.md)
