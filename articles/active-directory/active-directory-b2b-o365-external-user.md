---
title: "Uso compartido externo de Office 365 y colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "referencia de asignación de notificaciones para la colaboración B2B de Azure Active Directory"
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
ms.date: 05/24/2017
ms.author: sasubram
ms.openlocfilehash: cad0ce8f745f3d6ca14436fd714b08c60de0e459
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Uso compartido externo de Office 365 y colaboración B2B de Azure Active Directory

Técnicamente, es lo mismo utilizar el uso compartido externo de Office 365 (OneDrive, SharePoint Online, grupos unificados, etc.) y la colaboración B2B de Azure Active Directory (Azure AD). Todos los usos compartidos externos (excepto OneDrive o SharePoint Online), incluidos los invitados de grupos de Office 365, ya utilizan las API de invitación de colaboración B2B de Azure AD para el uso compartido.

OneDrive y SharePoint Online tiene un administrador de invitaciones independiente. La compatibilidad con el uso compartido externo en OneDrive o SharePoint Online comenzó antes que Azure AD desarrollara su compatibilidad. Con el tiempo, el uso compartido externo de OneDrive o SharePoint Online ha acumulado varias características y muchos millones de usuarios que usan el patrón de uso compartido integrado del producto. Sin embargo, existen algunas diferencias sutiles entre cómo funciona el uso compartido externo de OneDrive y SharePoint Online y la colaboración B2B de Azure AD:

- OneDrive y SharePoint Online agregan usuarios al directorio después de que los usuarios han canjeado sus invitaciones. Por lo tanto, antes del canje, el usuario no se muestra en el portal de Azure AD. Si, mientras tanto, un usuario recibe una invitación de otro sitio, se genera una nueva invitación. Sin embargo, cuando se usa la colaboración B2B de Azure AD, los usuarios se agregan inmediatamente cuando se les invita por lo que se muestran en todas partes.

- La experiencia de canje en OneDrive y SharePoint Online parece diferente de la experiencia de colaboración B2B de Azure AD. Después de que un usuario canjea una invitación, las experiencias se asemejan.

- Los usuarios invitados a la colaboración B2B de Azure AD se pueden seleccionar en los cuadros de diálogo de uso compartido de OneDrive y SharePoint Online. Los usuarios invitados a OneDrive y SharePoint Online también se muestran en Azure AD después de que canjean sus invitaciones.

- Para administrar el uso compartido externo en OneDrive o SharePoint Online con la colaboración B2B de Azure AD, establezca la configuración de uso compartido externo de OneDrive o SharePoint Online en **Only allow sharing with external users already in the directory** (Solo permitir uso compartido con usuarios externos que ya se encuentren en el directorio). Los usuarios pueden acceder a los sitios compartidos externamente y elegir entre colaboradores externos que haya agregado el administrador. El administrador puede agregar los colaboradores externos a través de la API de invitación de colaboración B2B.

![La configuración de uso compartido externo de OneDrive y SharePoint Online](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)
