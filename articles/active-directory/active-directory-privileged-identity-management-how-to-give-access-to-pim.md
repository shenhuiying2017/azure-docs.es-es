---
title: "Concesión de acceso para Privileged Identity Management - Azure | Microsoft Docs"
description: "Aprenda a agregar roles a los usuarios con la extensión Privileged Identity Management de Azure Active Directory para que así puedan administrar PIM."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: d4c53b53-2b37-41e6-813c-96ec08a1c897
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: aeaefb484b29da6e89c2c3c650a79a881b3fa5b6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Concesión de acceso para administrar Azure AD Privileged Identity Management
El administrador global que habilita Privileged Identity Management (PIM) de Azure AD para una organización, obtiene automáticamente asignaciones de roles y acceso a PIM. Nadie más obtiene acceso de escritura de forma predeterminada, ni siquiera otros administradores globales. Otros administradores globales, administradores de seguridad y lectores de seguridad tienen acceso de solo lectura a PIM de Azure AD. Para proporcionar acceso a PIM, el primer usuario puede asignar a otros el rol **Administrador de rol con privilegios** . Esta asignación se debe realizar desde dentro de PIM y no se puede cambiar mediante PowerShell u otros portales.

> [!NOTE]
> La administración de PIM de Azure AD requiere Azure MFA. Dado que las cuentas de Microsoft no se pueden registrar en Azure MFA, un usuario que inicia sesión con una cuenta de Microsoft no puede tener acceso a PIM de Azure AD.
> 
> 

Asegúrese de que siempre haya al menos dos usuarios en un rol de administrador de roles con privilegios, por si se diera el caso de que a un usuario se le impida el acceso o su cuenta se haya eliminado.

## <a name="give-another-user-access-to-manage-pim"></a>Concesión de acceso a otro usuario para administrar PIM
1. Vaya al [Portal de Azure](https://portal.azure.com/) y seleccione la aplicación **Privileged Identity Management de Azure AD** en el panel.
2. Seleccione **Administrar roles con privilegios** > **Administrador de rol con privilegios** > **Agregar**.
   
    ![Agregar administradores de roles con privilegios: captura de pantalla][1]
3. En la hoja Agregar usuarios administrados, el paso 1 ya está completo. Seleccione el paso 2, **Seleccionar usuarios** y busque el usuario que quiere agregar.
   
    ![Seleccionar usuarios: captura de pantalla][2]
4. Seleccione el usuario en los resultados de búsqueda y haga clic en **Listo**.
5. Haga clic en **Aceptar** para guardar la selección. El usuario que ha seleccionado aparecerá en la lista de administradores de roles con privilegios.
   
   * Cuando asigne un nuevo rol a alguien, esta persona se configura automáticamente como apto para activar este rol. Si quiere establecer el rol como permanente para este usuario, haga clic en él en la lista. Seleccione **convertir en permanente** en el menú de información de usuario.
6. Envíe al usuario un vínculo a [Introducción a Privileged Identity Management de Azure AD](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Eliminación de los derechos de acceso de otro usuario para administrar PIM
Antes de eliminar a alguien del rol de administrador de roles con privilegios, asegúrese siempre de que haya dos usuarios asignados a él.

1. En el panel de PIM, haga clic en el rol **Administrador de roles con privilegios**.  Se mostrará la lista de usuarios que tienen actualmente ese rol.
2. Haga clic en el usuario en la lista de usuarios.
3. Haga clic en **Quitar**.  Aparecerá un mensaje de confirmación.
4. Haga clic en **Sí** para quitar el usuario del rol.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
