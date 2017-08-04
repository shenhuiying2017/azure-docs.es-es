---
title: "Creación de una alerta del registro de actividad con una plantilla de Resource Manager | Microsoft Docs"
description: Reciba notificaciones cuando se creen recursos de Azure.
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 26b140fef46a176b21bddbd7588543e71c251ed6
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Creación de una alerta del registro de actividad con una plantilla de Resource Manager
En este artículo se muestra cómo utilizar una [plantilla de Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) para configurar alertas del registro de actividad. Esto permite configurar automáticamente las alertas en los recursos cuando se crean como parte del proceso de implementación automático.

Los pasos básicos son los siguientes:

1.  Crear una plantilla en forma de archivo JSON que describa cómo crear la alerta del registro de actividad.
2.  [Usar cualquier método para implementar la plantilla](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

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

También puede [visitar la Galería de inicio rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights) para encontrar algunos ejemplos de plantillas de registro de actividad de alerta.

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre las [alertas](monitoring-overview-alerts.md)  
- Cómo agregar [grupos de acciones mediante una plantilla de Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)
- [Creación de una alerta de registro de actividades para supervisar todas las operaciones del motor de escalado automático en su suscripción](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Creación de una alerta de registro de actividades para supervisar todas las operaciones con errores de reducción y escalado horizontal automático en su suscripción](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

