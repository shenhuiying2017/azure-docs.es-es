---
title: "Portal de suscripción sin intervención del administrador para la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "La colaboración con Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los asociados empresariales accedan de forma selectiva a las aplicaciones corporativas."
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: f629eeb6f12c8785cab2585190f70e98b02fa5b4
ms.lasthandoff: 04/12/2017


---


# <a name="self-service-sign-up-portal-for-azure-ad-b2b-collaboration"></a>Portal de registro de autoservicio para la colaboración B2B de Azure AD

Los clientes pueden hacer muchas cosas con las funcionalidades integradas del producto que se exponen a través de nuestras experiencias para administradores de TI en [Azure Portal](https://portal.azure.com), y en nuestro [panel de acceso a las aplicaciones](https://myapps.microsoft.com) para usuarios no administradores. Pero también somos conscientes de que las empresas necesitan personalizar el flujo de trabajo de incorporación para los usuarios de B2B para ajustarse a las necesidades de su organización. Puede hacerlo con [nuestra API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Al tratar esto con muchos de nuestro clientes, hemos visto una necesidad común que destaca sobre las demás. Es el caso de las organizaciones anfitrionas que pueden no saber (o desean conocer) de antemano quiénes son los colaboradores externos que necesitan acceso a sus recursos. Querían un modo de registro de autoservicio para los usuarios de sus empresas asociadas con un conjunto de directivas controladas por la organización anfitriona. Como esto es posible a través de nuestras API, publicamos un proyecto en Github que lo lleva a cabo: [proyecto de ejemplo de Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Nuestro proyecto de Github muestra cómo las organizaciones pueden usar nuestras API para proporcionar una funcionalidad de registro de autoservicio basada en directivas para sus asociados de confianza, con reglas que determinan a qué aplicaciones pueden tener acceso. De este modo, los usuarios de los asociados pueden tener acceso a los recursos adecuados cuando lo necesitan, de forma segura y sin que nadie de la organización anfitriona tenga que realizar operaciones manuales de incorporación. Puede implementar fácilmente el proyecto con un clic en una suscripción de Azure de su elección. ¡Inténtelo!

## <a name="as-is-code"></a>Código “tal cual”

Recuerde que este código está disponible como ejemplo para mostrar el uso de la API de invitación B2B de Azure Active Directory. Su equipo de desarrollo o el asociado deben realizar la personalización y revisión antes de implementarlo en un escenario de producción.

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:
* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory (P+F)](active-directory-b2b-faq.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
