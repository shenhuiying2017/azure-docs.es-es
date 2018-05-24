---
title: Ejemplos de código de Azure Active Directory | Microsoft Docs
description: Proporciona un índice de ejemplos de código de Azure Active Directory (punto de conexión v1), organizados por escenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 299e0d4fa53f9b8a2aef2fc881b136aa41aacfe4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157782"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Ejemplos de código de Azure Active Directory (punto de conexión V1)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Puede usar Microsoft Azure Active Directory (Azure AD) para agregar autenticación y autorización a sus aplicaciones web y API web.

En esta sección se proporcionan vínculos a ejemplos que puede usar para obtener más información sobre el punto de conexión de Azure AD V1. En estos ejemplos se muestra cómo se hace, junto con fragmentos de código que puede usar en sus aplicaciones. En la página de ejemplos de código, encontrará temas Léame detallados que le ayudarán con los requisitos, la instalación y la configuración. Y se comenta el código para ayudarle a comprender las secciones críticas.

> [!NOTE]
> Si los ejemplos de código de Azure AD V2 son de su interés, consulte los [ejemplos de código de v2.0 por escenario](active-directory-v2-code-samples.md).

Para comprender los escenarios básicos para cada tipo de ejemplo, consulte [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

También puede contribuir a nuestros ejemplos en GitHub. Para saber cómo, vea [Ejemplos y documentación de Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplicaciones de escritorio y móviles de cliente público que llaman a Microsoft Graph o a una API web

En los siguientes ejemplos se muestran aplicaciones de cliente público (aplicaciones de escritorio o móviles) que acceden a Microsoft Graph o a una API web en nombre de un usuario.

Aplicación cliente | Plataforma | Flujo y concesión | Llamada a Microsoft Graph | Llama a una API web de ASP.NET o de ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Escritorio (WPF)           | .NET/C# | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Móvil (UWP)            | .NET/C#  | Interactive | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (API web de un solo inquilino) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (API web de varios inquilinos)|
Móvil (Android, iOS, UWP)   | .NET/C# (Xamarin) | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Móvil (Android)           | Android/Java | Interactive |   [android](https://github.com/Azure-Samples/active-directory-android) |
Móvil (iOS)           | iOS/Objective C | Interactive |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Escritorio (consola)          | .NET/C# | Nombre de usuario y contraseña </p> Autenticación integrada de Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Escritorio (consola)           | .NET Core/C# | Perfil de dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Aplicaciones web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplicaciones web que inician sesión en nombre de los usuarios y llaman a Microsoft Graph o una API web con la identidad del usuario

 Plataforma | Únicamente inicio de sesión del usuario | Llamada a Microsoft Graph o a AAD Graph| Llamada a otra API web de ASP.NET o de ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplicaciones web que muestran el control de acceso basado en roles (autorización)

En los siguientes ejemplos se muestra cómo implementar el control de acceso basado en roles, que se utiliza para restringir los permisos de ciertas características de una aplicación web a determinados usuarios. Se autoriza a los usuarios dependiendo de si pertenecen a un grupo o un rol de Azure AD.

Plataforma | Muestra | DESCRIPCIÓN
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Aplicación web de .NET 4.5 MVC que usa **grupos** de Azure AD para la autorización
ASP.NET 4.5 | [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Aplicación web de .NET 4.5 MVC que usa **roles** de Azure AD para la autorización

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplicaciones demonio (que acceden a las API web con la identidad de la aplicación)

En los siguientes ejemplos se muestran aplicaciones de escritorio o web que acceden a Microsoft Graph o a una API web sin usuario (con la identidad de la aplicación).

Aplicación cliente | Plataforma | Flujo y concesión | Llamada a Microsoft Graph | Llama a una API web de ASP.NET o de ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Aplicación demonio (consola)          | .NET/C#  | Credenciales del cliente con secreto de la aplicación o certificado | | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplicación demonio (consola)         | .NET Core/C# | Credenciales del cliente con certificado| | [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Escritorio            | Java | Credenciales de cliente |   [java-native-headless](https://github.com/azure-samples/active-directory-java-native-headless) |
Aplicación web para ASP.NET  | .NET/C# | Credenciales de cliente |    | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>API web

### <a name="web-api-protected-by-azure-active-directory"></a>API web protegida por Azure Active Directory

En el ejemplo siguiente se muestra cómo proteger una API web de node.js con Azure AD.

Plataforma | Muestra | DESCRIPCIÓN
 -------- | ------------------- | ---------------------
Node.js | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  API web de NodeJS protegida mediante los tokens de acceso de Azure AD y OAuth 2.0.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Llamada de una API web a Microsoft Graph o a otra API web

En los ejemplos siguientes se muestra una API web que llama a otra API web. En el segundo ejemplo se muestra cómo controlar el acceso condicional.

 Plataforma |  Llamada a Microsoft Graph | Llamada a otra API web de ASP.NET o de ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Aplicación de una sola página

En este ejemplo se muestra cómo escribir una aplicación de una sola página protegida con Azure AD.

 Plataforma |  Llamada a Microsoft Graph | Llama a su propia API
 -------- |  --------------------- | -------------------------
JavaScript (Angular) o ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)

## <a name="other-microsoft-graph-samples"></a>Otros ejemplos de Microsoft Graph

Para obtener ejemplos y tutoriales en los que se muestren los diferentes patrones de uso para la API de Microsoft Graph, incluida la autenticación con Azure AD, consulte [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Ejemplos y tutoriales de la comunidad de Microsoft Graph).

## <a name="see-also"></a>Otras referencias

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

[Conceptos y referencia de Graph API de Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca auxiliar de Graph API de Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
