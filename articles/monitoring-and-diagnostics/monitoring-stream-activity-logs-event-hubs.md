---
title: Transmisión del registro de actividades de Azure a Event Hubs | Microsoft Docs
description: Aprenda a transmitir el registro de actividad de Azure a Event Hubs.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: johnkem
ms.openlocfilehash: 4b2d9866839f943f65beb271d44bc691441b0fb3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmisión del registro de actividad de Azure a Event Hubs
Puede transmitir el [registro de actividad de Azure](monitoring-overview-activity-logs.md) casi en tiempo real a cualquier replicación de dos maneras:

* Mediante la opción **Exportar** integrada del portal
* Mediante la habilitación del identificador de regla de Azure Service Bus en un perfil de registro por medio de los cmdlets de Azure PowerShell o de la CLI de Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Qué se puede hacer con el registro de actividad y Event Hubs
Estas son dos maneras en que puede usar la funcionalidad de streaming para el registro de actividad:

* **Transmisión a sistemas de registro y telemetría de terceros**: con el tiempo, el streaming de Azure Event Hubs se convertirá en el mecanismo para canalizar el registro de actividad a sistemas de información de seguridad y administración de eventos (SIEM) y soluciones de análisis de registro de terceros.
* **Creación de una plataforma personalizada de registro y telemetría**: si ya tiene una plataforma de telemetría integrada personalizada o está pensando en crear una, la gran escalabilidad en cuanto a suscripción y publicación de Event Hubs permite ingerir el registro de actividad de manera flexible. Para más información, consulte el [vídeo de Dan Rosanova sobre el uso de Event Hubs en una plataforma de telemetría a escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Habilitación del streaming del registro de actividad
Puede habilitar el streaming del registro de actividad mediante programación o a través del portal. En cualquier caso, seleccionará un espacio de nombres de Event Hubs y una directiva de acceso compartido para ese espacio de nombres. Se crea un centro de eventos con el nombre insights-logs-operationallogs en dicho espacio de nombres cuando se produce el primer evento nuevo de registro de actividad. 

Si no tiene un espacio de nombres de Event Hubs, primero debe crear uno. Si anteriormente se han transmitido eventos del registro de actividad a este espacio de nombres de Event Hubs, se reutiliza el centro de eventos que se creó anteriormente. 

La directiva de acceso compartido define los permisos que tiene el mecanismo de transmisión. En la actualidad, para transmitir a Event Hubs, se necesitan permisos de **administración**, **envío** y **escucha**. Puede crear o modificar directivas de acceso compartido para el espacio de nombres de Event Hubs en Azure Portal, en la pestaña **Configurar**. 

Para actualizar el perfil de registro del registro de actividad para incluir el streaming, el cliente que realiza el cambio debe tener el permiso ListKey sobre esa regla de autorización de Event Hubs. El espacio de nombres de Event Hubs no tiene que estar en la misma suscripción que la suscripción que emite los registros, siempre que el usuario que configura el ajuste tenga acceso RBAC adecuado a ambas suscripciones.

### <a name="via-the-azure-portal"></a>Mediante Azure Portal
1. Vaya a la sección **Registro de actividad** mediante la búsqueda de **Todos los servicios** en el lado izquierdo del portal.
   
   ![Selección del registro de actividad en la lista de servicios del portal](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Haga clic en el botón **Exportar** en la parte superior del registro.
   
   ![Botón Exportar en el portal](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Tenga en cuenta que la configuración de filtro que había aplicado mientras veía el registro de actividad en la vista anterior no tiene ningún efecto sobre la configuración de exportación. Solo es para filtrar lo que ve mientras explora el registro de actividad en el portal.
3. En la sección que aparece, seleccione **Todas las regiones**. No seleccione regiones específicas.
   
   ![Sección Exportar](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Si selecciona un valor distinto de **Todas las regiones**, se pasarán por alto eventos clave que espera recibir. El registro de actividad es un registro global (no regional), así que la mayoría de los eventos no tienen asociada una región. 
   > 

4. Haga clic en **Guardar** para guardar esta configuración. La configuración se aplica inmediatamente a la suscripción.
5. Si tiene varias suscripciones, repita esta acción y envíe todos los datos al mismo centro de eventos.

### <a name="via-powershell-cmdlets"></a>Mediante cmdlets de PowerShell
Si ya existe un perfil de registro, primero debe quitarlo.

1. Use `Get-AzureRmLogProfile` para identificar si existe un perfil de registro.
2. Si es así, use `Remove-AzureRmLogProfile` para quitarlo.
3. Use `Set-AzureRmLogProfile` para crear un perfil:

   ```powershell

   Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

   ```

El identificador de regla de Service Bus es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`. 

### <a name="via-azure-cli"></a>Mediante la CLI de Azure
Si ya existe un perfil de registro, primero debe quitarlo.

1. Use `azure insights logprofile list` para identificar si existe un perfil de registro.
2. Si es así, use `azure insights logprofile delete` para quitarlo.
3. Use `azure insights logprofile add` para crear un perfil:

   ```azurecli-interactive
   azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
   ```

El identificador de regla de Service Bus es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="consume-the-log-data-from-event-hubs"></a>Consumo de los datos de registro de Event Hubs
El esquema para el registro de actividad está disponible en [Supervisión de la actividad de suscripción con el registro de actividad de Azure](monitoring-overview-activity-logs.md). Cada evento está en una matriz de blobs JSON denominados *registros*.

## <a name="next-steps"></a>Pasos siguientes
* [(Archivado del registro de actividades en una cuenta de almacenamiento)](monitoring-archive-activity-log.md)
* [Lea la información general sobre el registro de actividades de Azure](monitoring-overview-activity-logs.md)
* [Configure una alerta basada en un evento del registro de actividades](insights-auditlog-to-webhook-email.md)

