---
title: 'Privileged Identity Management para recursos de Azure: extensión y renovación de roles| Microsoft Docs'
description: Este documento describe cómo extender y renovar los roles de los recursos de Azure para los recursos de PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Privileged Identity Management: extensión o renovación de roles de recursos

PIM para recursos de Azure introduce nuevos controles para administrar el ciclo de vida de acceso y asignación de los recursos de Azure. Los administradores pueden asignar pertenencia mediante las propiedades de fecha y hora de inicio y finalización. Cuando se aproxima el final de la asignación, PIM envía notificaciones por correo electrónico a los miembros afectados (pueden ser un usuario o un grupo) y a los administradores del recurso para asegurarse de que se mantiene el acceso adecuado. En el caso de que el acceso no se amplíe porque no se realice ninguna acción, las asignaciones se pueden renovar y permanecer visibles, en un estado expirado durante un máximo de 30 días.

## <a name="who-can-extend-and-renew"></a>¿Quién puede ampliar y renovar?

Las asignaciones de roles solo pueden ampliarlas o renovarlas los administradores del recurso. El miembro afectado puede solicitar que se amplíen roles a punto de expirar, así como que se renueven los que ya han expirado.

## <a name="when-are-notifications-sent"></a>¿Cuándo se envían las notificaciones?

PIM envía notificaciones por correo electrónico tanto a los administradores como a los miembros afectados de roles que expiran en 14 días y un día antes de la expiración. Se envía un correo electrónico adicional cuando una asignación ha expirado oficialmente. 

Los administradores reciben notificaciones cuando un miembro de un rol que va a expirar, o que ha expirado, solicita la extensión o renovación. Cuando un administrador específico resuelve la solicitud se envía a los restantes administradores una notificación de la decisión de resolución (aprobada o denegada) y al miembro que ha realizado la petición se le informa de la decisión. 

## <a name="extend-role-assignments"></a>Extensión de asignaciones de roles

Los pasos siguientes describen los pasos y la interfaz de usuario implicada en la solicitud, resolución o administración de una extensión o renovación de una asignación de roles. 

### <a name="member-extend"></a>Extensión de miembros

Los miembros de una asignación de roles pueden solicitar la ampliación de las asignaciones de roles directamente tanto desde las pestañas "Roles elegibles" o "Roles activos" de la página "Mis roles" de un recurso como desde el nivel superior de Mis roles del portal de PIM. Los miembros pueden solicitar una ampliación de los roles elegibles (asignados) y activos que expiran en los próximos 14 días.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Cuando la fecha y hora de finalización de la asignación es anterior a 14 días, el botón para "Extender" se convertirá en un vínculo activo en la interfaz de usuario. En el ejemplo siguiente, se supone que la fecha actual es 27 de marzo.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Para solicitar una extensión de la asignación de este rol, haga clic en "Extender" para abrir el formulario de solicitud.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Expanda "Detalles de asignación" para ver información acerca de la asignación original. Especifique un motivo para la solicitud de extensión y haga clic en "Extender".

>[!Note]
>Se recomienda incluir los detalles de por qué es necesaria la extensión y de cuánto tiempo debe ser la extensión (si se conoce).

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

En cuestión de minutos, los administradores de recursos reciben una notificación por correo electrónico en la que se les solicita que revisen la solicitud de extensión. Si ya se ha enviado una solicitud de extensión, aparecerá una notificación en la parte superior de Azure Portal en la que se explica el error.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

En la pestaña "Solicitudes pendientes" del menú de navegación izquierdo puede ver el estado de una solicitud o cancelarla.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Aprobación de administrador

Cuando un miembro envía una solicitud para extender la asignación de un rol, los administradores de recursos reciben una notificación por correo electrónico con los detalles de la asignación original. La notificación incluye un vínculo directo a la solicitud para que el administrador la apruebe o deniegue. 

Además de seguir el vínculo del correo electrónico, los administradores pueden aprobar o rechazar las solicitudes de otra forma, pueden dirigirse al portal de administración de PIM y seleccionar "Aprobar solicitudes" en el menú de navegación izquierdo.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Cuando un administrador selecciona Aprobar o Denegar se muestran los detalles de la solicitud, junto con un campo para especificar una justificación para los registros de auditoría.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Al aprobar una solicitud para ampliar la asignación de roles, los administradores de recursos pueden elegir una fecha y hora de inicio y finalización, y un tipo de asignación nuevos. El cambio del tipo de asignación puede ser necesario si el administrador desea proporcionar acceso limitado para completar una tarea concreta (por ejemplo, un día). En este ejemplo, el administrador puede cambiar la asignación de Elegible a Activo y proporcionar el solicitante un acceso sin que sea preciso que este lo active.

### <a name="admin-extend"></a>Extensión del administrador

En caso de que un miembro del rol olvide solicitar la extensión de la pertenencia al rol, o no pueda solicitarla, un administrador puede extender una asignación en nombre del miembro. Las extensiones administrativas de la pertenencia a un rol no requieren aprobación, pero las notificaciones se envían a los restantes administradores una vez que finaliza la ampliación del rol.

Para extender la pertenencia a un rol, vaya a la vista de rol o de miembro del recurso en el PIM. Busque al miembro que requiera una extensión y haga clic en "Extender" en la columna de acción.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Renovación de asignaciones de roles

Aunque conceptualmente es similar, el proceso para renovar una asignación de rol expirada es distinto del de solicitar una extensión tanto para los miembros como para los administradores. Con los pasos siguientes tanto los miembros como los administradores puede renovar el acceso a los roles que hayan expirado cuando sea necesario.

### <a name="member-renew"></a>Renovación de los miembros

Los miembros que ya no pueden acceder a los recursos pueden hacerlo a 30 días del historial de asignaciones expiradas. Para ello, deben dirigirse a Mis roles en el panel de navegación izquierdo de PIM y seleccionar la pestaña "Roles expirados" en la sección de roles de recursos de Azure.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

El valor predeterminado de la lista de roles que se muestran es Asignaciones elegibles. Utilice la lista desplegable para alternar entre los roles asignados Elegibles y Activos.

Para solicitar la renovación de cualquiera de las asignaciones de roles de la lista, seleccione la acción "Renovar" y especifique el motivo de la solicitud. Resulta útil indicar una duración, además de cualquier contexto adicional que ayude al administrador de recursos a la hora de decidir si la aprueba o la deniega.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Tras el envío del recurso de la solicitud los administradores reciben la notificación de que hay una solicitud de renovación de la asignación de un rol pendiente.

### <a name="admin-approves"></a>Aprobaciones de los administradores

Los administradores de recursos pueden acceder a la solicitud de renovación desde el vínculo de la notificación por correo electrónico, o bien pueden acceder a la PIM desde Azure Portal y seleccionar "Aprobar solicitudes" en el menú de navegación izquierdo.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Cuando un administrador selecciona Aprobar o Denegar se muestran los detalles de la solicitud, junto con un campo para especificar una justificación para los registros de auditoría.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Al aprobar una solicitud para renovar la asignación de roles, los administradores de recursos deben especificar una fecha y hora de inicio y finalización, y un tipo de asignación nuevos. 

### <a name="admin-renew"></a>Renovación por parte de los administradores

Los administradores de recursos pueden renovar las asignaciones de roles caducados desde la pestaña Miembros del menú de navegación izquierdo de un recurso, o desde la pestaña Roles expirados de un rol de recursos.

En la pantalla Miembros, seleccione Roles expirados para ver una lista de todas las asignaciones de roles que han expirado.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Pasos siguientes

[Solicitud de aprobación para activar](pim-resource-roles-approval-workflow.md)

[Activación de un rol](pim-resource-roles-use-the-audit-log.md)


