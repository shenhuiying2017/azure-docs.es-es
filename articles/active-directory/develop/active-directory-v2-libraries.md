---
title: "Bibliotecas de autenticación de Azure Active Directory v2.0 | Microsoft Docs"
description: "Las bibliotecas de cliente y de middleware de servidor compatibles, junto con vínculos dlibrary/source/samples relacionados, para el punto de conexión Azure Active Directory v2.0."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a1e2c4db54ca81325751ed85b26b904174e94078
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliotecas de autenticación de Azure Active Directory v2.0
El [punto de conexión de Azure Active Directory (Azure AD) v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) admite los protocolos OAuth 2.0 y OpenID Connect 1.0 estándar del sector. Puede utilizar diversas bibliotecas de Microsoft y otras organizaciones con el punto de conexión de v2.0.

Al compilar una aplicación que utiliza el punto de conexión v2.0, se recomienda usar bibliotecas que hayan escrito expertos en los dominios del protocolo que sigan una metodología de ciclo de vida de desarrollo de seguridad (SDL), como [a la que sigue Microsoft][Microsoft-SDL]. Si decide codificar manualmente los protocolos, se recomienda seguir la metodología SDL y observar detenidamente las consideraciones de seguridad de las especificaciones de los estándares de cada protocolo.

> [!NOTE]
> ¿Busca las bibliotecas de Azure AD v1.0 (ADAL)? Revise la [Guía de la biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). 
> 
> 

## <a name="types-of-libraries"></a>Tipos de bibliotecas
El punto de conexión de Azure AD v2.0 funciona con dos tipos de bibliotecas:

* **Bibliotecas de cliente**. Los servidores y clientes nativos utilizan bibliotecas de clientes para obtener tokens de acceso para llamar a un recurso, como Microsoft Graph.
* **Bibliotecas de middleware de servidores**. Las aplicaciones web usan bibliotecas de middleware de servidor para el inicio de sesión de usuario. Las API de web utilizan bibliotecas de middleware de servidor para validar los tokens que se envían mediante clientes nativos o mediante otros servidores.

## <a name="library-support"></a>Compatibilidad con bibliotecas
Puesto que puede elegir cualquier biblioteca que cumpla los estándares al utilizar el punto de conexión v2.0, es importante saber dónde obtener soporte técnico. Para problemas y solicitudes de características en el código de la biblioteca, póngase en contacto con el propietario de la biblioteca. Para problemas y solicitudes de características de la implementación del protocolo en el servicio, póngase en contacto con Microsoft.

Las bibliotecas se dividen en dos categorías de soporte técnico:

* **Soporte técnico de Microsoft**. Microsoft proporciona soluciones para estas bibliotecas y ha hecho las diligencias necesarias con SDL para estas bibliotecas.
* **Compatible**. Microsoft ha probado estas bibliotecas en escenarios básicos y ha confirmado que funcionan con el punto de conexión v2.0. Microsoft no proporciona correcciones para estas bibliotecas y no ha realizado una revisión de estas bibliotecas. Los problemas y las solicitudes de características deben dirigirse al proyecto de código abierto de la biblioteca.

Para obtener una lista de bibliotecas que funcionan con el punto de conexión v2.0, lea las secciones siguientes de este artículo.


## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente compatibles con Microsoft

> [!IMPORTANT]
> Las bibliotecas de versión preliminar MSAL son adecuadas para su uso en entorno de producción. Se ofrece el mismo soporte técnico de nivel de producción para estas bibliotecas que para nuestras bibliotecas de producción actuales (ADAL). Durante el período de versión preliminar pueden realizarse cambios a la API de MSAL, al formato de la memoria caché interna y a otros mecanismos de estas bibliotecas sin previo aviso, lo que deberá aceptar junto con correcciones de errores o mejoras de las características. Esto puede afectar a la aplicación. Por ejemplo, un cambio al formato de la memoria caché puede afectar a sus usuarios, como, por ejemplo, exigirles que vuelva a iniciar sesión. Un cambio a la API puede requerir que actualice el código. Cuando se ofrezca la versión de disponibilidad general, deberá actualizar a esta versión en los seis meses siguientes, ya que es posible que las aplicaciones escritas con una versión de biblioteca de versión preliminar dejen de funcionar.

| Plataforma | Biblioteca | Descargar | Código fuente | Muestra | Referencia
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Tienda Windows, UWP, Xamarin iOS y Android | MSAL .NET (versión preliminar) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicación de escritorio](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (versión preliminar) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Aplicación de una sola página](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (versión preliminar) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplicación para iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (versión preliminar) | [The Central Repository](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicación para Android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware de servidor compatibles de Microsoft

| Plataforma | Biblioteca | Descargar | Código fuente | Muestra | Referencia
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | Middleware OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicación MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Middleware OWIN OAuth Bearer para Azure AD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |  | |
| .NET 4.x | Controlador JWT para .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Middleware ASP.NET OpenID Connect |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Seguridad de ASP.Net (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Aplicación MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Middleware ASP.NET OAuth Bearer |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Seguridad de ASP.Net (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Controlador JWT para .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplicación web](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatibles
| Plataforma | Nombre de la biblioteca | Versión probada | Código fuente | Muestra |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Ejemplo de aplicación nativa](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Ejemplo de aplicación nativa](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |

## <a name="compatible-server-middleware-libraries"></a>Bibliotecas de middleware de servidores compatibles
| Plataforma | Nombre de la biblioteca | Versión probada | Código fuente | Muestra |
|:---:|:---:|:---:|:---:|:---:|
| Java | [Scribejava Scribe Java](https://github.com/scribejava/scribejava) | [Versión 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [PHP League oauth2-cliente](https://github.com/thephpleague/oauth2-client) | [Versión 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
| Python-Flask |[Matraz OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Matraz OAuthlib](https://github.com/lepture/flask-oauthlib) |[Aplicación web](https://github.com/Azure-Samples/active-directory-python-flask-graphapi-web-v2) |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Contenido relacionado
Para obtener más información sobre el punto de conexión de Azure AD v2.0, consulte la [introducción a la versión 2.0 del modelo de aplicaciones de Azure AD][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
