---
title: Enviar eventos personalizados para Azure Event Grid a la conexión híbrida | Microsoft Docs
description: Use Azure Event Grid y la CLI de Azure para publicar un tema y suscribirse a ese evento. Una conexión híbrida se usa para el punto de conexión.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 05/04/2018
ms.topic: article
ms.service: event-grid
ms.openlocfilehash: 42b3e88d4bf411aa8a0d3bb129795f0d8ab98525
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
---
# <a name="route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Enrutar eventos personalizados a Conexiones híbridas de Azure Relay con la CLI de Azure y Event Grid

Azure Event Grid es un servicio de eventos para la nube. La solución Conexiones híbridas de Azure Relay es uno de los controladores de eventos compatibles. Las conexiones híbridas se usan como el controlador de eventos cuando es necesario procesar los eventos de las aplicaciones que no tienen un punto de conexión público. Estas aplicaciones pueden estar dentro de la red empresarial corporativa. En este artículo, se usa la CLI de Azure para crear un tema personalizado, suscribirse al tema y desencadenar el evento para ver el resultado. Los eventos se envían a la conexión híbrida.

## <a name="prerequisites"></a>requisitos previos

En este artículo, se presupone que ya tiene una conexión híbrida y una aplicación de escucha. Para empezar a trabajar con las conexiones híbridas, consulte [Introducción a Conexiones híbridas de Relay: .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) o [Introducción a Conexiones híbridas de Relay: nodo](../service-bus-relay/relay-hybrid-connections-node-get-started.md).

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

## <a name="subscribe-to-a-topic"></a>Suscripción a un tema

Suscríbase a un tema para indicar a Event Grid los eventos cuyo seguimiento desea realizar. En el ejemplo siguiente se suscribirá al tema que creó y pasará el id. de recurso de la conexión híbrida para el punto de conexión. El id. de conexión híbrida tiene el formato siguiente:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

En el siguiente script, el id. de recurso se obtiene del espacio de nombres de Relay. Se genera el id. de la conexión híbrida y se suscribe a un tema de la cuadrícula de eventos. Se establece el tipo de punto de conexión en `hybridconnection` y se utiliza el id. de conexión híbrida para el punto de conexión.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"

az eventgrid event-subscription create \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid
```

## <a name="send-an-event-to-your-topic"></a>Envío de un evento al tema

Vamos a desencadenar un evento para ver cómo Event Grid distribuye el mensaje al punto de conexión. En primer lugar, vamos a obtener la dirección URL y la clave del tema personalizado. De nuevo, use el nombre de su tema en `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Para simplificar este artículo, va a utilizar datos de evento de ejemplo para enviar al tema. Normalmente, una aplicación o servicio de Azure enviaría los datos del evento. CURL es una utilidad que envía solicitudes HTTP. En este artículo, use CURL para enviar el evento al tema.  En el ejemplo siguiente se envían tres eventos al tema de la cuadrícula de eventos:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

La aplicación de agente de escucha debe recibir el mensaje de evento.

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
