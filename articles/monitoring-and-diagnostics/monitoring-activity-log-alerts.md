---
title: "Creación de alertas del registro de actividad | Microsoft Docs"
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
ms.date: 08/03/2017
ms.author: johnkem
ms.openlocfilehash: 3885469ec0e1fcc31386dd0ad7fe6cb5d03ab28e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-activity-log-alerts"></a>Creación de alertas del registro de actividad

## <a name="overview"></a>Información general
Las alertas del registro de actividad son alertas que se activan cuando un nuevo evento del registro de actividad cumple las condiciones especificadas en la alerta. Son recursos de Azure, por lo que pueden crearse con una plantilla de Azure Resource Manager. También se pueden crear, actualizar o eliminar en Azure Portal. Este artículo presenta los conceptos relativos a las alertas del registro de actividad. Seguidamente, se explica cómo usar Azure Portal para configurar alertas en eventos del registro de actividad.

Por lo general, se crean alertas del registro de actividad para recibir notificaciones cuando:

* Se produzcan cambios específicos en los recursos de la suscripción de Azure, que abarcan normalmente grupos de recursos o recursos en particular. Por ejemplo, si quiere que le notifiquen cuando se elimine alguna máquina virtual de myProductionResourceGroup. O, podría querer recibir una notificación si se asigna algún rol nuevo a un usuario de la suscripción.
* Se produce un evento de mantenimiento del servicio. Los eventos de mantenimiento del servicio incluyen la notificación de incidentes y eventos de mantenimiento que se aplican a recursos de la suscripción.

En cualquier caso, una alerta del registro de actividad solo supervisa eventos de la suscripción en la que se ha creado la alerta.

Puede configurar una alerta del registro de actividad según las propiedades de nivel superior del objeto JSON de un evento del registro de actividad. Sin embargo, el portal muestra las opciones más comunes:

- **Categoría**: Administración, Mantenimiento del servicio, Escalado automático y Recomendación. Para más información, consulte [Información general sobre el registro de actividad de Azure](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Para más información acerca de los eventos de mantenimiento del servicio, consulte [Recibir alertas del registro de actividad con las notificaciones del servicio](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Grupos de recursos**
- **Recurso**
- **Tipo de recurso**
- **Nombre de la operación**: el nombre de la operación de control de acceso basado en rol de Resource Manager.
- **Nivel**: el nivel de gravedad del evento (detallado, informativo, advertencia, error o crítico).
- **Estado**: el estado del evento, normalmente Iniciado, Error o Correcto.
- **Evento iniciado por**: también se conoce como el "llamador." La dirección de correo electrónico o el identificador de Azure Active Directory del usuario que realizó la operación.

>[!NOTE]
>Debe especificar al menos dos de los criterios anteriores en la alerta, siendo uno de ellos la categoría. No puede crear una alerta que se active cada vez que se crea un evento en los registros de actividad.
>
>

Cuando se activa una alerta del registro de actividad, usa un grupo de acciones para generar acciones o notificaciones. Un grupo de acciones es un conjunto reutilizable de destinatarios de notificación, como direcciones de correo electrónico, direcciones URL del webhook o números de teléfono para SMS. Se puede hacer referencia a los receptores desde varias alertas para centralizar y agrupar los canales de notificación. Al definir la alerta del registro de actividad, tiene dos opciones. Puede:

* Usar un grupo de acciones existente en la alerta del registro de actividad. 
* Crear un nuevo grupo de acciones. 

Para más información sobre los grupos de acciones, consulte [Creación y administración de grupos de acciones en Azure Portal](monitoring-action-groups.md).

Para más información acerca de las notificaciones de mantenimiento del servicio, consulte [Recibir alertas del registro de actividad con las notificaciones de mantenimiento del servicio](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Creación de una alerta para un evento del registro de actividad con un nuevo grupo de acciones mediante Azure Portal
1. En el [portal](https://portal.azure.com), seleccione **Monitor**.

    ![Servicio "Monitor"](./media/monitoring-activity-log-alerts/home-monitor.png)
2. En la sección **Registro de actividad**, seleccione **Alertas**.

    ![Pestaña "Alertas"](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. Seleccione **Agregar alerta de registro de actividad** y rellene los campos.

4. Escriba un nombre en el cuadro de texto **Nombre de alerta del registro de actividad** y seleccione una **Descripción**.

    ![Comando "Agregar alerta de registro de actividad"](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. El cuadro de texto **Suscripción** se rellena automáticamente con la suscripción actual. La suscripción es aquella en la que se guarda el grupo de acciones. Esta es la suscripción en la que se implementa el recurso de alerta y desde la que se supervisan los eventos del registro de actividad.

    ![Cuadro de diálogo "Agregar alerta de registro de actividad"](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. Seleccione el **Grupo de recursos** en el que se crea el recurso de la alerta. Este no es el grupo de recursos supervisado por la alerta. En su lugar, es el grupo de recursos donde se encuentra el recurso de la alerta.

7. Opcionalmente, seleccione una **Categoría de evento** para modificar los filtros adicionales que se muestran. Para eventos administrativos, los filtros incluyen **Grupo de recursos**, **Recurso**, **Tipo de recurso**, **Nombre de la operación**, **Nivel**, **Estado** y **Evento iniciado por**. Estos valores identifican los eventos que debe supervisar esta alerta.

    >[!NOTE]
    >Debe especificar al menos uno de los criterios anteriores en la alerta. No puede crear una alerta que se active cada vez que se crea un evento en los registros de actividad.
    >
    >

8. Escriba un nombre en el cuadro de texto **Nombre del grupo de acciones** y especifique un nombre en el cuadro de texto **Nombre corto**. El nombre corto se utiliza en lugar del nombre completo del grupo de acciones cuando se envían notificaciones mediante este grupo.

9.  Defina una lista de acciones proporcionando la siguiente información de la acción:

    a. **Nombre**: escriba el nombre, alias o identificador de la acción.

    b. **Tipo de acción**: seleccione SMS, correo electrónico o webhook.

    c. **Detalles**: según el tipo de acción, proporcione un número de teléfono, una dirección de correo electrónico o un identificador URI de webhook.

10. Seleccione **Aceptar** para crear la alerta.

La alerta tarda unos minutos en propagarse completamente y, a continuación, se activa. Se desencadena cuando los nuevos eventos coinciden con los criterios de la alerta.

Para más información, consulte [Conocer el esquema de webhook que se utiliza en las alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>El grupo de acciones definido en estos pasos es reutilizable, como grupo de acciones existente, en todas las futuras definiciones de alertas.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Creación de una alerta para un evento del registro de actividad para un grupo de acciones ya existente con Azure Portal
1. Siga los pasos del 1 al 7 de la sección anterior para crear la alerta del registro de actividad.

2. En **Notificar a través de**, seleccione el botón grupo de acciones **Existente**. Seleccione un grupo de acciones existente de la lista.

3. Seleccione **Aceptar** para crear la alerta.

La alerta tarda unos minutos en propagarse completamente y, a continuación, se activa. Se desencadena cuando los nuevos eventos coinciden con los criterios de la alerta.

## <a name="manage-your-alerts"></a>Administración de alertas

Después de crear una alerta, es visible en la sección Alertas de la hoja Supervisión. Seleccione la alerta que desea administrar para:

* Editarla.
* Eliminarla.
* Deshabilitarla o habilitarla, si desea detener temporalmente o reanudar la recepción de notificaciones de la alerta.

## <a name="next-steps"></a>Pasos siguientes
- Obtener una [Introducción a las alertas](monitoring-overview-alerts.md).
- Más información sobre la [Limitación del número de notificaciones](monitoring-alerts-rate-limiting.md).
- Revise el [Esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md).
- Más información sobre los [grupos de acciones](monitoring-action-groups.md).  
- Más información acerca de las [Notificaciones del estado del servicio](monitoring-service-notifications.md).
- Creación de una [alerta del registro de actividad para supervisar todas las operaciones del motor de escalado automático en su suscripción](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Creación de una [alerta del registro de actividad para supervisar todas las operaciones con errores de escalado automático y reducción horizontal en su suscripción](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
