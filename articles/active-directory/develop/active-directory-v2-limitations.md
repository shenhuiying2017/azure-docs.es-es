---
title: "Limitaciones y restricciones del punto de conexión de Azure Active Directory v2.0 | Microsoft Docs"
description: "Una lista de las limitaciones y restricciones del punto de conexión de Azure AD v2.0."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a81f505c189da31edb91d1b522d9f3140f821cb4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="should-i-use-the-v20-endpoint"></a>¿Debo usar el punto de conexión v2.0?
Cuando compile aplicaciones que se integren con Azure Active Directory, debe decidir si los protocolos de autenticación y el punto de conexión v2.0 cumplen con sus necesidades. El punto de conexión original de Azure Active Directory sigue siendo totalmente compatible y, en algunos aspectos, ofrece más características que el v2.0. Sin embargo, el punto de conexión v2.0 [presenta ventajas importantes](active-directory-v2-compare.md) para los desarrolladores.

Esta es nuestra recomendación simplificada para desarrolladores en este momento:

* Si tiene que admitir cuentas personales de Microsoft en la aplicación, use el punto de conexión v2.0. Pero antes, asegúrese de comprender las limitaciones que se analizan en este artículo.
* Si la aplicación solo tiene que admitir cuentas profesionales y educativas de Microsoft, no use el punto de conexión v2.0. En su lugar, consulte nuestra [Guía del desarrollador de Azure AD](active-directory-developers-guide.md).

Con el tiempo, desarrollaremos el punto de conexión v2.0 para eliminar las restricciones que mencionamos en este artículo, por lo que siempre debe usar este punto de conexión. Mientras tanto, este artículo pretender ayudarle a determinar si el punto de conexión v2.0 es correcto para usted. Actualizaremos este artículo constantemente para reflejar el estado actual del punto de conexión v2.0. Consúltelo de nuevo para volver a evaluar los requisitos en relación con las funcionalidades de v2.0.

Si tiene una aplicación de Azure AD existente que no usa el punto de conexión v2.0, no es necesario empezar desde cero. En el futuro, le proporcionaremos una forma de usar las aplicaciones de Azure AD existentes con el punto de conexión v2.0.

## <a name="restrictions-on-app-types"></a>Restricciones en los tipos de aplicación
Actualmente, el punto de conexión v2.0 no admite los siguientes tipos de aplicaciones. Para una descripción de los tipos de aplicación admitidos, consulte [Tipos de aplicación para el punto de conexión v2.0 de Azure Active Directory](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>API web independiente
Puede usar el punto de conexión v2.0 para [compilar una API web protegida con OAuth 2.0](active-directory-v2-flows.md#web-apis). Sin embargo, esa API web puede recibir tokens solo desde una aplicación con el mismo identificador de aplicación. No se puede obtener una API web desde un cliente con un identificador de aplicación distinto. El cliente no podrá solicitar ni obtener permisos para su API web.

Para ver cómo compilar una API web que acepta tokens de un cliente con el mismo identificador de aplicación, consulte los ejemplos de API web del punto de conexión v2.0 en nuestra sección de [introducción](active-directory-appmodel-v2-overview.md#getting-started).

## <a name="restrictions-on-app-registrations"></a>Restricciones en los registros de aplicaciones
Actualmente, para cada aplicación que desee integrar con el punto de conexión v2.0, debe crear un registro de aplicación en el nuevo [portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Las aplicaciones existentes de Azure AD o de la cuenta de Microsoft no son compatibles con el punto de conexión v2.0. Las aplicaciones registradas en cualquier otro portal que no sea el portal de registro de aplicaciones no son compatibles con el punto de conexión v2.0. En el futuro, planeamos proporcionar una forma de usar una aplicación existente como una aplicación v2.0. Sin embargo, actualmente no hay ninguna ruta de migración para que una aplicación existente funcione con el punto de conexión v2.0.

Además, los registros de aplicaciones que crea en el [portal de registro de aplicaciones](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tienen las siguientes salvedades:

* Solo se permiten dos secretos de aplicación por cada identificador de aplicación.
* Un registro de aplicaciones realizado por un usuario con una cuenta personal de Microsoft solo se puede ver y administrar mediante una sola cuenta de desarrollador. No se puede compartir entre varios desarrolladores.  Si quiere compartir el registro de aplicaciones entre varios desarrolladores, puede crear la aplicación iniciando sesión en el portal de registro con una cuenta de Azure AD.
* Existen varias restricciones para el formato del URI de redireccionamiento permitido. Para más información sobre los URI de redireccionamiento, consulte la sección siguiente.

## <a name="restrictions-on-redirect-uris"></a>Restricciones en los URI de redireccionamiento
Actualmente, las aplicaciones registradas en el portal de registro de aplicaciones están restringidas a un conjunto limitado de valores de parámetro de URI de redireccionamiento. El URI de redireccionamiento de aplicaciones y servicios web debe comenzar por el esquema `https`, y todos los valores de URI de redireccionamiento deben compartir un único dominio DNS. Por ejemplo, no puede registrar una aplicación web con uno de estos URI de redirección:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

El sistema de registro compara el nombre DNS completo del URI de redireccionamiento existente con el nombre DNS del URI de redireccionamiento que va a agregar. La solicitud para agregar el nombre DNS presentará un error si se cumple alguna de las condiciones siguientes:  

* Si el nombre DNS completo del nuevo URI de redireccionamiento no coincide con el nombre DNS del URI de redireccionamiento existente.
* Si el nombre DNS completo del nuevo URI de redireccionamiento no es un subdominio del URI de redireccionamiento existente.

Por ejemplo, si la aplicación tiene el siguiente URI de redireccionamiento:

`https://login.contoso.com`

Puede agregarla del modo siguiente:

`https://login.contoso.com/new`

En este caso, el nombre DNS es una coincidencia exacta. O bien, puede hacer lo siguiente:

`https://new.login.contoso.com`

En este caso, hace referencia a un subdominio DNS de login.contoso.com. Si desea que una aplicación tenga login-east.contoso.com y login-west.contoso.com como URI de redireccionamiento, debe agregar estos URI de redireccionamiento en este orden:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Puede agregar los dos últimos porque son subdominios del primero URI de redireccionamiento, contoso.com. Esta limitación se eliminará en una próxima versión.

Tenga en cuenta también que solo puede tener 20 direcciones URL de respuesta para una aplicación concreta.

Para información sobre cómo registrar una aplicación en el portal de registro de aplicaciones, consulte [Cómo registrar una aplicación con el punto de conexión v2.0](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services-and-apis"></a>Restricciones en los servicios y API
Actualmente, el punto de conexión v2.0 admite el inicio de sesión en cualquier aplicación registrada en el portal de registro de aplicaciones que se encuentre en la lista de [flujos de autenticación admitidos](active-directory-v2-flows.md). Sin embargo, estas aplicaciones pueden adquirir tokens de acceso OAuth 2.0 para un conjunto de recursos muy limitado. El punto de conexión v2.0 emite tokens de acceso solo para:

* La aplicación que solicita el token. Una aplicación puede adquirir un token de acceso por sí misma si la aplicación lógica se compone de varios componentes o niveles diferentes. Para ver este escenario en acción, consulte nuestros tutoriales de [Introducción](active-directory-appmodel-v2-overview.md#getting-started) .
* Las API de REST de Correo, Calendario y Contactos de Outlook, que se encuentran en https://outlook.office.com. Para información sobre cómo escribir una aplicación que tenga acceso a estas API, consulte los tutoriales de [Introducción a Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
* Las API de Microsoft Graph. Puede obtener más información sobre [Microsoft Graph](https://graph.microsoft.io) y los datos que tiene a su disposición.

No hay otros servicios compatibles en este momento. En el futuro se agregarán más Microsoft Online Services, además de la compatibilidad con sus propios servicios y API web personalizados.

## <a name="restrictions-on-libraries-and-sdks"></a>Restricciones en las bibliotecas y SDK
En este momento, la compatibilidad del punto de conexión v2.0 con las bibliotecas es limitada. Si desea usar el punto de conexión v2.0 en una aplicación de producción, tiene las opciones siguientes:

* Si compila una aplicación web, puede usar sin riesgo el software intermedio de lado servidor con carácter de disponibilidad general de Microsoft para realizar el inicio de sesión y la validación de tokens. Incluye el software intermedio OWIN Open ID Connect para ASP.NET y el complemento NodeJS Passport. Para ejemplos de código que usan el software intermedio de Microsoft, consulte nuestra sección de [introducción](active-directory-appmodel-v2-overview.md#getting-started).
* Si crea una aplicación de escritorio o para dispositivos móviles, puede usar una de las bibliotecas de autenticación de Microsoft (MSAL) de versión preliminar.  Estas bibliotecas están en una versión de versión preliminar compatible con producción, por lo que su uso en aplicaciones de producción es seguro. Puede obtener más información sobre los términos y condiciones de la versión preliminar y las bibliotecas disponibles en nuestra [referencia de bibliotecas de autenticación](active-directory-v2-libraries.md).
* En el caso de otras plataformas no cubiertas por las bibliotecas de Microsoft, pueden integrarse con el punto de conexión v2.0 enviando y recibiendo mensajes de protocolo directamente en el código de su aplicación. Los protocolos v2.0 OpenID Connect y OAuth [se documentan explícitamente](active-directory-v2-protocols.md) para ayudarle a realizar dicha integración.
* Por último, puede usar las bibliotecas de código abierto de Open ID Connect y OAuth para integrarse con el punto de conexión v2.0. El protocolo v2.0 debe ser compatible con muchas bibliotecas de código abierto de los protocolos sin cambios importantes. La disponibilidad de estos tipos de bibliotecas varía según el lenguaje y la plataforma. Los sitios web [Open ID Connect](http://openid.net/connect/) y [OAuth 2.0](http://oauth.net/2/) mantienen una lista de las implementaciones populares. Para más información, consulte [Azure Active Directory v2.0 y bibliotecas de autenticación](active-directory-v2-libraries.md) y la lista de bibliotecas de cliente de código abierto y los ejemplos que se probaron con el punto de conexión v2.0.

## <a name="restrictions-on-protocols"></a>Restricciones en los protocolos
El punto de conexión v2.0 no admite SAML ni WS-Federation; solo admite Open ID Connect y OAuth 2.0.  No todas las características y capacidades los protocolos OAuth se han incorporado al punto de conexión v2.0. Estas funcionalidades y características de protocolo actualmente *no están disponibles* en el punto de conexión v2.0:

* Los tokens de identificador que emite el punto de conexión v2.0 no contienen una notificación `email` para el usuario, aunque adquiera el permiso del usuario para ver su correo electrónico.
* El punto de conexión de información de usuario de OpenID Connect no está implementado en el punto de conexión v2.0. Sin embargo, todos los datos de perfil de usuario que recibiría posiblemente en este punto de conexión están disponibles desde el punto de conexión `/me` de Microsoft Graph.
* El punto de conexión v2.0 no admite la emisión de notificaciones de roles o grupos en los tokens de identificador.
* La [concesión de credenciales de contraseña de propietario del recurso OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3) no es compatible con el punto de conexión v2.0.

Además, el punto de conexión v2.0 no admite ninguna forma de los protocolos SAML o WS-Federation.

Para comprender mejor el alcance de la funcionalidad de protocolo que se admite en el punto de conexión v2.0, consulte nuestra [referencia a los protocolos OpenID Connect y OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restricciones de las cuentas profesionales y educativas
Si ha usado la biblioteca de autenticación de Active Directory (ADAL) en aplicaciones de Windows, es posible que haya aprovechado la autenticación integrada de Windows, que usa la concesión de aserción de Lenguaje de marcado de aserción de seguridad (SAML). Con esta concesión, los usuarios de los inquilinos de Azure AD federado pueden autenticarse de manera silenciosa con su instancia local de Active Directory sin escribir las credenciales. Actualmente, el punto de conexión v2.0 no admite la concesión de aserción de SAML.
