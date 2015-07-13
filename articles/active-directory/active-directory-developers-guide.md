<properties
   pageTitle="Guía del desarrollador de Azure Active Directory"
   description="Una guía completa sobre recursos orientados al desarrollador para Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/10/2015"
   ms.author="mbaldwin"/>


# Guía del desarrollador de Azure Active Directory

## Información general
Al ser una plataforma de administración de identidad como un servicio (IDMaaS), Azure Active Directory proporciona a los desarrolladores una manera eficaz de integrar la administración de identidades en sus aplicaciones. Los siguientes artículos proporcionan información general sobre la implementación y las características clave de Azure Active Directory. Se recomienda leerlos en orden, o bien saltar a [Introducción](#getting-started) si desea profundizar en el tema.


1. **[Beneficios de la integración con Azure AD](active-directory-how-to-integrate.md)**: descubra por qué la integración con Azure Active Directory ofrece la mejor solución para lograr una autorización y un inicio de sesión seguros.

1. **[Uso de Azure AD para iniciar sesión](active-directory-authentication-scenarios.md)**: aproveche la autenticación simplificada de Azure Active Directory para proporcionar inicio de sesión en la aplicación.

1. **[Consulta del directorio](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: use la API Graph de Azure Active Directory para acceder mediante programación a Azure AD a través de los extremos de la API de REST.

1. **[Descripción del modelo de aplicación](https://msdn.microsoft.com/library/azure/dn151122.aspx)**: obtenga información acerca del registro de la aplicación y las directrices de personalización de marca para las aplicaciones multiempresa.

1. **[Bibliotecas](https://msdn.microsoft.com/library/azure/dn151135.aspx)**: autentique fácilmente a los usuarios para obtener tokens de acceso con las bibliotecas de autenticación de Azure.

Para ver información general de AAD presentado en la conferencia Build 2015, consulte la sección [vídeos](#videos) a continuación.


## Introducción

Estos tutoriales están adaptados para varias plataformas y le permiten comenzar rápidamente a desarrollar con Azure Active Directory. Como requisito previo, debe [obtener un inquilino de Azure Active Directory](active-directory-howto-tenant.md).

#### Guías de inicio rápido de aplicaciones para móvil o PC

- [iOS](active-directory-devquickstarts-ios.md)
- [Android](active-directory-devquickstarts-android.md)
- [.NET](active-directory-devquickstarts-dotnet.md)
- [Windows Phone](active-directory-devquickstarts-windowsphone.md)
- [Tienda Windows](active-directory-devquickstarts-windowsstore.md)
- [Xamarin](active-directory-devquickstarts-xamarin.md)
- [Cordova](active-directory-devquickstarts-cordova.md)


#### Guías de inicio rápido de API web o aplicación web

- [Aplicación web .NET](active-directory-devquickstarts-webapp-dotnet.md)
- [API web .NET](active-directory-devquickstarts-webapi-dotnet.md)
- [Javascript](active-directory-devquickstarts-angular.md)
- [Node.js](active-directory-devquickstarts-webapi-nodejs.md)


## Procedimientos

En estos artículos, se describe cómo realizar tareas específicas con Azure Active Directory (AD).

- [Obtención de un inquilino de Azure AD](active-directory-howto-tenant.md)
- [Cómo conseguir que la aplicación aparezca en la galería de aplicaciones de Azure AD](active-directory-app-gallery-listing.md)
- [Cómo empezar a trabajar con las API de Office 365 en las aplicaciones](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Envío de aplicaciones web para Office 365 al panel de vendedores](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)


## Referencia

Estos artículos proporcionan información básica sobre las API de la biblioteca de autenticación y REST, los protocolos, los errores, los ejemplos de código y los extremos.

### Soporte técnico
- **[Dónde obtener soporte técnico](http://stackoverflow.com/questions/tagged/azure-active-directory)**: busque soluciones de Azure AD en Stack Overflow mediante las etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) y [adal](http://stackoverflow.com/questions/tagged/adal).

### Código

- **[Bibliotecas de código abierto de Azure AD](http://github.com/AzureAD)**: el modo más fácil de encontrar un origen de biblioteca es usar la [lista de la biblioteca](https://msdn.microsoft.com/library/azure/dn151135.aspx).

- **[Ejemplos de azure AD](http://github.com/AzureADSamples)**: la forma más sencilla de explorar la lista de ejemplos es usar el [índice de ejemplos de código](active-directory-code-samples.md).


### API Graph

- **[Referencia de API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: referencia REST para la API Graph de Azure Active Directory. [Conozca la nueva experiencia con la referencia de la API Graph interactiva](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **[Ámbitos de los permisos de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes)**: los ámbitos de los permisos de OAuth 2.0 que se utilizan para controlar el acceso que tiene una aplicación a los datos de directorio en un inquilino.


### Protocolos de autenticación

- **[Referencia del protocolo SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx)**: el protocolo SAML 2.0 permite que las aplicaciones proporcionen una experiencia de inicio de sesión único a sus usuarios.


- **[Referencia del protocolo OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)**: el protocolo OAuth 2.0 le permite autorizar el acceso a las aplicaciones web y las API web en su inquilino de Azure AD.


- **[Referencia de protocolo OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx)**: el protocolo OpenID Connect 1.0 amplía OAuth 2.0 para su uso como protocolo de autenticación.


- **[Referencia de protocolo WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx)**: el protocolo WS-Federation 1.2, tal y como se describe en la especificación Web Services Federation, versión 1.2.

- **[Notificaciones y tokens de seguridad admitidos](active-directory-token-and-claims.md)**: una guía en la que se describen y evalúan las notificaciones en los tokens de SAML 2.0 y JSON Web Tokens (JWT).

## Vídeos

### Build 2015

Estas presentaciones de información general sobre el desarrollo de aplicaciones con Azure Active Directory cuentan con ponentes que trabajan directamente en el equipo de ingeniería y explican temas fundamentales de tutorial, por ejemplo, IDMaaS, la autenticación, la federación de identidades o el inicio de sesión único.

- **[Azure Active Directory: administración de identidades como un servicio para las aplicaciones modernas](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)**
- **[Desarrollo de aplicaciones web modernas con Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)**
- **[Desarrollo de aplicaciones nativas web modernas con Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)**

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) es una serie periódica de vídeos de 1:1 que se publica los viernes y ofrece entrevistas breves de 10 o 15 minutos a expertos en diversos temas de Azure. Utilice la característica de filtro de servicios de la página para ver todos los vídeos de Azure Active Directory.

- **[Identidad de Azure 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)**
- **[Identidad de Azure 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)**
- **[Identidad de Azure 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)**

## Redes sociales

- **[Blog del equipo de Active Directory](http://blogs.technet.com/b/ad/)**: manténgase al tanto de los últimos avances en el mundo de Azure AD.

- **[Blog de Azure AD Graph](http://blogs.msdn.com/b/aadgraphteam)**: información de Azure AD específica para la API Graph.

- **[Identidad en la nube](http://www.cloudidentity.net)**: reflexiones de un PM de Azure sobre la administración de las identidades como servicio.

- **[Azure AD en Twitter](https://twitter.com/azuread)**: anuncios sobre Azure AD en 140 caracteres o menos.
<!--HONumber=62-->