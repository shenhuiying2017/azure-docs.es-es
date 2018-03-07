---
title: "Exposición de una aplicación en la galería de aplicaciones de Azure Active Directory | Microsoft Docs"
description: "Procedimientos para mostrar una aplicación compatible con el inicio de sesión único en la galería de aplicaciones de Azure Active Directory"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 502fb555bd3b381c9be0ff04e210cc07f9bf6cd8
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Aprenda a mostrar su aplicación en la galería de aplicaciones de Azure Active Directory


##  <a name="what-is-the-azure-ad-application-gallery"></a>¿Qué es la galería de aplicaciones de Azure AD?

Azure Active Directory (Azure AD) es un servicio de identidad basado en la nube. La [galería de aplicaciones de Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) es una tienda de aplicaciones de Microsoft Azure Marketplace donde se publican todos los conectores de aplicaciones que permiten el inicio de sesión único y el aprovisionamiento de usuarios. Los clientes que usan Azure AD como proveedor de identidades encontrarán publicados aquí diferentes conectores de aplicaciones SaaS. Los administradores de TI agregan los conectores desde la galería de aplicaciones, y los configuran y usan para poder realizar el inicio de sesión único y el aprovisionamiento. Azure AD admite todos los principales protocolos de federación de inicio de sesión único, como SAML 2.0, OpenID Connect, OAuth y WS-Fed. 

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>¿Qué ventajas tiene mostrar una aplicación en la galería?

*  Los clientes pueden disfrutar de la mejor experiencia posible de inicio de sesión único.

*  La configuración de la aplicación resulta sencilla y apenas requiere esfuerzo. 

*  Basta realizar una búsqueda rápida para encontrar la aplicación en la galería.

*  Todos los clientes de Azure AD de los niveles Gratis, Básico y Premium pueden disfrutar de esta integración. 

*  Los clientes mutuos pueden utilizar un tutorial de configuración paso a paso. 

*  Los clientes que usen SCIM pueden utilizar el aprovisionamiento de la misma aplicación.


##  <a name="prerequisites-implement-federation-protocol"></a>Requisitos previos: implementación del protocolo de federación

Para agregar una aplicación a la galería de Azure AD, debe implementar uno de los siguientes protocolos de federación compatibles con Azure AD. Lea los términos y condiciones de la galería de aplicaciones de Azure AD aquí. 

*   **OpenID Connect**: cree la aplicación multiinquilino en Azure AD e implemente el [marco de consentimiento de Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework) de la aplicación. Envíe la solicitud de inicio de sesión a un punto de conexión común para que cualquier cliente pueda proporcionar su consentimiento a la aplicación. Puede controlar el acceso de usuario en función del identificador del inquilino y el UPN del usuario que se recibieron en el token. Para integrar la aplicación con Azure AD, siga las [instrucciones para desarrolladores](active-directory-authentication-scenarios.md).

*   **SAML 2.0** o **WS-Fed**: la aplicación debe ser capaz de realizar la integración de SSO de SAML/WS-Fed en modo SP o IDP. Si la aplicación es compatible con SAML 2.0, se puede integrar directamente con un inquilino de Azure AD; siga [estas instrucciones para agregar una aplicación personalizada](../active-directory-saas-custom-apps.md).

*   **SSO de contraseña**: cree una aplicación web que tenga una página de inicio de sesión HTML para configurar el [inicio de sesión único basado en contraseña](../active-directory-appssoaccess-whatis.md). El SSO basado en contraseña, también conocido como almacenamiento de contraseñas, permite administrar el acceso y las contraseñas de los usuarios en aplicaciones web que no admiten la federación de identidades. También es útil para escenarios en los que varios usuarios deben compartir la misma cuenta, como las cuentas de las aplicaciones de redes sociales de la organización. 

## <a name="submit-the-request-in-the-portal"></a>Envío de la solicitud en el portal

Cuando haya comprobado que la integración de aplicaciones funciona con Azure AD, envíe la solicitud de acceso en nuestro [portal de red de aplicaciones](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Si tiene una cuenta de Office 365, utilícela para iniciar sesión en este portal. De lo contrario, utilice una cuenta Microsoft (como Outlook o Hotmail) para iniciar sesión.

Cuando inicie sesión, aparecerá la página siguiente. Escriba en el cuadro de texto la justificación empresarial por la que necesita obtener acceso y seleccione **Solicitar acceso**. Nuestro equipo revisará los detalles y le proporcionará el acceso según corresponda. Una vez hecho esto, podrá acceder al portal y enviar una solicitud detallada de la aplicación.

Si tiene algún problema para obtener acceso, póngase en contacto con el [equipo de integración del SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Solicitud de acceso en el portal de SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Escalas de tiempo
    
El período de tiempo que dura el proceso para mostrar una aplicación de SAML 2.0 o WS-Fed en la galería es de entre 7 y 10 días laborables.

   ![Escala de tiempo para agregar la aplicación SAML a la lista de la galería](./media/active-directory-app-gallery-listing/timeline.png)

El período de tiempo que dura el proceso para mostrar una aplicación de OpenID Connect en la galería es de entre 2 y 5 días laborables.

   ![Escala de tiempo para agregar la aplicación SAML a la lista de la galería](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Extensiones

Si necesita ponerse en contacto con una instancia superior, envíe un correo electrónico al [equipo de integración de SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) y le responderemos lo antes posible.

