---
title: "Incorporación de usuarios de otros directorios o compañías asociadas a la versión preliminar de Azure Active Directory | Microsoft Docs"
description: "Explica cómo agregar usuarios o cambiar la información de usuarios en Azure Active Directory, incluidos los usuarios externos e invitados."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e4f7a52c-e782-4bdf-a04e-2174e310785b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 3b31bd036d9c3ff8036b314b93cbddd94874ff63
ms.openlocfilehash: 9ccf5cad400321404981868a7ee9a4b86c396c6c


---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory-preview"></a>Incorporación de usuarios de otros directorios o compañías asociadas en la versión preliminar de Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-users-create-external-azure-portal.md)
> * [Portal de Azure clásico](active-directory-create-users-external.md)
>
>

En este artículo se explica cómo agregar usuarios de otros directorios de la versión preliminar de Azure Active Directory (Azure AD) o de compañías asociadas. [¿Qué hay en la versión preliminar?](active-directory-preview-explainer.md) Para más información sobre cómo agregar nuevos usuarios a su organización y cómo agregar usuarios con cuentas Microsoft, consulte el tema acerca de cómo [agregar o cambiar usuarios en Azure Active Directory](active-directory-users-create-azure-portal.md). De forma predeterminada, los usuarios agregados no tienen permisos de administrador, pero puede asignárselos en cualquier momento.

## <a name="add-a-user"></a>Adición de un usuario
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

   ![Apertura de Administración de usuarios](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)
3. En la hoja **Usuarios y grupos**, seleccione **Usuarios** y **Agregar**.

   ![Selección del comando Agregar](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)
4. En la hoja **Usuario**, proporcione un nombre para mostrar en **Nombre** y el nombre de inicio de sesión del usuario en **Nombre de usuario**.
5. Copie o anote la contraseña de usuario generada para poder proporcionársela al usuario cuando finalice este proceso.
6. Si lo desea, seleccione **Perfil** para agregar la siguiente información de los usuarios: el nombre y los apellidos, un puesto de trabajo y un departamento.

    ![Apertura del perfil de usuario](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

   * Seleccione **Grupos** para agregar el usuario a uno o varios grupos.

       ![Incorporación de un usuario a grupos](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)
   * Seleccione **Rol organizativo** para asignar el usuario a un rol de la lista **Roles**. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md).

       ![Asignación de un rol a un usuario](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)
7. Seleccione **Crear**.
8. Distribuya de manera segura la contraseña generada al nuevo usuario para que pueda iniciar sesión.

> [!IMPORTANT]
> Si su organización usa más de un dominio, debe tener en cuenta los siguientes problemas al agregar una cuenta de usuario:
>
> * Para agregar cuentas de usuario con el mismo nombre principal de usuario (UPN) en todos los dominios, agregue **primero**, por ejemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido de** geoffgrisso@contoso.com.
> * **No** agregue geoffgrisso@contoso.com antes de geoffgrisso@contoso.onmicrosoft.com. Este orden es importante y puede ser complicado de deshacer.
>
>

Si cambia la información de un usuario cuya identidad se sincroniza con el servicio Active Directory local, no puede cambiar la información del usuario en el Portal de Azure clásico. Para cambiar la información del usuario, utilice las herramientas de administración locales de Active Directory.

## <a name="next-steps"></a>Pasos siguientes
* [Adición de un usuario](active-directory-users-create-azure-portal.md)
* [Restablecer una contraseña de usuario en el nuevo Azure Portal](active-directory-users-reset-password-azure-portal.md)
* [Asignar a un usuario a un rol de Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Cambiar la información de trabajo de un usuario](active-directory-users-work-info-azure-portal.md)
* [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
* [Eliminar un usuario de Azure AD](active-directory-users-delete-user-azure-portal.md)



<!--HONumber=Feb17_HO3-->


