---
title: "Suscripción de Azure Event Grid con plantilla"
description: "Cree una suscripción de Event Grid con una plantilla de Resource Manager."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2b9f55f8e944d688b622e30a773e1a34698f22ec
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Uso de plantillas de Resource Manager para suscripción de Event Grid

En este artículo se muestra cómo usar una plantilla de Azure Resource Manager para crear suscripciones de Event Grid. El formato que usa difiere en función de si está suscrito a eventos de grupos de recursos o a eventos para un tipo de recurso determinado. Ambos formatos se muestran en este artículo.

## <a name="subscribe-to-resource-group-events"></a>Suscripción a eventos de grupos de recursos

Cuando se suscriba a eventos de grupos de recursos, use `Microsoft.EventGrid/eventSubscriptions` como el tipo de recurso. Para el tipo de punto de evento, use `WebHook` o `EventHub`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

Cuando implementa esta plantilla en un grupo de recursos, se suscribe a eventos de ese grupo de recursos.

## <a name="subscribe-to-resource-events"></a>Suscripción a eventos de recursos

Cuando se suscribe a eventos de recursos, asocia la suscripción al recurso correcto mediante la inclusión del tipo de recurso y el nombre en la definición de suscripción. Para el tipo de recurso, use `<provider-namespace>/<resource-type>/providers/eventSubscriptions`. Para el nombre, use `<resource-name>/Microsoft.EventGrid/<subscription-name>`. Para el tipo de punto de evento, use `WebHook` o `EventHub`.

En el ejemplo siguiente se muestra cómo suscribirse a los eventos de Blob Storage.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>pasos siguientes

* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Si desea ver una introducción a Resource Manager, consulte [Información general sobre Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).