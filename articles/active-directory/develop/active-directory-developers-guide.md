---
title: Azure Active Directory para desarrolladores | Microsoft Docs
description: "En este artículo se proporciona información general de inicio de sesión en las cuentas de trabajo y educativas de Microsoft con Azure Active Directory."
services: active-directory
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: bryanla
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: c7f758900ca89ed8bf08090cda5964eccc876e1d
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory para desarrolladores
Azure Active Directory es un servicio de identidad en la nube que permite a los desarrolladores iniciar la sesión de cualquier usuario en una cuenta de trabajo o educativa de Microsoft.  Esta documentación muestra cómo agregar compatibilidad de Azure AD a la aplicación mediante protocolos de autenticación estándar del sector, OAuth y OpenID Connect.

| | |
| --- | --- |
|[Conceptos básicos de autenticación](active-directory-authentication-scenarios.md) | Introducción a la autenticación con Azure AD |
|[Tipos de aplicaciones](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Información general sobre los escenarios de autenticación estándar compatibles con Azure AD |                                
                                                                              
## <a name="get-started"></a>Introducción
Estos programas de instalación interactiva le guiarán en la utilización de nuestras bibliotecas de autenticación para iniciar la sesión de los usuarios de Azure Active Directory.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Aplicaciones para dispositivos móviles y de escritorio](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Aplicaciones para dispositivos móviles y de escritorio</center> | [Información general](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Web Apps](./media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [Información general](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![Aplicaciones de una sola página](./media/active-directory-developers-guide/SPA.png)<br />Aplicaciones de una sola página</center> | [Información general](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript]((https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)) |  |  |
| <center>![API web](./media/active-directory-developers-guide/Web_API.png)<br />API web</center> | [Información general](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Service-to-service](./media/active-directory-developers-guide/Service_App.png)<br />Service-to-Service</center> | [Información general](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[Credenciales del cliente de OAuth 2.0](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>Guías
En estos artículos se informa de cómo realizar tareas comunes con Azure Active Directory.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registro de aplicaciones](active-directory-integrating-applications.md)           | Cómo registrar una aplicación en Azure AD |
|[Aplicaciones multiinquilino](active-directory-devhowto-multi-tenant-overview.md)    | Cómo iniciar sesión en cualquier cuenta de trabajo de Microsoft |
|[OAuth y OpenID Connect](active-directory-protocols-openid-connect-code.md)| Cómo iniciar sesión en los usuarios y llamar a las API web mediante nuestros modernos protocolos de autenticación |
|[Más guías...](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>Referencia
En estos artículos se detallan las API, los mensajes de protocolo y los términos que se usan en Azure Active Directory.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliotecas de autenticación (ADAL)](active-directory-authentication-libraries.md)   | Información general de las bibliotecas y los SDK que proporciona Azure AD |
| [Ejemplos de código](active-directory-code-samples.md)                                  | Lista de todos los ejemplos de código de Azure AD |
| [Glosario](active-directory-dev-glossary.md)                                      | Terminología y definiciones de palabras que se utilizan en esta documentación |
| [Más material de referencia...](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>Ayuda y soporte técnico
Estos son los mejores lugares para obtener ayuda con el desarrollo en Azure Active Directory.

|  |  
|---|
|[`azure-active-directory` de Stack Overflow y etiquetas `adal`](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[Comentarios sobre Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|

<br />

> [!NOTE]
> Si necesita iniciar sesión en cuentas personales de Microsoft, puede que desee considerar el uso del [punto de conexión de Azure AD v2.0](active-directory-appmodel-v2-overview.md).  El punto de conexión de la versión 2.0 de Azure AD es la unión de las cuentas personales de Microsoft y las cuentas de trabajo de Microsoft (de Azure AD) en un sistema de autenticación único.

