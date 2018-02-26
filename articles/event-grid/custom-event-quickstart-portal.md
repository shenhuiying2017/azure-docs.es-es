---
title: Eventos personalizados para Azure Event Grid con Azure Portal | Microsoft Docs
description: Use Azure Event Grid y PowerShell para publicar un tema y suscribirse a ese evento.
services: event-grid
keywords: 
author: tfitzmac
ms.author: tomfitz
ms.date: 01/30/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: f37d496d43bb24c51d6e1c11b77d9ceba48b7b23
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Creación y enrutamiento de eventos personalizados con Azure Portal y Event Grid

Azure Event Grid es un servicio de eventos para la nube. En este artículo, se usa Azure Portal para crear un tema personalizado, suscribirse al tema y desencadenar el evento para ver el resultado. Por lo general, los eventos se envían a un punto de conexión que responde al evento, como un webhook o Azure Function. Sin embargo, para simplificar las cosas, en este artículo los eventos se envían a una dirección URL que simplemente recopila los mensajes. Debe crear esta dirección URL mediante herramientas de terceros desde [RequestBin](https://requestb.in/) o [Hookbin](https://hookbin.com/).

>[!NOTE]
>Tenga en cuenta que **RequestBin** y **Hookbin** no están diseñados para el uso de alto rendimiento. El uso de estas herramientas es meramente ilustrativo. Si inserta más de un evento a la vez, puede que no vea todos los eventos en la herramienta.

Cuando haya terminado, verá que los datos del evento se han enviado a un punto de conexión.

![Datos de evento](./media/custom-event-quickstart-portal/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Los temas de Event Grid son recursos de Azure y se deben colocar en un grupo de recursos de Azure. El grupo de recursos de Azure es una colección lógica en la que se implementan y administran los recursos de Azure.

1. En el panel de la izquierda, seleccione **Grupos de recursos**. Después, seleccione **Agregar**.

   ![Creación de un grupo de recursos](./media/custom-event-quickstart-portal/create-resource-group.png)

1. Establezca el nombre del grupo de recursos en *gridResourceGroup* y la ubicación, en *westus2*. Seleccione **Crear**.

   ![Incorporación de los valores para el grupo de recursos](./media/custom-event-quickstart-portal/provide-resource-group-values.png)

## <a name="create-a-custom-topic"></a>Creación de un tema personalizado

Un tema proporciona un punto de conexión definido por el usuario en el que se registran los eventos. 

1. Para crear un tema en el grupo de recursos, seleccione **Todos los servicios** y busque *Event Grid*. Seleccione **Temas de Event Grid** entre las opciones disponibles.

   ![Creación del tema de Event Grid](./media/custom-event-quickstart-portal/create-event-grid-topic.png)

1. Seleccione **Agregar**.

   ![Incorporación del tema de Event Grid](./media/custom-event-quickstart-portal/add-topic.png)

1. Proporcione un nombre para el tema. El nombre del tema debe ser único porque se representa mediante una entrada DNS. Seleccione alguna de las [regiones admitidas](overview.md). Seleccione el grupo de recursos que creó anteriormente. Seleccione **Crear**.

   ![Incorporación de los valores para el tema de Event Grid](./media/custom-event-quickstart-portal/provide-topic-values.png)

1. Una vez creado el tema, seleccione **Actualizar** para verlo.

   ![Visualización del tema de Event Grid](./media/custom-event-quickstart-portal/see-topic.png)

## <a name="create-a-message-endpoint"></a>Creación de un punto de conexión de mensaje

Antes de suscribirse al tema, vamos a crear el punto de conexión para el mensaje de evento. En lugar de escribir código para responder al evento, vamos a crear un punto de conexión que recopile los mensajes para que pueda verlos. RequestBin y Hookbin son herramientas de terceros que permiten crear un punto de conexión y ver las solicitudes enviadas a este. Vaya a [RequestBin](https://requestb.in/) y haga clic en **Create a RequestBin** (Crear un elemento de RequestBin), o visite [Hookbin](https://hookbin.com/) y haga clic en **Crear un nuevo punto de conexión**.  Copie la dirección URL de la ubicación, la necesitará para suscribirse al tema.

## <a name="subscribe-to-a-topic"></a>Suscripción a un tema

Suscríbase a un tema para indicar a Event Grid los eventos cuyo seguimiento desea realizar. 

1. Para crear una suscripción de Event Grid, seleccione **Todos los servicios** y busque *Event Grid* de nuevo. Seleccione **Suscripciones de Event Grid** entre las opciones disponibles.

   ![Creación de una suscripción de Event Grid](./media/custom-event-quickstart-portal/create-subscription.png)

1. Seleccione **+ Suscripción de eventos**.

   ![Incorporación de una suscripción de Event Grid](./media/custom-event-quickstart-portal/add-subscription.png)

1. Proporcione un nombre único para la suscripción de eventos. Como tipo de tema, seleccione **Temas de Event Grid**. Como instancia, seleccione el tema personalizado que ha creado. Proporcione la dirección URL de RequestBin o Hookbin como punto de conexión para la notificación de eventos. Cuando termine de proporcionar los valores pertinentes, haga clic en **Crear**.

   ![Incorporación del valor de la suscripción de Event Grid](./media/custom-event-quickstart-portal/provide-subscription-values.png)

Ahora, vamos a desencadenar un evento para ver cómo Event Grid distribuye el mensaje al punto de conexión. Para simplificar las cosas, en este artículo se usa Cloud Shell para enviar los datos de evento de ejemplo al tema. Normalmente, una aplicación o servicio de Azure enviaría los datos del evento.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>Envío de un evento al tema

En primer lugar, vamos a obtener la dirección URL y la clave del tema. Use el nombre de su tema en `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

En el ejemplo siguiente se obtienen los datos del evento de ejemplo:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Si ejecuta `echo "$body"` puede ver el evento completo. El elemento `data` del archivo JSON es la carga del evento. En este campo, puede usar cualquier archivo JSON bien formado. También puede usar el campo de asunto para realizar enrutamiento y filtrado avanzados.

CURL es una utilidad que ejecuta solicitudes HTTP. En este artículo, use CURL para enviar un evento al tema. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Ha desencadenado el evento y Event Grid envió el mensaje al punto de conexión configurado durante la suscripción. Vaya a la dirección URL del punto de conexión que creó anteriormente. O bien, haga clic en Actualizar en el explorador abierto. Verá el evento que se acaba de enviar.

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
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

Si piensa seguir trabajando con este evento, no limpie los recursos creados en este artículo. Si no va a continuar, elimine los recursos creados con este artículo.

Seleccione el grupo de recursos y seleccione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>pasos siguientes

Ahora que sabe cómo crear suscripciones a temas y eventos, aprenda más sobre cómo Event Grid puede ayudarle:

- [About Event Grid](overview.md) (Acerca de Event Grid)
- [Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps)
- [Transmisión de macrodatos a un almacén de datos](event-grid-event-hubs-integration.md)
