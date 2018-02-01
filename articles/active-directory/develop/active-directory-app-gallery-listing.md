---
title: "Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory"
description: "Cómo mostrar una aplicación que admite el inicio de sesión único en la galería de Azure Active Directory | Microsoft Azure"
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
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory


##  <a name="what-is-azure-ad-app-gallery"></a>¿Qué es la galería de aplicaciones de Azure AD?

Azure AD es un servicio de identidad basado en la nube. [La galería de aplicaciones de Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) es un almacén común donde se publican todos los conectores de aplicaciones para poder realizar el inicio de sesión único y el aprovisionamiento de usuarios. Los clientes que usan Azure AD como proveedor de identidades buscan los diversos conectores de aplicaciones de SaaS que se publican aquí. El administrador de TI se encarga de agregar el conector de la galería de aplicaciones y lo configura y usa para poder realizar el inicio de sesión único y el aprovisionamiento. Azure AD admite los principales protocolos de federación como SAML 2.0, OpenID Connect, OAuth y WS-Fed para poder llevar a cabo el inicio de sesión único. 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>¿Qué ventajas tiene agregar la aplicación a la lista de la galería?

*  Proporciona la mejor experiencia de inicio de sesión único a los clientes.

*  Requiere una configuración de la aplicación simple y mínima.

*  Los clientes pueden buscar la aplicación y encontrarla en la galería. 

*  Cualquier cliente puede usar esta integración con independencia de la versión de la SKU de Azure AD (Gratis, Básica o Premium).

*  Tutorial de configuración paso a paso para clientes mutuos.

*  Opción de habilitar el aprovisionamiento de usuarios para la misma aplicación si usa SCIM.


##  <a name="what-are-the-pre-requisites"></a>¿Cuáles son los requisitos previos?

Para agregar una aplicación a la galería de Azure AD, debe implementar uno de los protocolos de federación que admite Azure AD. Lea los términos y condiciones de la galería de aplicaciones de Azure AD aquí. Si está usando: 

*   **OpenID Connect**: cree la aplicación multiinquilino en Azure AD e implemente el [marco de consentimiento de Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework) para la aplicación. Envíe la solicitud de inicio de sesión al punto de conexión común para que cualquier cliente pueda proporcionar su consentimiento en la aplicación. Puede controlar el acceso de usuario del cliente en función del identificador del inquilino y el UPN del usuario que se recibieron en el token. Para integrar la aplicación con Azure AD, siga las [instrucciones del desarrollador](active-directory-authentication-scenarios.md).

*   **SAML 2.0 o WS-Fed**: la aplicación debe tener la capacidad de realizar la integración de SSO de SAML/WS-Fed en modo SP o IDP. Cualquier aplicación compatible con SAML 2.0 se puede integrar directamente con un inquilino de Azure AD mediante [estas instrucciones para agregar una aplicación personalizada](../active-directory-saas-custom-apps.md).

*   **SSO de contraseña**: cree una aplicación web que tenga una página de inicio de sesión HTML para configurar el [inicio de sesión único basado en contraseña](../active-directory-appssoaccess-whatis.md). El SSO basado en contraseña, también conocido como almacenamiento de contraseñas, permite administrar el acceso y las contraseñas de los usuarios en aplicaciones web que no admiten la federación de identidades. También es útil para escenarios en los que varios usuarios necesitan compartir una sola cuenta, como las cuentas de aplicaciones de redes sociales de la organización. 

## <a name="process-for-submitting-the-request-in-the-portal"></a>Proceso para enviar la solicitud en el portal

Una vez haya comprobado que la integración de la aplicación funciona con Azure AD, debe enviar la solicitud de acceso a nuestro [Portal de red de aplicaciones](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Si tiene una cuenta de Office 365, puede usarla para iniciar sesión en este portal; en caso contrario, use su id. de Microsoft (Live ID, Outlook, Hotmail, etc.) para iniciar sesión. A continuación, verá la siguiente página que le solicitará el acceso. Proporcione una justificación de negocios en el cuadro de texto y haga clic en **Solicitar acceso**. Nuestro equipo revisará todos los detalles y le proporcionará acceso según corresponda. Una vez hecho esto, puede acceder al portal y enviar la solicitud detallada de la aplicación.

Si tiene algún problema para obtener acceso, póngase en contacto con el [equipo de integración del SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Solicitud de acceso en el portal de SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Escalas de tiempo
    
*   Proceso para agregar la aplicación de SAML 2.0 o WS-Fed en la galería: **entre 7 y 10 días laborables**

   ![Escala de tiempo para agregar la aplicación SAML a la lista de la galería](./media/active-directory-app-gallery-listing/timeline.png)

*   Proceso para agregar la aplicación de OpenID Connect en la galería: **entre 2 y 5 días laborables**

   ![Escala de tiempo para agregar la aplicación SAML a la lista de la galería](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Extensiones

Si quiere tener una extensión, envíe un correo electrónico al [equipo de integración de SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) y nos pondremos en contacto con usted lo antes posible.

