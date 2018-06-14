---
title: Portal de suscripción sin intervención del administrador para la colaboración B2B de Azure Active Directory | Microsoft Docs
description: La colaboración con Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los asociados empresariales accedan de forma selectiva a las aplicaciones corporativas.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b16f7c040212db6d0dbeb7161a18f205cf524090
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930442"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal de autoservicio para el registro en la colaboración B2B de Azure AD

Los clientes pueden hacer muchas cosas con las características integradas que se exponen mediante [Azure Portal](https://portal.azure.com) y el [panel de acceso a las aplicaciones](https://myapps.microsoft.com) para los usuarios finales. Sin embargo, puede que necesite personalizar el flujo de trabajo de incorporación para los usuarios de B2B para ajustarse a las necesidades de su organización. Puede hacerlo con la [API de invitación](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Como una organización que invita, puede que no sepa de antemano quiénes son los colaboradores externos individuales y quién necesita acceso a sus recursos. Necesita una forma de que los usuarios de empresas asociadas se registren ellos mismos con un conjunto de directivas que usted, como la organización que invita, controla. Este escenario es posible a través de las API. Hay un [proyecto de ejemplo en GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) que hace justamente eso.

Este proyecto de GitHub muestra cómo las organizaciones pueden usar las API para proporcionar una funcionalidad de registro de autoservicio basada en directivas para sus asociados de confianza, con reglas que determinan a qué aplicaciones pueden tener acceso. Los usuarios asociados pueden acceder a los recursos cuando los necesitan. Pueden hacerlo de forma segura, sin necesidad de que la organización que invita los incorpore manualmente. Puede implementar fácilmente el proyecto en una suscripción de Azure de su elección.

## <a name="as-is-code"></a>Código “tal cual”

Este código está disponible como ejemplo para mostrar el uso de la API de invitación B2B de Azure Active Directory. Su equipo de desarrollo o el asociado debe realizar la personalización y revisión antes de implementarlo en un escenario de producción.

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory (P+F)](active-directory-b2b-faq.md)