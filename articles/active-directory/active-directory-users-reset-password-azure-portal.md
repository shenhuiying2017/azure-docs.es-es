---
title: "Restablecimiento de contraseñas en Azure Active Directory | Microsoft Docs"
description: "El administrador inició el restablecimiento de contraseña de un usuario en Azure Active Directory"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: bea082081e3f3f52ba78188903a9536fe9de9392
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Restablecimiento de la contraseña de un usuario en Azure Active Directory

Los administradores pueden tener que restablecer la contraseña de un usuario en escenarios en los que la han olvidado, se han bloqueado, etc. Los pasos siguientes le guiarán en el restablecimiento de la contraseña de un usuario.

## <a name="how-to-reset-the-password-for-a-user"></a>Restablecimiento de la contraseña de un usuario

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga permisos de directorio para restablecer contraseñas de usuario.
2. Seleccione **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios**.
3. Seleccione el usuario para el que desee restablecer la contraseña.
2. Para el usuario seleccionado, seleccione **Restablecer contraseña**.

    ![Restablecimiento de la contraseña de un usuario desde el perfil de usuario en Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. En **Restablecer contraseña**, seleccione **Restablecer contraseña**.
7. Se muestra una contraseña temporal que puede proporcionar al usuario. Se le solicitará al usuario que cambie la contraseña la próxima vez que inicie sesión. 

   > [!NOTE]
   > Esta contraseña temporal no tiene tiempo de expiración, por lo que será válida hasta que el usuario inicie sesión y se le fuerce a cambiarla. 

## <a name="next-steps"></a>Pasos siguientes
* [Adición de un usuario](active-directory-users-create-azure-portal.md)
* [Asignación de roles de administrador a un usuario](active-directory-users-assign-role-azure-portal.md)
* [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
* [Eliminación de un usuario en Azure AD](active-directory-users-delete-user-azure-portal.md)
