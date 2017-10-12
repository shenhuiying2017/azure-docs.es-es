---
title: "Flujos de trabajo de aprobación de Azure Privileged Identity Management | Microsoft Docs"
description: "Obtenga información sobre los flujos de trabajo de aprobación de Privileged Identity Management (PIM)"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: cf6a9213fa0a1cba8725aabb42abe51b805ece7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="approvals-preview"></a>Aprobaciones (versión preliminar)

## <a name="overview"></a>Información general

Con las aprobaciones de Privileged Identity Management, puede configurar roles para requerir la aprobación para la activación y elegir uno o varios usuarios o grupos como aprobadores delegados. Siga leyendo para obtener información sobre cómo configurar roles y seleccionar aprobadores.

>[!NOTE]
Tenga en cuenta que esta característica está aún en desarrollo, por lo que puede encontrar errores. La funcionalidad, incluidas las convenciones de nomenclatura y de texto, está sujeta a modificaciones, por lo que no se puede considerar como definitiva.


## <a name="key-terminology"></a>Terminología clave

*Usuario de rol apto*: se trata de un usuario de la organización asignado a un rol de Azure AD como apto (el rol requiere activación).

*Aprobador delegado*: se trata de una o varias personas o grupos de Azure AD responsables de la aprobación de las solicitudes de activación de roles.

## <a name="scenarios"></a>Escenarios

La versión preliminar privada admite los siguientes escenarios:

**Como administrador de rol con privilegios (PRA), puede:**

-   [Habilitar la aprobación de roles específicos](#enable-approval-for-specific-roles)

-   [Especificar usuarios y grupos de aprobadores para la aprobación de solicitudes](#specify-approver-users-and/or-groups-to-approve-requests)

-   [Ver el historial de solicitudes y aprobaciones de todos los roles con privilegios](#view-request-and-approval-history-for-all-privileged-roles)

**Como un aprobador designado, puede:**

-   [Ver aprobaciones o solicitudes pendientes](#view-pending-approvals-requests)

-   [Aprobar o rechazar solicitudes de elevación de rol (de forma individual o masiva)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Proporcionar una justificación de la aprobación o el rechazo](#provide-justification-for-my-approval/rejection) 

**Como usuario de rol apto, puede:**

-   [Solicitar la activación de un rol que requiere aprobación](#request-activation-of-a-role-that-requires-approval)

-   [Ver el estado de la solicitud de activación](#view-the-status-of-your-request-to-activate)

-   [Completar la tarea en Azure AD si la activación se ha aprobado](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navegación

Se ha actualizado la navegación para admitir las aprobaciones

![](media/azure-ad-pim-approval-workflow/image001.png)

En la página de aterrizaje predeterminada se ofrece un acceso conveniente a información sobre PIM y a la documentación nueva sobre aprobaciones.

![](media/azure-ad-pim-approval-workflow/image002.png)

También se ha agregado una sección nueva para todos los usuarios de PIM, "My Audit History" (Mi historial de auditoría). Aquí puede encontrar toda la información relevante para su identidad. Se incluyen todas las solicitudes pendientes y completadas, todas las decisiones tomadas en relación con las solicitudes resueltas y todas las activaciones de roles anteriores en una misma ubicación.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Habilitar la aprobación de roles específicos

Para habilitar la aprobación de un rol específico, primero seleccione Directory Roles (Roles del directorio) en el panel de navegación izquierdo.

![](media/azure-ad-pim-approval-workflow/image004.png)

Busque y seleccione la configuración en Directory Roles (Roles del directorio) en el panel de navegación izquierdo.

![](media/azure-ad-pim-approval-workflow/image006.png)

Seleccione Roles con privilegios:

![](media/azure-ad-pim-approval-workflow/image009.png)

Seleccione "Activado" en la sección Requerir aprobación:

![](media/azure-ad-pim-approval-workflow/image011.png)

Una vez habilitada esta opción, la hoja se expandirá para mostrar los detalles siguientes:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Si NO especifica ningún aprobador, el PRA será el aprobador predeterminado. PRA deberá aprobar TODAS las solicitudes de activación de este rol.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Especificar usuarios y grupos de aprobadores para la aprobación de solicitudes

Para delegar la aprobación, haga clic en la opción "Seleccionar aprobadores":

![](media/azure-ad-pim-approval-workflow/image015.png)

Cuando se carga la hoja Seleccionar aprobadores, debe buscar un usuario o grupo específico con la barra de búsqueda que se encuentra en la parte superior o seleccionarlo en la lista rellenada previamente y, después, hacer clic en "Seleccionar" cuando haya terminado:

![](media/azure-ad-pim-approval-workflow/image017.png)

Nota: puede seleccionar varios usuarios o grupos a la vez.

La selección aparecerá en la lista de los aprobadores seleccionados tal como se muestra a continuación:

![](media/azure-ad-pim-approval-workflow/image019.png)

Para quitar un aprobador, simplemente haga clic en el botón Remove (Quitar) junto al nombre.

Para agregar aprobadores adicionales, repita el proceso.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Ver el historial de solicitudes y aprobaciones de todos los roles con privilegios

Para ver el historial de solicitudes y aprobaciones de todos los roles con privilegios, seleccione Historial de auditoría en el panel:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Puede ordenar los datos por acción y buscar el texto "Activación aprobada".

### <a name="view-pending-approvals-requests"></a>Ver aprobaciones o solicitudes pendientes

Como un aprobador delegado, recibirá notificaciones por correo electrónico cuando una solicitud está pendiente de aprobación. Para ver estas solicitudes en el portal de PIM, en el nuevo panel de navegación, seleccione la pestaña "Solicitudes de aprobación pendientes" en la barra de navegación izquierda.

![](media/azure-ad-pim-approval-workflow/image023.png)

Ahí se mostrará una lista de solicitudes pendientes de aprobación:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Aprobar o rechazar solicitudes de elevación de rol (de forma individual o masiva)

Seleccione las solicitudes que desea aprobar o rechazar y haga clic en el botón de la barra de acciones correspondiente a su decisión:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Proporcionar una justificación de la aprobación o el rechazo

Se abrirá una nueva hoja para aprobar o rechazar varias solicitudes a la vez. Escriba una justificación de la decisión que ha tomado y haga clic en los botones para aprobar o rechazar en la parte inferior de la hoja:

![](media/azure-ad-pim-approval-workflow/image029.png)

Una vez completado el proceso de solicitud, el símbolo de estado reflejará la decisión adoptada (en este ejemplo, la decisión es aprobar):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Solicitar la activación de un rol que requiere aprobación

La solicitud de activación de un rol que requiere aprobación puede iniciarse desde el panel de navegación de PIM antiguo o desde el nuevo panel de navegación, ya que el proceso de activación de roles sigue siendo el mismo. Solo tiene que seleccionar un rol en la lista de roles para activarlo:

![](media/azure-ad-pim-approval-workflow/image033.png)

Si un rol con privilegios requiere Multi-Factor Authentication, se le pedirá que complete esa tarea en primer lugar:

![](media/azure-ad-pim-approval-workflow/image035.png)

Cuando haya terminado, haga clic en Activar y proporcione una justificación (si es necesario):

![](media/azure-ad-pim-approval-workflow/image037.png)

El solicitante verá una notificación que indica que la solicitud está pendiente de aprobación:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Ver el estado de la solicitud de activación

Para ver el estado de una solicitud pendiente de activación, es necesario acceder desde el nuevo panel de navegación. En la barra de navegación de la izquierda, seleccione la pestaña "My Requests" (Mis solicitudes):

![](media/azure-ad-pim-approval-workflow/image041.png)

El estado de solicitud predeterminado es "Pendiente", pero puede alternar para ver todas las solicitudes o solo las solicitudes rechazadas.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Completar la tarea en Azure AD si la activación se ha aprobado

Una vez aprobada la solicitud, el rol se activa y puede continuar con cualquier tarea que requiere este rol.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Pasos siguientes

Sus comentarios son muy importantes, así que no dude en compartirlos con nosotros aquí.
