---
title: Azure Active Directory para desarrolladores | Microsoft Docs
description: "En este artículo se proporciona una introducción al inicio de sesión de las cuentas profesionales y educativas de Microsoft con Azure Active Directory."
services: active-directory
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 89a232af6387f6403e6e341cced16d06e9979dae
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory para desarrolladores
Azure Active Directory (Azure AD) es un servicio de identidad en la nube que permite a los desarrolladores iniciar la sesión de los usuarios en una cuenta profesional o educativa de Microsoft de forma segura. En esta documentación se muestra cómo agregar compatibilidad con Azure AD a una aplicación mediante los protocolos estándar del sector: OAuth 2.0 y OpenID Connect.

| | |
| --- | --- |
|[Conceptos básicos sobre autenticación](active-directory-authentication-scenarios.md) | Una introducción a la autenticación con Azure AD. |
|[Tipos de aplicaciones](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Una introducción a los escenarios de autenticación que se admiten en Azure AD. |                                
                                                                              
## <a name="get-started"></a>Introducción
Las siguientes configuraciones guiadas le llevan por el uso de las bibliotecas de autenticación de Microsoft para iniciar la sesión de los usuarios de Azure AD.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Aplicaciones móviles y de escritorio](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Aplicaciones móviles y de escritorio</center> | [Información general](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[.NET (UWP)](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Aplicaciones web](./media/active-directory-developers-guide/Web_app.png)<br />Aplicaciones web</center> | [Información general](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |  |
| <center>![Aplicaciones de una sola página](./media/active-directory-developers-guide/SPA.png)<br />Aplicaciones de una sola página</center> | [Información general](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![API web](./media/active-directory-developers-guide/Web_API.png)<br />API web</center> | [Información general](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Service-to-service](./media/active-directory-developers-guide/Service_App.png)<br />De servicio a servicio</center> | [Información general](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)|  |

## <a name="how-to-guides"></a>Guías de procedimientos
Las siguientes guías informan sobre cómo realizar tareas comunes con Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registro de la aplicación](active-directory-integrating-applications.md)           | Cómo registrar una aplicación en Azure AD. |
|[Aplicaciones multiinquilino](active-directory-devhowto-multi-tenant-overview.md)    | Cómo iniciar la sesión de cualquier cuenta profesional de Microsoft. |
|[Protocolos OAuth y OpenID Connect](active-directory-protocols-openid-connect-code.md)| Cómo iniciar la sesión de los usuarios y llamar a las API web mediante los protocolos de autenticación de Microsoft. |
|[Guías adicionales](active-directory-developers-guide-index.md#guides)        |  Una lista de las guías que están disponibles para Azure AD.   |

## <a name="reference-topics"></a>Temas de referencia
En los siguientes artículos se proporciona información detallada sobre las API, los mensajes de protocolo y los términos que se usan en Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliotecas de autenticación (ADAL)](active-directory-authentication-libraries.md)   | Una introducción a las bibliotecas y los SDK que se proporcionan en Azure AD. |
| [Ejemplos de código](active-directory-code-samples.md)                                  | Una lista de todos los ejemplos de código de Azure AD. |
| [Glosario](active-directory-dev-glossary.md)                                      | Terminología y definiciones de palabras que se usan en esta documentación. |
| [Temas de referencia adicionales](active-directory-developers-guide-index.md#reference)| Una lista de temas de referencia que están disponibles para Azure AD.   |


> [!NOTE]
> Si necesita iniciar la sesión de cuentas personales de Microsoft, puede que quiera considerar el uso del [punto de conexión de Azure AD v2.0](active-directory-appmodel-v2-overview.md). El punto de conexión de Azure AD v2.0 es la unión de las cuentas personales de Microsoft y las cuentas profesionales de Microsoft (de Azure AD) en un único sistema de autenticación.


[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]