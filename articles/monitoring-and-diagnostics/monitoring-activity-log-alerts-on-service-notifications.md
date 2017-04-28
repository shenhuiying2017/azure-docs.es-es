---
title: "Recepción de alertas de registro de actividad en notificaciones del servicio | Microsoft Docs"
description: "Reciba notificaciones por SMS, correo electrónico o webhook cuando se produzca el servicio de Azure."
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 57e136c1027cfa7c789803409cef354f3d52d222
ms.lasthandoff: 03/31/2017


---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Creación de alertas de registro de actividad en notificaciones del servicio
## <a name="overview"></a>Información general
En este artículo se explica cómo configurar las alertas de registro de actividad para las notificaciones de estado de un servicio mediante Azure Portal.  

Puede recibir una alerta basada en las notificaciones de estado del servicio para la suscripción de Azure.  
Puede configurar la alerta en función de:
- La clase de notificación de estado del servicio (incidente, mantenimiento, información, etc.).
- El estado de la notificación (Activa o Resuelta)
- El nivel de las notificaciones (informativo, advertencia, error)

También puede configurar a quién se debe enviar la alerta:
- Seleccione un grupo de acciones existente
- Cree un nuevo grupo de acciones (que puede usarse posteriormente para futuras alertas)

Puede aprender más sobre los grupos de acciones [aquí](monitoring-action-groups.md)

Puede configurar y obtener información sobre las alertas de notificación de estado del servicio mediante
- [Portal de Azure](monitoring-activity-log-alerts-on-service-notifications.md)
- [Plantillas de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-with-the-azure-portal"></a>Creación de una alerta basada en una notificación de estado del servicio para un nuevo grupo de acciones con Azure Portal
1.    En el [portal](https://portal.azure.com), navegue hasta el servicio **Monitor**.

    ![Supervisión](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2.    Haga clic en la opción **Monitor** para abrir la hoja del mismo nombre. Primero se abre la sección **Registro de actividades** .

3.    Ahora haga clic en la sección **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

4.    Seleccione el comando **Agregar alerta de registro de actividad** y rellene los campos

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

5.    Asigne un **nombre** a la alerta del registro de actividad y elija una **descripción**. Esta información aparecerá en las notificaciones enviadas cuando se desencadene esta alerta.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

6.    El campo **Suscripción** aparecerá relleno automáticamente con la suscripción que está usando actualmente.

7.    Elija el **grupo de recursos** para esta alerta.

8.    En **Categoría de eventos** seleccione la opción "Estado del servicio". Elija qué **tipo, estado** y **nivel** de notificaciones de estado del servicio le gustaría que se le notificase.

9.    Cree un **nuevo** grupo de acciones asignándole un **nombre** y un **nombre corto**. Se hará referencia a este nombre corto en las notificaciones que se envían cuando se desencadena esta alerta.

10.    A continuación, defina una lista de destinatarios proporcionando

    a. **Nombre:** el nombre, alias o identificador del destinatario.

    b. **Tipo de acción:** elija ponerse en contacto con el destinatario a través de SMS, correo electrónico o Webhook

    c. **Detalles:** según el tipo de acción elegido, proporcione un número de teléfono, una dirección de correo electrónico o un identificador URI de webhook.

11.    Seleccione **Aceptar** cuando termine para crear la alerta.

En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

Para más información sobre el esquema de webhook para las alertas de registro de actividad [haga clic aquí](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>El grupo de acciones definido en estos pasos será reutilizable, como grupo de acciones existente, para todas las futuras definiciones de alertas.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-with-the-azure-portal"></a>Creación de una alerta basada en una notificación de estado del servicio para un grupo de acciones existente con Azure Portal
1.    En el [portal](https://portal.azure.com), navegue hasta el servicio **Monitor**.

    ![Supervisión](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)
2.    Haga clic en la opción **Monitor** para abrir la hoja del mismo nombre. Primero se abre la sección **Registro de actividades** .

3.    Ahora haga clic en la sección **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)
4.    Seleccione el comando **Agregar alerta de registro de actividad** y rellene los campos

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)
5.    Asigne un **nombre** a la alerta del registro de actividad y elija una **descripción**. Esta información aparecerá en las notificaciones enviadas cuando se desencadene esta alerta.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-existing-action-group.png)
6.    El campo **Suscripción** aparecerá relleno automáticamente con la suscripción que está usando actualmente.

7.    Elija el **grupo de recursos** para esta alerta.

8.    En **Categoría de eventos** seleccione la opción "Estado del servicio". Elija qué **tipo, estado** y **nivel** de notificaciones de estado del servicio le gustaría que se le notificase.

9.    Elija **Notificar mediante** un **Grupo de acciones existente**. Seleccione el grupo de acciones correspondiente.

10.    Seleccione **Aceptar** cuando termine para crear la alerta.

En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

## <a name="managing-your-alerts"></a>Administración de las alertas

Una vez que haya creado una alerta, esta estará visible en la sección de alertas del servicio Monitor. Seleccione la alerta que desea administrar y podrá:
* **Editarla**.
* **Eliminarla**.
* **Deshabilitar** la alerta, si desea dejar de recibir notificaciones de esa alerta de manera temporal, o **habilitarla** si desea reanudar sus notificaciones.

## <a name="next-steps"></a>Pasos siguientes:
Más información acerca de [notificaciones de estado del servicio](monitoring-service-notifications.md)  
Revisar el [esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md) Obtener una [introducción sobre las alertas de registro de actividad](monitoring-overview-alerts.md) y aprender a cómo recibir alertas  
Más información sobre los [grupos de acciones](monitoring-action-groups.md)

