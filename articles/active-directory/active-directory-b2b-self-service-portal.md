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
ms.date: 05/24/2017
ms.author: sasubram
ms.openlocfilehash: 307373c75bbb87cec683f7a3097f8f159c9d5e61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal de autoservicio para el registro en la colaboración B2B de Azure AD

Los clientes pueden hacer muchas cosas con las características integradas que se exponen mediante nuestro [Azure Portal](https://portal.azure.com) de administración de TI y nuestro [panel de acceso a las aplicaciones](https://myapps.microsoft.com) para los usuarios finales. Pero también somos conscientes de que las empresas necesitan personalizar el flujo de trabajo de incorporación para los usuarios de B2B para ajustarse a las necesidades de su organización. Puede hacerlo con [nuestra API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

En las conversaciones con nuestros clientes, se observa una necesidad común que sobresale por encima de todas las demás. La organización que invita podría no saber de antemano quiénes son los colaboradores externos y quién necesita acceso a sus recursos. Quieren una forma de que los usuarios de empresas asociadas se registren ellos mismos con un conjunto de directivas que controla la organización que invita. Como este escenario es posible gracias a nuestras API, publicamos un proyecto en Github que lo lleva a cabo: [proyecto de ejemplo de Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Nuestro proyecto de Github muestra cómo las organizaciones pueden usar nuestras API para proporcionar una funcionalidad de registro de autoservicio basada en directivas para sus asociados de confianza, con reglas que determinan a qué aplicaciones pueden tener acceso. Los usuarios asociados pueden obtener acceso a los recursos cuando los necesiten, de forma segura y sin necesidad de que la organización que invita los incorpore manualmente. Puede implementar fácilmente el proyecto en una suscripción de Azure de su elección.

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