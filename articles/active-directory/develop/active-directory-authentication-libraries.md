---
title: "Bibliotecas de autenticación de Azure Active Directory | Microsoft Docs"
description: "La biblioteca de autenticación de Azure AD (ADAL) permite a los desarrolladores de aplicaciones cliente autenticar fácilmente a los usuarios en Active Directory (AD) local o en la nube y luego obtener tokens de acceso para proteger las llamadas de API."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: bryanla
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2ad287d383f65ea66382d1f449a76db596963f46
ms.lasthandoff: 03/29/2017


---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de autenticación de Azure Active Directory
La biblioteca de autenticación de Azure AD (ADAL) permite a los desarrolladores de aplicaciones cliente autenticar fácilmente a los usuarios en Active Directory (AD) local o en la nube y luego obtener tokens de acceso para proteger las llamadas de API. ADAL incluye muchas características que hacen que la autenticación resulte más fácil para los desarrolladores, como la compatibilidad asincrónica, una memoria caché de tokens configurable que almacena tokens de acceso y tokens de actualización, actualización automática de tokens cuando caduca un token de acceso y hay un token de actualización disponible, etc. Al controlar la mayor parte de la complejidad, ADAL puede ayudar a los desarrolladores a centrarse en la lógica de negocios de su aplicación y proteger recursos fácilmente sin ser un experto en seguridad.

ADAL está disponible en diversas plataformas.

### <a name="client-libraries"></a>Bibliotecas de cliente

| Plataforma | Biblioteca | Descargar | Código fuente | Muestra | Referencia
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Tienda Windows, UWP, Xamarin iOS y Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplicación de escritorio](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referencia](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) | 
| Cliente .NET, Tienda Windows, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2) | [Aplicación de escritorio](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) |[Referencia](https://docs.microsoft.com/en-us/active-directory/adal//v2/microsoft.identitymodel.clients.activedirectory) | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplicación de una sola página](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[CocoaPods](http://cocoadocs.org/docsets/ADAL/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplicación para iOS](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-ios) | [Referencia](http://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[The Central Repository](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplicación para Android](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplicación web de Java](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-java) | |

### <a name="server-libraries"></a>Bibliotecas de servidor 

| Plataforma | Biblioteca | Descargar | Código fuente | Muestra | Referencia
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN para AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicación MVC](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN para OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicación web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API web](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN para WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicación web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensiones de protocolo de identidad para .NET Framework 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Controlador JWT para .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |



## <a name="scenarios"></a>Escenarios
A continuación presentamos tres escenarios comunes en los que se puede usar ADAL para la autenticación.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Autenticación de usuarios de una aplicación cliente en un recurso remoto
En este escenario, un desarrollador tiene un cliente, como una aplicación WPF, que necesita acceso a un recurso remoto protegido con Azure AD, como una API web. Tiene una suscripción de Azure, sabe cómo invocar la API web de bajada y conoce el inquilino de Azure AD que la API web usa. Por consiguiente, puede usar ADAL para facilitar la autenticación con Azure AD, ya sea delegando por completo la experiencia de autenticación a ADAL o controlando explícitamente las credenciales del usuario. ADAL hace que resulte fácil autenticar el usuario, obtener un token de acceso y un token de actualización de Azure AD y luego usar el token de acceso para realizar solicitudes a la API web.

Para un código de ejemplo que muestra este escenario con autenticación en Azure AD, vea [Aplicación WPF cliente nativa para API web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Autenticación de una aplicación de servidor en un recurso remoto
En este escenario, un desarrollador tiene una aplicación en ejecución en un servidor que necesita acceso a un recurso remoto protegido con Azure AD, como una API web. Tiene una suscripción de Azure, sabe cómo invocar el servicio de bajada y conoce el inquilino de Azure AD que la API web usa. Por consiguiente, puede usar ADAL para facilitar la autenticación con Azure AD controlando explícitamente las credenciales de la aplicación. ADAL hace que resulte fácil recuperar un token de Azure AD con las credenciales del cliente de la aplicación y luego usar ese token para realizar solicitudes a la API web. ADAL también controla la administración de la duración del token de acceso almacenándolo en la caché y renovándolo cuando sea necesario. Para un código de ejemplo que muestra este escenario, vea [Aplicación de consola para API web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Autenticación de una aplicación de servidor en nombre del usuario para acceso a un recurso remoto
En este escenario, un desarrollador tiene una aplicación en ejecución en un servidor que necesita acceso a un recurso remoto protegido con Azure AD, como una API web. La solicitud también debe realizarse en nombre del usuario en Azure AD. Tiene una suscripción de Azure, sabe cómo invocar la API web de bajada y conoce el inquilino de Azure AD que el servicio usa. Cuando el usuario se autentica en la aplicación web, la aplicación puede obtener un código de autorización para el usuario de Azure AD. La aplicación web puede luego usar ADAL para obtener un token de acceso y un token de actualización en nombre del usuario con las credenciales de cliente y el código de autorización asociados a la aplicación de Azure AD. Cuando la aplicación web está en posesión del token de acceso, puede llamar a la API web hasta que el token expire. Cuando el token expira, la aplicación web puede usar ADAL para obtener un nuevo token de acceso con el token de actualización que recibió anteriormente.

## <a name="see-also"></a>Otras referencias
[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

[Escenarios de autenticación para Azure Active Directory](active-directory-authentication-scenarios.md)

[Ejemplos de código de Azure Active Directory](active-directory-code-samples.md)

