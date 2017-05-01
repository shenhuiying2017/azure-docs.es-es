---
title: "Incorporación de una aplicación multiinquilino a la galería de aplicaciones de Azure AD | Microsoft Docs"
description: "En este artículo se explica cómo se puede mostrar una aplicación multiinquilino personalizada en la galería de aplicaciones de Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 9f3caff0e998d670ce82b847525a63f2a85572d3
ms.lasthandoff: 04/17/2017


---

# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Incorporación de una aplicación multiinquilino a la galería de aplicaciones de Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>¿Qué es la galería de aplicaciones de Azure AD?

La galería de aplicaciones de Azure AD constituye un mecanismo ideal para mostrar la aplicación a los millones de clientes de Azure Active Directory y, de esta forma, ampliar el alcance y el impacto de la aplicación en el mercado. En los pasos siguientes, se explica cómo puede incluir la aplicación en la galería de aplicaciones de Azure AD.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Si la aplicación es compatible con SAML o con OpenIDConnect
Si tiene una aplicación multiinquilino y le gustaría que apareciera en la galería de aplicaciones de Azure AD, primero debe asegurarse de que es compatible con una de las siguientes tecnologías de inicio de sesión único:

1. **OpenID Connect** : integración directa con Azure AD mediante OpenID Connect para autenticación y la API de consentimiento de Azure AD para configuración. Si acaba de iniciar una integración y la aplicación no es compatible con SAML, este es el modo recomendado.
2. **SAML** : la aplicación ya tiene la capacidad de configurar proveedores de identidad de terceros mediante el protocolo SAML.

Si la aplicación multiinquilino es compatible con uno de estos modos de inicio de sesión único y le gustaría incluirla en la galería de aplicaciones de Azure AD, puede seguir el procedimiento del documento que se indica más abajo. Si desea empezar rápidamente, envíe un correo electrónico a **waadpartners@microsoft.com**.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Si la aplicación no es compatible con SAML ni con OpenIDConnect
Aunque la aplicación no sea compatible con ninguno de estos modos, puede incluirla en la galería. Para ello, tiene que usar nuestra tecnología de inicio de sesión único con contraseña. Si desea valorar con mayor profundidad esta opción, puede enviar un correo electrónico a **waadpartners@microsoft.com**.

## <a name="next-steps"></a>Pasos siguientes
[Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)

