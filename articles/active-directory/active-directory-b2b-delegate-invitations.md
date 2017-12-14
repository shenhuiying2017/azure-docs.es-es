---
title: "Delegación de invitaciones de la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Las propiedades de un usuario de colaboración B2B de Azure Active Directory son configurables."
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 08d06153ab64021d94cd3fdc8ac13839bb4c6908
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
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

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)
