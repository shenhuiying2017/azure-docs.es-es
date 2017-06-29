---

title: "Incorporación de nuevos usuarios en Azure Active Directory | Microsoft Docs"
description: "Explica cómo agregar nuevos usuarios o cambiar la información del usuario en Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: curtand;jeffsta
ms.reviewer: jeffsta
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 5f1f28b6e0135fbd76fa5f6664f457f259dd9dd4
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017



---
# <a name="add-new-users-to-azure-active-directory"></a>Adición de nuevos usuarios a Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-users-create-azure-portal.md)
> * [Portal de Azure clásico](active-directory-create-users.md)
>
>

En este artículo se explica cómo agregar nuevos usuarios de su organización en Azure Active Directory (Azure AD). 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

   ![Apertura de usuarios y grupos](./media/active-directory-users-create-azure-portal/create-users-user-management.png)
3. En la hoja **Usuarios y grupos**, seleccione **Todos los grupos** y, luego, **Agregar**.

   ![Selección del comando Agregar](./media/active-directory-users-create-azure-portal/create-users-add-command.png)
4. Especifique los detalles del usuario, como el **nombre** y el **nombre de usuario**. La parte del nombre de dominio del nombre de usuario debe ser el nombre de dominio del nombre de dominio predeterminado inicial foo.onmicrosoft.com o uno comprobado y no federado, como contoso.com.
5. Copie o anote la contraseña de usuario generada para poder proporcionársela al usuario cuando finalice este proceso.
6. También puede abrir y rellenar la información de las hojas **Perfil**, **Grupos** o **Rol del directorio** del usuario. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md).
7. En la hoja **Usuario**, seleccione **Crear**.
8. Distribuya de manera segura la contraseña generada al nuevo usuario para que pueda iniciar sesión.

### <a name="next-steps"></a>Pasos siguientes
* [Agregar un usuario externo](active-directory-users-create-external-azure-portal.md)
* [Restablecer una contraseña de usuario en el nuevo Azure Portal](active-directory-users-reset-password-azure-portal.md)
* [Cambiar la información de trabajo de un usuario](active-directory-users-work-info-azure-portal.md)
* [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
* [Eliminar un usuario de Azure AD](active-directory-users-delete-user-azure-portal.md)
* [Asignar a un usuario a un rol de Azure AD](active-directory-users-assign-role-azure-portal.md)

