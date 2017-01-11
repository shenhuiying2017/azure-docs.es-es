---
title: "Limitaciones y restricciones del punto de conexión de Azure Active Directory v2.0 | Microsoft Docs"
description: "Una lista de las limitaciones y restricciones del punto de conexión de Azure AD v2.0."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8fabc37ad8c7a99a09083422f740e5352e403438


---
# <a name="should-i-use-the-v20-endpoint"></a>¿Debo usar el punto de conexión v2.0?
Cuando compila aplicaciones que se integran con Azure Active Directory (Azure AD), debe decidir si los protocolos de autenticación y el punto de conexión v2.0 cumplen con sus necesidades. El punto de conexión original de Azure AD sigue siendo compatible y, en algunos aspectos, tiene más características que la versión 2.0. Sin embargo, el punto de conexión v2.0 [presenta ventajas importantes](active-directory-v2-compare.md) para los desarrolladores. Las ventajas de la versión 2.0 podrían incitarlo a usar el nuevo modelo de programación.

Esta es nuestra recomendación para el uso del punto de conexión v2.0 ahora:

* Si desea que se admitan cuentas de Microsoft en la aplicación, use el punto de conexión v2.0. Antes de que lo haga, asegúrese de comprender las limitaciones que se analizan en este artículo, especialmente las que se aplican a las cuentas profesionales y educativas.
* Si la aplicación solo debe admitir cuentas profesionales y educativas, use los [puntos de conexión originales de Azure AD](active-directory-developers-guide.md).

Con el tiempo, desarrollaremos el punto de conexión v2.0 para eliminar las restricciones que mencionamos en este artículo, por lo que siempre debe usar este punto de conexión. Mientras tanto, este artículo pretender ayudarle a determinar si el punto de conexión v2.0 es correcto para usted. Actualizaremos este artículo constantemente para reflejar el estado actual del punto de conexión v2.0. Consúltelo de nuevo para volver a evaluar los requisitos en relación con las funcionalidades de v2.0.

Si tiene una aplicación de Azure AD existente que no usa el punto de conexión v2.0, no es necesario empezar desde cero. En el futuro, le proporcionaremos una forma de usar las aplicaciones de Azure AD existentes con el punto de conexión v2.0.

## <a name="restrictions-on-app-types"></a>Restricciones en los tipos de aplicación
Actualmente, el punto de conexión v2.0 no admite los siguientes tipos de aplicaciones. Para una descripción de los tipos de aplicación admitidos, consulte [Tipos de aplicación para el punto de conexión v2.0 de Azure Active Directory](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>API web independiente
Puede usar el punto de conexión v2.0 para [compilar una API web protegida con OAuth 2.0](active-directory-v2-flows.md#web-apis). Sin embargo, esa API web puede recibir tokens solo desde una aplicación con el mismo identificador de aplicación. No se puede obtener una API web desde un cliente con un identificador de aplicación distinto. El cliente no podrá solicitar ni obtener permisos para su API web.

Para ver cómo compilar una API web que acepta tokens de un cliente con el mismo identificador de aplicación, consulte los ejemplos de API web del punto de conexión v2.0 en nuestra sección de [introducción](active-directory-appmodel-v2-overview.md#getting-started).

### <a name="web-api-on-behalf-of-flow"></a>Flujo en nombre de la API web
Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante el punto de conexión v2.0. Este escenario es común en los clientes nativos que tienen un back-end de API web que, a su vez, llama a una instancia de Microsoft Online Services u otra API web creada a medida que admite Azure AD.

Puede crear este escenario mediante la concesión de credenciales del portador de JSON Web Token (JWT) de OAuth 2.0, también conocido como flujo "en nombre de". Sin embargo, el flujo "en nombre de" no se admite actualmente para el punto de conexión v2.0. Para ver cómo funciona este flujo en el servicio Azure AD, disponible con carácter general, consulte el [ejemplo de código "en nombre de" en GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restricciones en los registros de aplicaciones
Actualmente, para cada aplicación que desee integrar con el punto de conexión v2.0, debe crear un registro de aplicación en el nuevo [portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Las aplicaciones existentes de Azure AD o de la cuenta de Microsoft no son compatibles con el punto de conexión v2.0. Las aplicaciones registradas en cualquier otro portal que no sea el portal de registro de aplicaciones no son compatibles con el punto de conexión v2.0. En el futuro, planeamos proporcionar una forma de usar una aplicación existente como una aplicación v2.0. Sin embargo, actualmente no hay ninguna ruta de migración para que una aplicación existente funcione con el punto de conexión v2.0.

Las aplicaciones registradas en el portal de registro de aplicaciones no funcionarán con el punto de conexión de autenticación original de Azure AD. Sin embargo, puede usar las aplicaciones que crea en el portal de registro de aplicaciones para una integración correcta con el punto de conexión de autenticación de cuentas de Microsoft `https://login.live.com`.

Además, los registros de aplicaciones que crea en el [portal de registro de aplicaciones](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tienen las siguientes salvedades:

* No se admite la propiedad **homepage**, que también se conoce como *dirección URL de inicio de sesión*. Sin una página principal, estas aplicaciones no aparecerán en el panel de Office MyApps.
* En este momento, solo se permiten dos secretos de aplicación por cada identificador de aplicación.
* Un registro de aplicaciones solo se puede ver y administrar mediante una cuenta de desarrollador única. No se puede compartir entre varios desarrolladores.
* Existen varias restricciones para el formato del URI de redireccionamiento permitido. Para más información sobre los URI de redireccionamiento, consulte la sección siguiente.

## <a name="restrictions-on-redirect-uris"></a>Restricciones en los URI de redireccionamiento
Actualmente, las aplicaciones registradas en el portal de registro de aplicaciones están restringidas a un conjunto limitado de valores de parámetro de URI de redireccionamiento. El URI de redireccionamiento de aplicaciones y servicios web debe comenzar por el esquema `https`, y todos los valores de URI de redireccionamiento deben compartir un único dominio DNS. Por ejemplo, no puede registrar una aplicación web con uno de estos URI de redireccionamiento:

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
* Para otras plataformas y aplicaciones nativas y móviles, también puede integrarse con el punto de conexión v2.0 directamente enviando y recibiendo mensajes de protocolo en el código de su aplicación. Los protocolos v2.0 OpenID Connect y OAuth [se documentan explícitamente](active-directory-v2-protocols.md) para ayudarle a realizar dicha integración.
* Por último, puede usar las bibliotecas de código abierto de Open ID Connect y OAuth para integrarse con el punto de conexión v2.0. El protocolo v2.0 debe ser compatible con muchas bibliotecas de código abierto de los protocolos sin cambios importantes. La disponibilidad de estos tipos de bibliotecas varía según el lenguaje y la plataforma. Los sitios web [Open ID Connect](http://openid.net/connect/) y [OAuth 2.0](http://oauth.net/2/) mantienen una lista de las implementaciones populares. Para más información, consulte [Azure Active Directory v2.0 y bibliotecas de autenticación](active-directory-v2-libraries.md) y la lista de bibliotecas de cliente de código abierto y los ejemplos que se probaron con el punto de conexión v2.0.

También publicamos una versión preliminar inicial de [Biblioteca de autenticación de Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) solo para .NET. Si lo desea, puede probar esta biblioteca en aplicaciones .NET de cliente y servidor, pero como se trata de una biblioteca en versión preliminar, no dispone de compatibilidad con calidad de disponibilidad general (GA).

## <a name="restrictions-on-protocols"></a>Restricciones en los protocolos
El punto de conexión v2.0 solo es compatible con OpenID Connect y OAuth 2.0. Sin embargo, no se han incorporado todas las características y capacidades de cada protocolo en el punto de conexión v2.0.

Estas funcionalidades y características de protocolo habituales actualmente *no son compatibles* en el punto de conexión v2.0:

* El parámetro `end_session_endpoint` de OpenID Connect, que permite que una aplicación finalice la sesión del usuario, no está disponible con el punto de conexión v2.0.
* Los tokens de identificador que emite el punto de conexión v2.0 solo tienen un identificador con pares del usuario. Es decir, dos aplicaciones distintas recibirán identificadores diferentes para el mismo usuario. Tenga en cuenta que, al consultar el punto de conexión `/me` de Microsoft Graph, puede obtener un identificador correlacionable del usuario que puede usarse en distintas aplicaciones.
* Los tokens de identificador que emite el punto de conexión v2.0 no contienen una notificación `email` para el usuario, aunque adquiera el permiso del usuario para ver su correo electrónico.
* El punto de conexión de información de usuario de OpenID Connect no está implementado en el punto de conexión v2.0. Sin embargo, todos los datos de perfil de usuario que recibiría posiblemente en este punto de conexión están disponibles desde el punto de conexión `/me` de Microsoft Graph.
* El punto de conexión v2.0 no admite la emisión de notificaciones de roles o grupos en los tokens de identificador.

Para comprender mejor el alcance de la funcionalidad de protocolo que se admite en el punto de conexión v2.0, consulte nuestra [referencia a los protocolos OpenID Connect y OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restricciones de las cuentas profesionales y educativas
El punto de conexión v2.0 todavía no admite algunas características específicas de los usuarios empresariales de Microsoft. Para más información, consulte las secciones siguientes.

### <a name="device-based-conditional-access-native-and-mobile-apps-and-microsoft-graph"></a>Acceso condicional basado en dispositivos, aplicaciones nativas y móviles, y Microsoft Graph
El punto de conexión v2.0 no admite aún la autenticación de dispositivos para aplicaciones móviles y nativas, como las que se ejecutan en iOS o Android, En el caso de algunas organizaciones, esto podría impedir que la aplicación nativa llame a Microsoft Graph. La autenticación de dispositivos se requiere cuando un administrador establece una directiva de acceso condicional basado en dispositivos en una aplicación. Para el punto de conexión v2.0, el escenario más probable para el acceso condicional basado en dispositivos es si un administrador estableciera una directiva en un recurso en Microsoft Graph, como la API de Outlook. Si un administrador establece esta directiva y la aplicación nativa solicita un token a Microsoft Graph, se producirá un error en la solicitud debido a que todavía no se admite la autenticación de dispositivos. Sin embargo, las aplicaciones web que solicitan tokens a Microsoft Graph se admiten cuando se configuran directivas basadas en dispositivos. En el escenario de las aplicaciones web, la autenticación de dispositivos se realiza a través del explorador web del usuario.

Como desarrollador, probablemente no pueda controlar cuándo se establecen las directivas en los recursos de Microsoft Graph. Es posible que ni siquiera se entere cuando suceda. Si está creando una aplicación para usuarios profesionales o educativos, debe usar [el punto de conexión de Azure AD original](active-directory-developers-guide.md) hasta que el punto de conexión v2.0 admita la autenticación de dispositivo. Puede obtener más información sobre el [acceso condicional basado en dispositivos en Azure AD](active-directory-conditional-access.md#device-based-conditional-access).

### <a name="windows-integrated-authentication-for-federated-tenants"></a>Autenticación integrada de Windows para inquilinos federados
Si usó la biblioteca de autenticación de Active Directory (ADAL) (con el punto de conexión original de Azure AD) en las aplicaciones de Windows, es posible que haya usado lo que se conoce como concesión de aserción de Lenguaje de marcado de aserción de seguridad (SAML). Con esta concesión, los usuarios de los inquilinos de Azure AD federado pueden autenticarse de manera silenciosa con su instancia local de Active Directory sin escribir las credenciales. Actualmente, el punto de conexión v2.0 no admite la concesión de aserción de SAML.




<!--HONumber=Nov16_HO3-->


