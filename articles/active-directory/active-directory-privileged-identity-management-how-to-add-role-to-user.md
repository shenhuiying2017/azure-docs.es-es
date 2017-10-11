---
title: "Incorporación o eliminación de un rol de usuario | Microsoft Docs"
description: "Aprenda a agregar roles a identidades con privilegios con la aplicación Privileged Identity Management de Azure Active Directory."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 6a47ced8-cf34-4ce8-bea2-e4fc548cfe22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim;oldportal;it-pro;
ms.openlocfilehash: 3ac07bb7b070f44595c099a454b3d0dbc66126c9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Privileged Identity Management de Azure AD: Incorporación o eliminación de un rol de usuario
Con Azure Active Directory (AD), un administrador global (o un administrador de empresa) puede actualizar a los usuarios que están asignados **permanentemente** a roles en Azure AD. Para ello, se usan cmdlets de PowerShell, como `Add-MsolRoleMember` y `Remove-MsolRoleMember`. O bien, se puede utilizar el Portal de Azure clásico, como se describe en [Asignación de roles de administrador en Azure Active Directory (Azure AD)](active-directory-assign-admin-roles.md).

La aplicación Privileged Identity Management de Azure AD permite también a los administradores de roles con privilegios realizar asignaciones de roles permanentes. Además, los administradores de rol con privilegios pueden hacer que los usuarios sean **aptos** para roles de administrador. Un administrador apto puede activar el rol cuando lo necesite y, cuando termina, sus permisos caducan.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Administración de roles con PIM en el Portal de Azure
En su organización, puede asignar a los usuarios roles administrativos diferentes en Azure AD, Office 365 y otros servicios y aplicaciones de Microsoft.  Encontrará más detalles sobre los roles disponibles en [Privileged Identity Management de Azure AD: Roles](active-directory-privileged-identity-management-roles.md).

Para agregar o quitar un usuario en un rol mediante Privileged Identity Management, abrir el panel PIM. Después, haga clic en el botón **Usuarios en roles de administrador** o seleccione un rol específico (por ejemplo, un administrador global) en la tabla de roles.

> [!NOTE]
> Si aún no ha habilitado PIM en el Portal de Azure, vaya a [Introducción a Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) para más información.

Si desea dar a otro usuario acceso a PIM, los roles que debe tener el usuario para PIM se describen con más detalle en [How to give access to manage Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)(Cómo proporcionar acceso para administrar Privileged Identity Management de Azure AD).

## <a name="add-a-user-to-a-role"></a>Adición de un usuario a un rol
1. En el [Portal de Azure](https://portal.azure.com/), seleccione el icono **Privileged Identity Management de Azure AD** en el panel.
2. Seleccione **Administrar roles con privilegios**.
3. En la tabla **Resumen de roles** , seleccione el rol que quiere administrar.
4. En la hoja de rol, seleccione **Agregar**.
5. Haga clic en **Seleccionar usuarios** y busque el usuario en la hoja **Seleccionar usuarios**.  
6. Seleccione el usuario en la lista de resultados de búsqueda y haga clic en **Listo**.
7. Haga clic en **Aceptar** para guardar la selección. El usuario que ha seleccionado aparecerá en la lista como apto para el rol.

> [!NOTE]
> Los nuevos usuarios de un rol solo son aptos para el rol de forma predeterminada. Si quiere que el rol sea permanente, haga clic en el usuario en la lista. La información del usuario aparecerá en una nueva hoja. Seleccione **Establecer como permanente** en el menú de información de usuario.  
> Si un usuario no se puede registrar para Azure Multi-factor Authentication (MFA), o está usando una cuenta de Microsoft (normalmente @outlook.com), necesario para que sean permanentes en todas sus funciones. Los administradores aptos deben registrarse en MFA durante la activación.

Ahora que el usuario es apto para un rol, hágale saber que puede activarlo de acuerdo con las instrucciones que se describen en [Activación y desactivación de un rol](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Eliminación de un usuario de un rol
Puede quitar a los usuarios de las asignaciones de rol apto, pero asegúrese de que siempre haya al menos un usuario que sea un administrador global permanente.

Siga estos pasos para quitar a un usuario específico de un rol:

1. Vaya al rol en la lista de roles, para ello, seleccione un rol en el panel de PIM de Azure AD o haga clic en el botón **Usuarios en roles de administrador** .
2. Haga clic en el usuario en la lista de usuarios.
3. Haga clic en **Quitar**. Un mensaje le pedirá que confirme la operación.
4. Haga clic en **Sí** para quitar el rol del usuario.

Si no está seguro de si los usuarios necesitan aún sus asignaciones de roles, puede [iniciar una revisión de acceso del rol](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

