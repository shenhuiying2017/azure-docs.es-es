---
title: Supervisión de alertas de copia de seguridad para máquinas virtuales de Azure | Microsoft Docs
description: Supervise eventos y alertas de los trabajos de copia de seguridad para máquinas virtuales de Azure. Envíe correo electrónico basado en alertas.
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: fbdce5c244d733a2978d473f01c8d875cbeaa65e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Supervisión de alertas de copias de seguridad de máquinas virtuales de Azure
Las alertas son las respuestas del servicio que indican que se ha alcanzado o superado un umbral de evento. Saber cuándo comenzaron los problemas puede ser vital para reducir los costos del negocio. Las alertas no se producen de forma programada por lo general, por lo que resulta útil conocerlas tan pronto como se produjeron. Por ejemplo, cuando se produce un error en un trabajo de copia de seguridad o de restauración, se produce una alerta en los cinco minutos siguientes al error. En el panel del almacén, el icono Alertas de copias de seguridad muestra eventos de nivel crítico y de advertencia. En la configuración de Alertas de copias de seguridad, puede ver todos los eventos. Pero, ¿qué hacer si se produce una alerta cuando está trabajando en otro asunto? Si no sabe cuándo se produce la alerta, esto puede ser un inconveniente secundario o puede llegar incluso a comprometer los datos. Para asegurarse de que las personas adecuadas se enteran de una alerta cuando esta se produce, configure el servicio para enviar notificaciones de alerta por correo electrónico. Para más información acerca de cómo configurar las notificaciones por correo electrónico, consulte [Configuración de notificaciones](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>¿Cómo se puede encontrar información sobre las alertas?
Para ver información sobre el evento que generó una alerta, debe abrir la sección Alertas de copias de seguridad. Hay dos maneras de abrir la sección Alertas de copias de seguridad: mediante el icono de Alertas de copias de seguridad del panel del almacén o mediante la sección Alertas y eventos.

Para abrir la hoja Alertas de copias de seguridad desde el icono correspondiente:

* En el icono **Alertas de copias de seguridad** del panel del almacén, haga clic en **Crítico** o **Advertencia** para ver los eventos operativos con ese nivel de gravedad.

    ![Icono Alertas de copias de seguridad](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Para abrir la hoja Alertas de copias de seguridad desde la sección Alertas y eventos:

1. En el panel del almacén, haga clic en **Toda la configuración**. ![Botón Toda la configuración](./media/backup-azure-monitor-vms/all-settings-button.png)
2. En la hoja **Configuración**, haga clic en **Alertas y eventos**. ![Botón Alertas y eventos](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. En la hoja **Alertas y eventos**, haga clic en **Alertas de copias de seguridad**. ![Botón Alertas de copias de seguridad](./media/backup-azure-monitor-vms/backup-alerts.png)

    La sección **Alertas de copias de seguridad** se abre y muestra las alertas filtradas.

    ![Icono Alertas de copias de seguridad](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Para ver información detallada sobre una alerta determinada, en la lista de eventos, haga clic en la alerta para abrir su sección **Detalles**.

    ![Detalle de evento](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Para personalizar los atributos mostrados en la lista, consulte [Visualización de atributos adicionales de eventos](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Configuración de notificaciones
 Puede configurar el servicio para que envíe notificaciones de correo electrónico para las alertas que se produjeron durante la última hora o cuando se producen determinados tipos de eventos.

Configuración de notificaciones de correo electrónico para alertas

1. En el menú Alertas de copias de seguridad, haga clic en **Configurar notificaciones**

    ![Menú Alertas de copias de seguridad](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Se abre la sección Configurar notificaciones.

    ![Hoja Configurar notificaciones](./media/backup-azure-monitor-vms/configure-notifications.png)
2. En la sección Configurar notificaciones, en Notificaciones de correo electrónico, haga clic en **Activar**.

    Los cuadros de diálogo Destinatarios y Gravedad tienen un asterisco junto a ellos porque esa información es obligatoria. Proporcione al menos una dirección de correo electrónico y seleccione al menos un nivel de gravedad.
3. En el cuadro de diálogo **Destinatarios (correo electrónico)** , escriba las direcciones de correo electrónico de las personas que recibirán las notificaciones. Utilice el formato: username@domainname.com. Separe varias direcciones de correo electrónico con un signo de punto y coma (;).
4. En el área **Notificar** área, elija **Por alerta** para que se envíe una notificación cuando se produzca la alerta especificada, o **Resumen cada hora** para que se envíe un resumen de la última hora.
5. En el cuadro de diálogo **Gravedad** , elija los niveles que desee que desencadenen notificaciones de correo electrónico.
6. Haga clic en **Save**(Guardar).

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Tipos de alerta disponibles para la copia de seguridad de máquinas virtuales de IaaS de Azure
   | Nivel de alerta | Alertas enviadas |
   | --- | --- |
   | Crítico | Error de copia de seguridad, error de recuperación |
   | Warning (Advertencia) | Para trabajos de copia de seguridad realizados correctamente con advertencias (por ejemplo, se produjo un error en algunos escritores al crear una instantánea) |
   | Informativo | Actualmente, no hay alertas informativas disponibles para la copia de seguridad de máquinas virtuales de Azure |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>¿Existen situaciones en las que no se envía ningún correo electrónico incluso si las notificaciones se configuran?
En efecto, hay situaciones en las que no se envía ninguna alerta aunque las notificaciones se hayan configurado correctamente. Para evitar ruido de alertas, no se envían notificaciones por correo electrónico en las situaciones siguientes:

* Si las notificaciones están configuradas mediante la opción Resumen cada hora y se genera una alerta y esta se resuelve en menos de una hora.
* Se canceló el trabajo.
* Se desencadena un trabajo de copia de seguridad y, a continuación, se produce un error y otro trabajo de copia de seguridad está en curso.
* Se inicia un trabajo de copia de seguridad programado para una máquina virtual habilitada para Resource Manager, pero ya no existe esta máquina virtual.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Uso de registros de actividad para obtener notificaciones de copias de seguridad correctas

Si desea que se le notifique que las copias de seguridad se han realizado correctamente, puede usar las alertas creadas en los [registros de actividad](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) del almacén.

### <a name="login-into-azure-portal"></a>Inicio de sesión en Azure Portal
Inicie sesión en Azure Portal y vaya al almacén de Azure Recovery Services que le interese. Haga clic en la sección "Registro de actividad" en las propiedades.

### <a name="identify-appropriate-log"></a>Identificación del registro apropiado

Aplique los filtros que se muestran en la ilustración siguiente para comprobar si va a recibir registros de actividad por las copias de seguridad correctas. Cambie el intervalo de tiempo en consecuencia para ver los registros.

![Registros de actividad](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Puede hacer clic en el segmento "JSON" para obtener más detalles; para verlo, cópielo y péguelo en un editor de texto. Se mostrarán los detalles del almacén y el elemento que desencadenó el registro de actividad, es decir, el elemento de copia de seguridad.

A continuación, haga clic en "Agregar alerta de registro de actividad" para generar alertas para todos los registros de este tipo.

### <a name="add-activity-log-alert"></a>Adición de alertas de registro de actividad

Al hacer clic en "Agregar alerta de registro de actividad" se muestra una pantalla, tal y como se ilustra a continuación:

![Alerta de registro de actividad](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png)
    
La suscripción y el grupo de recursos se usan para almacenar la alerta. Los criterios se rellenan previamente. Asegúrese de que todos los valores son pertinentes para sus necesidades.

Cuando las copias de seguridad son correctas, el nivel está marcado como "Informativo" y el estado como "Correcto".

Si selecciona el recurso de la imagen anterior, cuando los registros de actividad se anoten para ese recurso o almacén, se generará una alerta. Si quiere que la regla se aplique a todos los almacenes, deje el recurso vacío.

### <a name="define-action-on-alert-firing"></a>Definición de la acción tras activarse la alerta

Use el grupo de recursos para definir la acción tras generar una alerta. Puede hacer clic en "Tipo de acción" para saber más sobre las acciones disponibles, como correo electrónico, SMS o integración con ITSM, etc.

![Grupo de acciones del registro de actividad](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)


Una vez que se hace clic en Aceptar, se genera una alerta de registro de actividad y los sucesivos registros de actividad anotados por copias de seguridad correctas activan la acción definida en el grupo de acciones.

### <a name="limitations-on-alerts"></a>Limitaciones de las alertas
Las alertas basadas en eventos están sometidas a las siguientes limitaciones:

1. Las alertas se activan en todas las máquinas virtuales del almacén de Recovery Services. No se puede personalizar la alerta para un subconjunto de máquinas virtuales en un almacén de Recovery Services.
2. Las alertas se envían desde "alerts-noreply@mail.windowsazure.com". Actualmente, no se puede modificar el remitente de correo electrónico.

## <a name="next-steps"></a>Pasos siguientes
Para información sobre cómo volver a crear una máquina virtual a partir de un punto de recuperación, consulte [Restauración de máquinas virtuales en Azure](backup-azure-arm-restore-vms.md).

Si necesita información sobre la protección de las máquinas virtuales, consulte [Primer análisis: copia de seguridad de máquinas virtuales con ARM en un almacén de Recovery Services](backup-azure-vms-first-look-arm.md). 

Aprenda más sobre las tareas de administración de las copias de seguridad de máquina virtual en el artículo [Administración de copias de seguridad de máquinas virtuales de Azure](backup-azure-manage-vms.md).
