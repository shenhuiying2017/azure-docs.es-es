---
title: "Recepción de alertas del registro de actividad en notificaciones del servicio | Microsoft Docs"
description: "Reciba notificaciones por SMS, correo electrónico o webhook cuando se produzcan eventos en el servicio de Azure."
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
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: bf6a98fd7e7e11764bef174f9efd0635fa7efe9a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Creación de alertas del registro de actividad en notificaciones del servicio
## <a name="overview"></a>Información general
En este artículo se explica cómo configurar las alertas del registro de actividad para las notificaciones de mantenimiento de un servicio mediante Azure Portal.  

Puede recibir una alerta cuando Azure envía notificaciones de estado del servicio a la suscripción de Azure. Puede configurar la alerta en función de:

- La clase de notificación de mantenimiento del servicio (incidente, mantenimiento, información, etc.).
- Los servicios afectados.
- Las regiones afectadas.
- El estado de la notificación (activa o resuelta).
- El nivel de las notificaciones (informativo, advertencia, error).

También puede configurar a quién se debe enviar la alerta:

- Seleccione un grupo de acciones existente.
- Cree un nuevo grupo de acciones (que puede usarse para futuras alertas).

Para más información sobre los grupos de acciones, consulte [Creación y administración de grupos de acciones](monitoring-action-groups.md).

Para obtener información sobre cómo configurar las alertas de notificación de mantenimiento del servicio mediante plantillas de Azure Resource Manager, consulte [Plantillas de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Creación de una alerta basada en una notificación de mantenimiento del servicio para un nuevo grupo de acciones con Azure Portal
1. En el [portal](https://portal.azure.com), seleccione **Monitor**.

    ![Servicio "Monitor"](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2. En la sección **Registro de actividad**, seleccione **Alertas**.

    ![Pestaña "Alertas"](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

3. Seleccione **Agregar alerta de registro de actividad** y rellene los campos.

    ![Comando "Agregar alerta de registro de actividad"](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

4. Escriba un nombre en el cuadro de texto **Nombre de alerta de registro de actividad** y proporcione una **Descripción**.

    ![Cuadro de diálogo "Agregar alerta de registro de actividad"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

5. El cuadro de texto **Suscripción** se rellena automáticamente con la suscripción actual. Esta suscripción se usa para guardar la alerta del registro de actividad. El recurso de la alerta se implementa en esta suscripción y supervisa los eventos en el registro de actividad para dicho recurso.

6. Seleccione el **Grupo de recursos** en el que se crea el recurso de la alerta. Este no es el grupo de recursos que está supervisado por la alerta. En su lugar, es el grupo de recursos donde se encuentra el recurso de la alerta.

7. En el cuadro de texto **Categoría de evento**, seleccione **Estado del servicio**. Si lo desea, seleccione los valores de **Servicio**, **Región**, **Tipo**, **Estado** y **Nivel** de notificaciones de mantenimiento del servicio que desea recibir.

8. En **Alertar mediante**, seleccione el botón de grupo de acciones **Nuevo**. Escriba un nombre en el cuadro de texto **Nombre del grupo de acciones** y especifique un nombre en el cuadro de texto **Nombre corto**. Se hace referencia al nombre corto en las notificaciones que se envían cuando se desencadena esta alerta.

9. A continuación, defina una lista de destinatarios proporcionando:

    a. **Nombre**: el nombre, alias o identificador del destinatario.

    b. **Tipo de acción**: seleccione SMS, correo electrónico o webhook.

    c. **Detalles**: según el tipo de acción elegido, proporcione un número de teléfono, una dirección de correo electrónico o un identificador URI de webhook.

10. Seleccione **Aceptar** para crear la alerta.

En unos minutos, la alerta está activa y comienza a desencadenarse en función de las condiciones especificadas durante la creación.

Para obtener información sobre el esquema de webhook para las alertas del registro de actividad, consulte [Webhooks para alertas del registro de actividad de Azure](monitoring-activity-log-alerts-webhook.md).

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

Después de crear una alerta, es visible en la sección **Alertas** de la hoja **Supervisión**. Seleccione la alerta que desea administrar para:

* Editarla.
* Eliminarla.
* Deshabilitarla o habilitarla, si desea detener temporalmente o reanudar la recepción de notificaciones de la alerta.

## <a name="next-steps"></a>Pasos siguientes
- Más información acerca de las [Notificaciones del estado del servicio](monitoring-service-notifications.md).
- Más información sobre la [Limitación del número de notificaciones](monitoring-alerts-rate-limiting.md).
- Revise el [Esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md).
- Consulte la [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprenda cómo puede recibir alertas. 
- Más información sobre los [grupos de acciones](monitoring-action-groups.md).
