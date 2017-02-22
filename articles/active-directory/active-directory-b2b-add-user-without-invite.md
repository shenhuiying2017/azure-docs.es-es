---

title: "Incorporación de usuarios de colaboración B2B a Azure Active Directory sin invitación | Microsoft Docs"
description: "La colaboración B2B de Azure Active Directory permite a los trabajadores de la información agregar usuarios de su organización a Azure AD para acceder a aplicaciones corporativas."
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
ms.date: 02/10/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0eef684115f9c21ea61502a10576f74cac0ace8e
ms.openlocfilehash: 8fa8ba169ca85c33e52eee4a7cd5b84aa4012673


---

# <a name="add-b2b-collaboration-users-without-an-invitation"></a>Incorporación de usuarios de colaboración B2B sin invitación

Si el autor de la invitación pertenece a un rol que tiene privilegios de enumeración en el directorio de la organización del asociado, desde la que está agregando usuarios, los usuarios invitados se agregan a la organización invitadora sin necesidad de invitaciones.

Este es el escenario en el que resulta más útil:

1. Un usuario de la organización anfitriona (por ejemplo, WoodGrove) invita a un usuario de la organización asociada (por ejemplo, Sam@litware.com)) como invitado.
2. El administrador de la organización anfitriona configura directivas que permiten a Sam identificar y agregar otros usuarios de la organización del asociado (Litware).
3. Ahora, Sam puede agregar otros usuarios de Litware al directorio, los grupos o las aplicaciones de WoodGrove sin necesidad de canjear invitaciones. Si Sam tiene los privilegios de enumeración adecuados en Litware, se hará automáticamente.

### <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


