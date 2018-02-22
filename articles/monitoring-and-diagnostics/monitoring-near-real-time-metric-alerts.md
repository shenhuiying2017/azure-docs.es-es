---
title: "Alertas de métricas casi en tiempo real en Azure Monitor | Microsoft Docs"
description: "Aprenda a usar las alertas de métricas casi en tiempo real para supervisar métricas de recursos de Azure con una frecuencia de tan solo 1 minuto."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertas de métricas casi en tiempo real (versión preliminar)
Azure Monitor admite un nuevo tipo de alerta denominada alertas de métricas casi en tiempo real (versión preliminar). Esta función actualmente se encuentra en versión preliminar pública.

Las alertas de métricas casi en tiempo real se diferencian de las alertas de métricas normales de varias maneras:

- **Latencia mejorada**: las alertas de métricas casi en tiempo real pueden supervisar cambios en los valores de las métricas con una frecuencia de tan solo 1 minuto.
- **Más control sobre las condiciones de las métricas**: en las alertas de métricas casi en tiempo real puede definir reglas de alerta más sofisticadas. Las alertas admiten la supervisión de los valores máximos, mínimos, promedio y totales de las métricas.
- **Supervisión combinada de varias métricas**: las alertas de métricas casi en tiempo real pueden supervisar varias métricas (actualmente hasta dos métricas) con una sola regla. Si ambas métricas incumplen sus respectivos umbrales durante el período especificado, se desencadena una alerta.
- **Sistema de notificaciones modular**: las alertas de métricas casi en tiempo real usan [grupos de acciones](monitoring-action-groups.md). Puede usar grupos de acciones para crear acciones modulares. Puede volver a usar grupos de acciones para varias reglas de alerta.

> [!NOTE]
> La función de alertas de métricas casi en tiempo real se encuentra actualmente en versión preliminar pública. La funcionalidad y la experiencia del usuario están sujetas a cambios.
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>Recursos que puede utilizar con las alertas de métricas casi en tiempo real
Esta es la lista completa de los tipos de recursos que son compatibles con las alertas de métricas casi en tiempo real:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>Alertas de métricas casi en tiempo real para las métricas que utilizan dimensiones
Las alertas de métricas casi en tiempo real admiten alertas de métricas que utilizan dimensiones. Puede usar dimensiones para filtrar las métricas al nivel adecuado. Las alertas de métricas casi en tiempo real para las métricas que utilizan dimensiones son compatibles con los siguientes tipos de recursos:

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (solo compatible con las cuentas de almacenamiento de las regiones de EE. UU.)
* Microsoft.Storage/storageAccounts/services (solo compatible con las cuentas de almacenamiento de las regiones de EE. UU.)

## <a name="create-a-near-real-time-metric-alert"></a>Creación de una alerta de métricas casi en tiempo real
Actualmente solo puede crear alertas de métricas casi a tiempo real en Azure Portal. La compatibilidad con la configuración de alertas de métricas casi en tiempo real mediante PowerShell, la Interfaz de la línea de comandos de Azure (CLI de Azure) y las API de REST de Azure Monitor estará disponible próximamente.

La experiencia de creación de alertas de métricas casi en tiempo real se ha trasladado a la nueva página **Alertas (versión preliminar)**. Aunque la página actual de alertas muestra **Add Near Real-Time Metric Alerts** (Agregar alertas de métricas casi en tiempo real), se le redirige a la página **Alertas (versión preliminar)**.

Para aprender a crear una alerta de métricas casi en tiempo real, consulte [Creación de una regla de alertas en Azure Portal](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Administración de alertas de métricas casi en tiempo real
Tras crear una alerta de métricas casi en tiempo real, puede administrarla mediante los pasos descritos en [Administración de las alertas en Azure Portal](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Esquema de carga

La operación POST contiene el esquema y la carga útil de JSON siguientes para todas las alertas de métricas casi en tiempo real:

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>pasos siguientes

* Más información sobre la nueva [experiencia de Alertas (versión preliminar)](monitoring-overview-unified-alerts.md).
* Más información sobre las [alertas de registro en Alertas de Azure (versión preliminar)](monitor-alerts-unified-log.md).
* Más información sobre las [alertas en Azure](monitoring-overview-alerts.md).