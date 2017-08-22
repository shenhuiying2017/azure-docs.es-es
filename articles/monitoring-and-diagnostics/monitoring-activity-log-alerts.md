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
ms.date: 08/03/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c44ecc0ee61c58ab7590e9a21a5fe002068c6b83
ms.contentlocale: es-es
ms.lasthandoff: 08/04/2017

---
# <a name="create-activity-log-alerts"></a>Creación de alertas de registro de actividad

## <a name="overview"></a>Información general
Las alertas de registro de actividad son alertas que se activan cuando un nuevo evento de registro de actividad cumple las condiciones especificadas en la alerta. Como son recursos de Azure, se pueden crear mediante una plantilla de Resource Manager y se pueden crear, actualizar o eliminar en Azure Portal. En este artículo se presentan los conceptos que subyacen a las alertas de registro de actividad, y luego se muestra cómo se usa Azure Portal para configurar una alerta para eventos de registro de actividad.

Las alertas de registro de actividad se crean normalmente con dos fines:
1. Para recibir una notificación cuando se produzcan cambios específicos en los recursos de la suscripción de Azure, que abarcan normalmente grupos de recursos o recursos en particular. Por ejemplo, si quiere que le notifiquen cuando se elimine alguna máquina virtual de myProductionResourceGroup. O, si se asigna algún rol nuevo a un usuario de la suscripción.
2. Para recibir una notificación cuando tenga lugar un evento de estado del servicio. Los eventos de estado del servicio incluyen la notificación de incidentes y eventos de mantenimiento que se aplican a recursos de la suscripción.

En cualquier caso, la alerta de registro de actividad solo se supervisará en el caso de eventos de la suscripción en los que se creó la alerta.

Puede configurar una alerta de registro de actividad según las propiedades de nivel superior del objeto JSON de un evento de registro de actividad, pero el portal muestra las opciones más comunes:
- **Categoría**: Administración, Estado del servicio, Escalado automático, Recomendación. [Consulte este artículo para más información sobre las categorías de registro de actividad](./monitoring-overview-activity-logs.md#categories-in-the-activity-log) y [aprenda más sobre los eventos de estado del servicio aquí](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Grupo de recursos**
- **Recurso**
- **Tipo de recurso**
- **Nombre de la operación**: el nombre de la operación de RBAC de Resource Manager.
- **Nivel**: el nivel de gravedad del evento (detallado, informativo, advertencia, error, crítico).
- **Estado**: el estado del evento, normalmente "Iniciado," "Error" o "Correcto".
- **Evento iniciado por**: también se conoce como el "llamador". La dirección de correo electrónico o el identificador de Active Directory del usuario que realizó la operación.

>[!NOTE]
>Debe especificar al menos dos de los criterios anteriores en la alerta, siendo uno de ellos la categoría. No puede crear una alerta que se activa cada vez que se crea un evento en los registros de actividad.
>
>

Cuando se activa una alerta de registro de actividad, se usa un grupo de acciones para generar acciones o notificaciones. Un grupo de acciones es un conjunto reutilizable de receptores de notificación (direcciones de correo electrónico, direcciones URL de webhook o números de teléfono de SMS) a los que se puede hacer referencia desde varias alertas con el fin de centralizar y agrupar los canales de notificaciones. Puede usar un grupo de acciones existente en la alerta de registro de actividad, o crear uno nuevo al definir la alerta de registro de actividad. Puede aprender más sobre los grupos de acciones [aquí](monitoring-action-groups.md)

[Aquí](monitoring-activity-log-alerts-on-service-notifications.md) puede aprender más sobre las alertas de registro de actividad para las notificaciones de estado del servicio.

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Creación de una alerta para un evento de registro de actividad con un nuevo grupo de acciones con Azure Portal
1.  En el [portal](https://portal.azure.com), vaya al servicio **Monitor**.

    ![Supervisión](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Haga clic en la opción **Monitor** para abrir la hoja del mismo nombre. Primero se abre la hoja **Registro de actividad**.

3.  Ahora haga clic en la hoja **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Seleccione el comando **Agregar alerta de registro de actividad** y rellene los campos

5.  Asigne un **nombre** a la alerta del registro de actividad y elija una **descripción**.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  La **suscripción** seleccionada es aquella en la que se guarda la alerta del registro de actividad. Se rellena automáticamente con la suscripción en la que trabaja actualmente. Esta es la suscripción en la que se implementa el recurso de alerta y desde la que se supervisan los eventos del registro de actividad.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  Elija el **grupo de recursos** en el que se creará esta alerta. Observe que este no es el grupo de recursos que supervisará *la* alerta, sino el grupo de recursos donde se *implementará el propio recurso de alerta.*

8.  Opcionalmente, seleccione una **categoría de evento**, que modifica el conjunto de filtros adicionales que se muestran. Para eventos de administración, se incluyen opciones para filtrar por valores de **Grupo de recursos**, **Recurso**, **Tipo de recurso**, **Nombre de operación**, **Nivel**, **Estado** y **Evento iniciado por** para identificar los eventos que debe supervisar esta alerta.

>[!NOTE]
>Debe especificar al menos uno de los criterios anteriores en la alerta. No puede crear una alerta que se activa cada vez que se crea un evento en los registros de actividad.
>
>

9.  Cree un **nuevo** grupo de acciones y asígnele un **nombre** y un **nombre corto**. Se hará referencia al nombre corto en las notificaciones que se envían cuando se activa esta alerta.

10. A continuación, defina una lista de acciones proporcionando la siguiente información de la acción:

    a. **Nombre:** nombre, alias o identificador de la acción.

    b. **Tipo de acción:** elija el tipo de acción: SMS, Correo electrónico o Webhook

    c. **Detalles:** según el tipo de acción elegido, proporcione un número de teléfono, una dirección de correo electrónico o un identificador URI de webhook.

11. Seleccione **Aceptar** cuando termine para crear la alerta.

La alerta tarda unos minutos en propagarse totalmente y, a continuación, se activa y se desencadena cuando nuevos eventos coinciden con los criterios de la alerta.

[Consulte este documento para más información sobre el esquema de webhook de las alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>El grupo de acciones definido en estos pasos es reutilizable, como grupo de acciones existente, en todas las futuras definiciones de alertas.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Creación de una alerta para un evento de registro de actividad de un grupo de acciones ya existente con Azure Portal
1.  En el [portal](https://portal.azure.com), vaya al servicio **Monitor**.

    ![Supervisión](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Haga clic en la opción **Monitor** para abrir la hoja del mismo nombre. Primero se abre la sección **Registro de actividades** .

3.  Ahora haga clic en la sección **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Seleccione el comando **Agregar alerta de registro de actividad** y rellene los campos

5.  Asigne un **nombre** a la alerta del registro de actividad y elija una **descripción**. Esta información aparece en las notificaciones enviadas cuando se desencadena esta alerta.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  La **suscripción** seleccionada es aquella en la que se guarda la alerta del registro de actividad. Se rellena automáticamente con la suscripción en la que trabaja actualmente. Esta es la suscripción en la que se implementa el recurso de alerta y desde la que se supervisan los eventos del registro de actividad.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  Elija el **grupo de recursos** en el que se creará esta alerta. Observe que este no es el grupo de recursos que supervisará *la* alerta, sino el grupo de recursos donde se *implementará el propio recurso de alerta.*

8.  Opcionalmente, seleccione una **categoría de evento**, que modifica el conjunto de filtros adicionales que se muestran. Para eventos de administración, se incluyen opciones para filtrar por valores de **Grupo de recursos**, **Recurso**, **Tipo de recurso**, **Nombre de operación**, **Nivel**, **Estado** y **Evento iniciado por** para identificar los eventos que debe supervisar esta alerta.

9.  Elija **Notificar mediante** un **Grupo de acciones existente**. Seleccione un grupo de acciones existente de la lista.

10. Seleccione **Aceptar** cuando termine para crear la alerta.

La alerta tarda unos minutos en propagarse totalmente y, a continuación, se activa y se desencadena cuando nuevos eventos coinciden con los criterios de la alerta.

## <a name="managing-your-alerts"></a>Administración de las alertas

Cuando haya creado una alerta, será visible en la sección de alertas del servicio Monitor. Seleccione la alerta que desea administrar. Puede:
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
- [Cree una alerta de registro de actividades para supervisar todas las operaciones con errores de escalado automático y reducción horizontal en su suscripción.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

