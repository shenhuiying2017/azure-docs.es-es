---
title: "Adición o eliminación de usuarios en Azure Active Directory | Microsoft Docs"
description: Describe como agregar usuarios nuevos o eliminar usuarios existentes en Azure Active Directory.
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: f0f18f377f194e78c05e63e9b6cbc31c1b945335
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Inicio rápido: incorporación de nuevos usuarios a Azure Active Directory
En este artículo se explica cómo eliminar o agregar usuarios de su organización en el inquilino de Azure Active Directory (Azure AD) de la organización mediante Azure Portal o mediante la sincronización de los datos de las cuentas de usuario de Windows Server AD locales. 

## <a name="add-cloud-based-users"></a>Adición de usuarios basados en la nube
1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Azure Active Directory** y, después, **Usuarios y grupos**.
3. En **Usuarios y grupos**, seleccione **Todos los usuarios** y **Nuevo usuario**.
   ![Selección del comando Agregar](./media/add-users-azure-active-directory/add-user.png)
4. Especifique los detalles del usuario, como el **nombre** y el **nombre de usuario**. La parte del nombre de dominio del nombre de usuario debe ser el nombre de dominio del nombre de dominio predeterminado inicial "[nombre de dominio].onmicrosoft.com" o un [nombre de dominio personalizado](add-custom-domain.md) comprobado y no federado, como "contoso.com".
5. Copie o anote la contraseña de usuario generada para poder proporcionársela al usuario cuando finalice este proceso.
6. También puede abrir y rellenar la información de **Perfil**, **Grupos** o **Rol del directorio** del usuario. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles-azure-portal.md).
7. En **Usuario**, seleccione **Crear**.
8. Distribuya de manera segura la contraseña generada al nuevo usuario para que pueda iniciar sesión.

> [!TIP]
> También puede sincronizar datos de las cuentas de usuario desde una instancia local de Windows Server AD. Las soluciones de identidad de Microsoft abarcan funcionalidades locales y de nube, de forma que se crea una sola identidad de usuario para la autenticación y la autorización en todos los recursos, sin importar su ubicación. A esto le llamamos identidad híbrida. [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) se puede usar para integrar directorios locales con Azure Active Directory para obtener escenarios de identidad híbridos. Esto le permite proporcionar una identidad común para los usuarios de aplicaciones de Office 365, Azure y SaaS integradas con Azure AD. 

## <a name="delete-users-from-azure-ad"></a>Eliminación de usuarios desde Azure AD
1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Usuarios y grupos**.
3. En la hoja **Usuarios y grupos**, seleccione el usuarios que va a eliminar de la lista. 
4. En la hoja del usuario seleccionado, seleccione **Información general** y, después, en la barra de comandos, seleccione **Eliminar**.
   ![Selección del comando Agregar](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>Más información 
* [Adición de usuarios invitados de otro directorio](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Asignar a un usuario a un rol de Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
* [Restaurar un usuario eliminado](active-directory-users-restore.md)



## <a name="next-steps"></a>pasos siguientes
En esta guía de inicio rápido, ha aprendido a agregar usuarios nuevos a Azure AD Premium. 

Puede usar el vínculo siguiente para crear un usuario nuevo en Azure AD desde Azure Portal.

> [!div class="nextstepaction"]
> [Adición de usuarios a Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 