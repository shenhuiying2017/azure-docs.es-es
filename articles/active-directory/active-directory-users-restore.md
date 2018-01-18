---
title: Restaurar un usuario eliminado en Azure Active Directory | Microsoft Docs
description: "Cómo restaurar un usuario eliminado, ver los usuarios que se pueden restaurar o eliminar permanente un usuario en Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: c3b7550c2aea0e8bcb7998e0e8c732894b500403
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Restaurar un usuario eliminado en Azure Active Directory

En este artículo se detallan las instrucciones para restaurar o eliminar de forma permanente un usuario previamente eliminado. Cuando elimina un usuario en Azure Active Directory (Azure AD), el usuario eliminado queda retenido durante 30 días a contar desde la fecha de eliminación. Durante ese tiempo, se pueden restaurar tanto el usuario como sus propiedades. 

## <a name="required-permissions"></a>Permisos necesarios
Los siguientes permisos son suficientes para restaurar un usuario.

Rol  | Permisos 
--------- | ---------
Administrador de empresa<p>Soporte para asociados de nivel 1<p>Soporte para asociados de nivel 2<p>Administrador de cuenta de usuario | Puede restaurar usuarios eliminados 
Administrador de empresa<p>Soporte para asociados de nivel 1<p>Soporte para asociados de nivel 2<p>Administrador de cuenta de usuario | Pueden eliminar usuarios permanentemente.

## <a name="how-to-restore-a-deleted-user"></a>Cómo restaurar un usuario eliminado

En Azure Portal puede restaurar o eliminar permanentemente un usuario que ya haya sido eliminado.

1. En el [centro de administración de Azure AD](https://aad.portal.azure.com), seleccione **Usuarios y grupos** &gt; **Todos los usuarios**. 
2. En **Mostrar**, filtre la página para mostrar **Usuarios eliminados recientemente**. 
3. Seleccione uno o más usuarios que se hayan eliminado recientemente.
4. Seleccione **Restaurar usuario** o **Eliminar permanentemente**.

## <a name="next-steps"></a>pasos siguientes
En estos artículos se proporciona información adicional sobre la administración de usuarios de Azure Active Directory.

* [Inicio rápido: agregar o eliminar usuarios en Azure Active Directory](add-users-azure-active-directory.md)
* [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
* [Adición de usuarios invitados de otro directorio](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Asignar a un usuario a un rol de Azure AD](active-directory-users-assign-role-azure-portal.md)
