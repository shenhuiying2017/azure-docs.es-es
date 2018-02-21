---
title: "Crear alertas del registro de actividad y administrarlas mediante la nueva experiencia de alertas (versión preliminar) en Azure Monitor | Microsoft Docs"
description: "En este artículo se proporciona información acerca de cómo crear alertas del registro de actividad en la pestaña de alertas (versión preliminar) en Azure Monitor. En este artículo se detalla la nueva experiencia de usuario de esta característica."
author: JYOTHIRMAISURI
manager: vvithal
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: 
ms.openlocfilehash: afdd82617c47f0dee22c229feba87bdf79b90a69
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-preview-experience"></a>Crear alertas del registro de actividad mediante la nueva experiencia de alertas (versión preliminar)

Las alertas del registro de actividad son alertas que se activan cuando un nuevo evento del registro de actividad cumple las condiciones especificadas en la alerta.

Estas alertas son recursos de Azure, por lo que pueden crearse con una plantilla de Azure Resource Manager. También se pueden crear, actualizar o eliminar en Azure Portal. Este artículo presenta los conceptos relativos a las alertas del registro de actividad. Seguidamente, se explica cómo usar Azure Portal para configurar alertas en eventos del registro de actividad mediante la nueva experiencia [Alertas de Azure (versión preliminar)](monitoring-overview-unified-alerts.md).

Normalmente es necesario crear alertas del registro de actividad para recibir una notificación si se producen cambios específicos en los recursos de la suscripción de Azure, los cuales suelen abarcar grupos de recursos o algún recurso en particular. Por ejemplo, es posible que quiera recibir una notificación cuando se elimine una máquina virtual (grupo de recursos de ejemplo) en **myProductionResourceGroup**, o que quiera recibir una notificación si cualquier rol nuevo se asigna a un usuario de la suscripción.

Puede configurar una alerta del registro de actividad según las propiedades de nivel superior del objeto JSON de un evento del registro de actividad. Sin embargo, el portal muestra las opciones más comunes tal como se detalla en las secciones siguientes:

>[!NOTE]

> Cuando la categoría es "administrativo", debe especificar al menos uno de los criterios anteriores en la alerta. No puede crear una alerta que se active cada vez que se crea un evento en los registros de actividad.
>

Cuando se activa una alerta del registro de actividad, usa un grupo de acciones para generar acciones o notificaciones. Un grupo de acciones es un conjunto reutilizable de destinatarios de notificación, como direcciones de correo electrónico, direcciones URL del webhook o números de teléfono para SMS. Se puede hacer referencia a los receptores desde varias alertas para centralizar y agrupar los canales de notificación. Al definir la alerta del registro de actividad, tiene dos opciones. Puede:

* Usar un grupo de acciones existente en la alerta del registro de actividad.
* Crear un nuevo grupo de acciones.

Para más información sobre los grupos de acciones, consulte [Creación y administración de grupos de acciones en Azure Portal](monitoring-action-groups.md).

Para más información acerca de las notificaciones de mantenimiento del servicio, consulte [Recibir alertas del registro de actividad con las notificaciones de mantenimiento del servicio](monitoring-activity-log-alerts-on-service-notifications.md).


## <a name="whats-new-in-alerts-preview-for-activity-logs"></a>¿Qué novedades tiene la versión preliminar de alertas para los registros de actividad?

[Alertas de Azure (versión preliminar)](monitoring-overview-unified-alerts.md) proporciona una experiencia de usuario mejorada en sus alertas del registro de actividad. Gracias a la [experiencia de usuario mejorada para las alertas](monitoring-overview-unified-alerts.md), ahora puede realizar lo siguiente:

- [Crear](#create-an-alert-rule-for-an-activity-log) y [administrar](#view-and-manage-activity-log-alert-rules) las reglas de alertas del registro de actividad desde la hoja **Supervisar** > **Alertas (versión preliminar)**. Obtenga más información sobre los [registros de actividad](monitoring-overview-activity-logs.md).

- **Nuevas opciones para los destinos de las alertas**: al crear una nueva regla de alertas del registro de actividad, puede seleccionar un recurso o un grupo de recursos o una suscripción de destino.


## <a name="create-an-alert-rule-for-an-activity-log"></a>Crear una regla de alertas para un registro de actividad

> [!NOTE]

>  Al crear las reglas de alertas, asegúrese de lo siguiente:

> - La suscripción del ámbito no es diferente de la suscripción donde se creó la alerta.
- La alerta se configurará según los siguientes tipos de criterios: nivel, estado, autor de la llamada, grupo de recursos, id. del recurso, tipo de recurso o categoría de eventos.
- No hay ninguna condición o condiciones anidadas de tipo "anyOf" en la configuración de alertas JSON (básicamente, solo se permite una condición de tipo "AllOf" y que no tenga más tipos AllOf/anyOf).


Utilice el siguiente procedimiento:

1. En Azure Portal, seleccione **Supervisar** > **Alertas (versión preliminar).**
2. Haga clic en **Nueva regla de alertas** que se encuentra en la parte superior de la ventana **Alertas (versión preliminar)**.

     ![nueva regla de alertas](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     Aparece la ventana **Crear regla**.

      ![opciones de la nueva regla de alertas](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **En la condición Definir alerta**, proporcione la siguiente información y haga clic en **Hecho**.

    - **Destino de la alerta:** para ver y seleccionar el destino de la nueva alerta, use la opción **Filtrar por suscripción** / **Filtrar por tipo de recurso** y seleccione el recurso o grupo de recursos de la lista que se muestra.

    > [!NOTE]

    > puede seleccionar un recurso, un grupo de recursos o una suscripción completa.

    **Vista de ejemplo de la alerta de destino**

     ![Selección del destino](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - En **Criterios de destino, haga clic en **Agregar criterios** y seleccione el tipo de señal como **Registro de actividad**.

    - Seleccione la señal de la lista que se muestra.

    Puede seleccionar la escala de tiempo del historial de registro y la lógica de la alerta correspondiente a esta señal de destino:

    **Pantalla para agregar criterios**

    ![agregar criterios](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Tiempo del historial**: en las últimas 6/12/24 horas, durante la última semana.

    **Lógica de alerta**:

        - **Event Level**- The severity level of the event.**Verbose,Informational, Warning, Error**, or **Critical**.
        - **Status**: The status of the event.**Started, Failed**, or **Succeeded**.
        - **Event initiated by**: Also known as the caller; The email address or Azure Active Directory identifier of the user who performed the operation.

        **Sample signal graph with alert logic applied** :

        ![ criteria selected](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. En la opción que permite **definir las reglas de alertas**, proporcione los detalles siguientes:

    - **Nombre de regla de alertas** : el nombre de la nueva regla de alertas.
    - **Descripción**: la descripción de la nueva regla de alertas.
    - **Guardar alerta en el grupo de recursos**: seleccione el grupo de recursos donde quiera guardar esta nueva regla.

5. En el **grupo de acciones**, en el menú desplegable, especifique el grupo de acciones que quiere asignar a esta nueva regla de alertas. Asimismo, también puede [crear un nuevo grupo de acción](monitoring-action-groups.md) y asignarlo a la nueva regla. Para crear un nuevo grupo, haga clic en **+ Nuevo grupo**.

6. Para habilitar las reglas una vez creado, haga clic en **Sí** en la opción **Habilitar regla tras la creación**.
7. Haga clic en **Crear regla de alertas**.

    La nueva regla de alertas del registro de actividad se crea y aparece un mensaje de confirmación en la parte superior derecha de la ventana.

    ![ regla de alertas creada](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    Puede habilitar, deshabilitar, editar o eliminar una regla. [Obtenga más información](#view-and-manage-activity-log-alert-rules) acerca de cómo administrar las reglas del registro de actividad.

## <a name="view-and-manage-activity-log-alert-rules"></a>Ver y administrar las reglas de alertas del registro de actividad

1. En Azure Portal, haga clic en **Supervisar** > **Alertas (versión preliminar)** y haga clic en **Administrar reglas** en la parte superior izquierda de la ventana.

    ![ administración de reglas de alerta](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    Aparece la lista de reglas disponibles.

2. Busque la regla del registro de actividad que quiera modificar.

    ![ buscar las reglas de alertas del registro de actividad](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Puede usar los filtros disponibles: **Suscripción**, **Grupo de recursos**, o **Recursos** para buscar la regla de actividad que quiera editar.

    > [!NOTE]

    > Recuerde que solo puede editar las secciones **Descripción** , **Criterios de destino** y **Grupos de acción**.

3.  Seleccione la regla y haga doble clic para editar las opciones. Haga los cambios que sean necesarios y haga clic en **Guardar**.

    ![ administración de reglas de alerta](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  Puede deshabilitar, habilitar o eliminar una regla. Seleccione la opción adecuada en la parte superior de la ventana, después de seleccionar la regla tal como se detalla en el paso 2.


## <a name="next-steps"></a>pasos siguientes

- [Archivar las alertas del registro de actividad](monitoring-archive-activity-log.md)
- [Transmitir registros de actividad a Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
- [Migrar a los registros de actividad](monitoring-migrate-management-alerts.md)
