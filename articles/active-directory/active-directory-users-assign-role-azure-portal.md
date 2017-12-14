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
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 37f6049f2fc39bfa93135ce7c0d0013d7e37217c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Asignación de un usuario a roles de administrador en Azure Active Directory
En este artículo se explica cómo asignar un rol administrativo a un usuario en Azure Active Directory (Azure AD). Para obtener más información sobre cómo agregar nuevos usuarios en su organización, consulte [Incorporación de nuevos usuarios a Azure Active Directory](active-directory-users-create-azure-portal.md). De forma predeterminada, los usuarios agregados no tienen permisos de administrador, pero puede asignárselos en cualquier momento.

## <a name="assign-a-role-to-a-user"></a>Asignar de un rol a un usuario
1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Usuarios y grupos**.

   ![Apertura de Administración de usuarios](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Seleccione **Todos los usuarios**.

   ![Apertura de todos los grupos de usuarios](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
1. Seleccione un usuario en la lista.
2. En el usuario seleccionado, seleccione **Rol de directorio**, y, luego, asigne al usuario un rol de la lista **Rol de directorio**. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles-azure-portal.md).

      ![Asignación de un rol a un usuario](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
* [Adición de un usuario](active-directory-users-create-azure-portal.md)
* [Restablecer una contraseña de usuario en el nuevo Azure Portal](active-directory-users-reset-password-azure-portal.md)
* [Cambiar la información de trabajo de un usuario](active-directory-users-work-info-azure-portal.md)
* [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
* [Eliminar un usuario de Azure AD](active-directory-users-delete-user-azure-portal.md)
