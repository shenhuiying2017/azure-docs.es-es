---
title: Información general sobre la versión 2.0 de los puntos de conexión | Microsoft Docs
description: Introducción a la creación de aplicaciones con inicio de sesión de cuentas de Microsoft y de Azure Active Directory.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: dastrock

---
# <a name="sign-in-microsoft-account-&-azure-ad-users-in-a-single-app"></a>Inicio de sesión de usuarios de cuentas de Microsoft y de Azure AD en una sola aplicación
Antes, un desarrollador de aplicaciones que deseara admitir cuentas de Microsoft y Azure Active Directory debía realizar la integración con dos sistemas independientes.  Hemos incorporado una nueva versión de API de autenticación que permite el inicio de sesión de los usuarios con ambos tipos de cuentas mediante el sistema de Azure AD.  Este sistema de autenticación convergente se conoce como **punto de conexión v2.0**.  Con el punto de conexión v2.0, una integración sencilla le permite llegar a una audiencia que abarca millones de usuarios con cuentas personales, así como profesionales y educativas.

Las aplicaciones que usan el punto de conexión v2.0 también pueden usar las API de REST de [Microsoft Graph](https://graph.microsoft.io) y [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) con cualquier tipo de cuenta.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Introducción
[!VIDEO https://channel9.msdn.com/Events/Build/2016/P530/player]


Elija su plataforma favorita de la lista siguiente para compilar una aplicación mediante nuestros marcos y bibliotecas de código abierto.  Como alternativa, puede usar la documentación del protocolo OAuth 2.0 y OpenID Connect para enviar y recibir directamente mensajes de protocolo sin usar una biblioteca de autenticación.

<!-- TODO: Finalize this table  -->
[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="what's-new"></a>Novedades
La información conceptual que aquí se describe será útil para entender qué se puede y no se puede hacer con el punto de conexión v2.0.

* Obtenga información sobre los [tipos de aplicaciones que puede compilar con la versión 2.0 del punto de conexión](active-directory-v2-flows.md).
* Conozca las [limitaciones y restricciones](active-directory-v2-limitations.md) de la versión 2.0 del punto de conexión.
* Hace poco, agregamos compatibilidad para los [ámbitos restringidos a administradores](active-directory-v2-scopes.md) y la [concesión de credenciales de cliente de OAuth2](active-directory-v2-protocols-oauth-client-creds.md).  Le animamos a que pruebe estas características.

## <a name="reference"></a>Referencia
Estos vínculos le servirán para explorar la plataforma en profundidad:

* Build 2016: [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
* Obtenga ayuda acerca de Stack Overflow con las etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
* [Referencia de protocolos de v2.0](active-directory-v2-protocols.md)
* [Referencia de los tokens de v2.0](active-directory-v2-tokens.md)
* [Referencia de la biblioteca de la versión 2.0](active-directory-v2-libraries.md)
* [Ámbitos y consentimiento en el punto de conexión v2.0](active-directory-v2-scopes.md)
* [Portal de registro para aplicaciones de Microsoft](https://apps.dev.microsoft.com)
* [Referencia de API de REST de Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
* [Microsoft Graph](https://graph.microsoft.io)

<!--HONumber=Oct16_HO2-->


