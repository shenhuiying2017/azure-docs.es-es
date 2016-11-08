---
title: Habilitación automática de Configuración de diagnóstico con una plantilla de Resource Manager | Microsoft Docs
description: Aprenda a usar una plantilla de Resource Manager para crear una configuración de diagnóstico que le permitirá transmitir los registros de diagnóstico a centros de eventos o almacenarlos en una cuenta de almacenamiento.
author: johnkemnetz
manager: rboucher
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem

---
# Habilitación automática de Configuración de diagnóstico al crear recursos con una plantilla de Resource Manager
En este artículo se muestra cómo usar una [plantilla de Azure Resource Manager](../resource-group-authoring-templates.md) para establecer Configuración de diagnóstico en un recurso cuando se crea. Esto permite empezar automáticamente a transmitir las métricas y los registros de diagnóstico a Event Hubs, a archivarlos en una cuenta de almacenamiento o a enviarlos a Log Analytics cuando se crea un recurso.

El método para habilitar registros de diagnóstico mediante una plantilla de Resource Manager depende del tipo de recurso.

* Los recursos **no de proceso** (por ejemplo, Grupos de seguridad de red, Logic Apps, Automatización) usan [Configuración de diagnóstico como se describe en este artículo](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
* Los recursos **de proceso** (basados en WAD/LAD) usan el [archivo de configuración de WAD/LAD descrito en este artículo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

En este artículo se describe cómo configurar diagnósticos mediante cualquiera de estos métodos.

Los pasos básicos son los siguientes:

1. Cree una plantilla como archivo JSON que describa cómo crear el recurso y habilitar los diagnósticos.
2. [Implemente la plantilla mediante cualquier método de implementación](../resource-group-template-deploy.md).

A continuación, se ofrece un ejemplo del archivo JSON de plantilla que debe generar para los recursos de proceso y los que no lo son.

## Plantilla para recursos no de proceso
Para recursos no de proceso, debe hacer dos cosas:

1. Agregue parámetros al blob de parámetros para el nombre de cuenta de almacenamiento, el identificador de regla de Service Bus o el identificador del área de trabajo de Log Analytics de OMS (esto habilita el archivado de registros de diagnóstico en una cuenta de almacenamiento, el streaming de registros a Event Hubs o el envío de registros a Log Analytics).
   
    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
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
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
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
          ]
        }
      }
    ]
    ```

El blob de propiedades para la configuración de diagnóstico sigue [el formato descrito en este artículo](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Este es un ejemplo completo en el que se crea un grupo de seguridad de red y se activa el streaming a centros de eventos y el almacenamiento en una cuenta de almacenamiento.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
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
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
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

## Plantilla para recursos de proceso
Para habilitar los diagnósticos en un recurso de proceso, por ejemplo, un clúster de Service Fabric o de máquinas virtuales, necesitará hacer lo siguiente:

1. Agregue la extensión de Diagnósticos de Azure a la definición de recurso de máquina virtual.
2. Especifique una cuenta de almacenamiento y/o un centro de eventos como parámetro.
3. Agregue el contenido del archivo XML de WADCfg a la propiedad XMLCfg y dé el formato de escape correcto a todos los caracteres XML.

> [!WARNING]
> Este último paso puede ser complicado de realizar correctamente. [Consulte este artículo](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) para ver un ejemplo que divide el esquema de configuración de diagnóstico en variables con el formato y el escape correctos.
> 
> 

Se describe el proceso completo, con ejemplos, [en este documento](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

## Pasos siguientes
* [Más información sobre los registros de Diagnósticos de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
* [Transmita registros de diagnóstico de Azure a centros de eventos](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

<!---HONumber=AcomDC_0928_2016-->