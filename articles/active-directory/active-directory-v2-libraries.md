<properties
   pageTitle="Bibliotecas de la versión 2.0 de Azure Active Directory | Microsoft Azure"
   description="Proporciona una lista de todas las bibliotecas de cliente compatibles y de middleware de servidor, junto con vínculos dlibrary/source/samples relacionados, para el punto de conexión v2.0 de Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="skwan;bryanla"/>



# <a name="azure-active-directory-(ad)-v2.0-and-authentication-libraries"></a>Azure Active Directory (AD) v2.0 y bibliotecas de autenticación
El punto de conexión v2.0 de Azure AD admite los protocolos OAuth 2.0 y OpenID Connect 1.0 estándar del sector.  Una variedad de bibliotecas de Microsoft y de otros clientes pueden utilizarse con el punto de conexión v2.0.

Al compilar una aplicación que utiliza el punto de conexión v2.0, se recomienda usar bibliotecas que hayan escrito expertos en los dominios del protocolo que sigan una metodología de ciclo de vida de desarrollo de seguridad (SDL), [como la que sigue Microsoft][Microsoft-SDL].  Si decide codificar manualmente los protocolos, se recomienda seguir SDL y observar detenidamente las consideraciones de seguridad de las especificaciones de los estándares de cada uno de los protocolos.

## <a name="types-of-libraries"></a>Tipos de bibliotecas
Hay dos tipos de bibliotecas que funcionan con la versión 2.0: 

- **Bibliotecas de cliente**: se usan en los clientes nativos y en servidores con el fin de obtener tokens de acceso para llamar a un recurso, como Microsoft Graph.
- **Bibliotecas de middleware de servidor**: las utilizan las aplicaciones web para iniciar la sesión de un usuario y las API web para validar los tokens que envían los clientes nativos u otros servidores.

## <a name="library-support"></a>Compatibilidad con bibliotecas
Puesto que puede elegir cualquier biblioteca que cumpla los estándares al utilizar el punto de conexión v2.0, es importante saber dónde obtener soporte técnico.  Los problemas y las solicitudes de características del código de la biblioteca pasan al propietario de la biblioteca. Los problemas y las solicitudes de características de la implementación del protocolo en el servicio van a Microsoft.

Las bibliotecas se dividen en dos categorías de soporte técnico:

- **Compatibles con Microsoft**: Microsoft ofrece correcciones para estas bibliotecas.  Microsoft ha realizado la diligencia debida del ciclo de vida de desarrollo de seguridad en estas bibliotecas. 
- **Compatibles**: Microsoft ha probado un conjunto de bibliotecas en escenarios básicos y ha confirmado que funcionan con el punto de conexión v2.0.  Microsoft no proporciona correcciones para estas bibliotecas y no ha realizado una revisión de estas bibliotecas.  Los problemas y las solicitudes de características deben dirigirse al proyecto de código abierto de la biblioteca.

Para obtener una lista de bibliotecas que funcionan con el punto de conexión v2.0, lea las secciones siguientes. 

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente compatibles con Microsoft
| Plataforma| Nombre de la biblioteca| Descargar | Código fuente | Muestra |
| :-: | :-: | :-: | :-: | :-: |
| .NET, Windows Store y Xamarin | Biblioteca de autenticación de Microsoft (MSAL) para .NET | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL para .NET (GitHub)][ClientLib-NET-Repo] | [Ejemplo de cliente nativo de escritorio de Windows][ClientLib-NET-Sample] |
| Node.js | Complemento de Passport.js para Microsoft Azure Active Directory | [Passport-Azure-AD (npm)][ClientLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] | Próximamente |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware de servidor compatibles de Microsoft
| Plataforma| Nombre de la biblioteca| Descargar | Código fuente | Muestra |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4.x | Middleware de OWIN OpenID Connect para ASP.NET | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Proyecto Katana (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Ejemplo de aplicación web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x | Middleware de OWIN OAuth Bearer para ASP.Net | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Proyecto Katana (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Ejemplo de API web][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core | Middleware OWIN OpenID Connect para .Net Core | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [Seguridad de ASP.Net (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Ejemplo de aplicación web][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core | Middleware de OWIN OAuth Bearer para .Net Core | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [Seguridad de ASP.Net (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | Próximamente |
| Node.js | Complemento de Passport.js para Microsoft Azure Active Directory | [Passport-Azure-AD (npm)][ServerLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] | [Ejemplo de aplicación web][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .Net | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .Net (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatibles
| Plataforma| Nombre | Versión probada | Código fuente | Muestra |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [Ejemplo de aplicación nativa](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [Ejemplo de aplicación nativa](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | 1.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | Próximamente |
| Python - Flask | [Matraz OAuthlib](https://github.com/lepture/flask-oauthlib) | 0.9.3 | [Matraz OAuthlib](https://github.com/lepture/flask-oauthlib) | Próximamente |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) | Próximamente |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Bibliotecas de middleware de servidores compatibles 
Próximamente


## <a name="related-content"></a>Contenido relacionado
Para más información sobre el punto de conexión V2.0 de Azure AD, consulte [Inicio de sesión de usuarios de cuentas de Microsoft y de Azure AD en una sola aplicación][AAD-App-Model-V2-Overview]. 

Use la siguiente sección de comentarios DISQUS para proporcionar sus opiniones y ayudarnos a afinar y remodelar nuestro contenido.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:
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




<!--HONumber=Oct16_HO2-->


