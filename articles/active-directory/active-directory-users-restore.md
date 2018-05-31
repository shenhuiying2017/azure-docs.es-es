---
title: Restauración o eliminación permanente de un usuario recién eliminado en Azure Active Directory | Microsoft Docs
description: Cómo restaurar un usuario eliminado, ver los usuarios que se pueden restaurar o eliminar permanente un usuario en Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/09/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 8dbb546954f8eee0bf997b3d2f4f92d67c0b7869
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930881"
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Restaurar un usuario eliminado en Azure Active Directory

En este artículo se detallan las instrucciones para restaurar o eliminar de forma permanente un usuario previamente eliminado. Cuando elimina un usuario en Azure Active Directory (Azure AD), el usuario eliminado queda retenido durante 30 días a contar desde la fecha de eliminación. Durante ese tiempo, se pueden restaurar tanto el usuario como sus propiedades. 

> [!wARNING]
> Después de que se elimina permanentemente el usuario, no se puede restaurar.


## <a name="how-to-restore-a-recently-deleted-user"></a>Cómo restaurar un usuario recién eliminado
Cuando recién se elimina un usuario, se conserva toda la información de directorio. Si se restaura el usuario, también se restaura esa información.

1. En el [centro de administración de Azure AD](https://aad.portal.azure.com), seleccione **Usuarios** &gt; **Usuarios eliminados**. 
2. Seleccione uno o más usuarios que se hayan eliminado recientemente.
3. Seleccione **Restaurar usuario**.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>Cómo eliminar permanentemente un usuario recién eliminado

1. En el [centro de administración de Azure AD](https://aad.portal.azure.com), seleccione **Usuarios** &gt; **Usuarios eliminados**. 
2. Seleccione uno o más usuarios que se hayan eliminado recientemente.
3. Seleccione **Eliminar permanentemente**.

## <a name="required-permissions"></a>Permisos necesarios
Los siguientes permisos son suficientes para restaurar un usuario.

Rol  | Permisos 
--------- | ---------
Administrador de empresa<p>Soporte para asociados de nivel 1<p>Soporte para asociados de nivel 2<p>Administrador de cuenta de usuario | Puede restaurar usuarios eliminados 
Administrador de empresa<p>Soporte para asociados de nivel 1<p>Soporte para asociados de nivel 2<p>Administrador de cuenta de usuario | Pueden eliminar usuarios permanentemente.

## <a name="next-steps"></a>Pasos siguientes
En estos artículos se proporciona información adicional sobre la administración de usuarios de Azure Active Directory.

* [Inicio rápido: agregar o eliminar usuarios en Azure Active Directory](add-users-azure-active-directory.md)
* [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
* [Adición de usuarios invitados de otro directorio](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Asignar a un usuario a un rol de Azure AD](active-directory-users-assign-role-azure-portal.md)
