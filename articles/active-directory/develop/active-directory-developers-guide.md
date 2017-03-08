---
title: "Guía del desarrollador de Azure Active Directory | Microsoft Docs"
description: "Este artículo ofrece una guía completa sobre recursos orientados al desarrollador para Azure Active Directory."
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 8da480acc04922a3636821c5303f6380236c5ea3
ms.openlocfilehash: 1bff76371ea1ca3ee68c9f04ea24a42f95bb7da7
ms.lasthandoff: 02/17/2017


---
# <a name="azure-active-directory-developers-guide"></a>Guía del desarrollador de Azure Active Directory
## <a name="overview"></a>Información general
Al ser una plataforma de administración de identidades como servicio (IDMaaS), Azure Active Directory (AD) proporciona a los desarrolladores una manera eficaz de integrar la administración de identidades en sus aplicaciones. Los siguientes artículos ofrecen información general sobre la implementación y las características clave de Azure AD. Se recomienda leerlos por orden o, si desea profundizar en el tema, ir directamente a [Introducción](#getting-started) .

1. [Beneficios de la integración con Azure AD](active-directory-how-to-integrate.md): descubra por qué la integración con Azure AD ofrece la mejor solución para lograr una autorización y un inicio de sesión seguros.
2. [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md): aproveche la autenticación simplificada de Azure AD para el inicio de sesión en la aplicación.
3. [Integración de aplicaciones con Azure AD](active-directory-integrating-applications.md): obtenga información sobre cómo agregar, actualizar y quitar aplicaciones de Azure AD, y acerca de las directrices de personalización de marca para aplicaciones integradas.
4. [API Graph de Azure AD](active-directory-graph-api.md): use la API Graph de Azure AD para acceder mediante programación a Azure AD a través de los puntos de conexión de API de REST. También es accesible a través de la API Graph de Azure AD a través de [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph proporciona una API unificada que permite el acceso a varias API del servicio Microsoft Cloud a través de un punto de conexión de API de REST único y con un token de acceso único.
5. [Bibliotecas de autenticación de Azure AD](active-directory-authentication-libraries.md): autentique a los usuarios fácilmente para obtener tokens de acceso mediante las bibliotecas de autenticación de Azure para .NET, JavaScript, Objective-C, Android, etc.

## <a name="getting-started"></a>Introducción
Estos tutoriales están adaptados para varias plataformas y pueden ayudarle a empezar a desarrollar rápidamente con Azure Active Directory. Como requisito previo, debe [obtener un inquilino de Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guías de inicio rápido de aplicaciones para móvil y PC
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | 
|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Windows</br>Universal](active-directory-devquickstarts-windowsstore.md) |

|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|
|[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[Integración directa</br>con OAuth 2.0](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Guías de inicio rápido de aplicaciones web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) |
|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integración directa</br> con OpenID Connect](active-directory-protocols-openid-connect-code.md) |

| [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|
|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integración directa</br> con OpenID Connect](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Guías de inicio rápido de API web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.js](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="querying-the-directory-quickstart-guide"></a>Consultas a la guía de inicio rápido de directorio
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [API Graph](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>Procedimientos
En estos artículos se describe cómo realizar tareas específicas con Azure Active Directory:

* [Obtención de un inquilino de Azure AD](active-directory-howto-tenant.md)
* [Inicio de sesión de cualquier usuario de AD mediante el patrón de aplicación multiinquilino](active-directory-devhowto-multi-tenant-overview.md)
* [Uso de un certificado, en lugar de un secreto, para autenticar la identidad de una aplicación](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/)
* Habilitación de SSO entre aplicaciones con ADAL, en dispositivos [Android](active-directory-sso-android.md) e [iOS](active-directory-sso-ios.md)
* [Certificación de la aplicación AppSource para Azure AD](active-directory-devhowto-appsource-certified.md)
* [Presentación de la aplicación en la galería de aplicaciones de Azure AD](active-directory-app-gallery-listing.md)
* [Envío de aplicaciones web para Office 365 al panel de vendedores](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Registro de una aplicación con Azure Active Directory mediante Azure Portal](../active-directory-app-registration.md)
* [Descripción del manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md)
* [Comprender las directrices de personalización de marca para los botones de adquisición de aplicación y de inicio de sesión en la aplicación cliente](active-directory-branding-guidelines.md)
* [Vista previa: compilación de aplicaciones que inician sesión para los usuarios tanto con cuentas personales como profesionales o educativas](active-directory-appmodel-v2-overview.md)
* [Vista previa: compilación de aplicaciones que registran e inician la sesión de consumidores](../../active-directory-b2c/active-directory-b2c-overview.md)
* [Vista previa: configuración de la duración de los tokens de Azure AD](../active-directory-configurable-token-lifetimes.md) mediante PowerShell. Para más información acerca de cómo realizar la configuración a través de API Graph de Azure AD, consulte [Operations on policy | Graph API reference](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) (Operaciones en directiva | Referencia de API Graph) y [Policy entity](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) (Entidad de directiva).

## <a name="reference"></a>Referencia
Estos artículos proporcionan información básica sobre las API de REST y de la biblioteca de autenticación, los protocolos, los errores, los ejemplos de código y los extremos.  

### <a name="support"></a>Soporte técnico
* [Tagged questions](http://stackoverflow.com/questions/tagged/azure-active-directory): (Preguntas con etiquetas) encuentre soluciones de Azure Active Directory en Stack Overflow buscando las etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) y [adal](http://stackoverflow.com/questions/tagged/adal).
* Consulte el [glosario para desarrolladores de Azure AD](active-directory-dev-glossary.md) para obtener definiciones de algunos de los términos más habituales relacionados con la integración y el desarrollo de aplicaciones.

### <a name="code"></a>Código
* [Bibliotecas de código abierto de Azure Active Directory](http://github.com/AzureAD): la forma más fácil de encontrar el código fuente de una biblioteca es usar la [lista de bibliotecas](active-directory-authentication-libraries.md).
* [Ejemplos de Azure Active Directory](https://github.com/azure-samples?query=active-directory): la forma más sencilla de navegar por la lista de ejemplos es usar el [índice de ejemplos de código](active-directory-code-samples.md).
* [Active Directory Authentication Library (ADAL) para .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet): la documentación de referencia está disponible tanto para [la última versión principal](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) como para [la versión principal anterior](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API Graph
* [Referencia de API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx): referencia de REST para la API Graph de Azure Active Directory. [Vea la experiencia con la referencia interactiva de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* [Ámbitos de los permisos de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): ámbitos de los permisos de OAuth 2.0 que sirven para controlar el acceso que una aplicación tiene a los datos de directorio en un inquilino.

### <a name="authentication-and-authorization-protocols"></a>Protocolos de autenticación y autorización
* [Sustitución de claves de firma de Azure AD](active-directory-signing-key-rollover.md): aprenda sobre la cadencia de sustitución de claves de firma de Azure AD y sobre cómo actualizar la clave en los escenarios de aplicación más comunes.
* [Protocolo OAuth 2.0: uso de la concesión de código de autorización](active-directory-protocols-oauth-code.md): puede usar la concesión de código del protocolo de OAuth 2.0 para autorizar el acceso a aplicaciones web y API web en su inquilino de Azure Active Directory.
* [Protocolo OAuth 2.0: descripción del flujo de concesión implícita](active-directory-dev-understanding-oauth2-implicit-grant.md): aprenda más sobre la concesión de autorización implícita y si es adecuada para su aplicación.
* [Protocolo OAuth 2.0: llamadas de servicio a servicio con las credenciales del cliente](active-directory-protocols-oauth-service-to-service.md): la concesión de credenciales del cliente de OAuth 2.0 permite a un servicio web (un cliente confidencial) usar sus propias credenciales para autenticarse al llamar a otro servicio web, en lugar de suplantar a un usuario. En este escenario, el cliente normalmente es un servicio web de nivel intermedio, un servicio demonio o un sitio web.
* [Protocolo OpenID Connect 1.0: inicio de sesión y autenticación](active-directory-protocols-openid-connect-code.md): el protocolo OpenID Connect 1.0 amplía OAuth 2.0 para su uso como protocolo de autenticación. Una aplicación cliente puede recibir un elemento id_token para administrar el proceso de inicio de sesión o aumentar el flujo del código de autorización para recibir el código de autorización y un elemento id_token.
* [Referencia del protocolo SAML 2.0](active-directory-saml-protocol-reference.md): el protocolo SAML 2.0 permite que las aplicaciones ofrezcan una experiencia de inicio de sesión único a sus usuarios.
* [Protocolo WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory admite WS-Federation 1.2 de acuerdo con la especificación Web Services Federation versión 1.2. Para más información sobre los documentos de metadatos de federación, consulte [Metadatos de federación](active-directory-federation-metadata.md).
* [Tipos de token y de notificación admitidos](active-directory-token-and-claims.md): puede usar esta guía para entender y evaluar las notificaciones en los tokens web JSON (JWT) y SAML 2.0.

## <a name="videos"></a>Vídeos
### <a name="build"></a>Compilación
Los ponentes de estas presentaciones de información general sobre el desarrollo de aplicaciones con características de Azure Active Directory trabajan directamente en el equipo de ingeniería. Las presentaciones tratan temas fundamentales, entre los que se incluyen IDMaaS, autenticación, federación de identidades e inicio de sesión único.

* [Microsoft Identity: State of the Union and Future Direction (Microsoft Identity: estado de la unión y la dirección en el futuro)](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory: Administración de identidades como servicio para aplicaciones modernas](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Desarrollo de aplicaciones web modernas con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Desarrollo de aplicaciones nativas modernas con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) es una serie periódica de vídeos de 1:1 que se publica los viernes y ofrece entrevistas breves (de 10 a 15 minutos) a expertos en diversos temas de Azure.  Utilice la característica de filtro de servicios de la página para ver todos los vídeos de Azure Active Directory.

* [Identidad de Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Identidad de Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Identidad de Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Redes sociales
* [Blog del equipo de Active Directory](http://blogs.technet.com/b/ad/): manténgase al tanto de los últimos avances en el mundo de Azure Active Directory.
* [Blog del equipo de Azure Active Directory Graph](https://blogs.msdn.microsoft.com/aadgraphteam): información de Azure Active Directory específica de la API Graph.
* [Identidad en la nube](http://www.cloudidentity.net): reflexiones de un PM de Azure Active Directory sobre la administración de identidades como servicio.  
* [Azure Active Directory en Twitter](https://twitter.com/azuread): anuncios de Azure Active Directory en 140 caracteres o menos.

## <a name="windows-server-on-premises-development"></a>Desarrollo de Windows Server local
Para obtener instrucciones sobre el uso del desarrollo de Windows Server y Servicios de federación de Active Directory (AD FS), consulte:

* [Escenarios de AD FS para desarrolladores](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): proporciona información general de los componentes de AD FS y de cómo funcionan, con detalles sobre los escenarios de autenticación y autorización compatibles.
* [Tutoriales de AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): una lista de artículos de tutoriales que proporcionan instrucciones paso a paso sobre la implementación de los flujos de autenticación y autorización relacionados.

