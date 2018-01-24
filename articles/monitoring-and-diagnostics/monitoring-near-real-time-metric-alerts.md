---
title: "Alertas de métricas casi en tiempo real en Azure Monitor | Microsoft Docs"
description: "Las alertas de métricas casi en tiempo real le permiten supervisar las métricas de recursos de Azure con una frecuencia de un minuto."
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
ms.openlocfilehash: cd1002929ad749ac1742e914a9f2411f09ec91d5
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertas de métricas casi en tiempo real (versión preliminar)
Azure Monitor ahora admite un nuevo tipo de alerta de métricas denominada alertas de métricas casi en tiempo real (versión preliminar) Esta característica actualmente está en su versión preliminar pública.
Estas alertas se diferencian de las alertas de métricas normales de varias maneras:

- **Latencia mejorada**: las alertas de métricas casi en tiempo real pueden supervisar los cambios en los valores de las métricas con una frecuencia de tan solo 1 minuto.
- **Más control sobre las condiciones de las métricas**: las alertas de métricas casi en tiempo real permiten a los usuarios definir reglas de alerta más sofisticadas. Las alertas admiten ahora la supervisión de los valores máximos, mínimos, promedio y totales de las métricas.
- **Supervisión combinada de varias métricas**: las alertas de métricas casi en tiempo real pueden supervisar varias métricas (actualmente dos) con una sola regla. La alerta se desencadena si ambas métricas incumplen sus respectivos umbrales durante un período especificado.
- **Sistema de notificaciones modular**: las alertas de métricas casi en tiempo real usan [grupos de acciones](monitoring-action-groups.md). Esta funcionalidad permite a los usuarios crear acciones de forma modular y reutilizarlas para muchas reglas de alertas.

> [!NOTE]
> La característica de alertas de métricas casi en tiempo real se encuentra actualmente en versión preliminar pública. La funcionalidad y la experiencia del usuario están sujetas a cambios.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>¿Para qué recursos pueden crearse alertas de métricas casi en tiempo real?
Lista completa de los tipos de recursos que son compatibles con las alertas de métricas casi en tiempo real:

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

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>Near Real-Time Metric Alerts para métricas con dimensiones
Near Real-Time Metric Alerts admite alertas para métricas con dimensiones. Las dimensiones son una manera de filtrar las métricas al nivel adecuado. Las alertas para métricas con dimensiones casi en tiempo real se admiten para los tipos de recursos siguientes

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (solo se admiten para las cuentas de almacenamiento de las regiones de EE. UU.)
* Microsoft.Storage/storageAccounts/services (solo se admiten para las cuentas de almacenamiento de las regiones de EE. UU.)


## <a name="create-a-near-real-time-metric-alert"></a>Creación de una alerta de métricas casi en tiempo real
Actualmente, las alertas de métricas casi en tiempo real solo pueden crearse mediante Azure Portal. Próximamente estará disponible la compatibilidad para la configuración de las alertas de métricas casi en tiempo real mediante PowerShell, la interfaz de línea de comandos (CLI) y la API de REST de Azure Monitor.

La experiencia de creación de alertas de Near Real-Time Metric Alerts ha cambiado a la nueva experiencia **Alerts (versión preliminar)**. Aunque la página actual de Alerts muestra **Add Near Real-Time Metric Alerts** (Agregar alerta Near Real-Time Metric Alerts), se redirige a la nueva experiencia.

Puede crear una alerta para métricas casi en tiempo real mediante los pasos que [aquí](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal) se describen.

## <a name="managing-near-real-time-metric-alerts"></a>Administración de alertas de métricas casi en tiempo real
Cuando haya creado una alerta **Near Real-Time Metric Alerts**, puede administrarla mediante los pasos que [aquí](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal) se describen.

## <a name="next-steps"></a>pasos siguientes

* [Más información sobre la nueva experiencia Alerts (versión preliminar)](monitoring-overview-unified-alerts.md)
* [Información acerca de las alertas de registro de Azure Alerts (versión preliminar)](monitor-alerts-unified-log.md)
* [Información acerca de las alertas en Azure](monitoring-overview-alerts.md)