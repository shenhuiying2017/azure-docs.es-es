---
title: Alertas de métricas casi en tiempo real en Azure Monitor | Microsoft Docs
description: Aprenda a usar las alertas de métricas casi en tiempo real para supervisar métricas de recursos de Azure con una frecuencia de tan solo 1 minuto.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 15b9b0b69f3805b3e3af1d3973fd3a77bea62ab9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-newer-metric-alerts-for-azure-services-in-azure-portal"></a>Usar las nuevas alertas de métricas para los servicios de Azure de Azure Portal
Azure Monitor admite un nuevo tipo de alerta denominada alertas de métricas casi en tiempo real. 

Las alertas de métricas casi en tiempo real se diferencian de las [alertas de métricas clásicas](insights-alerts-portal.md) de varias maneras:

- **Latencia mejorada**: las alertas de métricas casi en tiempo real se pueden ejecutar con una frecuencia de cada minuto. Las alertas de métricas anteriores siempre se ejecutan con una frecuencia de 5 minutos.
- **Compatibilidad con métricas multidimensionales**: puede enviar alertas sobre las métricas dimensionales que le permiten supervisar un segmento interesante de la métrica.
- **Más control sobre las condiciones de las métricas**: en las alertas de métricas casi en tiempo real puede definir reglas de alerta más sofisticadas. Las alertas admiten la supervisión de los valores máximos, mínimos, promedio y totales de las métricas.
- **Supervisión combinada de varias métricas**: las alertas de métricas casi en tiempo real pueden supervisar varias métricas (actualmente hasta dos métricas) con una sola regla. Si ambas métricas incumplen sus respectivos umbrales durante el período especificado, se desencadena una alerta.
- **Sistema de notificaciones modular**: las alertas de métricas casi en tiempo real usan [grupos de acciones](monitoring-action-groups.md). Puede usar grupos de acciones para crear acciones modulares. Puede volver a usar grupos de acciones para varias reglas de alerta.
- **Métricas de los registros**: a partir de los datos de registros populares que se introducen en [Log Analytics](../log-analytics/log-analytics-overview.md), las métricas pueden extraerse en Azure Monitor y recibir alertas casi en tiempo real.


## <a name="metrics-and-dimensions-supported"></a>Métricas y dimensiones compatibles
Las alertas de métricas casi en tiempo real admiten alertas de métricas que utilizan dimensiones. Puede usar dimensiones para filtrar las métricas al nivel adecuado. Todas las métricas compatibles junto con las dimensiones aplicables se pueden explorar y visualizar en el [Explorador de métricas de Azure Monitor (versión preliminar)](monitoring-metric-charts.md).

Esta es la lista completa de orígenes de métricas basados en Azure Monitor compatibles para las alertas de métricas casi en tiempo real:

|Tipo de recurso  |Dimensiones compatibles  | Métricas disponibles|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Sí        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Sí   | [Cuentas de Automation](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/D| [Cuentas de Batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/D     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    N/D     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/D      |[Conjuntos de escalado de máquina virtual](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   Sí     |[Factorías de datos V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/D      |[DB para MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/D     | [DB para PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Sí      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     N/D    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/D     | [Puertas de enlace de aplicaciones](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  N/D       |[Direcciones IP públicas](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   N/D      |[Servicios de búsqueda](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Sí       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Sí     | [Cuentas de almacenamiento](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Sí    | [Servicios de blob](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [servicios de archivo](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [servicios de cola](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) y [servicios de tabla](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/D       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    N/D     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (versión preliminar) | Sí|[Áreas de trabajo de Log Analytics](#support-for-oms-logs-as-metrics-for-alerting)|


## <a name="create-a-newer-metric-alert"></a>Creación de una alerta de métrica nueva
Actualmente, solo puede crear alertas de métricas nuevas en Azure Portal o API de REST. La compatibilidad con la configuración de alertas de métricas casi en tiempo real mediante PowerShell, la Interfaz de la línea de comandos de Azure (CLI de Azure) estará disponible próximamente.

Para aprender a crear una alerta de métricas nueva en Azure Portal, consulte [Creación de una regla de alertas en Azure Portal](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-newer-metric-alerts"></a>Administrar alertas de métricas más recientes
Tras crear una alerta de métricas casi en tiempo real, puede administrarla mediante los pasos descritos en [Administración de las alertas en Azure Portal](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="support-for-oms-logs-as-metrics-for-alerting"></a>Compatibilidad con registros de OMS como métricas de alertas

También puede utilizar alertas de métricas casi en tiempo real en los registros de OMS populares extraídos como métricas como parte de las métricas de vista previa de los registros.  
- [Contadores de rendimiento](../log-analytics/log-analytics-data-sources-performance-counters.md) para equipos con Windows y Linux
- [Registros de latidos para Agent Health](../operations-management-suite/oms-solution-agenthealth.md)
- Registros de [administración de actualizaciones](../operations-management-suite/oms-solution-update-management.md)

Esta es la lista completa de orígenes de métricas basados en registros de OMS compatibles para las alertas de métricas casi en tiempo real:

Detalles/nombre de métricas  |Dimensiones compatibles  | Tipo de registro  |
|---------|---------|---------|
|Average_Avg. Segundos de disco/lecturas     |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
| Average_Avg. Segundos de disco/escrituras     |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
| Average_Current Disk Queue Length   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
| Average_Disk Reads/sec    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
| Average_Disk Transfers/sec    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
|   Average_% Free Space    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
| Average_Available MBytes     |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
| Average_% Committed Bytes In Use    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
| Average_Bytes Received/sec    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
|  Average_Bytes Sent/sec    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
|  Average_Bytes Total/sec    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
|  Average_% Processor Time    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
|   Average_Processor Queue Length    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Windows      |
|   Average_% Free Inodes   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Free Space   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Used Inodes  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Used Space   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Disk Read Bytes/sec    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Disk Reads/sec |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Disk Transfers/sec |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Disk Write Bytes/sec   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Disk Writes/sec    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Free Megabytes |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Logical Disk Bytes/sec |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Available Memory |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Available Swap Space |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Used Memory  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Used Swap Space  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Available MBytes Memory    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Available MBytes Swap  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Page Reads/sec |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Page Writes/sec    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Pages/sec  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Used MBytes Swap Space |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Used Memory MBytes |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Total Bytes Transmitted    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Total Bytes Received   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Total Bytes    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Total Packets Transmitted  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Total Packets Received |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Total Rx Errors    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Total Tx Errors    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Total Collisions   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Avg. Segundos de disco/lecturas |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Avg. Segundos de disco/transferencias |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Avg. Segundos de disco/escrituras    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Physical Disk Bytes/sec    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Pct Privileged Time    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Pct User Time  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Used Memory kBytes |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Virtual Shared Memory  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% DPC Time |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Idle Time    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Interrupt Time   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% IO Wait Time |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Nice Time    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Privileged Time  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% Processor Time   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_% User Time    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Free Physical Memory   |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Free Space in Paging Files |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Free Virtual Memory    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Processes  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Size Stored In Paging Files    |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Uptime |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Average_Users  |     Sí: Computer, ObjectName, InstanceName, CounterPath y SourceSystem    |   Contador de rendimiento de Linux      |
|    Latido  |     Sí: Computer, OSType, Version y SourceComputerId    |   Registros de latidos |
|    Actualizar |     Sí: Computer, Product, Classification, UpdateState, Optional y Approved    |   Administración de actualizaciones |

> [!NOTE]
> La métrica o dimensión específicas solo se mostrarán si los datos de ellas existen durante el período elegido. Estas métricas están disponibles para los clientes con áreas de trabajo de Este de EE. UU., Centro-oeste de EE. UU. y Europa Occidental que han participado en la versión preliminar. Si quiere formar parte de esta versión preliminar, regístrese mediante [la encuesta](https://aka.ms/MetricLogPreview).


## <a name="payload-schema"></a>Esquema de carga

La operación POST contiene el esquema y la carga útil de JSON siguientes para todas las alertas de métricas casi en tiempo real cuando se usa un [grupo de acciones](monitoring-action-groups.md) configurado correctamente:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre la nueva [experiencia de alertas](monitoring-overview-unified-alerts.md).
* Más información sobre las [alertas de registro en Azure](monitor-alerts-unified-log.md).
* Más información sobre las [alertas en Azure](monitoring-overview-alerts.md).
