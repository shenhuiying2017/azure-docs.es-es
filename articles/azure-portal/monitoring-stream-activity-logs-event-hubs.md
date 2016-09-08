<properties
	pageTitle="Transmisión del registro de actividad de Azure a centros de eventos | Microsoft Azure"
	description="Aprenda a transmitir el registro de actividad de Azure a centros de eventos."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="johnkem"/>

# Transmisión del registro de actividad de Azure a centros de eventos
El [**registro de actividad de Azure**](./monitoring-overview-activity-logs.md) se puede transmitir casi en tiempo real a cualquier aplicación mediante la opción "Exportar" integrada en el portal o habilitando el identificador de regla del Bus de servicio en un perfil de registro por medio de los cmdlets de Azure PowerShell o la CLI de Azure.

## Qué se puede hacer con el registro de actividad y los centros de eventos
Estas son solo algunas formas de usar la funcionalidad de streaming para el registro de actividad:

- **Transmisión a sistemas de registro y telemetría de terceros**: con el tiempo, el streaming de centros de eventos se convertirá en el mecanismo para canalizar el registro de actividad a sistemas de información de seguridad y administración de eventos (SIEM) y soluciones de análisis de registro de terceros.
- **Creación de una plataforma personalizada de registro y telemetría**: si ya tiene una plataforma de telemetría personalizada o está pensando en crear una, la gran escalabilidad en cuanto a la suscripción y la publicación de los centros de eventos permite introducir el registro de actividad de manera flexible. [Consulte la guía de Dan Rosanova para usar centros de eventos en una plataforma de telemetría de escala global aquí.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## Habilitación del streaming del registro de actividad
Puede habilitar el streaming del registro de actividad mediante programación o a través del portal. En cualquier caso, debe elegir un espacio de nombres del Bus de servicio (o crearlo, si no existe ninguno); el espacio de nombres seleccionado es el lugar donde se crea el centro de eventos (si es la primera vez que se transmite el registro de actividad) o a donde se transmite (si ya ha transmitido el registro de actividad a este espacio de nombres). La directiva define los permisos que tiene el mecanismo de streaming. En la actualidad, para transmitir a un centro de eventos, se necesitan los permisos de **administración**, **lectura** y **envío**. Puede crear o modificar las directivas de acceso compartido del espacio de nombres del Bus de servicio en el Portal clásico en la pestaña "Configurar" para el espacio de nombres del Bus de servicio. Para actualizar el perfil de registro del registro de actividad para incluir el streaming, el cliente debe tener el permiso ListKey en la regla de autorización del Bus de servicio.

### Mediante el Portal de Azure 
1. Vaya a la hoja de **registro de actividad** mediante el menú en el lado izquierdo del portal.

    ![Ir al registro de actividad en el portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Haga clic en el botón **Exportar** en la parte superior de la hoja.

    ![Botón Exportar en el portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. En la hoja que aparece, puede seleccionar las regiones para las que transmitir eventos y el espacio de nombres del Bus de servicio donde desea que se cree un centro de eventos para transmitirlos.

    ![Exportar en hoja de registro de actividad](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Haga clic en **Guardar** para guardar la configuración. La configuración se aplica inmediatamente a la suscripción.


### Mediante cmdlets de PowerShell
Si ya existe un perfil de registro, primero debe quitarlo.

1. Use `Get-AzureRmLogProfile` para identificar si existe un perfil de registro.
2. Si es así, use `Remove-AzureRmLogProfile` para quitarlo.
3. Use `Set-AzureRmLogProfile` para crear un perfil:

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

El identificador de regla del Bus de servicio es una cadena con este formato: por ejemplo, {Id. de recurso del Bus de servicio}/authorizationrules/{nombre de clave}.

### Mediante la CLI de Azure
Si ya existe un perfil de registro, primero debe quitarlo.

1. Use `azure insights logprofile list` para identificar si existe un perfil de registro.
2. Si es así, use `azure insights logprofile delete` para quitarlo.
3. Use `azure insights logprofile add` para crear un perfil:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

El identificador de regla del Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`.
 
## ¿Cómo se consumen los datos de registro procedentes de centros de eventos?
[El esquema para el registro de actividad está disponible aquí](./monitoring-overview-activity-logs.md). Cada evento está en una matriz de blobs JSON denominados "registros".

## Pasos siguientes
- [Archive the Activity Log to a storage account](./monitoring-archive-activity-log.md) (Archivado del registro de actividad en una cuenta de almacenamiento)
- [Lea la información general sobre el registro de actividad de Azure](./monitoring-overview-activity-logs.md)
- [Configure una alerta basada en un evento del registro de actividad](./insights-auditlog-to-webhook-email.md)

<!---HONumber=AcomDC_0824_2016-->