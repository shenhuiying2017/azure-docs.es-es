---
title: "Información general sobre los registros de diagnóstico de Azure | Microsoft Docs"
description: "Aprenda qué son los registros de diagnóstico de Azure y cómo puede usarlos para entender los eventos que se producen en un recurso de Azure."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: johnkem; magoedte
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 5675a65e3b48e39f44dc320b7b87910ab759b764
ms.lasthandoff: 03/14/2017


---
# <a name="collect-and-consume-diagnostic-data-from-your-azure-resources"></a>Recopile y use los datos de diagnóstico provenientes de los recursos de Azure

## <a name="what-are-azure-diagnostic-logs"></a>Qué son los registros de diagnóstico de Azure
Los **registros de diagnóstico de Azure** son registros emitidos por un recurso que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. El contenido de estos registros varía según el tipo de recurso. Por ejemplo, los registros del sistema de eventos de Windows son una categoría de registro de diagnóstico para máquinas virtuales y los registros de blob, tabla y cola son categorías de los registros de diagnóstico para cuentas de almacenamiento.

Los registros de diagnóstico son distintos del [registro de actividad (anteriormente conocido como registro de auditoría o registro operativo)](monitoring-overview-activity-logs.md). El registro de actividad proporciona información sobre las operaciones que se realizaron en los recursos de su suscripción. Los registros de diagnóstico proporcionan información detallada sobre las operaciones que el mismo recurso realiza.

No todos los recursos admiten el nuevo tipo de registros de diagnóstico que se describe aquí. En este artículo se incluye una sección en la que se muestran los tipos de recurso que admiten los nuevos registros de diagnóstico.

![Comparación de los registros de diagnóstico y otros tipos de registros ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Ilustración 1: Comparación de los registros de diagnóstico y otros tipos de registros

## <a name="what-you-can-do-with-diagnostic-logs"></a>Qué se puede hacer con los registros de diagnóstico
Estas son algunas de las cosas que puede hacer con los registros de diagnóstico:

![Ubicación lógica de registros de diagnóstico](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Guardarlos en una [**cuenta de almacenamiento**](monitoring-archive-diagnostic-logs.md) para archivarlos o inspeccionarlos manualmente. Puede especificar el tiempo de retención (en días) mediante **Configuración de diagnóstico**.
* [Transmitirlos a **centros de eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md) para la ingestión en un servicio de terceros o una solución de análisis personalizado como PowerBI.
* Analizarlos con [Log Analytics de OMS](../log-analytics/log-analytics-azure-storage.md)

Puede usar una cuenta de almacenamiento o un espacio de nombres de centro de eventos que no esté en la misma suscripción que el que emite los registros. El usuario que configura los ajustes debe tener el acceso de RBAC adecuado a ambas suscripciones.

## <a name="diagnostic-settings"></a>Configuración de diagnóstico
Los registros de diagnóstico para recursos no de proceso se configuran mediante Configuración de diagnóstico. **Configuración de diagnóstico** para un control de recurso:

* Dónde se envían los registros de diagnóstico (cuenta de almacenamiento, centros de eventos o Log Analytics de OMS).
* Qué categorías de registro se envían.
* El tiempo durante el cual cada categoría de registro se debe conservar en una cuenta de almacenamiento
    - Una retención de cero días significa que los registros se conservan de forma indefinida. De lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2147483647.
    - Si se establecen directivas de retención, pero el almacenamiento de registros en una cuenta de almacenamiento está deshabilitado (por ejemplo, si solo se han seleccionado las opciones de Event Hubs u OMS), las directivas de retención no surten ningún efecto.
    - Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy.

Estas configuraciones se establecen con facilidad mediante la hoja Diagnósticos para un recurso en Azure Portal, mediante los comandos de Azure PowerShell y de la CLI, o mediante la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Los registros de diagnóstico y las métricas para recursos de proceso (por ejemplo, máquinas virtuales o Service Fabric) usan [un mecanismo diferente para la configuración y la selección de salidas](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Cómo habilitar la recopilación de registros de diagnóstico
La recopilación de registros de diagnóstico se puede habilitar como parte de la creación de un recurso o después de crear un recurso mediante la hoja del recurso en el Portal. También puede habilitar los registros de diagnóstico en cualquier momento mediante comandos de Azure PowerShell o de la CLI, o con la API de REST de Azure Monitor.

> [!TIP]
> Es posible que estas instrucciones no se apliquen directamente a cada recurso. Consulte los vínculos de esquema al final de esta página para ver los pasos especiales que se pueden aplicar a determinados tipos de recursos.
>
>

[Este artículo muestra cómo puede usar una plantilla de recursos para habilitar Configuración de diagnóstico al crear un recurso.](monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Habilitación de los registros de diagnóstico en el portal
Puede habilitar los registros de diagnóstico en Azure Portal al crear tipos de recursos de proceso al habilitar la extensión de Diagnósticos de Azure de Windows o Linux:

1. Vaya a **Nuevo** y elija el recurso que le interesa.
2. Después de establecer la configuración básica y seleccionar un tamaño, en la hoja **Configuración**, en **Supervisión**, seleccione **Habilitado** y elija una cuenta de almacenamiento donde almacenar los registros de diagnóstico. Cuando envíe diagnósticos a una cuenta de almacenamiento, se le cobra según las tarifas de datos normales relativas a almacenamiento y transacciones.

   ![Habilitar los registros de diagnóstico durante la creación de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3. Haga clic en **Aceptar** y cree el recurso.

Para los recursos que no son de proceso, puede habilitar los registros de diagnóstico en Azure Portal una vez creado un recurso de la forma siguiente:

1. Vaya a la hoja del recurso y abra la hoja **Diagnósticos** .
2. Haga clic en **Activado** y seleccione una cuenta de almacenamiento o un centro de eventos.

   ![Habilitar los registros de diagnóstico después de la creación de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. En **Registros**, seleccione qué **categorías de registro** desea recopilar o transmitir.
4. Haga clic en **Guardar**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Habilitación de los registros de diagnóstico en PowerShell
Para habilitar los registros de diagnóstico con cmdlets de Azure PowerShell, use los siguientes comandos.

Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

Para habilitar el streaming de registros de diagnóstico a un centro de eventos, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

El identificador de regla del Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Puede obtener el identificador de recurso de su área de trabajo de Log Analytics con el comando siguiente:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="enable-diagnostic-logs-via-cli"></a>Habilitación de los registros de diagnóstico en CLI
Para habilitar los registros de diagnóstico con la CLI de Azure, use los siguientes comandos:

Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

Para habilitar el streaming de registros de diagnóstico a un centro de eventos, use este comando:

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

El identificador de regla del Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Habilitación de los registros de diagnóstico en la API de REST
Para cambiar la configuración de diagnóstico con la API de REST de Azure Monitor, consulte [este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Administración de Configuración de diagnóstico en el portal
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

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Servicios admitidos y esquema para registros de diagnóstico
El esquema para los registros de diagnóstico varía según la categoría de registro y el recurso.   

| Servicio | Esquema y documentos |
| --- | --- |
| Load Balancer |[Log Analytics para Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Grupos de seguridad de red |[Análisis del registro para grupos de seguridad de red (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Puertas de enlace de aplicaciones |[Registro de diagnóstico para la Puerta de enlace de aplicaciones](../application-gateway/application-gateway-diagnostics.md) |
| Almacén de claves |[Registro del Almacén de claves de Azure](../key-vault/key-vault-logging.md) |
| Búsqueda de Azure |[Habilitación y uso de Análisis de tráfico de búsqueda](../search/search-traffic-analytics.md) |
| Almacén de Data Lake |[Acceso a los registros de diagnóstico de Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Análisis de Data Lake |[Acceso a los registros de diagnóstico de Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Aplicaciones lógicas |[Esquema de seguimiento personalizado de Logic Apps B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure Batch |[Registros de diagnósticos de Azure Batch](../batch/batch-diagnostics.md) |
| Automatización de Azure |[Log Analytics para Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Centros de eventos |[Registros de diagnóstico de Azure Event Hubs](../event-hubs/event-hubs-diagnostic-logs.md) |
| Stream Analytics |[Registros de diagnósticos de trabajos](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Bus de servicio |[Registros de diagnóstico de Azure Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md) |


## <a name="supported-log-categories-per-resource-type"></a>Categorías de registro admitidas por tipo de recurso
|Tipo de recurso|Categoría|Nombre para mostrar de categoría|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|Registros de trabajo|
|Microsoft.Automation/automationAccounts|JobStreams|Flujos de trabajo|
|Microsoft.Batch/batchAccounts|ServiceLog|Registros de servicios|
|Microsoft.DataLakeAnalytics/accounts|Auditoría|Registros de auditoría|
|Microsoft.DataLakeAnalytics/accounts|Solicitudes|Registros de solicitud|
|Microsoft.DataLakeStore/accounts|Auditoría|Registros de auditoría|
|Microsoft.DataLakeStore/accounts|Solicitudes|Registros de solicitud|
|Microsoft.EventHub/namespaces|ArchiveLogs|Registros de archivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Registros operativos|
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
|Microsoft.Search/searchServices|OperationLogs|Registros de operaciones|
|Microsoft.ServerManagement/nodes|RequestLogs|Registros de solicitud|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Registros operativos|
|Microsoft.StreamAnalytics/streamingjobs|Ejecución|Ejecución|
|Microsoft.StreamAnalytics/streamingjobs|Creación|Creación|

## <a name="next-steps"></a>Pasos siguientes

* [Transmisión de registros de diagnóstico de Azure a **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Cambio de la configuración de diagnóstico con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Análisis de registros desde Azure Storage con Log Analytics](../log-analytics/log-analytics-azure-storage.md)

