---
title: "Incorporación de una aplicación multiinquilino a la galería de aplicaciones de Azure AD | Microsoft Docs"
description: "En este artículo se explica cómo se puede mostrar una aplicación multiinquilino personalizada en la galería de aplicaciones de Azure AD"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Incorporación de una aplicación multiinquilino a la galería de aplicaciones de Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>¿Qué es la galería de aplicaciones de Azure AD?

Azure AD es un servicio de identidad basado en la nube. [La galería de aplicaciones de Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) es un almacén común donde se publican todos los conectores de aplicaciones para poder realizar el inicio de sesión único y el aprovisionamiento de usuarios. Los clientes que usan Azure AD como proveedor de identidades buscan los diversos conectores de aplicaciones de SaaS que se publican aquí. El administrador de TI se encarga de agregar el conector de la galería de aplicaciones y lo configura y usa para poder realizar el inicio de sesión único y el aprovisionamiento. Azure AD admite los principales protocolos de federación, como SAML 2.0, OpenID Connect, OAuth y WS-Fed, para el inicio de sesión único. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Si la aplicación es compatible con SAML o con OpenIDConnect
Si tiene una aplicación multiinquilino y le gustaría que apareciera en la galería de aplicaciones de Azure AD, primero debe asegurarse de que es compatible con una de las siguientes tecnologías de inicio de sesión único:

1. **OpenID Connect**: cree la aplicación multiinquilino en Azure AD e implemente el [marco de consentimiento de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) para la aplicación. Envíe la solicitud de inicio de sesión al punto de conexión común para que cualquier cliente pueda proporcionar su consentimiento en la aplicación. Puede controlar el acceso de usuario del cliente en función del identificador del inquilino y el UPN del usuario que se recibieron en el token. Envíe la aplicación tal y como se menciona en este [artículo](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

2. **SAML**: si la aplicación es compatible con SAML 2.0, entonces podrá incluirse en la galería y las instrucciones se encuentran [aquí](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)

Si la aplicación multiinquilino es compatible con uno de estos modos de inicio de sesión único y le gustaría incluirla en la galería de aplicaciones de Azure AD, puede seguir el procedimiento que se menciona en [este](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) artículo. 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Si la aplicación no es compatible con SAML ni con OpenIDConnect
Aunque la aplicación no sea compatible con ninguno de estos modos, puede incluirla en la galería. Para ello, tiene que usar nuestra tecnología de inicio de sesión único con contraseña.

**SSO de contraseña**: cree una aplicación web que tenga una página de inicio de sesión HTML para configurar el [inicio de sesión único basado en contraseña](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis). El SSO basado en contraseña, también conocido como almacenamiento de contraseñas, permite administrar el acceso y las contraseñas de los usuarios en aplicaciones web que no admiten la federación de identidades. También es útil para escenarios en los que varios usuarios necesitan compartir una sola cuenta, como las cuentas de aplicaciones de redes sociales de la organización. 

Si quiere incluir la aplicación con esta tecnología, envíe la solicitud tal como se describe en [este](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) artículo.

## <a name="escalations"></a>Extensiones

Si quiere tener una extensión, envíe un correo electrónico al [equipo de integración de SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) y nos pondremos en contacto con usted lo antes posible.

## <a name="next-steps"></a>pasos siguientes
[Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
