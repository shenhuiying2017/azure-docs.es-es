---
title: Asignación de campos personalizados a esquemas de Azure Event Grid
description: Se describe cómo convertir el esquema personalizado en el esquema de Azure Event Grid.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301974"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Asignación de campos personalizados a esquemas de Event Grid

Si los datos del evento no coinciden con el [esquema de Event Grid](event-schema.md), puede usar Event Grid para enrutar el evento a los suscriptores. En este artículo se describe cómo asignar el esquema al de Event Grid.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Esquema del evento original

Supongamos que tiene una aplicación que envía eventos con el siguiente formato:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Aunque dicho formato no coincide con el esquema requerido, Event Grid permite asignar los campos al esquema. También pueden recibir los valores en el esquema original.

## <a name="create-custom-topic-with-mapped-fields"></a>Creación de temas personalizados con campos asignados

Al crear un tema personalizado, especifique cómo asignar campos del evento original al esquema de Event Grid. Hay tres propiedades que se pueden usar para personalizar la asignación:

* El parámetro `--input-schema` especifica el tipo de esquema. Las opciones disponibles son *cloudeventv01schema*, *customeventschema* y *eventgridschema*. El valor predeterminado es eventgridschema. Al crear la asignación personalizada entre el esquema y el esquema de Event Grid, use customeventschema. Si los eventos están en el esquema CloudEvents, use cloudeventv01schema.

* El parámetro `--input-mapping-default-values` especifica los valores predeterminados para los campos en el esquema de Event Grid. Puede definir valores predeterminados para *subject*, *eventtype* y *dataversion*. Normalmente, se usa este parámetro si el esquema personalizado no incluye un campo que se corresponde con alguno de esos tres campos. Por ejemplo, puede especificar en el campo dataversion que la versión de datos siempre se defina en **1.0**.

* El parámetro `--input-mapping-fields` asigna los campos del esquema al esquema de Event Grid. Especifique los valores en pares clave-valor separados por espacios. Para el nombre de clave, usa el nombre del campo de Event Grid. Para el valor, utilice el nombre del campo. Puede usar los nombres de clave para *id*, *topic*, *eventtime*, *subject*, *eventtype* y *dataversion*.

En el ejemplo siguiente se crea un tema personalizado con algunos campos asignados y predeterminados:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Suscripción a temas de Event Grid

Al suscribirse al tema personalizado, especifique el esquema que le gustaría utilizar para recibir los eventos. Use el parámetro `--event-delivery-schema` y establézcalo en *cloudeventv01schema*, *eventgridschema* o *inputeventschema*. El valor predeterminado es eventgridschema.

Los ejemplos de esta sección usan una instancia de Queue Storage para el controlador de eventos. Para más información, vea [Enrutamiento de eventos personalizados a Azure Queue Storage](custom-event-to-queue-storage.md).

El siguiente ejemplo se suscribe a un tema de Event Grid y usa el esquema predeterminado de Event Grid:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

En el ejemplo siguiente se usa el esquema de entrada del evento:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>Publicación de eventos en temas

Ahora ya puede enviar un evento al tema personalizado y ver el resultado de la asignación. A continuación se indica el script para publicar un evento en el [esquema de ejemplo](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Ahora, vea su instancia de Queue Storage. Las dos suscripciones entregaron eventos en diferentes esquemas.

La primera suscripción usó el esquema de Event Grid. El formato del evento entregado es:

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

Estos campos contienen las asignaciones del tema personalizado. **myEventTypeField** se asigna a **EventType**. Se usan los valores predeterminados para **DataVersion** y **Subject**. El objeto **Data** contiene los campos del esquema del evento original.

La segunda suscripción usó el esquema del evento de entrada. El formato del evento entregado es:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Tenga en cuenta que se entregaron los campos originales.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información acerca de los reintentos y las entregas de eventos, consulte [Entrega y reintento de entrega de mensajes de Event Grid](delivery-and-retry.md).
* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).
