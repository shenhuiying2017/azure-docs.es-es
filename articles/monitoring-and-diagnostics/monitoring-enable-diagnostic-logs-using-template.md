---
title: "Habilitación automática de Configuración de diagnóstico con una plantilla de Resource Manager | Microsoft Docs"
description: "Aprenda a usar una plantilla de Resource Manager para crear una configuración de diagnóstico que le permitirá transmitir los registros de diagnóstico a Event Hubs o almacenarlos en una cuenta de almacenamiento."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: johnkem
ms.openlocfilehash: 6355433dab7bac910dd89a50b74df13d6cf1b8fc
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Habilitación automática de Configuración de diagnóstico al crear recursos con una plantilla de Resource Manager
En este artículo se muestra cómo usar una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para establecer Configuración de diagnóstico en un recurso cuando se crea. Esto permite empezar automáticamente a transmitir las métricas y los registros de diagnóstico a Event Hubs, a archivarlos en una cuenta de almacenamiento o a enviarlos a Log Analytics cuando se crea un recurso.

El método para habilitar registros de diagnóstico mediante una plantilla de Resource Manager depende del tipo de recurso.

* **no de proceso** (por ejemplo, Grupos de seguridad de red, Logic Apps, Automation) usan [Configuración de diagnóstico como se describe en este artículo](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
* **de proceso** (basados en WAD/LAD) usan el [archivo de configuración de WAD/LAD descrito en este artículo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

En este artículo se describe cómo configurar diagnósticos mediante cualquiera de estos métodos.

Los pasos básicos son los siguientes:

1. Cree una plantilla como archivo JSON que describa cómo crear el recurso y habilitar los diagnósticos.
2. [Implemente la plantilla mediante cualquier método de implementación](../azure-resource-manager/resource-group-template-deploy.md).

A continuación, se ofrece un ejemplo del archivo JSON de plantilla que debe generar para los recursos de proceso y los que no lo son.

## <a name="non-compute-resource-template"></a>Plantilla para recursos no de proceso
Para recursos no de proceso, debe hacer dos cosas:

1. Agregue parámetros al blob de parámetros para el nombre de cuenta de almacenamiento, el identificador de regla de autorización de centro de eventos o el identificador del área de trabajo de Log Analytics de OMS (que permite el archivado de registros de diagnóstico en una cuenta de almacenamiento, el streaming de registros a Event Hubs o el envío de registros a Log Analytics).
   
    ```json
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. En la matriz de recursos del recurso para el que desea habilitar los registros de diagnóstico, agregue un recurso de tipo `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2017-05-01-preview",
        "properties": {
          "name": "[parameters('settingName')]",
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
          "eventHubName": "[parameters('eventHubName')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "category": "AllMetrics",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

El blob de propiedades para la configuración de diagnóstico sigue [el formato descrito en este artículo](https://docs.microsoft.com/rest/api/monitor/ServiceDiagnosticSettings/CreateOrUpdate). Al agregar la propiedad `metrics`, también podrá enviar métricas de recursos para estos mismos resultados, siempre y cuando [los recursos admitan las métricas de Azure Monitor](monitoring-supported-metrics.md).

Este es un ejemplo completo en el que se crea una aplicación lógica y se activa la transmisión a Event Hubs y el almacenamiento en una cuenta de almacenamiento.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2017-05-01-preview",
          "properties": {
            "name": "[parameters('settingName')]",
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Plantilla para recursos de proceso
Para habilitar los diagnósticos en un recurso de proceso, por ejemplo, un clúster de Service Fabric o de máquinas virtuales, necesitará hacer lo siguiente:

1. Agregue la extensión de Diagnósticos de Azure a la definición de recurso de máquina virtual.
2. Especifique una cuenta de almacenamiento y/o un centro de eventos como parámetro.
3. Agregue el contenido del archivo XML de WADCfg a la propiedad XMLCfg y dé el formato de escape correcto a todos los caracteres XML.

> [!WARNING]
> Este último paso puede ser complicado de realizar correctamente. [Consulte este artículo](../virtual-machines/windows/extensions-diagnostics-template.md#diagnostics-configuration-variables) para ver un ejemplo que divide el esquema de configuración de diagnóstico en variables con el formato y el escape correctos.
> 
> 

Se describe el proceso completo, con ejemplos, [en este documento](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los registros de Diagnósticos de Azure](monitoring-overview-of-diagnostic-logs.md)
* [Transmita registros de diagnóstico de Azure a Event Hubs](monitoring-stream-diagnostic-logs-to-event-hubs.md)

