---
title: "Tipos de aplicación para el punto de conexión v2.0 de Azure Active Directory | Microsoft Docs"
description: "Tipos de aplicaciones y escenarios admitidos por el punto de conexión v2.0 de Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 9d59e7f0e8f326c40be86e199d7712f6c565cc13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Tipos de aplicación para el punto de conexión v2.0 de Azure Active Directory
El punto de conexión v2.0 de Azure Active Directory (Azure AD) admite la autenticación de una variedad de arquitecturas de aplicaciones modernas, todas ellas basadas en los protocolos estándar del sector [OAuth 2.0 u OpenID Connect](active-directory-v2-protocols.md). En este artículo se describen los tipos de aplicaciones que puede crear mediante Azure AD v2.0, con independencia de su plataforma o idioma preferidos. La información de este artículo está diseñada para ayudarle a entender los escenarios de alto nivel antes de [empezar a trabajar con el código](active-directory-appmodel-v2-overview.md#getting-started).

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con la versión 2.0 del punto de conexión. Para determinar si debe usar la versión 2.0 del punto de conexión, obtenga información sobre las [limitaciones de esta versión](active-directory-v2-limitations.md).
> 
> 

## <a name="the-basics"></a>Conceptos básicos
Cada aplicación que use el punto de conexión v2.0 debe registrarla en el [portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com). El proceso de registro de la aplicación recopila y asigna algunos valores a la aplicación:

* Un **id. de aplicación** que identifica de forma única su aplicación
* Un **URI de redirección** que puede usarse para dirigir las respuestas de nuevo a la aplicación
* Algunos otros valores específicos de cada escenario.

Para más información, aprenda a [registrar una aplicación](active-directory-v2-app-registration.md).

Una vez registrada, la aplicación se comunica con Azure AD mediante el envío de solicitudes al punto de conexión v2.0 de Azure AD. Proporcionamos bibliotecas y marcos de código abierto que controlan los detalles de estas solicitudes. También tiene la opción de implementar la lógica de autenticación por su cuenta mediante la creación de solicitudes a estos puntos de conexión:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Aplicaciones web
En las aplicaciones web (. NET, PHP, Java, Ruby, Python, Node) a las que el usuario accede a través de un explorador, puede usar [OpenID Connect](active-directory-v2-protocols.md) para el inicio de sesión de usuario. En OpenID Connect, la aplicación web recibe un identificador de token. Un token de id. es un token de seguridad que comprueba la identidad del usuario y proporciona información sobre el usuario en forma de notificaciones:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Puede aprender más sobre todos los tipos de token y notificaciones disponibles para una aplicación en la [referencia de token v2.0](active-directory-v2-tokens.md).

En las aplicaciones de servidor web, el flujo de autenticación de inicio de sesión realiza estos pasos de alto nivel:

![Flujo de autenticación de aplicaciones web](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Puede confirmar la identidad del usuario mediante la validación del token de id. con una clave de firma pública recibida por el punto de conexión v2.0. Se establece una cookie de sesión, que puede usarse para identificar al usuario en las sucesivas solicitudes de página.

Para ver este escenario en acción, pruebe uno de los ejemplos de código de inicio de sesión de aplicación web en nuestra sección de [introducción](active-directory-appmodel-v2-overview.md#getting-started) de v2.0.

Además del inicio de sesión sencillo, una aplicación web de servidor podría tener la necesidad de acceder a otros servivio web, como una API de REST. En este caso, la aplicación de servidor web participa en un flujo combinado de OpenID Connect y OAuth 2.0, mediante el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols.md). Para más información sobre este escenario, lea acerca de cómo [comenzar con aplicaciones web y API web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>API web
Puede usar el punto de conexión v2.0 para proteger los servicios web, como la API web RESTful de su aplicación. En lugar de tokens de id. y cookies de sesión, una API web usa un token de acceso de OAuth 2.0 para proteger sus datos y para autenticar las solicitudes entrantes. El llamador de una API web anexa un token de acceso en el encabezado de autorización de una solicitud HTTP, como esta:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

La API web usa el token de acceso para comprobar la identidad del llamador de API y extraer información sobre el llamador de notificaciones que se codifican en dicho token. Para más información sobre todos los tipos de tokens y notificaciones que están disponibles para una aplicación, consulte la [referencia de tokens de v2.0](active-directory-v2-tokens.md).

Una API web puede ofrecer a los usuarios la capacidad para administrar la participación/no participación de ciertas funcionalidades o datos mediante la exposición de permisos, conocidos también como [ámbitos](active-directory-v2-scopes.md). Para que una aplicación de llamada adquiera permiso para un ámbito, el usuario debe consentir el ámbito durante un flujo. El punto de conexión v2.0 solicita al usuario permiso y luego registra los permisos en todos los tokens de acceso que recibe la API web. La API web valida los tokens de acceso que recibe en cada llamada y realiza comprobaciones de autorización.

Una API web puede recibir tokens de acceso de todos los tipos de aplicaciones, incluidas las aplicaciones de servidor web, aplicaciones móviles y de escritorio, aplicaciones de una página, demonios del lado del servidor e incluso otras API web. El flujo de alto nivel de una API web se parece a este:

![Flujo de autenticación de API web](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Para aprender a proteger una API web con tokens de acceso de OAuth2, consulte los ejemplos de código de API web en nuestra sección de [introducción](active-directory-appmodel-v2-overview.md#getting-started).

En muchos casos, las API web también tienen que realizar solicitudes salientes a otras API web de bajada protegidas por Azure Active Directory.  Para ello, las API web pueden aprovechar las ventajas del flujo **en nombre de** de Azure AD, que permite a la API web intercambiar un token de acceso entrante por otro token de acceso que se usará en las solicitudes salientes.  El flujo "en nombre de" del punto de conexión v2.0 se describe [aquí con mayor detalle](active-directory-v2-protocols-oauth-on-behalf-of.md).

## <a name="mobile-and-native-apps"></a>Aplicaciones móviles y nativas
Las aplicaciones instaladas en un dispositivo, como las aplicaciones móviles y de escritorio, suelen necesitar el acceso a servicios back-end o a las API web que almacenan datos y realizan varias funciones en nombre del usuario. Estas aplicaciones pueden agregar el inicio de sesión y la autorización a los servicios back-end mediante el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

En este flujo, la aplicación recibe un código de autorización del punto de conexión v2.0 cuando el usuario inicia sesión. El código de autorización representa el permiso de la aplicación para llamar a servicios de back-end en nombre del usuario que inició la sesión. La aplicación podrá intercambiar el código de autorización en segundo plano para un token de acceso de OAuth 2.0 y un token de actualización. La aplicación puede usar el token de acceso para autenticar las API web en las solicitudes HTTP y utilizar el token de actualización para obtener nuevos tokens de acceso cuando expiren los antiguos.

![Flujo de autenticación de la aplicación nativa](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Aplicaciones de una página (JavaScript)
Muchas aplicaciones modernas tienen un front-end de aplicación de una página escrito principalmente en JavaScript. A menudo, se escribe con un marco como AngularJS, Ember.js o Durandal.js. El punto de conexión v2.0 de Azure AD admite estas aplicaciones mediante el [flujo implícito de OAuth 2.0](active-directory-v2-protocols-implicit.md).

En este flujo, la aplicación recibe tokens directamente del punto de conexión de autorización de v2.0, sin necesidad de realizar ningún intercambio de servidor a servidor. Toda la lógica de autenticación y el control de sesiones tiene lugar por completo en el cliente de JavaScript, sin redireccionamientos de página adicionales.

![Flujo de autenticación implícita](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Para ver este escenario en acción, pruebe uno de los ejemplos de código de aplicación de una única página en nuestra sección de [introducción](active-directory-appmodel-v2-overview.md#getting-started).

## <a name="daemons-and-server-side-apps"></a>Demonios y aplicaciones de servidor
Las aplicaciones que contienen procesos de larga duración o que funcionan sin la interacción con un usuario también necesitan un modo de acceder a recursos protegidos, como las API web. Estas aplicaciones pueden autenticarse y obtener tokens mediante la identidad de la aplicación, en lugar de una identidad delegada del usuario, con el flujo de credenciales de cliente de OAuth 2.0.

En este flujo, la aplicación interactúa directamente con el punto de conexión `/token` para obtener los puntos de conexión:

![Flujo de autenticación de aplicación de demonio](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Para compilar una aplicación demonio, consulte la documentación de credenciales de cliente en nuestra sección de [introducción](active-directory-appmodel-v2-overview.md#getting-started) o pruebe una [aplicación de ejemplo de .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
