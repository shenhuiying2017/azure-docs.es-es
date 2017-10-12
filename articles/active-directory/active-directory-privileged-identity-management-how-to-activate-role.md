---
title: "Activación o desactivación de un rol | Microsoft Docs"
description: "Aprenda a activar roles para identidades con privilegios con la aplicación Privileged Identity Management de Azure."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1ce9e2e7-452b-4f66-9588-0d9cd2539e45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a143fd78eae52fda0cbadb7e74fd8209f24629a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Activación y desactivación de roles en Privileged Identity Management de Azure AD
Privileged Identity Management de Azure Active Directory (AD) simplifica el modo en que las empresas administra el acceso con privilegios a los recursos de Azure AD y en otros servicios en línea de Microsoft como Office 365 o Microsoft Intune.  

Si se le ha considerado apto para un rol administrativo, significa que puede activar el rol cuando tenga la necesidad de realizar acciones que requieran ciertos privilegios. Por ejemplo, si administra de vez en cuando características de Office 365, los administradores de roles con privilegios de su organización puede que no le hayan asignado el rol de administrador global permanente, ya que ese rol afecta también a otros servicios. En su lugar, pueden asignarle roles de Azure AD como administrador de Exchange Online. Puede solicitar la activación de ese rol cuando necesite sus privilegios y entonces tendrá control administrativo durante un período predeterminado.

Este artículo va dirigido a los administradores que necesitan activar su rol en Privileged Identity Management (PIM) de Azure AD. Le llevará por los pasos para activar un rol cuando necesite los permisos y desactivar el rol cuando haya terminado. Además, los administradores con roles con privilegios pueden requerir aprobación para activar un rol (versión preliminar). Obtenga más información sobre [flujos de trabajo de aprobación de PIM](./privileged-identity-management/azure-ad-pim-approval-workflow.md) aquí.

## <a name="add-the-privileged-identity-management-application"></a>Incorporación de la aplicación Privileged Identity Management
Use la aplicación Privileged Identity Management de Azure AD en el [Portal de Azure](https://portal.azure.com/) para solicitar una activación del rol aunque vaya a trabajar en otro portal o mediante PowerShell. Si no tiene la aplicación en el Portal de Azure, siga estos pasos para comenzar:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione su nombre de usuario en la esquina superior derecha del Portal de Azure y elija el directorio donde va a trabajar.
3. Seleccione **Más servicios** y use el cuadro de texto Filtro para buscar **Azure AD Privileged Identity Management**.
4. Active **Anclar al panel** y haga clic en **Crear**. Se abre la aplicación Privileged Identity Management.

## <a name="activate-a-role"></a>Activación de un rol
Cuando necesite asumir un rol, puede solicitar la activación de una solicitud seleccionando la opción de exploración **Mis roles** en la columna de exploración de la izquierda, en la aplicación Privileged Identity Management de Azure AD.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y seleccione el icono de Privileged Identity Management de Azure AD.
2. Seleccione **My Roles** (Mis roles). Aparecerá una lista de sus roles aptos asignados en la agrupación en la parte superior de la página.
3. Seleccione un rol para activar.
4. Seleccione **Activar**. Aparecerá la hoja **Solicitar activación del role** .
5. Algunos roles requieren Multi-Factor Authentication (MFA) para poder activarlos. Solo tiene que autenticarse una vez por sesión.
   
    ![Comprobación con MFA antes de la activación del rol: captura de pantalla][2]
6. En el campo de texto, escriba el motivo de la solicitud de activación.  Algunos roles requieren que proporcione un número de vale de problema.
7. Seleccione **Aceptar**.  Si el rol no requiere aprobación, ahora se activa y aparece en la lista de roles activos (directamente debajo de la lista de asignaciones de roles válidos). Si el [rol requiere aprobación](./privileged-identity-management/azure-ad-pim-approval-workflow.md) para activarse, aparecerá brevemente una notificación del sistema en la esquina superior derecha del explorador que le informa de que la solicitud está pendiente de aprobación.

    ![Notificación de solicitud pendiente: captura de pantalla][3]

## <a name="deactivate-a-role"></a>Desactivación de un rol
Cuando se ha activado un rol, si alcanza su límite de tiempo (de duración elegible), se desactiva automáticamente.

Si completa pronto sus tareas administrativas, también puede desactivar un rol de forma manual en la aplicación Privileged Identity Management de Azure AD.  Seleccione **Mis roles**, elija el rol con el que haya terminado en la agrupación **Asignaciones de roles activas** y, a continuación, seleccione **Desactivar**.  

## <a name="cancel-a-pending-request"></a>Cancelación de una solicitud pendiente
En caso de que no necesite activar un rol que requiera aprobación, puede cancelar una solicitud pendiente en cualquier momento. Basta con seleccionar la opción de exploración **Mis roles** en la columna de exploración de la izquierda, en la aplicación Privileged Identity Management de Azure AD.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y seleccione el icono de Privileged Identity Management de Azure AD.
2. Seleccione **My Roles** (Mis roles). Aparecerá una lista de sus roles aptos asignados en la agrupación en la parte superior de la página.
3. Seleccione un rol.
4. Seleccione entonces el banner **Activation is pending approval** (La activación está pendiente de aprobación) en la hoja de detalles de activación del rol.
5. Seleccione **Cancelar** en la parte superior de la hoja **Pendiente de aprobación**.

   ![Captura de pantalla de la solicitud de cancelación pendiente][4]

## <a name="next-steps"></a>Pasos siguientes
Si le interesa aprender más sobre Privileged Identity Management de Azure AD, puede encontrar más información en los siguientes vínculos.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
