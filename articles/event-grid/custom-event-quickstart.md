---
title: Eventos personalizados para Azure Event Grid con la CLI | Microsoft Docs
description: Use Azure Event Grid y la CLI de Azure para publicar un tema y suscribirse a ese evento.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: d68df064614c262bd9755be0688841fdb64af762
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301808"
---
# <a name="create-and-route-custom-events-with-azure-cli-and-event-grid"></a>Creación y enrutamiento de eventos personalizados con la CLI de Azure y Event Grid

Azure Event Grid es un servicio de eventos para la nube. En este artículo, se usa la CLI de Azure para crear un tema personalizado, suscribirse al tema y desencadenar el evento para ver el resultado. Por lo general, los eventos se envían a un punto de conexión que responde al evento, como un webhook o Azure Function. Sin embargo, para simplificar las cosas, en este artículo los eventos se envían a una dirección URL que simplemente recopila los mensajes. Ha creado esta dirección URL mediante una herramienta de terceros de [Hookbin](https://hookbin.com/).

>[!NOTE]
>**Hookbin** no está pensado para el uso de alto rendimiento. El uso de esta herramienta es meramente ilustrativo. Si inserta más de un evento a la vez, puede que no vea todos los eventos en la herramienta.

Cuando haya terminado, verá que los datos del evento se han enviado a un punto de conexión.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI de Azure localmente, para los fines de este artículo, es preciso que ejecute la versión más reciente (2.0.24 o posterior). Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

Si no usa Cloud Shell, primero debe iniciar sesión con `az login`.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Los temas de Event Grid son recursos de Azure y se deben colocar en un grupo de recursos de Azure. El grupo de recursos de Azure es una colección lógica en la que se implementan y administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#az_group_create). 

En el ejemplo siguiente, se crea un grupo de recursos denominado *gridResourceGroup* en la ubicación *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Creación de un tema personalizado

Un tema de cuadrícula de eventos proporciona un punto de conexión definido por el usuario en el que se registran los eventos. En el ejemplo siguiente se crea el tema personalizado en el grupo de recursos. Reemplace `<topic_name>` por un nombre único para el tema. El nombre del tema debe ser único porque se representa mediante una entrada DNS.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>Creación de un punto de conexión de mensaje

Antes de suscribirse al tema, vamos a crear el punto de conexión para el mensaje de evento. En lugar de escribir código para responder al evento, vamos a crear un punto de conexión que recopile los mensajes para que pueda verlos. Hookbin es una herramienta de terceros que permite crear un punto de conexión y ver las solicitudes que se le envían. Vaya a [Hookbin](https://hookbin.com/) y haga clic en **Create New Endpoint** (Crear nuevo punto de conexión).  Copie la dirección URL de la ubicación, la necesitará para suscribirse al tema.

## <a name="subscribe-to-a-topic"></a>Suscripción a un tema

Suscríbase a un tema para indicar a Event Grid los eventos cuyo seguimiento desea realizar. En el ejemplo siguiente se suscribirá al tema que ha creado y pasará la dirección URL de Hookbin como punto de conexión en la notificación de eventos. Reemplace `<event_subscription_name>` por un nombre único para la suscripción y `<endpoint_URL>` por el valor de la sección anterior. Al especificar un punto de conexión cuando se suscribe, Event Grid controla el enrutamiento de los eventos a ese punto de conexión. En `<topic_name>`, use el valor que creó anteriormente. 

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="send-an-event-to-your-topic"></a>Envío de un evento al tema

Vamos a desencadenar un evento para ver cómo Event Grid distribuye el mensaje al punto de conexión. En primer lugar, vamos a obtener la dirección URL y la clave del tema personalizado. De nuevo, use el nombre de su tema en `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Para simplificar este artículo, va a utilizar datos de evento de ejemplo para enviar al tema. Normalmente, una aplicación o servicio de Azure enviaría los datos del evento. En el ejemplo siguiente se obtienen los datos del evento:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Para ver el evento completo, use `echo "$body"`. El elemento `data` del archivo JSON es la carga del evento. En este campo, puede usar cualquier archivo JSON bien formado. También puede usar el campo de asunto para realizar enrutamiento y filtrado avanzados.

CURL es una utilidad que envía solicitudes HTTP. En este artículo, use CURL para enviar el evento al tema. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Ha desencadenado el evento y Event Grid ha enviado el mensaje al punto de conexión que configuró al realizar la suscripción. Vaya a la dirección URL del punto de conexión que creó anteriormente. O bien, haga clic en Actualizar en el explorador abierto. Verá el evento que se acaba de enviar. 

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
Si piensa seguir trabajando con este evento, no limpie los recursos creados en este artículo. En caso contrario, use el siguiente comando para eliminar los recursos creados en este artículo.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo crear suscripciones a temas y eventos, aprenda más sobre cómo Event Grid puede ayudarle:

- [About Event Grid](overview.md) (Acerca de Event Grid)
- [Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps)
- [Transmisión de macrodatos a un almacén de datos](event-grid-event-hubs-integration.md)
