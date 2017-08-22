---
title: "Información general sobre los registros de diagnóstico de Azure | Microsoft Docs"
description: "Aprenda qué son los registros de diagnóstico de Azure y cómo puede usarlos para entender los eventos que se producen en un recurso de Azure."
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
ms.date: 08/02/2017
ms.author: johnkem; magoedte
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 8961676a60d922912e383937ca38c5d2f89a348a
ms.contentlocale: es-es
ms.lasthandoff: 08/04/2017

---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Recopile y use los datos de registro provenientes de los recursos de Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Qué son los registros de diagnóstico de Azure
Los **registros de diagnóstico de nivel de recursos de Azure** son registros emitidos por un recurso que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. El contenido de estos registros varía según el tipo de recurso. Por ejemplo, los contadores de regla de grupo de seguridad de red y las auditorías de Key Vault son dos categorías de registros de recursos.

Lo registros de diagnóstico de nivel de recursos son distintos del [registro de actividad](monitoring-overview-activity-logs.md). El registro de actividad proporciona una visión general de las operaciones que se realizaron en los recursos de la suscripción con Resource Manager, por ejemplo, crear una máquina virtual o eliminar una aplicación de lógica. El registro de actividad es un registro de nivel de suscripción. Los registros de diagnóstico de nivel de recursos proporcionan una visión general de las operaciones realizadas dentro del mismo recurso, por ejemplo, obtener un secreto de un almacén de claves.

Los registros de diagnóstico de nivel de recursos también difieren de los registros de diagnóstico de nivel de sistema operativo invitado. Estos son los recopilados por un agente que se ejecuta dentro de una máquina virtual u otro tipo de recurso admitido. Los registros de diagnóstico de nivel de recursos no requieren ningún agente y capturan datos específicos de recurso de la plataforma Azure, mientras que los registros de diagnóstico de nivel de sistema operativo invitado capturan los datos desde el sistema operativo y las aplicaciones que se ejecutan en una máquina virtual.

No todos los recursos admiten el nuevo tipo de registros de diagnóstico de recursos que se describe aquí. En este artículo se incluye una sección en la que se muestran los tipos de recurso que admiten los nuevos registros de diagnóstico de nivel de recursos.

![Comparación de los registros de diagnóstico de recursos y otros tipos de registros ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Figura 1: Comparación de los registros de diagnóstico de recursos y otros tipos de registros

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Qué se puede hacer con los registros de diagnóstico de nivel de recursos
Estas son algunas de las cosas que puede hacer con los registros de diagnóstico de recursos:

![Ubicación lógica de los registros de diagnóstico de recursos](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Guardarlos en una [**cuenta de almacenamiento**](monitoring-archive-diagnostic-logs.md) para archivarlos o inspeccionarlos manualmente. Puede especificar el tiempo de retención (en días) usando la **Configuración de diagnóstico de recursos**.
* [Transmitirlos a **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md) para la ingestión en un servicio de terceros o una solución de análisis personalizado como PowerBI.
* Analizarlos con [Log Analytics de OMS](../log-analytics/log-analytics-azure-storage.md)

Puede usar una cuenta de almacenamiento o un espacio de nombres de Event Hubs que no esté en la misma suscripción que el que emite los registros. El usuario que configura los ajustes debe tener el acceso de RBAC adecuado a ambas suscripciones.

## <a name="resource-diagnostic-settings"></a>Configuración de diagnóstico de recursos
Los registros de diagnóstico de recursos para recursos que no son de proceso se configuran mediante la configuración de diagnóstico de recursos. **Configuración de diagnóstico de recursos** para un control de recursos:

* Dónde se envían los registros de diagnóstico (cuenta de almacenamiento, Event Hubs o Log Analytics de OMS).
* Qué categorías de registro se envían.
* Cuánto tiempo se debe conservar cada categoría de registro en una cuenta de almacenamiento
    - Una retención de cero días significa que los registros se conservan de forma indefinida. De lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2147483647.
    - Si se establecen directivas de retención, pero el almacenamiento de registros en una cuenta de almacenamiento está deshabilitado (por ejemplo, si solo se han seleccionado las opciones de Event Hubs u OMS), las directivas de retención no surten ningún efecto.
    - Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy.

Estos ajustes se configuran con facilidad mediante la hoja de diagnósticos para un recurso en Azure Portal, mediante los comandos de Azure PowerShell y de la CLI, o mediante la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Los registros de diagnóstico y las métricas para recursos de proceso (por ejemplo, máquinas virtuales o Service Fabric) usan [un mecanismo diferente para la configuración y la selección de salidas](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Procedimientos para habilitar la recopilación de registros de diagnóstico de recursos
La recopilación de registros de diagnóstico de recursos se puede habilitar [como parte de la creación de un recurso en una plantilla de Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) o después de crear un recurso mediante la hoja del recurso en el portal. También puede habilitar la recolección en cualquier momento mediante comandos de Azure PowerShell o de la CLI, o con la API de REST de Azure Monitor.

> [!TIP]
> Es posible que estas instrucciones no se apliquen directamente a cada recurso. Consulte los vínculos de esquema al final de esta página para ver los pasos especiales que se pueden aplicar a determinados tipos de recursos.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Habilitación de la recopilación de registros de diagnóstico de recursos en el portal
Puede habilitar la recopilación de registros de diagnóstico de recursos en Azure Portal una vez creado un recurso siguiendo los pasos descritos a continuación:

1. Vaya a la hoja del recurso y abra la hoja **Diagnósticos** .
2. Haga clic en **Activado** y seleccione una cuenta de almacenamiento o un centro de eventos.

   ![Habilitar los registros de diagnóstico después de la creación de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. En **Registros**, seleccione qué **categorías de registro** desea recopilar o transmitir.
4. Haga clic en **Guardar**.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Habilitación de la recopilación de registros de diagnóstico de recursos mediante PowerShell
Para habilitar la recopilación de registros de diagnóstico de recursos con Azure PowerShell, use los siguientes comandos:

Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

Para habilitar el streaming de registros de diagnóstico a un centro de eventos, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

El identificador de regla de Service Bus es una cadena con este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Puede obtener el identificador de recurso de su área de trabajo de Log Analytics con el comando siguiente:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>Habilitación de la recopilación de registros de diagnóstico de recursos mediante la CLI
Para habilitar la recopilación de registros de diagnóstico de recursos con la CLI de Azure, use los siguientes comandos:

Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

Para habilitar el streaming de registros de diagnóstico a un centro de eventos, use este comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

El identificador de regla de Service Bus es una cadena con este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Habilitación de la recopilación de registros de diagnóstico de recursos mediante la API de REST
Para cambiar la configuración de diagnóstico con la API de REST de Azure Monitor, consulte [este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Administración de la configuración de diagnóstico de recursos en el portal
Asegúrese de que todos los recursos estén instalados con la configuración de diagnóstico. Vaya a la hoja **Supervisión** del portal y abra la hoja **Registros de diagnóstico**.

![Hoja Registros de diagnóstico en el portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Puede que tenga que hacer clic en "More services" (Más servicios) para encontrar la hoja Supervisión.

En esta hoja puede ver y filtrar todos los recursos que admiten los registros de diagnóstico y ver si están habilitados para los diagnósticos. También puede revisar a qué cuenta de almacenamiento, centro de eventos o área de trabajo de Log Analytics fluyen esos registros.

![Resultados de la hoja Registros de diagnóstico en el portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Al hacer clic en un recurso, se muestran todos los registros que se han almacenado en la cuenta de almacenamiento y se ofrece la opción para desactivar o modificar la configuración de diagnóstico. Haga clic en el icono de descarga para descargar los registros para un período de tiempo determinado.

![Hoja Registros de diagnóstico de un recurso](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> Los registros de diagnóstico solo aparecen en esta vista y estarán disponibles para su descarga si ha configurado las opciones de diagnóstico para guardarlos en una cuenta de almacenamiento.
>
>

Al hacer clic en el vínculo **Configuración de diagnóstico**, se abre la hoja Configuración de diagnóstico, donde puede habilitar, deshabilitar o modificar la configuración para el recurso seleccionado.

## <a name="supported-services-and-schema-for-resource-diagnostic-logs"></a>Servicios admitidos y esquema para los registros de diagnóstico de recursos
El esquema para los registros de diagnóstico de recursos varía según la categoría de registro y el recurso.   

| Servicio | Esquema y documentos |
| --- | --- |
| API Management | Esquema no disponible. |
| Puertas de enlace de aplicaciones |[Registro de diagnóstico para Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics para Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registros de diagnósticos de Azure Batch](../batch/batch-diagnostics.md) |
| Customer Insights | Esquema no disponible. |
| Content Delivery Network | Esquema no disponible. |
| CosmosDB | Esquema no disponible. |
| Data Lake Analytics |[Acceso a los registros de diagnóstico de Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Acceso a los registros de diagnóstico de Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Registros de diagnóstico de Azure Event Hubs](../event-hubs/event-hubs-diagnostic-logs.md) |
| Key Vault |[Registro de Azure Key Vault](../key-vault/key-vault-logging.md) |
| Load Balancer |[Log Analytics para Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Aplicaciones lógicas |[Esquema de seguimiento personalizado de Logic Apps B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de seguridad de red |[Análisis del registro para grupos de seguridad de red (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Recovery Services | Esquema no disponible.|
| Search |[Habilitación y uso de Análisis de tráfico de búsqueda](../search/search-traffic-analytics.md) |
| Servidor de administración | Esquema no disponible. |
| Service Bus |[Registros de diagnóstico de Azure Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Stream Analytics |[Registros de diagnósticos de trabajos](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |

## <a name="supported-log-categories-per-resource-type"></a>Categorías de registro admitidas por tipo de recurso
|Tipo de recurso|Categoría|Nombre para mostrar de categoría|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|Registros relacionados con la puerta de enlace de ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Registros de trabajo|
|Microsoft.Automation/automationAccounts|JobStreams|Flujos de trabajo|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Estado del nodo de DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Registros de servicios|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtiene las métricas del punto de conexión; por ejemplo, ancho de banda, salida, etc.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataLakeAnalytics/accounts|Auditoría|Registros de auditoría|
|Microsoft.DataLakeAnalytics/accounts|Solicitudes|Registros de solicitud|
|Microsoft.DataLakeStore/accounts|Auditoría|Registros de auditoría|
|Microsoft.DataLakeStore/accounts|Solicitudes|Registros de solicitud|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Registros de archivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Registros operativos|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Registros de escalado automático|
|Microsoft.KeyVault/vaults|AuditEvent|Registros de auditoría|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico en tiempo de ejecución de flujo de trabajo|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Eventos de seguimiento de la cuenta de integración|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Evento de grupo de seguridad de red|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de reglas de grupo de seguridad de red|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta de equilibrador de carga|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Estado de mantenimiento de sondeo de equilibrador de carga|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Registro de acceso de Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Registro de rendimiento de Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Registro de Firewall de Application Gateway|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Datos de informes de Azure Backup|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Trabajos de Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventos de Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Elementos replicados de Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Registros de operaciones|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Registros operativos|
|Microsoft.StreamAnalytics/streamingjobs|Ejecución|Ejecución|
|Microsoft.StreamAnalytics/streamingjobs|Creación|Creación|

## <a name="next-steps"></a>Pasos siguientes

* [Transmisión de registros de diagnóstico a **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Cambio de la configuración de diagnóstico de recursos con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Análisis de registros desde Azure Storage con Log Analytics](../log-analytics/log-analytics-azure-storage.md)

