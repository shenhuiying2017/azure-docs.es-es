---
title: "Asignación de un usuario a roles de administrador en Azure Active Directory | Microsoft Docs"
description: "Explica cómo cambiar la información administrativa de los usuarios en Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: dcb52e9de98d881474007410f3db599682e151ce
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Asignación de un usuario a roles de administrador en Azure Active Directory
En este artículo se explica cómo asignar un rol administrativo a un usuario en Azure Active Directory (Azure AD). Para obtener más información sobre cómo agregar nuevos usuarios en su organización, consulte [Incorporación de nuevos usuarios a Azure Active Directory](active-directory-users-create-azure-portal.md). De forma predeterminada, los usuarios agregados no tienen permisos de administrador, pero puede asignárselos en cualquier momento.

## <a name="assign-a-role-to-a-user"></a>Asignar de un rol a un usuario
1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Usuarios y grupos**.

   ![Apertura de Administración de usuarios](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Seleccione **Todos los usuarios**.
  
  ![Apertura de todos los grupos de usuarios](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Seleccione un usuario en la lista.
5. En el usuario seleccionado, seleccione **Rol de directorio**, y, luego, asigne al usuario un rol de la lista **Rol de directorio**. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles-azure-portal.md).

      ![Asignación de un rol a un usuario](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Seleccione **Guardar**.

## <a name="next-steps"></a>pasos siguientes
* [Guía de inicio rápido: Agregar o eliminar usuarios en Azure Active Directory](add-users-azure-active-directory.md)
* [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
* [Adición de usuarios invitados de otro directorio](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Asignar a un usuario a un rol de Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Restaurar un usuario eliminado](active-directory-users-restore.md)
