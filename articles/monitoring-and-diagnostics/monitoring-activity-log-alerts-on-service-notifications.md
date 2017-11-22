---
title: "Recepción de alertas de registro de actividad en las notificaciones del servicio de Azure | Microsoft Docs"
description: "Reciba notificaciones por SMS, correo electrónico o webhook cuando se produzcan eventos en el servicio de Azure."
author: johnkemnetz
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: 6e011ea3d9d8f8453068d43e390cfba46dfb3277
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Creación de alertas del registro de actividad en notificaciones del servicio
## <a name="overview"></a>Información general
En este artículo se explica cómo configurar las alertas del registro de actividad para las notificaciones de mantenimiento de un servicio mediante Azure Portal.  

Puede recibir una alerta cuando Azure envía notificaciones de estado del servicio a la suscripción de Azure. Puede configurar la alerta en función de:

- La clase de notificación de estado del servicio (problemas de servicio, mantenimiento planificado y avisos de estado).
- La suscripción afectada.
- Los servicios afectados.
- Las regiones afectadas.

También puede configurar a quién se debe enviar la alerta:

- Seleccione un grupo de acciones existente.
- Cree un nuevo grupo de acciones (que puede usarse para futuras alertas).

Para más información sobre los grupos de acciones, consulte [Creación y administración de grupos de acciones](monitoring-action-groups.md).

Para obtener información sobre cómo configurar las alertas de notificación de mantenimiento del servicio mediante plantillas de Azure Resource Manager, consulte [Plantillas de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Creación de una alerta basada en una notificación de mantenimiento del servicio para un nuevo grupo de acciones con Azure Portal
1. En el [portal](https://portal.azure.com), seleccione **Estado del servicio**.

    ![El servicio "Estado del servicio"](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. En la sección **Alertas**, seleccione **Alertas de estado**.

    ![La pestaña "Alertas de estado"](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Seleccione **Crear alerta de estado de servicio** y rellene los campos.

    ![El comando "Crear alerta de estado de servicio"](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Escriba un nombre en el cuadro de texto **Nombre de alerta de registro de actividad** y proporcione una **Descripción**.

    ![Cuadro de diálogo "Agregar alerta de registro de actividad"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group-sh.png)

5. El cuadro de texto **Suscripción** se rellena automáticamente con la suscripción actual. Esta suscripción se usa para guardar la alerta del registro de actividad. El recurso de la alerta se implementa en esta suscripción y supervisa los eventos en el registro de actividad para dicho recurso.

6. Seleccione el **Grupo de recursos** en el que se crea el recurso de la alerta. Este no es el grupo de recursos que está supervisado por la alerta. En su lugar, es el grupo de recursos donde se encuentra el recurso de la alerta.

7. El cuadro **Categoría de eventos** se establece automáticamente en **Estado del servicio**. Si lo desea, seleccione los valores de **Servicio**, **Región** y **Tipo** de notificaciones de estado del servicio que desee recibir.

8. En **Alertar mediante**, seleccione el botón de grupo de acciones **Nuevo**. Escriba un nombre en el cuadro de texto **Nombre del grupo de acciones** y especifique un nombre en el cuadro de texto **Nombre corto**. Se hace referencia al nombre corto en las notificaciones que se envían cuando se desencadena esta alerta.

9. A continuación, defina una lista de destinatarios proporcionando:

    a. **Nombre**: el nombre, alias o identificador del destinatario.

    b. **Tipo de acción**: seleccione SMS, correo electrónico, webhook, aplicación de Azure, etc.

    c. **Detalles**: según el tipo de acción elegido, proporcione un número de teléfono, una dirección de correo electrónico, un identificador URI de webhook, etc.

10. Seleccione **Aceptar** para crear la alerta.

En unos minutos, la alerta está activa y comienza a desencadenarse en función de las condiciones especificadas durante la creación.

Obtenga información acerca de cómo [configurar notificaciones de webhook para los sistemas de administración de problemas existentes](../service-health/service-health-alert-webhook-guide.md). Para obtener información sobre el esquema de webhook para las alertas del registro de actividad, consulte [Webhooks para alertas del registro de actividad de Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>El grupo de acciones definido en estos pasos es reutilizable, como grupo de acciones existente, en todas las futuras definiciones de alertas.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Creación de una alerta basada en una notificación de mantenimiento del servicio para un grupo de acciones existente con Azure Portal

1. Siga los pasos del 1 al 7 de la sección anterior para crear la notificación de mantenimiento del servicio. 

2. En **Alertar mediante**, seleccione el botón de grupo de acciones **Existente**. Seleccione el grupo adecuado.

3. Seleccione **Aceptar** para crear la alerta.

En unos minutos, la alerta está activa y comienza a desencadenarse en función de las condiciones especificadas durante la creación.

## <a name="manage-your-alerts"></a>Administración de alertas

Después de crear una alerta, es visible en la sección **Alertas** de **Supervisión**. Seleccione la alerta que desea administrar para:

* Editarla.
* Eliminarla.
* Deshabilitarla o habilitarla, si desea detener temporalmente o reanudar la recepción de notificaciones de la alerta.

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información acerca de cómo [configurar notificaciones de webhook para los sistemas de administración de problemas existentes](../service-health/service-health-alert-webhook-guide.md).
- Más información acerca de las [Notificaciones del estado del servicio](monitoring-service-notifications.md).
- Más información sobre la [Limitación del número de notificaciones](monitoring-alerts-rate-limiting.md).
- Revise el [Esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md).
- Consulte la [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprenda cómo puede recibir alertas. 
- Más información sobre los [grupos de acciones](monitoring-action-groups.md).
