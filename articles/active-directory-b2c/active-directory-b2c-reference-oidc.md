---
title: "Inicio de sesión web con OpenID Connect - Azure AD B2C | Microsoft Docs"
description: "Creación de aplicaciones web mediante la implementación del protocolo de autenticación OpenID Connect de Azure Active Directory"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 21d420c8-3c10-4319-b681-adf2e89e7ede
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: 0eb4194307d1d3953fa1cd88ac014ac7c2ba7311
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: inicio de sesión web con OpenID Connect
OpenID Connect es un protocolo de autenticación basado en OAuth 2.0 que se puede usar para que los usuarios inicien sesión de forma segura en las aplicaciones web. Con la implementación de OpenID Connect de Azure Active Directory B2C (Azure AD B2C) puede externalizar el registro, el inicio de sesión y otras experiencias de administración de identidades en sus aplicaciones web a Azure Active Directory (Azure AD). Esta guía le enseñará cómo hacerlo de manera independiente del lenguaje. En ella se describe cómo enviar y recibir mensajes HTTP sin utilizar ninguna de nuestras bibliotecas de código abierto.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) amplía el protocolo de *autorización* de OAuth 2.0 para su uso como protocolo de *autenticación*. Esto le permite realizar el inicio de sesión único mediante OAuth. Presenta el concepto de un *token de identificador*, que es un token de seguridad que permite al cliente comprobar la identidad del usuario y obtener información del perfil básica sobre el usuario.

Al ampliar OAuth 2.0, también permite que las aplicaciones adquieran *tokens de acceso* de forma segura. access_tokens se puede usar para acceder a los recursos protegidos por un [servidor de autorización](active-directory-b2c-reference-protocols.md#the-basics). Nosotros recomendamos OpenID Connect si va a crear una aplicación web que esté hospedada en un servidor y a la que se accede mediante un explorador. Si desea agregar administración de identidades a sus aplicaciones móviles o de escritorio con Azure AD B2C, debe usar [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) , en lugar de OpenID Connect.

Azure AD B2C extiende el protocolo OpenID Connect estándar para realizar algo más que una autorización y autenticación simples. Presenta el [parámetro de directiva](active-directory-b2c-reference-policies.md), que le permite usar OpenID Connect para agregar experiencias de usuario a su aplicación (como el registro, el inicio de sesión y la administración de perfiles). Aquí le mostraremos cómo usar OpenID Connect y directivas para implementar cada una de estas experiencias en sus aplicaciones web. También le mostraremos cómo obtener tokens de acceso para obtener acceso a las API web.

Las solicitudes HTTP de ejemplo de la siguiente sección usan nuestro directorio de ejemplo de B2C, fabrikamb2c.onmicrosoft.com, así como nuestras directivas y nuestra aplicación de ejemplo, https://aadb2cplayground.azurewebsites.net. Puede probar las solicitudes por sí mismo con estos valores, o bien puede reemplazarlos por los suyos propios.
Aprenda a [obtener su propio inquilino, aplicación y directivas B2C](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Envío de solicitudes de autenticación
Cuando su aplicación web necesita autenticar al usuario y ejecutar la directiva, puede dirigir al usuario al extremo `/authorize` . Esta es la parte interactiva del flujo, donde el usuario lleva a cabo la acción, dependiendo de la directiva.

En esta solicitud, el cliente indica los permisos que necesita adquirir del usuario en el parámetro `scope` y la directiva que se va a ejecutar en el parámetro `p`. En las siguientes secciones se proporcionan tres ejemplos (con saltos de línea para facilitar la lectura), cada uno con una directiva diferente. Para hacerse una idea de cómo funciona cada solicitud, intente pegar la solicitud en un explorador y volver a ejecutarlo.

#### <a name="use-a-sign-in-policy"></a>Uso de una directiva de inicio de sesión
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Uso de una directiva de registro
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Uso de una directiva de edición de perfil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| client_id |Obligatorio |El identificador de aplicación que el [Portal de Azure](https://portal.azure.com/) asignó a la aplicación. |
| response_type |Obligatorio |El tipo de respuesta, que debe incluir un token de identificador para OpenID Connect. Si su aplicación web también necesita tokens para llamar a una API web, puede usar `code+id_token`, como hemos hecho aquí. |
| redirect_uri |Recomendado |El parámetro `redirect_uri` de su aplicación, donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los parámetros `redirect_uri` que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito |Obligatorio |Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión el usuario y obtener los datos del usuario en forma de tokens de identificador (más adelante encontrará más información al respecto). El ámbito `offline_access` es opcional para las aplicaciones web. Indica que la aplicación necesitará un *token de actualización* para tener un acceso de larga duración a los recursos. |
| response_mode |Recomendado |El método que debe usarse para devolver el código de autorización resultante a la aplicación. Puede ser `query`, `form_post` o `fragment`.  Para mayor seguridad se recomienda el modo de respuesta `form_post`. |
| state |Recomendado |Un valor incluido en la solicitud que también se devolverá en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria  para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página en la que estaban. |
| valor de seguridad |Obligatorio |Un valor incluido en la solicitud (generado por la aplicación) que se incluirá como notificación en el token de identificador resultante. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que se puede usar para identificar el origen de la solicitud. |
| p |Obligatorio |La directiva que se va a ejecutar. Es el nombre de una directiva que se crea en el inquilino B2C. El valor del nombre de directiva debe comenzar por `b2c\_1\_`. Obtenga más información sobre las directivas y el [marco de directiva extensible](active-directory-b2c-reference-policies.md). |
| símbolo del sistema |Opcional |El tipo de interacción necesaria con el usuario. El único valor válido en este momento es `login`, que obliga al usuario a escribir sus credenciales en esa solicitud. El inicio de sesión único no surtirá efecto. |

En este punto se pedirá al usuario que complete el flujo de trabajo de la directiva. Esto puede implicar que el usuario tenga que escribir su nombre de usuario y contraseña, iniciar sesión con una identidad social, registrarse en el directorio o llevar a cabo otros pasos, en función de cómo esté definida la directiva.

Una vez que el usuario haya completado la directiva, Azure AD devolverá una respuesta a su aplicación en el parámetro `redirect_uri` indicado usando el método que se especifica en el parámetro `response_mode`. La respuesta es la misma para cada uno de los casos anteriores, independientemente de la directiva que se ejecute.

Una respuesta correcta al usar `response_mode=fragment` tiene el siguiente aspecto:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Description |
| --- | --- |
| ID_token |El token de identificador que la aplicación solicitó. Puede usar el token de identificador para comprobar la identidad del usuario y comenzar una sesión con el usuario. En [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md) se incluye más información sobre los tokens de identificador y su contenido. |
| código |El código de autorización que solicitó la aplicación si usó `response_type=code+id_token`. La aplicación puede usar el código de autorización para solicitar un token de acceso para un recurso de destino. Los códigos de autorización tienen una duración muy breve. Normalmente, caducan al cabo de unos 10 minutos. |
| state |Si un parámetro `state` está incluido en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores `state` de la solicitud y de la respuesta sean idénticos. |

Las respuestas de error también se pueden enviar al parámetro `redirect_uri` para que la aplicación pueda controlarlas de manera adecuada:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que se puede usar para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error_description |Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |
| state |Vea la descripción completa en la primera tabla de esta sección. Si un parámetro `state` está incluido en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores `state` de la solicitud y de la respuesta sean idénticos. |

## <a name="validate-the-id-token"></a>Validar el token de identificador
Recibir un token de identificador no es suficiente para autenticar al usuario. Debe validar la firma del token de identificador y comprobar las notificaciones en el token en función de los requisitos de la aplicación. Azure AD B2C usa [tokens web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y la criptografía de clave pública para firmar los tokens y comprobar que son válidos.

Hay muchas bibliotecas de código abierto disponibles para validar los JWT, según el lenguaje de preferencia. Se recomienda explorar esas opciones en lugar de implementar su propia lógica de validación. Esta información será útil para averiguar cómo usar adecuadamente dichas bibliotecas.

Azure AD B2C tiene un extremo de metadatos OpenID Connect, que permite a una aplicación obtener información sobre Azure AD B2C en tiempo de ejecución. En esta información se incluyen los extremos, los contenidos del token y las claves de firma de los token. Hay un documento de metadatos JSON para cada directiva en su inquilino de B2C. Por ejemplo, el documento de metadatos de la directiva `b2c_1_sign_in` en `fabrikamb2c.onmicrosoft.com` se encuentra en:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una de las propiedades de este documento de configuración es `jwks_uri`, cuyo valor para la misma directiva sería:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Tiene dos opciones para determinar qué directiva se usó en la firma de un token de identificador (y de dónde se deben obtener los metadatos). En primer lugar, el nombre de la directiva se incluye en la notificación `acr` del token de identificador. Para obtener información sobre cómo analizar las notificaciones de un token de identificador, consulte [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md). La otra opción consiste en codificar la directiva en el valor del parámetro `state` al emitir la solicitud y descodificarla para determinar qué directiva se ha usado. Cualquiera de estos métodos es válido.

Cuando haya adquirido el documento de metadatos del punto de conexión de metadatos de OpenID Connect, podrá usar las claves públicas RSA de 256 bits (que están ubicadas en este punto de conexión) para validar la firma del token de identificador. Podría haber varias claves enumeradas en este punto de conexión en cualquier momento, cada una identificada con una notificación `kid`. El encabezado del token de identificador también contiene una notificación `kid`, que indica cuál de estas claves se usó para firmar el token de identificador. Para obtener más información, consulte la [referencia de tokens de Azure AD B2C](active-directory-b2c-reference-tokens.md) (en concreto, la sección sobre cómo [validar los tokens](active-directory-b2c-reference-tokens.md#token-validation)).
<!--TODO: Improve the information on this-->

Cuando haya validado la firma del token de identificador, deberá verificar varias notificaciones. Por ejemplo:

* Debería validar la notificación `nonce` para evitar ataques de reproducción del token. Su valor debería ser el que especificó en la solicitud de inicio de sesión.
* Debería validar la notificación `aud` para asegurarse de que se ha generado el token de identificador para su aplicación. Su valor debería ser el identificador de la aplicación.
* Debería validar las notificaciones `iat` y `exp` para asegurarse de que el token de identificador no haya expirado.

También hay otras validaciones que deberá llevar a cabo. Estas se describen en detalle en la [especificación de OpenID Connect Core](http://openid.net/specs/openid-connect-core-1_0.html).  Podría también querer validar notificaciones adicionales, en función de su escenario. Algunas validaciones comunes incluyen:

* Asegurarse de que la organización o el usuario se han registrado en la aplicación.
* Asegurarse de que el usuario tiene la autorización o los privilegios adecuados.
* Asegurarse de que se haya producido un determinado nivel de autenticación, como Azure Multi-Factor Authentication.

Para obtener más información sobre las notificaciones en un token de identificador, consulte [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md).

Cuando haya validado el token de identificador, podrá iniciar una sesión con el usuario. Puede usar las notificaciones del token de identificador para obtener información sobre el usuario en la aplicación. Entre los usos de esta información se incluye la visualización, los registros y la autorización.

## <a name="get-a-token"></a>Obtención de un token
Si necesita que la aplicación web solo ejecute directivas, puede omitir las siguientes secciones. Estas secciones son aplicables solo a las aplicaciones web que necesitan efectuar llamadas autenticadas a una API web y que también están protegidas por Azure AD B2C.

Puede canjear el código de autorización que adquirió (mediante `response_type=code+id_token`) por un token para el recurso deseado; para ello, debe enviar una solicitud `POST` al punto de conexión `/token`. Actualmente, el único recurso para el que puede solicitar un token es la API web de back-end de la aplicación. La convención para solicitar un token para sí mismo es usar el identificador de cliente de la aplicación como ámbito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| p |Obligatorio |La directiva usada para adquirir el código de autorización. No puede usar una directiva diferente en esta solicitud. Tenga en cuenta que este parámetro se agrega a la cadena de consulta, no al cuerpo de `POST`. |
| client_id |Obligatorio |El identificador de aplicación que el [Portal de Azure](https://portal.azure.com/) asignó a la aplicación. |
| grant_type |Obligatorio |El tipo de concesión, que debe ser `authorization_code` para el flujo de código de autorización. |
| ámbito |Recomendado |Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión con el usuario y obtener datos de este en forma de parámetros id_token. Se puede usar para obtener tokens para la propia API web de back-end de la aplicación, representada por el mismo id. de aplicación que el cliente. El ámbito `offline_access` indica que la aplicación necesitará un token de actualización para un acceso de larga duración a los recursos. |
| código |Obligatorio |El código de autorización que adquirió en el primer segmento del flujo. |
| redirect_uri |Obligatorio |El parámetro `redirect_uri` de la aplicación en la que recibió el código de autorización. |
| client_secret |Obligatorio |El secreto de la aplicación que generó en el [Portal de Azure](https://portal.azure.com/). El secreto de aplicación es un artefacto de seguridad importante. Debe almacenarlo de forma segura en el servidor. También debe rotar este secreto del cliente de forma periódica. |

Una respuesta correcta del token tiene el siguiente aspecto:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parámetro | Description |
| --- | --- |
| not_before |Hora a la que el token se considera válido, en tiempo de época. |
| token_type |El valor del tipo de token. El único tipo que admite Azure AD es `Bearer`. |
| access_token |El token JWT firmado solicitado. |
| ámbito |Los ámbitos para los que el token es válido. Se pueden usar para almacenar en caché los tokens para su posterior uso. |
| expires_in |El período de validez del token de acceso (en segundos). |
| refresh_token |Un token de actualización de OAuth 2.0. La aplicación puede usar este token para adquirir tokens adicionales una vez que expire el token actual. Los tokens de actualización son de larga duración y pueden usarse para conservar el acceso a los recursos durante largos períodos. Para obtener más información, consulte la [referencia de tokens de B2C](active-directory-b2c-reference-tokens.md). Tenga en cuenta que debe haber usado el ámbito `offline_access` en las solicitudes de token y de autorización para recibir un token de actualización. |

Las respuestas de error tienen un aspecto similar al siguiente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que se puede usar para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error_description |Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

## <a name="use-the-token"></a>Uso del token
Ahora que ha adquirido correctamente un token de acceso, puede usar el token en las solicitudes a las API web de back-end incluyéndolo en el encabezado `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Actualización del token
Los tokens de identificador tienen una corta duración. Debe actualizarlos después de que caducan para que puedan seguir obteniendo acceso a los recursos. Para ello, envíe otra solicitud `POST` al punto de conexión `/token`. Esta vez, proporcione el parámetro `refresh_token` en lugar del parámetro `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parámetro | Obligatorio | Description |
| --- | --- | --- |
| p |Obligatorio |La directiva usada para adquirir el token de actualización original. No puede usar una directiva diferente en esta solicitud. Tenga en cuenta que este parámetro se agrega a la cadena de consulta, no al cuerpo de POST. |
| client_id |Obligatorio |El identificador de aplicación que el [Portal de Azure](https://portal.azure.com/) asignó a la aplicación. |
| grant_type |Obligatorio |El tipo de concesión, que debe ser un token de actualización para esta sección del flujo de código de autorización. |
| ámbito |Recomendado |Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión con el usuario y obtener datos de este en forma de tokens de identificador. Se puede usar para obtener tokens para la propia API web de back-end de la aplicación, representada por el mismo id. de aplicación que el cliente. El ámbito `offline_access` indica que la aplicación necesitará un token de actualización para un acceso de larga duración a los recursos. |
| redirect_uri |Recomendado |El parámetro `redirect_uri` de la aplicación en la que recibió el código de autorización. |
| refresh_token |Obligatorio |El token de actualización original que adquirió en el segundo segmento del flujo. Tenga en cuenta que debe haber usado el ámbito `offline_access` en las solicitudes de token y de autorización para recibir un token de actualización. |
| client_secret |Obligatorio |El secreto de la aplicación que generó en el [Portal de Azure](https://portal.azure.com/). El secreto de aplicación es un artefacto de seguridad importante. Debe almacenarlo de forma segura en el servidor. También debe rotar este secreto del cliente de forma periódica. |

Una respuesta correcta del token tiene el siguiente aspecto:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parámetro | Description |
| --- | --- |
| not_before |Hora a la que el token se considera válido, en tiempo de época. |
| token_type |El valor del tipo de token. El único tipo que admite Azure AD es `Bearer`. |
| access_token |El token JWT firmado solicitado. |
| ámbito |El ámbito para el que el token es válido, que se puede usar para almacenar en caché los tokens para su uso posterior. |
| expires_in |El período de validez del token de acceso (en segundos). |
| refresh_token |Un token de actualización de OAuth 2.0. La aplicación puede usar este token para adquirir tokens adicionales una vez que expire el token actual.  Los tokens de actualización son de larga duración y pueden usarse para conservar el acceso a los recursos durante largos períodos. Para obtener más información, consulte la [referencia del token B2C](active-directory-b2c-reference-tokens.md). |

Las respuestas de error tienen un aspecto similar al siguiente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que se puede usar para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error_description |Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

## <a name="send-a-sign-out-request"></a>Envío de una solicitud de cierre de sesión
Si desea cerrar la sesión del usuario de la aplicación, no basta con borrar las cookies de su aplicación o finalizar la sesión con el usuario. También debe redirigir al usuario a Azure AD para que cierre la sesión. Si no lo hace, el usuario podría autenticarse de nuevo en su aplicación sin volver a escribir sus credenciales. Esto se debe a que el usuario tendrá una sesión válida de inicio de sesión único con Azure AD.

Lo que puede hacer es redirigir al usuario al punto de conexión `end_session` que aparece en el documento de metadatos de OpenID Connect que se ha descrito anteriormente en la sección "Validar el token de identificador":

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| p |Obligatorio |La directiva que quiere usar para cerrar la sesión del usuario de la aplicación. |
| post_logout_redirect_uri |Recomendado |La dirección URL a la que se debe redirigir al usuario después de un cierre de sesión correcto. Si no se incluye, Azure AD B2C mostrará un mensaje genérico al usuario. |

> [!NOTE]
> Aunque el hecho de dirigir al usuario al punto de conexión `end_session` borrará algún estado de inicio de sesión único del usuario con Azure AD B2C, no se cerrará la sesión del proveedor de identidades social (IDP) del usuario. Si el usuario selecciona el mismo IDP durante un inicio de sesión posterior, se volverá a autenticar sin especificar sus credenciales. Si un usuario quiere cerrar sesión en su aplicación B2C, eso no significa necesariamente que quiera cerrar sesión en su cuenta de Facebook. Sin embargo, en el caso de las cuentas locales, la sesión del usuario se terminará correctamente.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Uso de un inquilino de B2C propio
Si quiere probar estas solicitudes por sí mismo, primero debe llevar a cabo estos tres pasos y, luego, reemplazar los valores de ejemplo descritos anteriormente por los suyos propios:

1. [Crear un inquilino de B2C](active-directory-b2c-get-started.md)y utilizar el nombre del inquilino en las solicitudes.
2. [Cree una aplicación](active-directory-b2c-app-registration.md) para obtener un identificador de aplicación. Incluya una aplicación web o una API web en la aplicación. Opcionalmente puede crear un secreto de aplicación.
3. [Crear directivas](active-directory-b2c-reference-policies.md) para obtener los nombres de las directivas.

