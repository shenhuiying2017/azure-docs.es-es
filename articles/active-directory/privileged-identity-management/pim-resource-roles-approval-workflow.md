---
title: 'Privileged Identity Management: flujo de trabajo de aprobación roles de recursos de Azure| Microsoft Docs'
description: Describe el proceso del flujo de trabajo de aprobación en los recursos de Azure.
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
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Privileged Identity Management: aprobación de roles de recursos

Con el flujo de trabajo de aprobación de PIM para roles de recursos de Azure, los administradores pueden proteger o restringir aún más el acceso a los recursos críticos mediante la solicitud de aprobación para activar las asignaciones de roles. El concepto de una jerarquía de recursos es único de los roles de recursos de Azure. Esta jerarquía permite la herencia de las asignaciones de roles de un objeto de recurso primario hacia abajo a todos los recursos secundarios subordinados del contenedor primario. 

Por ejemplo Bob, un administrador de recursos, utiliza PIM para asignar Alicia como miembro apto al rol de Propietario de la suscripción de Contoso. Con esta asignación Alice su aptitud se amplía a todos los contenedores del grupo de recursos de la suscripción de Contoso y a todos los recursos (por ejemplo, máquinas virtuales) de cada grupo de recursos de la suscripción. Supongamos que hay tres grupos de recursos en la suscripción a Contoso; Fabrikam Test, Fabrikam Dev y Fabrikam Prod. Cada uno de ellos contiene una única máquina virtual.

Los valores de PIM se configuran en todos los roles de un recurso y, a diferencia de las asignaciones, no se heredan y se aplican estrictamente al rol del recurso. [Más información acerca de asignaciones aptos y visibilidad de recursos.](pim-resource-roles-eligible-visibility.md)

En el ejemplo anterior, Bob utiliza PIM para exigir a todos los miembros del rol Propietario de la suscripción de Contoso la aprobación de una solicitud para activarse. Para proteger los recursos del grupo de recursos Fabrikam Prod, Bob requiere también la aprobación de los miembros del rol Propietario de este recurso. Los roles de propietario de Fabrikam Test y Fabrikam Dev no requieren la aprobación para activarse.

Cuando Alice solicita la activación de su rol Propietario de la suscripción de Contoso, se debe aprobar o denegar su solicitud antes de que se active en el rol. Además, si Alice decide [establecer que el ámbito de su activación](pim-resource-roles-activate-your-roles.md#just-enough-administration) sea el grupo de recursos Fabrikam Prod, dicha solicitud también se aprobar o denegar. Sin embargo, si Alice decide establecer que el ámbito de su activación sean Fabrikam Test o Fabrikam Dev, o ambos, no se requerirá aprobación.

Es posible que el flujo de trabajo de aprobación no sea necesario para todos los miembros de un rol. Considere un escenario donde su organización contrata varios asociados de contrato para ayudarle en el desarrollo de una aplicación que se ejecutará en una suscripción de Azure. Como administrador de recursos, desea que tengan acceso apto sin necesidad de aprobación, pero que los asociados de contrato deban solicitar aprobación. Para configurar el flujo de trabajo de aprobación solo para los asociados de contrato puede crear un rol personalizado con los mismos permisos que el rol asignado a los empleados y solicitar aprobación para activar dicho rol. [Más información acerca de los roles personalizados](pim-resource-roles-custom-role-policy.md).

Siga estos pasos para configurar el flujo de trabajo de aprobación y especificar quién puede aprobar o rechazar solicitudes.

## <a name="require-approval-to-activate"></a>Solicitud de aprobación para activar

Vaya a PIM en Azure Portal y seleccione un recurso de la lista.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

En el menú de navegación izquierdo, seleccione **Configuración de roles**.

Busque un rol, selecciónelo y haga clic en **Editar** para modificar su configuración.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

En la sección Activación, active la casilla **Se requiere aprobación para activar**.

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Especificación de aprobadores

Haga clic en **Seleccionar aprobadores** para abrir la pantalla de selección.

>[!NOTE]
>Debe seleccionar al menos un usuario o grupo para actualizar la configuración. No hay aprobadores predeterminados.

Los administradores de recursos pueden agregar cualquier combinación de usuarios y grupos a la lista de aprobadores. 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Solicitud de aprobación para activar

La solicitud de aprobación no tiene ningún efecto sobre el procedimiento que debe seguir cualquier miembro para realizar la activación. [Revise los pasos para activar un rol](pim-resource-roles-activate-your-roles.md).

Si un miembro solicitó la activación de un rol que requiere aprobación y el rol ha dejado de ser necesario, el miembro puede cancelar su solicitud en PIM.

Para realizar la cancelación, vaya a PIM y seleccione "Mis solicitudes". Busque la solicitud y haga clic en "Cancelar".

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Aprobación o denegación de una solicitud

Para aprobar o denegar una solicitud es preciso ser miembro de la lista de aprobadores. En PIM, seleccione "Aprobar solicitudes" en la pestaña en el menú de navegación izquierdo y busque la solicitud.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

Seleccione la solicitud, proporcione una justificación para la decisión y seleccione aprobar o denegar, con lo que se resuelve la solicitud.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notificaciones de flujo de trabajo

- Todos los miembros de la lista de aprobadores reciben una notificación por correo electrónico cuando una solicitud de un rol está pendiente de su revisión. Las notificaciones por correo electrónico incluyen un vínculo directo a la solicitud donde el aprobador puede aprobarla o rechazarla.
- Las solicitudes las resuelve el primer miembro de la lista que las aprueba o rechaza. 
- Cuando un aprobador responde a la solicitud, se envía una notificación a todos los miembros de la lista de aprobadores. 
- Los administradores de recursos reciben una notificación cuando un miembro aprobado se activa en su rol. 

>[!Note]
>En un evento en el que un administrador de recursos cree que el miembro aprobado no debe estar activo, puede quitar la asignación de rol activo en PIM. Aunque los administradores de recursos no reciben notificación de solicitudes pendientes, salvo que sean miembros de la lista de aprobadores, pueden ver y cancelar las solicitudes pendientes de todos los usuarios. Para ello, solo deben ver las solicitudes pendientes en PIM. 

## <a name="next-steps"></a>Pasos siguientes

[Aplicación de la configuración de PIM a grupos de usuarios únicos](pim-resource-roles-custom-role-policy.md)
