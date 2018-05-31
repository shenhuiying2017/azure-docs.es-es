---
title: Flujo de trabajo de aprobación de roles de recursos de Azure en Privileged Identity Management|Microsoft Docs
description: En este artículo se explica el proceso del flujo de trabajo de aprobación para recursos de Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 7781c858a5c0e4db8593df0cf77b868b6fd23622
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151100"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Flujo de trabajo de aprobación de roles de recursos de Azure en Privileged Identity Management

Con el flujo de trabajo de aprobación de Privileged Identity Management (PIM) para roles de recursos de Azure, los administradores pueden proteger o restringir aún más el acceso a los recursos críticos. Es decir, los administradores pueden solicitar aprobación para activar asignaciones de roles. 

El concepto de una jerarquía de recursos es único de los roles de recursos de Azure. Esta jerarquía permite la herencia de asignaciones de roles de un objeto de recurso primario a todos los recursos secundarios del contenedor primario. 

Por ejemplo: Bob, un administrador de recursos, usa PIM para asignar a Alice como miembro apto para el rol de Propietario de la suscripción de Contoso. Con esta asignación, Alice es un propietario apto de todos los contenedores del grupo de recursos de la suscripción de Contoso. Alice también es un propietario apto de todos los recursos (como máquinas virtuales) de cada grupo de recursos de la suscripción. 

Supongamos que hay tres grupos de recursos en la suscripción a Contoso: Fabrikam Test, Fabrikam Dev y Fabrikam Prod. Cada uno de ellos contiene una única máquina virtual.

La configuración de PIM se configura para cada rol de un recurso. A diferencia de las asignaciones, la configuración no se hereda y se aplica estrictamente al rol del recurso. [Obtenga más información sobre asignaciones aptas y visibilidad de recursos](pim-resource-roles-eligible-visibility.md).

Siguiendo con el ejemplo: Bob usa PIM para exigir a todos los miembros del rol Propietario de la suscripción de Contoso que soliciten aprobación para activarse. Para ayudar a proteger los recursos del grupo de recursos Fabrikam Prod, Bob además exige aprobación de los miembros del rol Propietario de este recurso. Los roles de propietario de Fabrikam Test y Fabrikam Dev no necesitan aprobación para activarse.

Cuando Alice solicita la activación de su rol Propietario de la suscripción de Contoso, se debe aprobar o denegar su solicitud antes de que se active en el rol. Además, si Alice decide [limitar el ámbito de su activación](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) al grupo de recursos Fabrikam Prod, un aprobador debe aprobar o denegar dicha solicitud. Pero si Alice decide limitar el ámbito de su activación a Fabrikam Test o Fabrikam Dev, o ambos, no se requiere aprobación.

Es posible que el flujo de trabajo de aprobación no sea necesario para todos los miembros de un rol. Piense en un escenario en que la organización contrata a varios subcontratados para que ayuden en el desarrollo de una aplicación que se va a ejecutar en una suscripción de Azure. Como administrador de recursos, quiere que los empleados tengan acceso apto sin necesidad de aprobación, pero que los subcontratados deban solicitar aprobación. Para configurar el flujo de trabajo de aprobación solo para los subcontratados, puede crear un rol personalizado con los mismos permisos que el rol asignado a los empleados. Puede exigir aprobación para activar ese rol personalizado. [Más información acerca de los roles personalizados](pim-resource-roles-custom-role-policy.md).

Para configurar el flujo de trabajo de aprobación y especificar quién puede aprobar o rechazar solicitudes, use los siguientes procedimientos.

## <a name="require-approval-to-activate"></a>Solicitud de aprobación para activar

1. Vaya a PIM en Azure Portal y seleccione un recurso de la lista.

   ![Panel "Recursos de Azure" con un recurso seleccionado](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. En el panel izquierdo, seleccione **Configuración de roles**.

3. Busque un rol, selecciónelo y luego seleccione **Editar** para modificar la configuración.

   ![Botón "Editar" del rol de operador](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. En la sección **Activación**, active la casilla **Se requiere aprobación para activar**.

   ![Sección "Activación" de Configuración de roles](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Especificación de aprobadores

Haga clic en **Seleccionar aprobadores** para abrir el panel **Seleccione un usuario o grupo**.

>[!NOTE]
>Debe seleccionar al menos un usuario o grupo para actualizar la configuración. No hay aprobadores predeterminados.

Los administradores de recursos pueden agregar cualquier combinación de usuarios y grupos a la lista de aprobadores. 

![Panel "Seleccione un usuario o grupo" con un usuario seleccionado](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Solicitud de aprobación para activar

La solicitud de aprobación no tiene ningún efecto sobre el procedimiento que debe seguir un miembro para realizar la activación. [Revise los pasos para activar un rol](pim-resource-roles-activate-your-roles.md).

Si un miembro ha solicitado la activación de un rol que exige aprobación y el rol ha dejado de ser necesario, el miembro puede cancelar su solicitud en PIM.

Para cancelar, vaya a PIM y seleccione **Mis solicitudes**. Busque la solicitud y seleccione **Cancelar**.

![Panel "Mis solicitudes"](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Aprobación o denegación de una solicitud

Para aprobar o denegar una solicitud es preciso ser miembro de la lista de aprobadores. 

1. En PIM, seleccione **Aprobar solicitudes** en la pestaña del menú izquierdo y busque la solicitud.

   ![Panel "Aprobar solicitudes"](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Seleccione la solicitud, proporcione una justificación para la decisión y seleccione **Aprobar** o **Denegar**. Se resuelve la solicitud.

   ![Solicitud seleccionada con información detallada](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notificaciones de flujo de trabajo

Estos son algunos datos sobre las notificaciones de flujo de trabajo:

- Todos los miembros de la lista de aprobadores reciben una notificación por correo electrónico cuando una solicitud de un rol está pendiente de su revisión. Las notificaciones por correo electrónico incluyen un vínculo directo a la solicitud donde el aprobador puede aprobarla o rechazarla.
- Las solicitudes las resuelve el primer miembro de la lista que aprueba o rechaza. 
- Cuando un aprobador responde a la solicitud, se envía una notificación a todos los miembros de la lista de aprobadores. 
- Los administradores de recursos reciben una notificación cuando un miembro aprobado se activa en su rol. 

>[!Note]
>Un administrador de recursos que cree que un miembro aprobado no debe estar activo puede quitar la asignación de rol activo en PIM. Aunque los administradores de recursos no reciben notificación de solicitudes pendientes, salvo que sean miembros de la lista de aprobadores, pueden ver y cancelar las solicitudes pendientes de todos los usuarios al ver las solicitudes pendientes en PIM. 

## <a name="next-steps"></a>Pasos siguientes

[Aplicación de la configuración de PIM a grupos de usuarios únicos](pim-resource-roles-custom-role-policy.md)
