---
title: Delegación de invitaciones de la colaboración B2B de Azure Active Directory | Microsoft Docs
description: Las propiedades de un usuario de colaboración B2B de Azure Active Directory son configurables.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: a4a1303c3992b8c576650859eea65591392b9b20
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929312"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegación de invitaciones de la colaboración B2B de Azure Active Directory

Con la colaboración negocio a negocio (B2B) de Azure Active Directory (Azure AD), no tiene que ser un administrador global para enviar invitaciones. Puede usar directivas y delegar invitaciones en usuarios cuyos roles les permitan enviar invitaciones. Una nueva forma importante de delegar las invitaciones de usuarios invitados es a través del rol de invitador invitado.

## <a name="guest-inviter-role"></a>Rol de invitador de personas
Podemos asignamos al usuario el rol de invitador invitado para enviar invitaciones. No tiene que ser miembro del rol administrador global para enviar invitaciones. De forma predeterminada, los usuarios normales también pueden invocar la API de invitación, a menos que un administrador global haya deshabilitado las invitaciones para los usuarios normales. Un usuario también puede invocar la API mediante Azure Portal o PowerShell.

Este es un ejemplo que muestra cómo usar PowerShell para agregar un usuario al rol Invitador de personas:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Controlar quién puede invitar

![Controlar el procedimiento de invitación](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Gracias a la colaboración B2B de Azure AD, el administrador de inquilinos puede establecer las siguientes directivas de invitación:

- Desactivar invitaciones
- Solo los administradores y usuarios del rol Invitador de personas pueden invitar
- Los administradores, el rol Invitador de personas y los miembros pueden invitar
- Todos los usuarios, incluidos los invitados, pueden invitar

De forma predeterminada, los inquilinos se establecen en #4. (Todos los usuarios, incluidos los invitados, pueden invitar a usuarios B2B).

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos sobre la colaboración de B2B de Azure AD:

- [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
- [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)


