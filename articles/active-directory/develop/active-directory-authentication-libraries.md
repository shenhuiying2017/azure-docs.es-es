---
title: "Bibliotecas de autenticación de Azure Active Directory | Microsoft Docs"
description: "La biblioteca de autenticación de Azure AD (ADAL) permite a los desarrolladores de aplicaciones cliente autenticar fácilmente a los usuarios en Active Directory (AD) local o en la nube y luego obtener tokens de acceso para proteger las llamadas de API."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: f017e3d323b98660fdee902770652b3165e70e5e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de autenticación de Azure Active Directory
La Biblioteca de autenticación de Azure Active Directory (ADAL) permite a los desarrolladores de aplicaciones autenticar a los usuarios en Active Directory (AD) local o en la nube y obtener tokens para proteger las llamadas de API. ADAL facilita la autenticación para los desarrolladores a través de características como:
 - Una memoria caché de tokens configurable que almacena tokens de acceso y tokens de actualización
 - Actualización de tokens automática disponible cuando expira un token de acceso y un token de actualización
 - Compatibilidad con las llamadas a métodos asincrónicos
 - Y mucho más

> [!NOTE]
> ¿Busca las bibliotecas de Azure AD v2.0 (MSAL)? Revise la [Guía de la biblioteca MSAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries). 
> 
> 

### <a name="client-libraries"></a>Bibliotecas de cliente

| Plataforma | Biblioteca | Descargar | Código fuente | Muestra | Referencia
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Tienda Windows, UWP, Xamarin iOS y Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplicación de escritorio](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referencia](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) | 
| Cliente .NET, Tienda Windows, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplicación de escritorio](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplicación de una sola página](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplicación para iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referencia](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[The Central Repository](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplicación para Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplicaciones web de Java](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) | | |

### <a name="server-libraries"></a>Bibliotecas de servidor 

| Plataforma | Biblioteca | Descargar | Código fuente | Muestra | Referencia
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN para AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicación MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN para OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicación web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN para WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicación web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensiones de protocolo de identidad para .NET Framework 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Controlador JWT para .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>Bibliotecas de cliente v2.0 (MSAL)

El [punto de conexión de Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) combina cuentas de Microsoft y Azure AD detrás de un único punto de conexión. Para obtener acceso a este punto de conexión, los desarrolladores pueden usar la [bibliotecas MSAL de la versión preliminar admitidas para entornos de producción](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) en lugar de ADAL.

| Plataforma | Biblioteca | Descargar | Código fuente | Muestra | Referencia
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Tienda Windows, UWP, Xamarin iOS y Android |MSAL para .NET (versión preliminar) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicación de escritorio](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Referencia](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) | 
| JavaScript |MSAL para JavaScript (versión preliminar) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Aplicación de una sola página](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Referencia](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) | 
| iOS |MSAL para iOS (versión preliminar) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplicación para iOS](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Referencia](https://azuread.github.io/docs/objc/) |
| Android |MSAL para Android (versión preliminar) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicación para Android](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Referencia](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Escenarios

A continuación presentamos tres escenarios comunes en los que se puede usar ADAL para la autenticación de un cliente que accede a un recurso remoto:  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autenticación de los usuarios de una aplicación cliente nativa que se ejecuta en un dispositivo 

En este escenario, un desarrollador tiene un cliente WPF que necesita acceso a un recurso remoto protegido con Azure AD, como una API web. Tiene una suscripción de Azure, sabe cómo invocar la API web de bajada y conoce el inquilino de Azure AD que la API web usa. Por consiguiente, puede usar ADAL para facilitar la autenticación con Azure AD, ya sea delegando por completo la experiencia de autenticación a ADAL o controlando explícitamente las credenciales del usuario. ADAL hace que resulte fácil autenticar el usuario, obtener un token de acceso y un token de actualización de Azure AD y luego usar el token de acceso para realizar solicitudes a la API web.

Para un código de ejemplo que muestra este escenario con autenticación en Azure AD, vea [Aplicación WPF cliente nativa para API web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autenticación de una aplicación cliente confidencial que se ejecuta en un servidor web

En este escenario, un desarrollador tiene una aplicación en ejecución en un servidor que necesita acceso a un recurso remoto protegido con Azure AD, como una API web. Tiene una suscripción de Azure, sabe cómo invocar el servicio de bajada y conoce el inquilino de Azure AD que la API web usa. Por consiguiente, puede usar ADAL para facilitar la autenticación con Azure AD controlando explícitamente las credenciales de la aplicación. ADAL hace que resulte fácil recuperar un token de Azure AD con las credenciales del cliente de la aplicación y luego usar ese token para realizar solicitudes a la API web. ADAL también controla la administración de la duración del token de acceso almacenándolo en la caché y renovándolo cuando sea necesario. Para un código de ejemplo que muestra este escenario, vea [Aplicación de consola demonio para API web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autenticación de una aplicación cliente confidencial que se ejecuta en un servidor web en nombre de un usuario 

En este escenario, un desarrollador tiene una aplicación en ejecución en un servidor que necesita acceso a un recurso remoto protegido con Azure AD, como una API web. La solicitud también tiene que realizarse en nombre del usuario en Azure AD. Tiene una suscripción de Azure, sabe cómo invocar la API web de bajada y conoce el inquilino de Azure AD que el servicio usa. Cuando el usuario se autentica en la aplicación web, la aplicación puede obtener un código de autorización para el usuario de Azure AD. La aplicación web puede luego usar ADAL para obtener un token de acceso y un token de actualización en nombre del usuario con las credenciales de cliente y el código de autorización asociados a la aplicación de Azure AD. Cuando la aplicación web está en posesión del token de acceso, puede llamar a la API web hasta que el token expire. Cuando el token expira, la aplicación web puede usar ADAL para obtener un nuevo token de acceso con el token de actualización que recibió anteriormente. Para un código de ejemplo que muestra este escenario, vea [Cliente nativo de API web a API web](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Otras referencias

- [Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)
- [Escenarios de autenticación para Azure Active Directory](active-directory-authentication-scenarios.md)
- [Ejemplos de código de Azure Active Directory](active-directory-code-samples.md)
