---
title: "Supervisión de copias de seguridad de máquinas virtuales implementadas por Resource Manager | Microsoft Docs"
description: "Supervise eventos y alertas de copias de seguridad de máquinas virtuales implementadas por Resource Manager. Envíe correo electrónico basado en alertas."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: b9dc3f52e5fc275bc56b9964f2115833f2dde42e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Supervisión de alertas de copias de seguridad de máquinas virtuales de Azure
Las alertas son las respuestas del servicio que indican que se ha alcanzado o superado un umbral de evento. Saber cuándo comenzaron los problemas puede ser vital para reducir los costos del negocio. Las alertas no se producen de forma programada por lo general, por lo que resulta útil conocerlas tan pronto como se produjeron. Por ejemplo, cuando se produce un error en un trabajo de copia de seguridad o de restauración, se produce una alerta en los cinco minutos siguientes al error. En el panel del almacén, el icono Alertas de copias de seguridad muestra eventos de nivel crítico y de advertencia. En la configuración de Alertas de copias de seguridad, puede ver todos los eventos. Pero, ¿qué hacer si se produce una alerta cuando está trabajando en otro asunto? Si no sabe cuándo se produce la alerta, esto puede ser un inconveniente secundario o puede llegar incluso a comprometer los datos. Para asegurarse de que las personas adecuadas se enteran de una alerta cuando esta se produce, configure el servicio para enviar notificaciones de alerta por correo electrónico. Para más información acerca de cómo configurar las notificaciones por correo electrónico, consulte [Configuración de notificaciones](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>¿Cómo se puede encontrar información sobre las alertas?
Para ver información sobre el evento que generó una alerta, debe abrir la hoja de Alertas de copias de seguridad. Hay dos maneras de abrir la hoja de Alertas de copias de seguridad: mediante el icono de Alertas de copias de seguridad del panel del almacén o mediante la hoja Alertas y eventos.

Para abrir la hoja Alertas de copias de seguridad desde el icono correspondiente:

* En el icono **Alertas de copias de seguridad** del panel del almacén, haga clic en **Crítico** o **Advertencia** para ver los eventos operativos con ese nivel de gravedad.

    ![Icono Alertas de copias de seguridad](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Para abrir la hoja Alertas de copias de seguridad desde la hoja Alertas y eventos:

1. En el panel del almacén, haga clic en **Toda la configuración**. ![Botón Toda la configuración](./media/backup-azure-monitor-vms/all-settings-button.png)
2. En la hoja **Configuración**, haga clic en **Alertas y eventos**. ![Botón Alertas y eventos](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. En la hoja **Alertas y eventos**, haga clic en **Alertas de copias de seguridad**. ![Botón Alertas de copias de seguridad](./media/backup-azure-monitor-vms/backup-alerts.png)

    La hoja **Alertas de copias de seguridad** se abre y muestra las alertas filtradas.

    ![Icono Alertas de copias de seguridad](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Para ver información detallada acerca de una alerta determinada, en la lista de eventos, haga clic en la alerta para abrir su hoja **Detalles** .

    ![Detalle de evento](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Para personalizar los atributos mostrados en la lista, consulte [Visualización de atributos adicionales de eventos](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Configuración de notificaciones
 Puede configurar el servicio para que envíe notificaciones de correo electrónico para las alertas que se produjeron durante la última hora o cuando se producen determinados tipos de eventos.

Configuración de notificaciones de correo electrónico para alertas

1. En el menú Alertas de copias de seguridad, haga clic en **Configurar notificaciones**

    ![Menú Alertas de copias de seguridad](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Se abre la hoja Configurar notificaciones.

    ![Hoja Configurar notificaciones](./media/backup-azure-monitor-vms/configure-notifications.png)
2. En la hoja Configurar notificaciones, para las notificaciones de correo electrónico, haga clic en **Activar**.

    Los cuadros de diálogo Destinatarios y Gravedad tienen un asterisco junto a ellos porque esa información es obligatoria. Proporcione al menos una dirección de correo electrónico y seleccione al menos un nivel de gravedad.
3. En el cuadro de diálogo **Destinatarios (correo electrónico)** , escriba las direcciones de correo electrónico de las personas que recibirán las notificaciones. Utilice el formato: username@domainname.com. Separe varias direcciones de correo electrónico con un signo de punto y coma (;).
4. En el área **Notificar** área, elija **Por alerta** para que se envíe una notificación cuando se produzca la alerta especificada, o **Resumen cada hora** para que se envíe un resumen de la última hora.
5. En el cuadro de diálogo **Gravedad** , elija los niveles que desee que desencadenen notificaciones de correo electrónico.
6. Haga clic en **Save**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Tipos de alerta disponibles para la copia de seguridad de máquinas virtuales de IaaS de Azure
   | Nivel de alerta | Alertas enviadas |
   | --- | --- |
   | Crítico |Error de copia de seguridad, error de recuperación |
   | Advertencia |None |
   | Informativo |None |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>¿Existen situaciones en las que no se envía ningún correo electrónico incluso si las notificaciones se configuran?
En efecto, hay situaciones en las que no se envía ninguna alerta aunque las notificaciones se hayan configurado correctamente. Para evitar ruido de alertas, no se envían notificaciones por correo electrónico en las situaciones siguientes:

* Si las notificaciones están configuradas mediante la opción Resumen cada hora y se genera una alerta y esta se resuelve en menos de una hora.
* Se canceló el trabajo.
* Se desencadena un trabajo de copia de seguridad y, a continuación, se produce un error y otro trabajo de copia de seguridad está en curso.
* Se inicia un trabajo de copia de seguridad programado para una máquina virtual habilitada para Resource Manager, pero ya no existe esta máquina virtual.

## <a name="customize-your-view-of-events"></a>Personalización de la vista de eventos
La configuración de **registros de auditoría** viene con un conjunto predefinido de filtros y columnas que muestran la información operativa del evento. Puede personalizar la vista para que cuando se abra la hoja **Eventos** se muestre la información que desee.

1. En el [panel del almacén](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), busque y haga clic en **Registros de auditoría** para abrir la hoja **Eventos**.

    ![Registros de auditoría](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    Se abre la hoja **Eventos** con los eventos operativos filtrados solo para el almacén actual.

    ![Filtro Registros de auditoría](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    En la hoja se muestra la lista de eventos críticos, con error, de advertencia e informativos que se produjeron la semana anterior. El intervalo es un valor predeterminado establecido en el **Filtro**. Además, en la hoja **Eventos** también se muestra un gráfico de barras que hace un seguimiento de cuándo se produjeron los eventos. Si no desea ver el gráfico de barras, en el menú **Eventos**, haga clic en **Ocultar gráfico** para desactivarlo. La vista predeterminada de Eventos muestra información de operación, nivel, estado, recursos y tiempo. Para más información acerca de cómo exponer atributos adicionales de eventos, consulte la sección [Expansión de la información de evento](backup-azure-monitor-vms.md#view-additional-event-attributes).
2. Para obtener información adicional sobre un evento operativo, en la columna **Operación**, haga clic en un evento operativo para abrir su hoja. La hoja contiene información detallada acerca de los eventos. Los eventos se agrupan por su Id. de correlación y según una lista de los eventos que se produjeron en el intervalo de tiempo.

    ![Detalles de la operación](./media/backup-azure-monitor-vms/audit-logs-details-window.png)
3. Para ver información detallada acerca de un evento determinado, en la lista de eventos, haga clic en el evento para abrir su hoja **Detalles** .

    ![Detalle de evento](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    La información de nivel de evento es la más detallada. Si prefiere ver esta cantidad de información sobre cada evento y desea agregar este nivel de detalle a la hoja **Eventos** , consulte la sección [Expansión de la información de evento](backup-azure-monitor-vms.md#view-additional-event-attributes).

## <a name="customize-the-event-filter"></a>Personalización del filtro de eventos
Utilice la opción **Filtrar** para ajustar o elegir la información que debe aparecer en una hoja determinada. Para filtrar la información del evento:

1. En el [panel del almacén](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), busque y haga clic en **Registros de auditoría** para abrir la hoja **Eventos**.

    ![Registros de auditoría](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    Se abre la hoja **Eventos** con los eventos operativos filtrados solo para el almacén actual.

    ![Filtro Registros de auditoría](./media/backup-azure-monitor-vms/audit-logs-filter.png)
2. En el menú **Eventos**, haga clic en **Filtrar** para abrir esa hoja.

    ![Hoja de filtro abierta](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)
3. En la hoja **Filtro**, ajuste los filtros **Nivel**, **Intervalo de tiempo** y **Llamador**. Los demás filtros no están disponibles porque se establecieron para proporcionar la información actual del almacén de Servicios de recuperación.

    ![Registros de auditoria: detalles de la consulta](./media/backup-azure-monitor-vms/filter-blade.png)

    Puede especificar un valor para **Nivel** de evento: Crítico, Error, Advertencia o Informativo. Puede elegir cualquier combinación de niveles de evento, pero debe tener al menos uno seleccionado. Active el nivel o desactívelo. El filtro **Intervalo de tiempo** permite especificar el intervalo para capturar eventos. Si usa un intervalo personalizado, puede establecer las horas de inicio y finalización.
4. Cuando esté listo para consultar los registros de operaciones mediante el filtro, haga clic en **Actualizar**. Los resultados se muestran en la hoja **Eventos** .

    ![Detalles de la operación](./media/backup-azure-monitor-vms/edited-list-of-events.png)

### <a name="view-additional-event-attributes"></a>Visualización de atributos adicionales de eventos
Mediante el botón **Columnas**, puede habilitar atributos adicionales de eventos para que aparezcan en la lista de la hoja **Eventos**. La lista predeterminada de eventos muestra información de operación, nivel, estado, recursos y tiempo. Cómo habilitar atributos adicionales:

1. En la hoja **Eventos**, haga clic en **Columnas**.

    ![Abrir columnas](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    Se abre la hoja **Elegir columnas** .

    ![Hoja Columnas](./media/backup-azure-monitor-vms/columns-blade.png)
2. Para seleccionar el atributo, haga clic en la casilla. La casilla del atributo se activa y se desactiva.
3. Haga clic en **Restablecer** para restablecer la lista de atributos de la hoja **Eventos**. Después de agregar o quitar los atributos de la lista, use **Restablecer** para ver la nueva lista de atributos de eventos.
4. Haga clic en **Actualizar** para actualizar los datos de los atributos de los eventos. La tabla siguiente proporciona información acerca de cada atributo.

| Nombre de la columna | Description |
| --- | --- |
| Operación |El nombre de la operación |
| Nivel |El nivel de la operación, los valores pueden ser: informativo, advertencia, error o crítico |
| Estado |Estado descriptivo de la operación |
| Recurso |Dirección URL que identifica el recurso. También conocido como el identificador de recurso |
| Hora |Hora, medida desde la hora actual, en la que se produjo el evento |
| Autor de llamada |Quién o qué llamó o desencadenó el evento. Puede ser el sistema o un usuario |
| Timestamp |La hora en la que se desencadenó el evento |
| El grupos de recursos |El grupo de recursos asociado |
| Tipo de recurso |El tipo de recurso interno utilizado por Resource Manager |
| Id. de suscripción |El identificador de suscripción asociado |
| Categoría |Categoría del evento |
| Id. de correlación |Identificador común para eventos relacionados |

## <a name="use-powershell-to-customize-alerts"></a>Uso de PowerShell para personalizar las alertas
Puede obtener notificaciones de alerta personalizadas para los trabajos del portal. Para obtener estos trabajos, defina reglas de alerta con PowerShell en los eventos de registros operativos. Use *PowerShell versión 1.3.0 o posterior*.

Para definir una notificación personalizada que avise de errores de copia de seguridad, use un comando como el siguiente script:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.RecoveryServices/recoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/Microsoft.RecoveryServices/vaults/trinadhVault -Actions $actionEmail
```

**ResourceId** : puede obtener este ResourceId de los registros de auditoría. ResourceId es una dirección URL que se proporciona en la columna de recursos de los registros de operaciones.

**OperationName**: OperationName está en el formato "Microsoft.RecoveryServices/recoveryServicesVault/*EventName*" donde *EventName* puede ser:<br/>

* Registrar <br/>
* Unregister  <br/>
* ConfigureProtection  <br/>
* Backup  <br/>
* Restore  <br/>
* StopProtection  <br/>
* DeleteBackupData  <br/>
* CreateProtectionPolicy  <br/>
* DeleteProtectionPolicy  <br/>
* UpdateProtectionPolicy  <br/>

**Status** : los valores admitidos son: Started, Succeeded o Failed.

**ResourceGroup** : es el grupo de recursos al que pertenece el recurso. Puede agregar la columna de grupo de recursos a los registros generados. El grupo de recursos es uno de los tipos de información de evento disponibles.

**Name** : nombre de la regla de alerta.

**CustomEmail** : especifique la dirección de correo electrónico personalizada a la que desea enviar una notificación de alerta

**SendToServiceOwners** : esta opción envía notificaciones de alerta a todos los administradores y coadministradores de la suscripción. Se puede utilizar en el cmdlet **New-AzureRmAlertRuleEmail** .

### <a name="limitations-on-alerts"></a>Limitaciones de las alertas
Las alertas basadas en eventos están sometidas a las siguientes limitaciones:

1. Las alertas se activan en todas las máquinas virtuales del almacén de Servicios de recuperación. No se puede personalizar la alerta para un subconjunto de máquinas virtuales en un almacén de Servicios de recuperación.
2. Esta característica se encuentra en versión preliminar. [Más información](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Las alertas se envían desde "alerts-noreply@mail.windowsazure.com". Actualmente, no se puede modificar el remitente de correo electrónico.

## <a name="next-steps"></a>Pasos siguientes
Los registros de eventos permiten excelentes análisis finales y soportes técnicos de auditoría para las operaciones de copia de seguridad. Se registran las siguientes operaciones:

* Register 
* Unregister 
* Configuración de protección
* Copia de seguridad (tanto programada como a petición)
* Restore 
* Detener protección
* Eliminación de datos de copia de seguridad
* Add policy
* Eliminación de directiva
* Actualización de directiva
* Cancelar trabajo

Para obtener una explicación detallada acerca de los eventos, las operaciones y los registros de auditoría a través de los servicios de Azure, consulte el artículo [Visualización de eventos y registros de auditoría](../monitoring-and-diagnostics/insights-debugging-with-events.md).

Para información sobre cómo volver a crear una máquina virtual a partir de un punto de recuperación, consulte [Restauración de máquinas virtuales en Azure](backup-azure-restore-vms.md). Si necesita información sobre la protección de las máquinas virtuales, consulte [Primer análisis: copia de seguridad de máquinas virtuales con ARM en un almacén de Servicios de recuperación](backup-azure-vms-first-look-arm.md). Obtenga información acerca de las tareas de administración para las copias de seguridad de máquinas virtuales en el artículo [Administración de copias de seguridad de máquinas virtuales de Azure](backup-azure-manage-vms.md).
