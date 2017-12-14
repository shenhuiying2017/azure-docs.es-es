---
title: "Servicios y esquemas admitidos de los registros de diagnóstico de Azure | Microsoft Docs"
description: "Conozca el esquema de los eventos y servicios admitidos para los registros de diagnóstico de Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: johnkem
ms.openlocfilehash: 1a58db2d424e4280fd56be972d48df89648e8c13
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Servicios, esquemas y categorías admitidos en los registros de diagnóstico de Azure

Los [registros de diagnóstico de recursos de Azure](monitoring-overview-of-diagnostic-logs.md) son los registros emitidos por los recursos de Azure que describen el funcionamiento de ese recurso. Estos registros son específicos del tipo de recurso. En este artículo se describe el conjunto de servicios y el esquema de eventos admitidos para los eventos emitidos por cada servicio. También se incluye una lista completa de las categorías de registro disponibles por tipo de recurso.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Servicios y esquemas admitidos para los registros de diagnóstico de recursos
El esquema para los registros de diagnóstico de recursos varía según la categoría de registro y el recurso.   

| Servicio | Esquema y documentos |
| --- | --- |
| Analysis Services | Esquema no disponible. |
| API Management | [Registros de diagnóstico de API Management](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Puertas de enlace de aplicaciones |[Registro de diagnóstico para Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics para Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registros de diagnósticos de Azure Batch](../batch/batch-diagnostics.md) |
| Customer Insights | Esquema no disponible. |
| Content Delivery Network | Esquema no disponible. |
| CosmosDB | [Registro de Azure Cosmos DB](../cosmos-db/logging.md) |
| Data Lake Analytics |[Acceso a los registros de diagnóstico de Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Acceso a los registros de diagnóstico de Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Registros de diagnóstico de Azure Event Hubs](../event-hubs/event-hubs-diagnostic-logs.md) |
| IoT Hub | [Operaciones de IoT Hub](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Registro de Azure Key Vault](../key-vault/key-vault-logging.md) |
| Load Balancer |[Log Analytics para Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Esquema de seguimiento personalizado de Logic Apps B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de seguridad de red |[Análisis del registro para grupos de seguridad de red (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Protección contra DDOS | Esquema no disponible. |
| Recovery Services | [Modelo de datos para Azure Backup](../backup/backup-azure-reports-data-model.md)|
| Search |[Habilitación y uso de Análisis de tráfico de búsqueda](../search/search-traffic-analytics.md) |
| Servidor de administración | Esquema no disponible. |
| Service Bus |[Registros de diagnóstico de Azure Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Registro de diagnóstico de Azure SQL Database](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Registros de diagnósticos de trabajos](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Virtual Networks | Esquema no disponible. |

## <a name="supported-log-categories-per-resource-type"></a>Categorías de registro admitidas por tipo de recurso
|Tipo de recurso|Categoría|Nombre para mostrar de categoría|
|---|---|---|
|microsoft.aadiam/tenants|Inicio de sesión|Inicio de sesión|
|Microsoft.AnalysisServices/servers|Motor|Motor|
|Microsoft.AnalysisServices/servers|Servicio|Servicio|
|Microsoft.ApiManagement/service|GatewayLogs|Registros relacionados con la puerta de enlace de ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Registros de trabajo|
|Microsoft.Automation/automationAccounts|JobStreams|Flujos de trabajo|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Estado del nodo de DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Registros de servicios|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtiene las métricas del punto de conexión; por ejemplo, ancho de banda, salida, etc.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Registro de ejecuciones de actividad de canalización|
|Microsoft.DataFactory/factories|PipelineRuns|Registro de ejecuciones de canalización|
|Microsoft.DataFactory/factories|TriggerRuns|Registro de ejecuciones de desencadenador|
|Microsoft.DataLakeAnalytics/accounts|Auditoría|Registros de auditoría|
|Microsoft.DataLakeAnalytics/accounts|Solicitudes|Registros de solicitud|
|Microsoft.DataLakeStore/accounts|Auditoría|Registros de auditoría|
|Microsoft.DataLakeStore/accounts|Solicitudes|Registros de solicitud|
|Microsoft.Devices/IotHubs|Conexiones|Conexiones|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetría de dispositivo|
|Microsoft.Devices/IotHubs|C2DCommands|Comandos C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operaciones de identidad de dispositivos|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operaciones de carga de archivos|
|Microsoft.Devices/IotHubs|Rutas|Rutas|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operaciones gemelas C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Consultas gemelas|
|Microsoft.Devices/IotHubs|JobsOperations|Operaciones de trabajos|
|Microsoft.Devices/IotHubs|DirectMethods|Métodos directos|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operaciones de dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operaciones del servicio|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Registros de archivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Registros operativos|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Registros de escalado automático|
|Microsoft.KeyVault/vaults|AuditEvent|Registros de auditoría|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico en tiempo de ejecución de flujo de trabajo|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Eventos de seguimiento de la cuenta de integración|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Evento de grupo de seguridad de red|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de reglas de grupo de seguridad de red|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Evento de flujo de reglas de grupo de seguridad de red|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta de equilibrador de carga|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Estado de mantenimiento de sondeo de equilibrador de carga|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notificaciones de protección contra DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertas de protección de máquina virtual|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Registro de acceso de Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Registro de rendimiento de Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Registro de Firewall de Application Gateway|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Registros de diagnóstico de puerta de enlace|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Registros de diagnóstico de túnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Registros de diagnóstico de ruta|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Evento de resultados de estado de sondeo de Traffic Manager|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tabla de contadores GWM|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Datos de informes de Azure Backup|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Trabajos de Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventos de Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Elementos replicados de Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Estadísticas de replicación de Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Puntos de recuperación de Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Registros de operaciones|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Registros operativos|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Estadísticas de tiempo de ejecución del Almacén de consultas|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Estadísticas de espera del Almacén de consultas|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Estadísticas de espera de base de datos|
|Microsoft.Sql/servers/databases|Tiempos de expiración|Tiempos de expiración|
|Microsoft.Sql/servers/databases|Blocks|Blocks|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.StreamAnalytics/streamingjobs|Ejecución|Ejecución|
|Microsoft.StreamAnalytics/streamingjobs|Creación|Creación|

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre los registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md)
* [Transmisión de registros de diagnóstico a **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Cambio de la configuración de diagnóstico de recursos con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Análisis de registros desde Azure Storage con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
