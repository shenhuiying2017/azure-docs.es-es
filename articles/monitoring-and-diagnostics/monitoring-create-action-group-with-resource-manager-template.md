---
title: "Creación de grupos de acciones con plantillas de Resource Manager | Microsoft Docs"
description: "Los grupos de acciones le permiten enviar notificaciones por correo electrónico o SMS, o llamar a webhooks cuando se producen determinados eventos."
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 2dd7b14f1466fa7244a2af2c030d8b794658aaad
ms.contentlocale: es-es
ms.lasthandoff: 04/12/2017


---

# <a name="create-an-action-group-with-a-resource-manager-template"></a>Creación de un grupo de acciones con una plantilla de Resource Manager
En este artículo se muestra cómo utilizar una [plantilla de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) para configurar grupos de acciones. Las plantillas le permiten configurar automáticamente grupos de acciones en los recursos cuando se crean para asegurarse de que todas las partes correctas reciben las notificaciones pertinentes cuando se desencadena una alerta.

Los pasos básicos son los siguientes:

1.    Crear una plantilla en forma de archivo JSON que describa cómo crear la alerta del grupo de acciones.
2.    [Usar cualquier método para implementar la plantilla](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

A continuación se describe cómo crear una plantilla de Resource Manager primero para un grupo de acciones solamente y, después, para un grupo de acciones durante la creación de otro recurso.

## <a name="resource-manager-template-for-an-action-group"></a>Plantilla de Resource Manager para un grupo de acciones

Para crear un grupo de acciones mediante una plantilla de Resource Manager, cree un recurso de tipo `Microsoft.Insights/actionGroups` y rellene todas las propiedades relacionadas. A continuación encontrará un par de plantillas de ejemplo que crean un grupo de acciones.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Pasos siguientes
Más información sobre los [grupos de acciones](monitoring-action-groups.md)  
Más información sobre las [alertas](monitoring-overview-alerts.md)  
Cómo agregar [alertas mediante una plantilla de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

