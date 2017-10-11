---
title: "Usar autenticación de Azure AD para acceder a la API de Azure Media Services con REST | Microsoft Docs"
description: "Aprenda a acceder a la API de Azure Media Services con la autenticación de Azure Active Directory mediante REST."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 1c62857699fb29b3583363e1c6f2dc7874635f40
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Usar autenticación de Azure AD para acceder a la API de Azure Media Services con REST

El equipo de Azure Media Services ha lanzado la compatibilidad con la autenticación de Azure Active Directory (Azure AD) para el acceso a Azure Media Services. También ha anunciado planes para dejar de usar la autenticación de Azure Access Control Service para el acceso a Media Services. Dado que cada suscripción de Azure y cada cuenta de Media Services están asociadas a un inquilino de Azure AD, la compatibilidad con la autenticación de Azure AD ofrece muchas ventajas de seguridad. Para obtener detalles sobre este cambio y esta migración (si usa el SDK de .NET para Media Services para la aplicación), vea las entradas de blog y los artículos siguientes:

- [Azure Media Services announces support for Azure AD and deprecation of Access Control authentication](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation) (Azure Media Services anuncia la compatibilidad con Azure AD y el desuso de la autenticación de Access Control)
- [Access Azure Media Services API by using Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md) (Acceder a la API de Azure Media Services mediante la autenticación de Azure AD)
- [Use Azure AD authentication to access Azure Media Services API by using Microsoft .NET](media-services-dotnet-get-started-with-aad.md) (Usar autenticación de Azure AD para acceder a la API de Azure Media Services con Microsoft .NET)
- [Getting started with Azure AD authentication by using the Azure portal](media-services-portal-get-started-with-aad.md) (Introducción a la autenticación de Azure AD mediante Azure Portal)

Algunos clientes necesitan desarrollar sus soluciones de Media Services con las restricciones siguientes:

*   Usan un lenguaje de programación que no es Microsoft .NET ni C#, o el entorno de runtime no es Windows.
*   Las bibliotecas de Azure AD, como la Biblioteca de autenticación de Active Directory, no están disponibles para el lenguaje de programación o no se pueden usar para su entorno de runtime.

Algunos clientes han desarrollado aplicaciones mediante la API de REST para la autenticación de Access Control y el acceso a Azure Media Services. Para estos clientes, necesita una forma de usar solo la API de REST para la autenticación de Azure AD y el subsiguiente acceso a Azure Media Services. Necesita no depender de ninguna de las bibliotecas de Azure AD ni del SDK de .NET para Media Services. En este artículo se describe una solución y se proporciona código de ejemplo para este escenario. Dado que el código es todo llamadas API de REST, sin ninguna dependencia con ninguna biblioteca de Azure AD o Azure Media Services, se puede traducir fácilmente a cualquier otro lenguaje de programación.

> [!IMPORTANT]
> Actualmente Media Services es compatible con el modelo de autenticación de Azure Access Control Service. No obstante, la autenticación de Access Control dejará de usarse el 1 de junio de 2018. Se recomienda migrar tan pronto como sea posible al modelo de autenticación de Azure AD.


## <a name="design"></a>Diseño

En este artículo se usa el siguiente diseño de autenticación y autorización:

*  Protocolo de autorización: OAuth 2.0. OAuth 2.0 es un estándar de seguridad web que cubre la autenticación y la autorización. Es compatible con Google, Microsoft, Facebook y PayPal. Se ratificó en octubre de 2012. Microsoft es firme partidario de OAuth 2.0 y OpenID Connect. Ambos estándares son compatibles con varios servicios y bibliotecas de cliente, incluidos Azure Active Directory, Open Web Interface for .NET (OWIN), Katana y las bibliotecas de Azure AD.
*  Tipo de concesión: credenciales de cliente. Credenciales de cliente es uno de los cuatro tipos de concesión de OAuth 2.0. A menudo se usa para el acceso a la API de Microsoft Graph de Azure AD.
*  Modo de autenticación: entidad de servicio. El otro modo de autenticación es la autenticación interactiva o de usuario.

En el flujo de autenticación y autorización de Azure AD para el uso de Media Services hay implicados un total de cuatro aplicaciones o servicios. Las aplicaciones, los servicios y el flujo se describen en la tabla siguiente:

|Tipo de aplicación |Application |Flujo|
|---|---|---|
|Cliente | Aplicación o solución de cliente | Esta aplicación (en realidad, su proxy) se registra en el inquilino de Azure AD en el que residen la suscripción de Azure y la cuenta del servicio multimedia. Luego se concede a la entidad de servicio de la aplicación registrada el rol de Propietario o Colaborador en el control de acceso (IAM) de la cuenta del servicio multimedia. La entidad de servicio se representa mediante el identificador de cliente de la aplicación y el secreto de cliente. |
|Proveedor de identidades (IDP) | Azure AD como IDP | La entidad de servicio de la aplicación registrada (id. de cliente y secreto de cliente) se autentica con Azure AD como IDP. Esta autenticación se realiza interna e implícitamente. Como en el flujo de credenciales de cliente, se autentica el cliente en lugar del usuario. |
|Servicio de token seguro (STS) o servidor OAuth | Azure AD como STS | Después de la autenticación mediante el IDP (o el servidor OAuth en términos de OAuth 2.0), Azure AD emite un token de acceso o JSON Web Token (JWT) como STS o servidor OAuth para el acceso al recurso de nivel intermedio: en nuestro caso, el punto de conexión de la API de REST de Media Services. |
|Recurso | API de REST de Media Services | Todas las llamadas API de REST de Media Services son autorizadas por un token de acceso emitido por Azure AD como STS o servidor OAuth. |

Si ejecuta el código de ejemplo y captura un JWT o un token de acceso, el JWT tiene los siguientes atributos:

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

Estas son las asignaciones entre los atributos del JWT y las cuatro aplicaciones o servicios de la tabla anterior:

|Tipo de aplicación |Application |Atributo del JWT |
|---|---|---|
|Cliente |Aplicación o solución de cliente |appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6". Identificador de cliente de una aplicación que se registrará en Azure AD en la sección siguiente. |
|Proveedor de identidades (IDP) | Azure AD como IDP |idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/".  El GUID es el identificador del inquilino de Microsoft (microsoft.onmicrosoft.com). Cada inquilino tiene su propio identificador único. |
|Servicio de token seguro (STS) o servidor OAuth |Azure AD como STS | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/". El GUID es el identificador del inquilino de Microsoft (microsoft.onmicrosoft.com). |
|Recurso | API de REST de Media Services |aud: "https://rest.media.azure.net". Destinatario o audiencia del token de acceso. |

## <a name="steps-for-setup"></a>Pasos de configuración

Para registrar y configurar una aplicación de Azure AD para la autenticación de Azure AD y para obtener un token de acceso para llamar a un punto de conexión de la API de REST de Azure Media Services, realice los pasos siguientes:

1.  En el [portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885), registre una aplicación de Azure AD (por ejemplo, wzmediaservice) para el inquilino de Azure AD (por ejemplo, microsoft.onmicrosoft.com). No importa si se ha registrado como aplicación web o nativa. Además, puede elegir cualquier dirección URL de inicio de sesión y de respuesta (por ejemplo, http://wzmediaservice.com para ambas).
2. En el [portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885), vaya a la pestaña **Configurar** de la aplicación. Anote el **Id. de cliente**. Luego, en **Claves**, genere una **clave de cliente** (secreto del cliente). 

    > [!NOTE] 
    > Anote el secreto del cliente. No se volverá a mostrar.
    
3.  En [Azure Portal](http://ms.portal.azure.com), vaya a la cuenta de Media Services. Seleccione el panel **Control de acceso (IAM)**. Agregue un nuevo miembro que tenga el rol Propietario o Colaborador. Para la entidad de seguridad, busque el nombre de la aplicación registrada en el paso 1 (en este ejemplo, wzmediaservice).

## <a name="info-to-collect"></a>Información que se debe recopilar

Para preparar la codificación de REST, recopile los siguientes puntos de datos que se incluirán en el código:

*   Azure AD como punto de conexión de STS: https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token. Desde este punto de conexión se solicita un token de acceso JWT. Además de actuar como IDP, Azure AD también lo hace como STS. Azure AD emite un JWT para el acceso a los recursos (un token de acceso). Un token JWT tiene distintas notificaciones.
*   API de REST de Azure Media Services como recurso o audiencia: https://rest.media.azure.net.
*   Id. de cliente: vea el paso 2 de [Pasos de configuración](#steps-for-setup).
*   Secreto del cliente: vea el paso 2 de [Pasos de configuración](#steps-for-setup).
*   El punto de conexión de la API de REST de la cuenta de Media Services en el formato siguiente:

    https://[media_service_account_name].restv2.[data_center].media.azure.net/API 

    Este es el punto de conexión en el que se realizan todas las llamadas API de REST de Media Services en la aplicación. Por ejemplo, https://willzhanmswjapan.restv2.japanwest.media.azure.net/API.

Luego puede colocar estos cinco parámetros en el archivo web.config o app.config para su uso en el código.

## <a name="sample-code"></a>Código de ejemplo

Puede encontrar el código de ejemplo en [Azure AD Authentication for Azure Media Services Access: Both via REST API](https://github.com/willzhan/WAMSRESTSoln) (Autenticación de Azure AD para el acceso a Azure Media Services: a través de la API de REST).

El código de ejemplo tiene dos partes:

*   Un proyecto de biblioteca DLL que tiene todo el código de la API de REST para la autorización y autenticación de Azure AD. También tiene un método para realizar llamadas API de REST al punto de conexión de la API de REST de Media Services, con el token de acceso.
*   Un cliente de prueba de consola, que inicia la autenticación de Azure AD y llama a distintas API de REST de Media Services.

El proyecto de ejemplo tiene tres características:

*   Autenticaciones de Azure AD a través de la concesión de credenciales de cliente mediante la API de REST únicamente.
*   Acceso a Azure Media Services mediante la API de REST únicamente.
*   Acceso a Azure Storage mediante la API de REST únicamente (al igual que para crear una cuenta de Media Services, mediante la API de REST).


## <a name="where-is-the-refresh-token"></a>¿Dónde está el token de actualización?

Algunos lectores podrían preguntar: ¿dónde está el token de actualización? ¿Por qué no usar aquí un token de actualización?

El propósito de un token de actualización no es actualizar un token de acceso. Por el contrario, está diseñado para omitir la intervención del usuario o la autenticación del usuario final y aun así obtener un token de acceso válido cuando expire un token anterior. Un nombre más adecuado para un token de actualización sería algo como "token de omisión de reinicio de sesión de usuario".

Si usa el flujo de concesión de autorización de OAuth 2.0 (nombre de usuario y contraseña que actúan en nombre de un usuario), un token de actualización ayuda a obtener un token de acceso renovado sin solicitar intervención del usuario. Pero para el flujo de concesión de credenciales de cliente de OAuth 2.0 que se describe en este artículo, el cliente actúa en su propio nombre. No necesita ninguna intervención del usuario y el servidor de autorización no necesita (ni va a) proporcionar un token de actualización. Si depura el método **GetUrlEncodedJWT**, observe que la respuesta desde el punto de conexión del token tiene un token de acceso, pero ningún token de actualización.

## <a name="next-steps"></a>Pasos siguientes

Empiece a trabajar en la [carga de archivos en la cuenta](media-services-dotnet-upload-files.md).
