---
title: "Limitaciones de colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Limitaciones actuales de la colaboración B2B de Azure Active Directory"
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
ms.date: 05/04/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: f13f4ac6e32f568fb5f56261b542e0e2c49f5b3d
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitaciones de colaboración B2B de Azure AD
La colaboración B2B de Active Directory (Azure AD) de Azure está sujeta actualmente a las limitaciones descritas en este artículo.

## <a name="possible-double-multi-factor-authentication"></a>Posibilidad de doble autenticación multifactor
Con B2B de Azure AD, puede aplicar la autenticación multifactor en la organización del recurso (la organización que invita). Los motivos de este enfoque se detallan en [Acceso condicional para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md). Esto significa que si un asociado ya tiene instalada Multi-Factor Authentication y la exige, los usuarios del asociado podrían tener que realizar la autenticación una vez en su organización principal y luego de nuevo en la suya.

En una versión futura, tenemos previsto introducir una directiva donde se pueda evitar el problema de la doble autenticación eligiendo confiar en la autenticación multifactor del asociado.


## <a name="instant-on"></a>Activación instantánea
En los flujos de colaboración B2B, hemos agregado usuarios al directorio y los hemos actualizado dinámicamente durante el canje de invitación, la asignación de aplicaciones y así sucesivamente. Las actualizaciones y escrituras se producen normalmente en una instancia de directorio y se deben replicar en todas las instancias. La replicación se completará una vez que se actualicen todas las instancias. A veces, cuando el objeto se escribe o se actualiza en una instancia del directorio y la llamada para recuperar este objeto tiene una carga equilibrada en otra instancia, pueden aparecer problemas de autorización. Nos hemos esforzado para eliminar o reducir estas latencias de replicación, pero es posible que en algunos casos excepcionales puedan producirse todavía. Si esto sucede, actualice o vuelva a intentarlo. Si está escribiendo una aplicación mediante la API, los reintentos con algún retroceso es una buena práctica defensiva para mitigar este problema.

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
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)

