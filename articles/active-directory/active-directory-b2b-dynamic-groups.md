---
title: "Grupos dinámicos y colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Se muestra cómo usar grupos dinámicos de Azure AD con colaboración B2B de Active Directory de Azure"
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
ms.date: 12/14/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: d60b7f70332d6183a67003c1dceb96241fc6bae4
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinámicos y colaboración B2B de Azure Active Directory

## <a name="what-are-dynamic-groups"></a>¿Qué son los grupos dinámicos?
La configuración dinámica de pertenencia a grupos de seguridad de Azure Active Directory (Azure AD) está disponible en [Azure Portal](https://portal.azure.com). Los administradores pueden establecer reglas para rellenar los grupos que se crean en Azure AD en función de los atributos de usuario (por ejemplo, userType, department o country). Los miembros se agregan automáticamente a un grupo de seguridad, o se quitan de este, según sus atributos. Estos grupos pueden proporcionar acceso a aplicaciones o recursos en la nube (documentos y sitios de SharePoint) y para asignar licencias a miembros. Obtenga más información sobre los grupos dinámicos en [Grupos dedicados en Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

La [licencia Azure AD Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory/) apropiada se necesita para crear y usar grupos dinámicos. Obtenga más información en el artículo [Creación de reglas de pertenencia dinámica a grupos basadas en atributos en Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>¿Cuáles son los grupos dinámicos integrados?
El grupo dinámico **Todos los usuarios** permite a los administradores de inquilinos crear un grupo que contenga todos los usuarios del inquilino con un solo clic. De forma predeterminada, el grupo **Todos los usuarios** incluye todos los usuarios del directorio, incluidos los miembros y los invitados.
En el nuevo portal de administración de Azure Active Directory, puede optar por habilitar el grupo **Todos los usuarios** en la vista de configuración del grupo.

![Se muestra cómo establecer el grupo Todos los usuarios en Sí](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Protección del grupo dinámico Todos los usuarios
De manera predeterminada, el grupo **Todos los usuarios** contendrá también los usuarios de colaboración de B2B (invitados). Puede proteger más su grupo **Todos los usuarios** con una regla para quitar los usuarios invitados. La ilustración siguiente muestra el grupo **Todos los usuarios** modificado para excluir a los invitados.

![Se muestra una regla en la que el tipo de usuario no es igual a invitado](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

Puede que también encuentre esto útil para crear un grupo dinámico que contenga solo usuarios invitados, de manera que pueda aplicar directivas (como directivas de acceso condicional de Azure AD) para ellos.
Este el aspecto que tendría ese grupo:

![Se muestra una regla en la que el tipo de usuario es igual a invitado](media/active-directory-b2b-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)
