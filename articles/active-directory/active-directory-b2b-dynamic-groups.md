---
title: "Grupos dinámicos y colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "La colaboración B2B de Active Directory de Azure puede utilizarse con grupos dinámicos de Azure AD"
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
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: a483e219c1e730e174baa0ffa8ba471b0724f0da


---

# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinámicos y colaboración B2B de Azure Active Directory

## <a name="what-are-dynamic-groups"></a>¿Qué son los grupos dinámicos?
La configuración dinámica de pertenencia a grupos de seguridad de Azure Active Directory (Azure AD) está disponible en la versión preliminar pública en [Azure Portal](https://portal.azure.com). Los administradores pueden establecer reglas para rellenar los grupos que se crean en Azure Active Directory en función de los atributos de usuario (por ejemplo, userType, department o country). De este modo, los miembros se agregan automáticamente a un grupo de seguridad, o se quita de ellos, según los cambios en los atributos. Estos grupos se pueden usar para dar acceso a aplicaciones o recursos en la nube, como documentos y sitios de SharePoint, y también para asignar licencias a usuarios. Obtenga más información sobre los grupos dinámicos en [Grupos dedicados en Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Con una suscripción de AAD Premium P1 o P2, ahora, Azure Portal ofrece la posibilidad de crear reglas avanzadas para permitir pertenencias dinámicas más complejas basadas en atributos para grupos de la versión preliminar de Azure Active Directory. Obtenga más información sobre cómo crear reglas avanzadas en [Uso de atributos para crear reglas avanzadas para la pertenencia a grupos en la versión preliminar de Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>¿Cuáles son los grupos dinámicos integrados?
El grupo dinámico **Todos los usuarios** permite a los administradores de inquilinos crear un grupo que contenga todos los usuarios del inquilino con un solo clic. De forma predeterminada, el grupo **Todos los usuarios** incluye todos los usuarios del directorio, incluidos los miembros y los invitados.
En el nuevo portal de administración de Azure Active Directory, puede optar por habilitar el grupo **Todos los usuarios** en la vista de configuración del grupo.

![Grupos integrados](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

Al proteger de forma predeterminada el grupo dinámico Todos los usuarios, el grupo **Todos los usuarios** contendrá los usuarios de colaboración de B2B (invitados). Si desea proteger aún más el grupo **Todos los usuarios** mediante la eliminación de usuarios invitados, puede hacerlo fácilmente modificando la regla basada en atributos del grupo **Todos los usuarios**. La ilustración siguiente muestra el grupo **Todos los usuarios** modificado para excluir los invitados.

![Habilitación de todos los grupos de usuarios](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

También podría resultarle útil crear un nuevo grupo dinámico que contenga solo los usuarios invitados, sobre todo al seleccionar directivas como destino (por ejemplo, las directivas de acceso condicional) para usuarios invitados.
A continuación, se muestra una ilustración del aspecto que podría tener un grupo:

![Exclusión de usuarios invitados](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>Pasos siguientes

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



<!--HONumber=Feb17_HO1-->


