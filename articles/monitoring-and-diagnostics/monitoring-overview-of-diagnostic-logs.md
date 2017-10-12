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
ms.date: 08/21/2017
ms.author: johnkem; magoedte
ms.openlocfilehash: d59abde29fc7b73a799e5bf3659b02f824b693de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Recopile y use los datos de registro provenientes de los recursos de Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Qué son los registros de diagnóstico de Azure
Los **registros de diagnóstico de nivel de recursos de Azure** son registros emitidos por un recurso que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. El contenido de estos registros varía según el tipo de recurso. Por ejemplo, los contadores de regla de grupo de seguridad de red y las auditorías de Key Vault son dos categorías de registros de recursos.

Lo registros de diagnóstico de nivel de recursos son distintos del [registro de actividad](monitoring-overview-activity-logs.md). El registro de actividad proporciona una visión general de las operaciones que se realizaron en los recursos de la suscripción con Resource Manager, por ejemplo, crear una máquina virtual o eliminar una aplicación de lógica. El registro de actividad es un registro de nivel de suscripción. Los registros de diagnóstico de nivel de recursos proporcionan una visión general de las operaciones realizadas dentro del mismo recurso, por ejemplo, obtener un secreto de un almacén de claves.

Los registros de diagnóstico de nivel de recursos también difieren de los registros de diagnóstico de nivel de sistema operativo invitado. Estos son los recopilados por un agente que se ejecuta dentro de una máquina virtual u otro tipo de recurso admitido. Los registros de diagnóstico de nivel de recursos no requieren ningún agente y capturan datos específicos de recurso de la plataforma Azure, mientras que los registros de diagnóstico de nivel de sistema operativo invitado capturan los datos desde el sistema operativo y las aplicaciones que se ejecutan en una máquina virtual.

No todos los recursos admiten el nuevo tipo de registros de diagnóstico de recursos que se describe aquí. En este artículo se incluye una sección en la que se muestran los tipos de recurso que admiten los nuevos registros de diagnóstico de nivel de recursos.

![Comparación de los registros de diagnóstico de recursos y otros tipos de registros ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Qué se puede hacer con los registros de diagnóstico de nivel de recursos
Estas son algunas de las cosas que puede hacer con los registros de diagnóstico de recursos:

![Ubicación lógica de los registros de diagnóstico de recursos](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Guardarlos en una [**cuenta de almacenamiento**](monitoring-archive-diagnostic-logs.md) para archivarlos o inspeccionarlos manualmente. Puede especificar el tiempo de retención (en días) usando la **configuración de diagnóstico de recursos**.
* [Transmitirlos a **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md) para la ingestión en un servicio de terceros o una solución de análisis personalizado como PowerBI.
* Analizarlos con [Log Analytics de OMS](../log-analytics/log-analytics-azure-storage.md)

Puede usar una cuenta de almacenamiento o un espacio de nombres de Event Hubs que no esté en la misma suscripción que el que emite los registros. El usuario que configura los ajustes debe tener el acceso de RBAC adecuado a ambas suscripciones.

## <a name="resource-diagnostic-settings"></a>Configuración de diagnóstico de recursos
Los registros de diagnóstico de recursos para recursos que no son de proceso se configuran mediante la configuración de diagnóstico de recursos. **Configuración de diagnóstico de recursos** para un control de recursos:

* Dónde se envían los registros de diagnóstico y las métricas (cuenta de almacenamiento, Event Hubs o Log Analytics de OMS).
* Qué categorías de registro se envían y si se envían también datos de métrica.
* Cuánto tiempo se debe conservar cada categoría de registro en una cuenta de almacenamiento
    - Una retención de cero días significa que los registros se conservan de forma indefinida. De lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2147483647.
    - Si se establecen directivas de retención, pero el almacenamiento de registros en una cuenta de almacenamiento está deshabilitado (por ejemplo, si solo se han seleccionado las opciones de Event Hubs u OMS), las directivas de retención no surten ningún efecto.
    - Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy.

Estas opciones se configuran con facilidad a través de la configuración de los diagnósticos para un recurso en Azure Portal, mediante los comandos de Azure PowerShell y de la CLI, o mediante la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Los registros de diagnóstico y las métricas de la capa de sistema operativo invitado de los recursos de proceso (por ejemplo, máquinas virtuales o Service Fabric) usan [un mecanismo diferente para la configuración y la selección de salidas](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Procedimientos para habilitar la recopilación de registros de diagnóstico de recursos
La recopilación de registros de diagnóstico de recursos se puede habilitar [como parte de la creación de un recurso en una plantilla de Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) o después de crear un recurso mediante la página del recurso en el portal. También puede habilitar la recolección en cualquier momento mediante comandos de Azure PowerShell o de la CLI, o con la API de REST de Azure Monitor.

> [!TIP]
> Es posible que estas instrucciones no se apliquen directamente a cada recurso. Consulte los vínculos de esquema al final de esta página para ver los pasos especiales que se pueden aplicar a determinados tipos de recursos.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Habilitación de la recopilación de registros de diagnóstico de recursos en el portal
Puede habilitar la recopilación de registros de diagnóstico de recursos en Azure Portal una vez creado un recurso yendo a un recurso concreto o a Azure Monitor. Para habilitar esta opción a través de Azure Monitor:

1. En [Azure Portal](http://portal.azure.com), desplácese a Azure Monitor y haga clic en **Configuración de diagnóstico**

    ![Sección de supervisión de Azure Monitor](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Si lo desea, filtre la lista por tipo de recurso o por grupo de recursos y, a continuación, haga clic en el recurso para el que desea establecer la configuración de diagnóstico.

3. Si no existe ninguna configuración en el recurso que ha seleccionado, se le pide que cree una. Haga clic en "Activar diagnóstico".

   ![Agregar configuración de diagnóstico: sin configuración actual](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   Si hay una configuración actual en el recurso, verá una lista de opciones ya configuradas en este recurso. Haga clic en "Agregar configuración de diagnóstico".

   ![Agregar configuración de diagnóstico: configuración actual](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Asigne un nombre a su configuración, active las casillas para cada destino al que le gustaría enviar datos y configure el recurso que se utiliza para cada uno. Además, puede establecer un número de días para conservar estos registros mediante los controles deslizantes de **Retención (días)** (solo aplicable al destino de la cuenta de almacenamiento). Con una retención de cero días, los registros se almacenan indefinidamente.
   
   ![Agregar configuración de diagnóstico: configuración actual](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)
    
4. Haga clic en **Guardar**.

Transcurridos unos instantes, la nueva configuración aparece en la lista de opciones para este recurso y los registros de diagnóstico se envían a los destinos especificados en cuanto se generan nuevos datos de eventos.

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
Asegúrese de que todos los recursos estén instalados con la configuración de diagnóstico. Vaya a **Monitor** en el portal y abra **Configuración de diagnóstico**.

![Hoja Registros de diagnóstico en el portal](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Puede que tenga que hacer clic en "More services" (Más servicios) para encontrar la sección Supervisión.

Aquí puede ver y filtrar todos los recursos que admiten la configuración de diagnósticos y comprobar si está habilitada para los diagnósticos. También puede explorar en profundidad para comprobar si hay varias opciones de configuración establecidas en un recurso y a qué cuenta de almacenamiento, espacio de nombres de Event Hubs o área de trabajo de Log Analytics se transmiten los datos.

![Resultados de los registros de diagnóstico en el portal](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Al agregar una opción de configuración de diagnóstico, se abre la vista Configuración de diagnóstico, donde puede habilitar, deshabilitar o modificar la configuración de diagnóstico para el recurso seleccionado.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>Servicios admitidos, categorías y esquemas para los registros de diagnóstico de recursos
[Consulte este artículo](monitoring-diagnostic-logs-schema.md) para obtener una lista completa de los servicios admitidos y las categorías de registro y los esquemas utilizados por esos servicios.

## <a name="next-steps"></a>Pasos siguientes

* [Transmisión de registros de diagnóstico a **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Cambio de la configuración de diagnóstico de recursos con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Análisis de registros desde Azure Storage con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
