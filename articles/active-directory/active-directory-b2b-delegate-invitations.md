---
title: "Delegación de invitaciones de la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Las propiedades de un usuario de colaboración B2B de Azure Active Directory son configurables."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 6c6d757a770613498bedca0f8f3a965241d692eb


---

# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegación de invitaciones de la colaboración B2B de Azure Active Directory

Con la actualización de la versión preliminar pública de la colaboración B2B de Azure Active Directory (Azure AD), no tiene que ser administrador global para invitar a usuarios. Puede controlar quién puede invitar a través de directivas y delegar invitaciones en los usuarios de los roles con permisos para invitar. Una nueva forma importante de delegar las invitaciones de usuarios invitados es a través del rol de invitador invitado.

## <a name="guest-inviter-role"></a>Rol de invitador invitado
Podemos asignamos al usuario el rol de invitador invitado para enviar invitaciones. No tienes que ser miembro del grupo de administrador global para enviar invitaciones. De forma predeterminada, los usuarios normales también pueden invocar la API de invitación, a menos que un administrador global haya deshabilitado las invitaciones para los usuarios normales. Puede hacerlo a través de Azure Portal y también de PowerShell.

Este es un ejemplo que muestra la incorporación de un usuario al rol de invitador invitado a través de PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress >
```

## <a name="controlling-who-can-invite"></a>Control de a quién se puede invitar

![Control del procedimiento de invitación](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Gracias a la colaboración B2B de Azure AD, el administrador de inquilinos puede establecer las siguientes directivas de invitación:

1. Desactivar invitaciones
2. Solo los administradores y usuarios del rol de invitador invitado pueden invitar
3. Solo los administradores, el rol de invitador invitado y los miembros pueden invitar
4. Todos los usuarios, incluidos los invitados, pueden invitar

De forma predeterminada, los inquilinos tienen la directiva 4 (todos los usuarios, incluidos los invitados, pueden invitar a usuarios B2B).

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



<!--HONumber=Feb17_HO1-->


