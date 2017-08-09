---
title: "Creación de alertas de registro de actividad | Microsoft Docs"
description: "Reciba notificaciones por SMS, webhook y correo electrónico cuando se produzcan determinados eventos en el registro de actividad."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 709e20bc6f0d32509f2cdd4c5a9e69021238dba0
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="create-activity-log-alerts"></a>Creación de alertas de registro de actividad

## <a name="overview"></a>Información general
Las alertas de registro de actividad son alertas que se activan cuando un nuevo evento de registro de actividad cumple las condiciones especificadas en la alerta. Son recursos de Azure por lo que se pueden administrar mediante Azure Portal o Azure Resource Manager (ARM). En este artículo se explica cómo usar Azure Portal para configurar alertas en eventos de registro de actividad.

Puede recibir alertas sobre operaciones que se realizaron en los recursos de la suscripción o acerca de eventos de estado del servicio que pueden afectar al estado de los recursos. Una alerta de registro de actividad permite supervisar los eventos del registro de actividad de la suscripción específica en la que está implementada la alerta.

Puede configurar la alerta en función de:
* Categoría de eventos (para [eventos de estado de servicio haga clic aquí](monitoring-activity-log-alerts-on-service-notifications.md))
- Grupo de recursos
- Recurso
- Tipo de recurso
- Nombre de la operación
- El nivel de las notificaciones (detallado, informativo, advertencia, error, crítico)
- Status
- Evento iniciado por

>[!NOTE]
>Debe especificar al menos uno de los criterios anteriores en la alerta. No puede crear una alerta que se activa cada vez que se crea un evento en los registros de actividad.
>
>

También puede configurar las acciones que se ejecutarán cuando se activa se active:
* Seleccione un grupo de acciones existente
- Cree un nuevo grupo de acciones (que puede usarse posteriormente para futuras alertas)

Puede aprender más sobre los grupos de acciones [aquí](monitoring-action-groups.md)

[Aquí](monitoring-activity-log-alerts-on-service-notifications.md) puede obtener información acerca de las alertas de registro de actividad para la notificación de estado de servicio.

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Creación de una alerta para un evento de registro de actividad con un nuevo grupo de acciones con Azure Portal
1.  En el [portal](https://portal.azure.com), navegue hasta el servicio **Monitor**.

    ![Supervisión](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Haga clic en la opción **Monitor** para abrir la hoja del mismo nombre. Primero se abre la sección **Registro de actividades** .

3.  Ahora haga clic en la sección **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Seleccione el comando **Agregar alerta de registro de actividad** y rellene los campos

5.  Asigne un **nombre** a la alerta del registro de actividad y elija una **descripción**.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  La **suscripción** es aquella en la que se guardará la alerta del registro de actividad. Se rellena automáticamente con la suscripción que usa actualmente. Se trata de la suscripción en la que se implementará y supervisará el recurso de alerta.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  Elija el **grupo de recursos** al que se asociará esta alerta en la **suscripción**.

8.  Proporcione los valores para **Categoría de eventos**, **Grupo de recursos**, **Recursos**, **Tipo de recursos**, **Nombre de la operación**, **Nivel**, **Estado** y **Evento iniciado por** para identificar los eventos que debe supervisar esta alerta.

>[!NOTE]
>Debe especificar al menos uno de los criterios anteriores en la alerta. No puede crear una alerta que se activa cada vez que se crea un evento en los registros de actividad.
>
>

9.  Cree un **nuevo** grupo de acciones asignándole un **nombre** y un **nombre corto**. Se hará referencia a este nombre corto en las notificaciones que se envían cuando se activa esta alerta.

10. A continuación, defina una lista de acciones proporcionando la siguiente información de la acción:

    a. **Nombre:** nombre, alias o identificador de la acción.

    b. **Tipo de acción:** elija el tipo de acción: SMS, Correo electrónico o Webhook

    c. **Detalles:** según el tipo de acción elegido, proporcione un número de teléfono, una dirección de correo electrónico o un identificador URI de webhook.

11. Seleccione **Aceptar** cuando termine para crear la alerta.

En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

Para más información sobre el esquema de webhook para las alertas de registro de actividad [haga clic aquí](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>El grupo de acciones definido en estos pasos será reutilizable, como grupo de acciones existente, para todas las futuras definiciones de alertas.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Creación de una alerta para un evento de registro de actividad de un grupo de acciones ya existente con Azure Portal
1.  En el [portal](https://portal.azure.com), navegue hasta el servicio **Monitor**.

    ![Supervisión](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Haga clic en la opción **Monitor** para abrir la hoja del mismo nombre. Primero se abre la sección **Registro de actividades** .

3.  Ahora haga clic en la sección **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Seleccione el comando **Agregar alerta de registro de actividad** y rellene los campos

5.  Asigne un **nombre** a la alerta del registro de actividad y elija una **descripción**. Esta información aparecerá en las notificaciones enviadas cuando se desencadene esta alerta.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  La **suscripción** es aquella en la que se guardará la alerta del registro de actividad. Se rellena automáticamente con la suscripción que usa actualmente. Se trata de la suscripción en la que se implementará y supervisará el recurso de alerta.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  Elija el **grupo de recursos** al que se asociará esta alerta en la **suscripción**.

8.  Proporcione los valores para **Categoría de eventos**, **Grupo de recursos**, **Recursos**, **Tipo de recursos**, **Nombre de la operación**, **Nivel**, **Estado** y **Evento iniciado por** para identificar los eventos a los que se debe aplicar esta alerta.

9.  Elija **Notificar mediante** un **Grupo de acciones existente**. Seleccione el grupo de acciones correspondiente.

10. Seleccione **Aceptar** cuando termine para crear la alerta.

En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

## <a name="managing-your-alerts"></a>Administración de las alertas

Una vez que haya creado una alerta, esta estará visible en la sección de alertas del servicio Monitor. Seleccione la alerta que desea administrar y podrá:
* **Editarla**.
* **Eliminarla**.
* **Deshabilitar** la alerta, si desea dejar de recibir notificaciones de esa alerta de manera temporal, o **habilitarla** si desea reanudar sus notificaciones.

## <a name="next-steps"></a>Pasos siguientes
- Obtener una [introducción a las alertas](monitoring-overview-alerts.md)
- Más información sobre la [limitación del número de notificaciones](monitoring-alerts-rate-limiting.md)
- Revisión del [esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md)
- Más información sobre los [grupos de acciones](monitoring-action-groups.md)  
- Más información acerca de [notificaciones de estado del servicio](monitoring-service-notifications.md)
- [Cree una alerta de registro de actividades para supervisar todas las operaciones del motor de escalado automático en su suscripción.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Cree una alerta de registro de actividades para supervisar todas las operaciones con errores de reducción y escalado horizontales automáticos en su suscripción.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

