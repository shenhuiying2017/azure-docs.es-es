---
title: "Creación de una alerta del registro de actividad con una plantilla de Resource Manager | Microsoft Docs"
description: Reciba notificaciones cuando se creen recursos de Azure.
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
ms.openlocfilehash: 239b8fe2a7724bb34e7060c90af73825e61d8398
ms.contentlocale: es-es
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Creación de una alerta del registro de actividad con una plantilla de Resource Manager
En este artículo se muestra cómo utilizar una [plantilla de Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) para configurar alertas del registro de actividad. Esto permite configurar automáticamente las alertas en los recursos cuando se crean como parte del proceso de implementación automático.

Los pasos básicos son los siguientes:

1.    Crear una plantilla en forma de archivo JSON que describa cómo crear la alerta del registro de actividad.
2.    [Usar cualquier método para implementar la plantilla](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

A continuación se describe cómo crear una plantilla de Resource Manager primero para una alerta del registro de actividad solamente y, después, para una alerta del registro de actividad durante la creación de otro recurso.

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Plantilla de Resource Manager para una alerta del registro de actividad
Para crear una alerta del registro de actividad mediante una plantilla de Resource Manager, cree un recurso de tipo `microsoft.insights/activityLogAlerts` y rellene todas las propiedades relacionadas. A continuación se muestra una plantilla que crea una alerta del registro de actividad.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ] 
        },
        "actions": {
          "actionGroups": 
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes
Más información sobre las [alertas](monitoring-overview-alerts.md)  
Cómo agregar [grupos de acciones mediante una plantilla de Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)

