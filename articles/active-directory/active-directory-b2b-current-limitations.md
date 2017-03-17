---
title: "Limitaciones de colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Limitaciones actuales de versión preliminar de la colaboración B2B de Azure Active Directory"
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
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0e71a840d4f503779131ee4a21fe6063d33185f1
ms.openlocfilehash: cbefca2d45a332cd57cfea49dfeaa300426d5502
ms.lasthandoff: 02/24/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitaciones de colaboración B2B de Azure AD
La colaboración B2B de Active Directory (Azure AD) de Azure está sujeta actualmente a las limitaciones descritas en este artículo.

## <a name="invitation-apis-are-in-preview"></a>Las API de invitación están en versión preliminar
La superficie de API es nuestra dirección de avance anticipada. Sin embargo, al igual que todas las versiones preliminares, la API está sujeta al contrato de espacio de nombres de versión preliminar. La API se moverá a una versión numerada con nuestra versión de disponibilidad general (GA).

## <a name="possible-double-multi-factor-authentication"></a>Posibilidad de doble autenticación multifactor
Esta redundancia puede surgir si su asociado ya tiene implementada una directiva de Azure Multi-Factor Authentication. La autenticación multifactor de la colaboración B2B se realiza y administra en la organización que realiza la invitación. Dicha autenticación es conveniente porque abarca todas las identidades y le proporciona control sobre la seguridad de la autenticación de sus invitados de la colaboración B2B.

Sin embargo, si un asociado ya tiene instalada Multi-Factor Authentication y la exige, los usuarios del asociado podrían tener que realizar la autenticación una vez en su organización principal y luego de nuevo en la suya.

En una versión futura, tenemos previsto introducir una directiva donde se pueda evitar el problema de la doble autenticación eligiendo confiar en la autenticación multifactor del asociado.

## <a name="instant-on"></a>Activación instantánea
En los flujos de colaboración B2B, hemos agregado usuarios al directorio y los hemos actualizado dinámicamente durante el canje de invitación, la asignación de aplicaciones y así sucesivamente. Las actualizaciones y escrituras se producen normalmente en una instancia de directorio y se deben replicar en todas las instancias. Hemos observado que, debido a la cantidad finita de tiempo que puede tardar en completarse la replicación, en ocasiones pueden surgir problemas de autorización. Estamos trabajando duro para minimizar o eliminar estos problemas antes de nuestra versión de GA. Aunque es poco probable que los experimente, si lo hace, pruebe a actualizar o reintente la operación para resolverlos.

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

