---
title: "Flujo de código de autorización - Azure AD B2C | Microsoft Docs"
description: "Aprenda a crear aplicaciones web mediante el protocolo de autenticación de Azure AD B2C y OpenID Connect."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: c371aaab-813a-4317-97df-b62e2f53d865
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: 99a292c6be66016264e528525a5920667207b605
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: flujo de código de autorización de OAuth 2.0
Puede usar la concesión de un código de autorización de OAuth 2.0 en las aplicaciones instaladas en un dispositivo para obtener acceso a recursos protegidos, como las API web. Mediante la implementación de OAuth 2.0 de Azure Active Directory B2C (Azure AD B2C) puede agregar tareas de registro, inicio de sesión y otras tareas de administración de identidades a las aplicaciones móviles y de escritorio. Este artículo es independiente del lenguaje y en él describimos cómo enviar y recibir mensajes HTTP sin usar ninguna biblioteca de código abierto.

<!-- TODO: Need link to libraries -->

El flujo de código de autorización de OAuth 2.0 se describe en la [sección 4.1 de la especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749). Puede usarlo para llevar a cabo la autenticación y la autorización en la mayoría de los tipos de aplicación, incluidas las [aplicaciones web](active-directory-b2c-apps.md#web-apps) y las [aplicaciones instaladas de forma nativa](active-directory-b2c-apps.md#mobile-and-native-apps). Puede usar el flujo de código de autorización de OAuth 2.0 para adquirir *tokens de acceso* de forma segura para las aplicaciones, que se pueden usar para obtener acceso a los recursos protegidos por un [servidor de autorización](active-directory-b2c-reference-protocols.md#the-basics).

Este artículo se centra en el flujo de código de autorización de OAuth 2.0 de **clientes públicos**. Un cliente público es cualquier aplicación cliente que no es de confianza para mantener la integridad de una contraseña secreta de forma segura. Esto incluye las aplicaciones móviles, las aplicaciones de escritorio y prácticamente cualquier aplicación que se ejecute en un dispositivo y necesite obtener tokens de acceso. 

> [!NOTE]
> Para agregar la administración de identidades a una aplicación web mediante Azure AD B2C, use [OpenID Connect](active-directory-b2c-reference-oidc.md) en lugar de OAuth 2.0.

Azure AD B2C extiende los flujos de OAuth 2.0 estándar para realizar algo más que una autorización y autenticación simples. Introduce el [parámetro de directiva](active-directory-b2c-reference-policies.md). Con las directivas integradas puede usar OAuth 2.0 para agregar experiencias de usuario a la aplicación, como el registro, el inicio de sesión y la administración de perfiles. En este artículo le mostramos cómo usar OAuth 2.0 y las directivas para implementar cada una de estas experiencias en sus aplicaciones nativas. También le mostramos cómo obtener tokens de acceso para obtener acceso a las API web.

En las solicitudes HTTP de ejemplo de este artículo se usa nuestro directorio de ejemplo de Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. También se usa nuestra aplicación y nuestras directivas de ejemplo. Puede probar las solicitudes por sí mismo con estos valores, o bien puede reemplazarlos por los suyos propios.
Obtenga información sobre cómo [obtener su propio directorio, aplicación y directivas de Azure AD B2C](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Obtener un código de autorización
El flujo del código de autorización comienza con el cliente dirigiendo al usuario al punto de conexión `/authorize` . Esta es la parte interactiva del flujo, en la que el usuario lleva a cabo la acción. En esta solicitud, el cliente indica en el parámetro `scope` los permisos que debe obtener por parte del usuario. En el parámetro `p` indica la directiva que se debe ejecutar. Los siguientes tres ejemplos (con saltos de línea para facilitar la lectura) usan directivas diferentes.

### <a name="use-a-sign-in-policy"></a>Uso de una directiva de inicio de sesión
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Uso de una directiva de registro
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Uso de una directiva de edición de perfil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| client_id |Obligatorio |Identificador de aplicación asignado a la aplicación en [Azure Portal](https://portal.azure.com). |
| response_type |Obligatorio |El tipo de respuesta, que debe incluir `code` para el flujo de código de autorización. |
| redirect_uri |Obligatorio |El URI de redirección de la aplicación, donde su aplicación envía y recibe las respuestas de autenticación. Debe coincidir exactamente con uno de los URI de redirección que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito |Obligatorio |Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure Active Directory (Azure AD) los dos permisos que se solicitan. El uso del identificador de cliente como ámbito indica que la aplicación necesita un token de acceso que se puede usar con su propio servicio o API web, representado por el mismo identificador de cliente.  El ámbito `offline_access` indica que la aplicación necesita un token de actualización para un acceso de larga duración a los recursos. También puede usar el ámbito `openid` para solicitar un token de identificador desde Azure AD B2C. |
| response_mode |Recomendado |El método que se usa para devolver el código de autorización resultante a la aplicación. Puede ser `query`, `form_post` o `fragment`. |
| state |Recomendado |Un valor incluido en la solicitud que se devolverá en la respuesta del token. Puede ser una cadena de cualquier contenido que quiera usar. Se suele usar un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación. Por ejemplo, la página en la que se encontraba el usuario o la directiva que se está ejecutando. |
| p |Obligatorio |La directiva que se ejecuta. Es el nombre de una directiva que se crea en el directorio de Azure AD B2C. El valor del nombre de directiva debe comenzar por **b2c\_1\_**. Para obtener más información sobre las directivas, consulte [Azure Active Directory B2C: marco de directivas extensible](active-directory-b2c-reference-policies.md). |
| símbolo del sistema |Opcional |El tipo de interacción necesaria con el usuario. Actualmente, el único valor válido es `login`, que obliga al usuario a escribir sus credenciales en esa solicitud. El inicio de sesión único no surtirá efecto. |

En este punto se pedirá al usuario que complete el flujo de trabajo de la directiva. Esto puede implicar que el usuario tenga que escribir su nombre de usuario y contraseña, iniciar sesión con una identidad social, registrarse en el directorio o realizar otros pasos. Las acciones del usuario dependerán de cómo se defina la directiva.

Cuando el usuario haya completado la directiva, Azure AD devolverá una respuesta a la aplicación en el valor que ha usado para `redirect_uri`. Usa el método especificado en el parámetro `response_mode`. La respuesta es exactamente la misma para cada uno de los escenarios de acción del usuario, independientemente de la directiva que se haya ejecutado.

Una respuesta correcta que usa `response_mode=query` tiene este aspecto:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parámetro | Description |
| --- | --- |
| código |El código de autorización que la aplicación solicitó. La aplicación puede usar el código de autorización para solicitar un token de acceso para un recurso de destino. Los códigos de autorización tienen una duración muy breve. Normalmente, caducan al cabo de unos 10 minutos. |
| state |Vea la descripción completa en la tabla de la sección anterior. Si un parámetro `state` está incluido en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores `state` de la solicitud y de la respuesta sean idénticos. |

Las respuestas de error también se pueden enviar al URI de redirección para que la aplicación pueda controlarlas correctamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que se puede usar para clasificar los tipos de errores que se producen. También puede usar la cadena para reaccionar frente a errores. |
| error_description |Un mensaje de error específico que puede ayudarlo a identificar la causa raíz de un error de autenticación. |
| state |Vea la descripción completa en la tabla anterior. Si un parámetro `state` está incluido en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores `state` de la solicitud y de la respuesta sean idénticos. |

## <a name="2-get-a-token"></a>2. Obtención de un token
Ahora que ha adquirido un código de autorización, puede canjear el elemento `code` por un token al recurso deseado mediante el envío de una solicitud POST al punto de conexión `/token`. En Azure AD B2C, el único recurso para el que puede solicitar un token es la API web de back-end de la aplicación. La convención usada para solicitar un token para sí mismo consiste en usar el identificador de cliente de la aplicación como ámbito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| p |Obligatorio |La directiva usada para adquirir el código de autorización. No puede usar una directiva diferente en esta solicitud. Tenga en cuenta que este parámetro se agrega a la *cadena de consulta*, no al cuerpo de POST. |
| client_id |Obligatorio |Identificador de aplicación asignado a la aplicación en [Azure Portal](https://portal.azure.com). |
| grant_type |Obligatorio |Tipo de concesión. Para el flujo de código de autorización, el tipo de concesión debe ser `authorization_code`. |
| ámbito |Recomendado |Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El uso del identificador de cliente como ámbito indica que la aplicación necesita un token de acceso que se puede usar con su propio servicio o API web, representado por el mismo identificador de cliente.  El ámbito `offline_access` indica que la aplicación necesita un token de actualización para un acceso de larga duración a los recursos.  También puede usar el ámbito `openid` para solicitar un token de identificador desde Azure AD B2C. |
| código |Obligatorio |El código de autorización que adquirió en el primer segmento del flujo. |
| redirect_uri |Obligatorio |El URI de redirección de la aplicación en la que recibió el código de autorización. |

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
| token_type |El valor del tipo de token. El único tipo que admite Azure AD es portador. |
| access_token |El JSON Web Token (JWT) firmado que ha solicitado. |
| ámbito |Los ámbitos para los que el token es válido. También puede usar ámbitos para almacenar en caché los tokens para su posterior uso. |
| expires_in |El período de tiempo que el token es válido (en segundos). |
| refresh_token |Un token de actualización de OAuth 2.0. La aplicación puede usar este token para adquirir tokens adicionales una vez que expire el token actual. Los tokens de actualización tienen una duración larga. Puede usarlos para conservar el acceso a los recursos durante largos períodos de tiempo. Para obtener más información, consulte [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md). |

Las respuestas de error tienen un aspecto similar al siguiente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que se puede usar para clasificar los tipos de errores que se producen. También puede usar la cadena para reaccionar frente a errores. |
| error_description |Un mensaje de error específico que puede ayudarlo a identificar la causa raíz de un error de autenticación. |

## <a name="3-use-the-token"></a>3. Uso del token
Ahora que ha adquirido correctamente un token de acceso, puede usar el token en las solicitudes a las API web de back-end incluyéndolo en el encabezado `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Actualización del token
Los tokens de acceso y los tokens de identificación tienen una corta duración. Una vez expirados, debe actualizarlos para poder seguir obteniendo acceso a los recursos. Para ello, envíe otra solicitud POST al punto de conexión `/token`. Esta vez, proporcione el elemento `refresh_token`, en lugar de `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| p |Obligatorio |La directiva usada para adquirir el token de actualización original. No puede usar una directiva diferente en esta solicitud. Tenga en cuenta que este parámetro se agrega a la *cadena de consulta*, no al cuerpo de POST. |
| client_id |Recomendado |Identificador de aplicación asignado a la aplicación en [Azure Portal](https://portal.azure.com). |
| grant_type |Obligatorio |Tipo de concesión. Para este segmento del flujo de código de autorización, el tipo de concesión debe ser `refresh_token`. |
| ámbito |Recomendado |Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El uso del identificador de cliente como ámbito indica que la aplicación necesita un token de acceso que se puede usar con su propio servicio o API web, representado por el mismo identificador de cliente.  El ámbito `offline_access` indica que la aplicación necesitará un token de actualización para un acceso de larga duración a los recursos.  También puede usar el ámbito `openid` para solicitar un token de identificador desde Azure AD B2C. |
| redirect_uri |Opcional |El URI de redirección de la aplicación en la que recibió el código de autorización. |
| refresh_token |Obligatorio |El token de actualización original que adquirió en el segundo segmento del flujo. |

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
| token_type |El valor del tipo de token. El único tipo que admite Azure AD es portador. |
| access_token |El JWT firmado que ha solicitado. |
| ámbito |Los ámbitos para los que el token es válido. También puede usar los ámbitos para almacenar en caché los tokens para su posterior uso. |
| expires_in |El período de tiempo que el token es válido (en segundos). |
| refresh_token |Un token de actualización de OAuth 2.0. La aplicación puede usar este token para adquirir tokens adicionales una vez que expire el token actual. Los tokens de actualización son de larga duración y pueden usarse para conservar el acceso a los recursos durante largos periodos. Para obtener más información, consulte [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md). |

Las respuestas de error tienen un aspecto similar al siguiente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que se puede usar para clasificar los tipos de errores que se producen. También puede usar la cadena para reaccionar frente a errores. |
| error_description |Un mensaje de error específico que puede ayudarlo a identificar la causa raíz de un error de autenticación. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Usar su propio directorio de Azure AD B2C
Para probar estas solicitudes, lleve a cabo los siguientes pasos. Reemplace los valores de ejemplo que hemos usado en este artículo por los suyos propios.

1. [Cree un directorio de Azure AD B2C](active-directory-b2c-get-started.md). Use el nombre del directorio en las solicitudes.
2. [Cree una aplicación](active-directory-b2c-app-registration.md) para obtener un identificador de aplicación y un URI de redirección. Incluya un cliente nativo en la aplicación.
3. [Crear directivas](active-directory-b2c-reference-policies.md) para obtener los nombres de las directivas.

