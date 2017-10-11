---
title: Azure Active Directory v2.0 y el protocolo OpenID Connect | Microsoft Docs
description: "Cree aplicaciones web mediante la implementación de Azure AD v2.0 del protocolo de autenticación OpenID Connect."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: d63692f02b3dec50a1e7df034b8915bb450b4cfd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# Azure Active Directory v2.0 y el protocolo OpenID Connect
OpenID Connect es un protocolo de autenticación basado en OAuth 2.0 que se puede usar para que un usuario inicie sesión de forma segura en una aplicación web. Cuando usa la implementación de OpenID Connect del punto de conexión v2.0, puede agregar inicio de sesión y acceso a API a las aplicaciones basadas en web. En este artículo se muestra cómo hacerlo, independientemente del lenguaje. Describiremos cómo enviar y recibir mensajes HTTP sin usar ninguna biblioteca de código abierto de Microsoft.

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con la versión 2.0 del punto de conexión. Para determinar si debe usar la versión 2.0 del punto de conexión, obtenga información sobre las [limitaciones de esta versión](active-directory-v2-limitations.md).
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) amplía el protocolo de *autorización* de OAuth 2.0 para usarlo como un protocolo de *autenticación*, lo que le permite realizar inicios de sesión únicos mediante OAuth. OpenID Connect presenta el concepto de un *token de identificador*, que es un token de seguridad que permite al cliente comprobar la identidad del usuario. El token de identificador también obtiene información de perfil básica sobre el usuario. Como OpenID Connect amplía OAuth 2.0, las aplicaciones pueden adquirir de forma segura *tokens de acceso*, que se pueden usar para obtener acceso a los recursos protegidos mediante un [servidor de autorización](active-directory-v2-protocols.md#the-basics). Se recomienda usar OpenID Connect si compila una [aplicación web](active-directory-v2-flows.md#web-apps) hospedada en un servidor y a la que se obtiene acceso a través de un explorador.

## Diagrama de protocolo: inicio de sesión
El diagrama siguiente muestra los pasos de un flujo de inicio de sesión más básico. En este artículo se describe detalladamente cada paso.

![Protocolo OpenID Connect: inicio de sesión](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## Captura del documento de metadatos de OpenID Connect
OpenID Connect describe un documento de metadatos que contiene la mayor parte de la información necesaria para que una aplicación realice el inicio de sesión. Esta información incluye las direcciones URL que se usan y la ubicación de las claves de firma pública del servicio. En el caso del punto de conexión v2.0, este es el documento de metadatos de OpenID Connect que debe usar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

`{tenant}` puede tomar uno de estos cuatro valores:

| Valor | Descripción |
| --- | --- |
| `common` |Los usuarios con una cuenta personal de Microsoft y una cuenta profesional o educativa de Azure Active Directory (Azure AD) pueden iniciar sesión en la aplicación. |
| `organizations` |Solo los usuarios con cuentas profesionales o educativas de Azure AD pueden iniciar sesión en la aplicación. |
| `consumers` |Solo los usuarios con una cuenta de Microsoft personal pueden iniciar sesión en la aplicación. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` |Solo los usuarios con una cuenta profesional o educativa de un inquilino específico de Azure AD puede iniciar sesión en la aplicación. Puede usarse el nombre de dominio descriptivo del inquilino de Azure AD o bien el identificador de GUID del inquilino. |

Los metadatos son un documento de notación de objetos JavaScript (JSON) simple. Consulte el fragmento de código siguiente para ver un ejemplo. El contenido del fragmento de código se describe detalladamente en la [especificación de OpenID Connect](https://openid.net).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Normalmente, este documento de metadatos se usa para configurar una biblioteca o SDK de OpenID Connect; la biblioteca usa los metadatos para realizar su trabajo. Sin embargo, si no usa una biblioteca de OpenID Connect anterior a la generación, puede seguir los pasos del resto de este artículo para realizar el inicio de sesión en una aplicación web mediante el punto de conexión v2.0.

## Envío de la solicitud de inicio de sesión
Cuando su aplicación web deba autenticar al usuario, puede dirigirlo al punto de conexión `/authorize` . Esta solicitud es similar al primer segmento del [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md), con estas importantes diferencias:

* La solicitud debe incluir el ámbito `openid` en el parámetro `scope`.
* El parámetro `response_type` debe incluir `id_token`.
* La solicitud debe incluir el parámetro `nonce` .

Por ejemplo:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Haga clic en el vínculo siguiente para ejecutar esta solicitud. Una vez que inicie sesión, el explorador se redirigirá a https://localhost/myapp/, con un token de identificador en la barra de direcciones. Tenga en cuenta que esta solicitud usa `response_mode=query` (solo con fines de demostración). Se recomienda que use `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parámetro | Condición | Descripción |
| --- | --- | --- |
| tenant |Obligatorio |Puede usar el valor `{tenant}` en la ruta de acceso de la solicitud para controlar quién puede iniciar sesión en la aplicación. Los valores permitidos son `common`, `organizations`, `consumers` y los identificadores de inquilinos. Para más información, consulte los [conceptos básicos sobre el protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id |Obligatorio |El identificador de aplicación que el [portal de registro de aplicaciones](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) asignó a la aplicación. |
| response_type |Obligatorio |Debe incluir `id_token` para el inicio de sesión en OpenID Connect. También puede incluir otros valores `response_types`, como `code`. |
| redirect_uri |Recomendado |El URI de redireccionamiento de la aplicación, adonde la aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los URI de redireccionamiento que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito |Obligatorio |Una lista de ámbitos separada por espacios. Asegúrese de incluir el ámbito `openid`para OpenID Connect, lo que se traduce en el permiso de inicio de sesión en la interfaz de usuario de consentimiento. También puede incluir otros ámbitos en esta solicitud para solicitar el consentimiento. |
| valor de seguridad |Obligatorio |Un valor incluido en la solicitud, generado por la aplicación, que se incluirá en el valor id_token resultante como una notificación. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Habitualmente, el valor es una cadena única aleatoria que se puede usar para identificar el origen de la solicitud. |
| response_mode |Recomendado |Especifica el método que se debe usar para enviar el código de autorización resultante de nuevo a la aplicación. Puede ser `query`, `form_post` o `fragment`. En el caso de las aplicaciones web, se recomienda usar `response_mode=form_post` para asegurar la transferencia más segura de tokens a la aplicación. |
| state |Recomendado |Un valor incluido en la solicitud que también se devolverá en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se usa normalmente un valor único generado de forma aleatoria para [evitar los ataques de falsificación de solicitudes entre sitios](http://tools.ietf.org/html/rfc6749#section-10.12). El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, como la página o la vista en la que estaba el usuario. |
| símbolo del sistema |Opcional |Indica el tipo de interacción necesaria con el usuario. Los únicos valores válidos en este momento son `login`, `none` y `consent`. La notificación `prompt=login` obliga al usuario a escribir sus credenciales en esa solicitud, lo que niega el inicio de sesión único. La notificación `prompt=none` es lo contrario. Esta notificación se asegura de que al usuario no se le presente ninguna solicitud interactiva de ningún tipo. Si la solicitud no se puede completar sin notificaciones mediante el inicio de sesión único, el punto de conexión v2.0 devuelve un error. La notificación `prompt=consent` desencadena el cuadro de diálogo de consentimiento de OAuth después de que el usuario inicia sesión. El cuadro de diálogo le pide al usuario que conceda permisos a la aplicación. |
| login_hint |Opcional |Puede usar este parámetro para rellenar previamente el campo de nombre de usuario y dirección de correo electrónico de la página de inicio de sesión del usuario, si sabe el nombre de usuario con anticipación. A menudo, las aplicaciones usan este parámetro durante la reautenticación, una vez que ya se extrajo el nombre de usuario de un inicio de sesión anterior mediante la notificación `preferred_username`. |
| domain_hint |Opcional |Este valor puede ser `consumers` o `organizations`. Si se incluye, omite el proceso de detección basado en correo electrónico por el que pasa el usuario de la página de inicio de sesión v2.0, para obtener una experiencia de usuario ligeramente más sencilla. A menudo, las aplicaciones usarán este parámetro durante la reautenticación; para ello, extraen la notificación `tid` desde el token de identificador. Si el valor de la notificación `tid` es `9188040d-6c67-4c5b-b112-36a304b66dad`, use `domain_hint=consumers`. De lo contrario, use `domain_hint=organizations`. |

En este punto, se le pide al usuario que escriba sus credenciales y que complete la autenticación. El punto de conexión v2.0 comprueba que el usuario dio su consentimiento a los permisos indicados en el parámetro de la consulta `scope`. Si el usuario no dio su consentimiento a ninguno de estos permisos, el punto de conexión v2.0 solicita al usuario que dé su consentimiento para los permisos requeridos. Puede leer más información sobre los [permisos, el consentimiento y las aplicaciones multiinquilino](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y da su consentimiento, el punto de conexión v2.0 devuelve una respuesta a su aplicación en el URI de redireccionamiento indicado mediante el método especificado en el parámetro `response_mode`.

### Respuesta correcta
Una respuesta correcta cuando usa `response_mode=form_post` tiene el aspecto siguiente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parámetro | Description |
| --- | --- |
| ID_token |El token de identificador que la aplicación solicitó. Puede usar el parámetro `id_token` para comprobar la identidad del usuario y comenzar una sesión con el usuario. Para más detalles sobre los tokens de identificador y su contenido, consulte la [referencia a los tokens del punto de conexión v2.0](active-directory-v2-tokens.md). |
| state |Si un parámetro `state` está incluido en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

### Respuesta de error
Las respuestas de error también se pueden enviar al URI de redireccionamiento para que la aplicación pueda controlarlas. Una respuesta de error tiene el aspecto siguiente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que puede usar para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error_description |Un mensaje de error específico que puede ayudarlo a identificar la causa raíz de un error de autenticación. |

### Códigos de error correspondientes a errores de puntos de conexión de autorización
En la tabla siguiente se describen los códigos de error que puede devolver el parámetro `error` de la respuesta de error:

| Código de error | Descripción | Acción del cliente |
| --- | --- | --- |
| invalid_request |Error de protocolo, como un parámetro obligatorio que falta. |Corrija el error y vuelva a enviar la solicitud. Se trata de un error de desarrollo que habitualmente se detecta durante las pruebas iniciales. |
| unauthorized_client |La aplicación cliente no puede solicitar un código de autorización. |Este error suele producirse cuando la aplicación cliente no está registrada en Azure AD o no se ha agregado al inquilino de Azure AD del usuario. La aplicación puede pedir al usuario consentimiento para instalar la aplicación y agregarla a Azure AD. |
| access_denied |El propietario del recurso denegó el consentimiento. |La aplicación cliente puede notificar al usuario que no puede continuar, salvo que este dé su consentimiento. |
| unsupported_response_type |El servidor de autorización no admite el tipo de respuesta de la solicitud. |Corrija el error y vuelva a enviar la solicitud. Se trata de un error de desarrollo que habitualmente se detecta durante las pruebas iniciales. |
| server_error |El servidor ha detectado un error inesperado. |Vuelva a intentarlo. Estos errores pueden deberse a condiciones temporales. La aplicación cliente podría explicar al usuario que su respuesta se retrasó debido a un error temporal. |
| temporarily_unavailable |De manera temporal, el servidor está demasiado ocupado para atender la solicitud. |Vuelva a intentarlo. La aplicación podría explicar al usuario que su respuesta se retrasó debido a una condición temporal. |
| invalid_resource |El recurso de destino no es válido porque no existe, porque Azure AD no puede encontrarlo o porque no está configurado correctamente. |Esto indica que el recurso, si existe, no se configuró en el inquilino. La aplicación puede pedir al usuario consentimiento para instalar la aplicación y agregarla a Azure AD. |

## Validar el token de identificador
Recibir un token de identificador no es suficiente para autenticar al usuario. También debe validar la firma del token de identificador y comprobar las notificaciones en el token en función de los requisitos de la aplicación. El punto de conexión v2.0 usa [tokens web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y criptografía de clave pública para firmar los tokens y comprobar que son válidos.

Puede elegir validar el token de identificador en el código de cliente, pero lo habitual es enviar el token de identificador a un servidor back-end y realizar allí la validación. Después de validar la firma del token de identificador, deberá comprobar algunas notificaciones. Para más información, incluida información sobre cómo [validar tokens](active-directory-v2-tokens.md#validating-tokens) e [información importante sobre la sustitución de clave de firma](active-directory-v2-tokens.md#validating-tokens), consulte la [referencia a los tokens v2.0](active-directory-v2-tokens.md). Se recomienda usar una biblioteca para analizar y validar los tokens. Hay al menos una de estas bibliotecas disponibles para la mayoría de los lenguajes y plataformas.
<!--TODO: Improve the information on this-->

También es posible que desee validar notificaciones adicionales, en función del escenario. Algunas validaciones comunes incluyen:

* Asegurarse de que el usuario o la organización se registró en la aplicación.
* Asegurarse de que el usuario tiene privilegios o una autorización que se necesita.
* Asegurarse de que se produjo un determinado nivel de autenticación, como la autenticación multifactor.

Para más información sobre las notificaciones en un token de identificador, consulte la [referencia a los tokens de punto de conexión v2.0](active-directory-v2-tokens.md).

Una vez que valide completamente el token de identificador, puede comenzar una sesión con el usuario. Use las notificaciones del token de identificador para información sobre el usuario de la aplicación. Puede usar esta información para su visualización, registros, autorizaciones, etc.

## Envío de una solicitud de cierre de sesión
Si desea cerrar la sesión del usuario en la aplicación, no basta con borrar las cookies de la aplicación o finalizar la sesión del usuario. También debe redirigir al usuario al punto de conexión v2.0 para cerrar la sesión. Si no lo hace, el usuario se vuelve a autenticar en su aplicación sin escribir sus credenciales de nuevo, porque tendrá una sesión válida de inicio de sesión único con el punto de conexión v2.0.

Puede redirigir al usuario al objeto `end_session_endpoint` que aparece en el documento de metadatos OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parámetro | Condición | Descripción |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Recomendado | La dirección URL al que se redirige al usuario tras cerrar sesión correctamente. Si no se incluye el parámetro, se muestra al usuario un mensaje genérico que genera el punto de conexión v2.0. Esta dirección URL debe coincidir con uno de los URI de redirección registrados para su aplicación en el portal de registro de aplicaciones.  |

## Cierre de sesión único
Cuando redirige al usuario a `end_session_endpoint`, el punto de conexión v2.0 borra la sesión del usuario del explorador. Sin embargo, el usuario puede permanecer con la sesión iniciada en otras aplicaciones que usan cuentas de Microsoft para la autenticación. Para permitir que esas aplicaciones cierren la sesión del usuario de manera simultánea, el punto de conexión v2.0 envía una solicitud HTTP GET al elemento `LogoutUrl` registrado de todas las aplicaciones en las que tiene una sesión iniciada. Las aplicaciones deben responder a esta solicitud mediante la eliminación de la sesión que identifica al usuario y la devolución de una respuesta `200`.  Si quiere admitir el inicio de sesión único en la aplicación, debe implementar este elemento `LogoutUrl` en el código de la aplicación.  Para configurar `LogoutUrl`, hágalo en el portal de registro de aplicaciones.

## Diagrama de protocolo: adquisición de tokens
Muchas aplicaciones web no solo deben iniciar la sesión del usuario, sino también obtener acceso a un servicio web en nombre del usuario mediante OAuth. En este escenario se combina OpenID Connect para la autenticación de usuario, al tiempo que se obtiene, a la vez, un código de autorización que puede usar para obtener tokens de acceso si usa el flujo de código de autorización de OAuth.

El flujo completo de adquisición de tokens e inicio de sesión de OpenID Connect es similar al diagrama siguiente. En las secciones siguientes del artículo se describe detalladamente cada paso.

![Protocolo OpenID Connect: adquisición de tokens](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## Obtención de tokens de acceso
Para obtener tokens de acceso, modifique la solicitud de inicio de sesión:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Haga clic en el vínculo siguiente para ejecutar esta solicitud. Una vez que inicie sesión, el explorador se redirigirá a https://localhost/myapp/, con un token de identificador y un código en la barra de direcciones. Tenga en cuenta que esta solicitud usa `response_mode=query` (solo con fines de demostración). Se recomienda que use `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

Con la inclusión de los ámbitos de permiso en la solicitud y mediante el uso de `response_type=id_token code`, el punto de conexión v2.0 garantiza que el usuario dio su consentimiento a los permisos indicados en el parámetro de consulta `scope`. Devuelve un código de autorización a la aplicación para intercambiarla por un token de acceso.

### Respuesta correcta
Una respuesta correcta al usar `response_mode=form_post` tiene el siguiente aspecto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parámetro | Description |
| --- | --- |
| ID_token |El token de identificador que la aplicación solicitó. Puede usar el token de identificador para comprobar la identidad del usuario y comenzar una sesión con el usuario. Encontrará más detalles sobre los tokens de identificador y su contenido en la [referencia a los tokens del punto de conexión v2.0](active-directory-v2-tokens.md). |
| código |El código de autorización que la aplicación solicitó. La aplicación puede utilizar el código de autorización para solicitar un token de acceso para el recurso de destino. Un código de autorización tiene una duración muy breve. Habitualmente, un código de autorización expira en alrededor de 10 minutos. |
| state |Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

### Respuesta de error
Las respuestas de error también se pueden enviar al URI de redireccionamiento para que la aplicación pueda controlarlas correctamente. Una respuesta de error tiene el aspecto siguiente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que puede usar para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error_description |Un mensaje de error específico que puede ayudarlo a identificar la causa raíz de un error de autenticación. |

Para una descripción de los posibles códigos de error y las respuestas de cliente recomendadas, consulte [Códigos de error correspondientes a errores de puntos de conexión de autorización](#error-codes-for-authorization-endpoint-errors).

Cuando tiene un código de autorización y un token de identificador, puede iniciar la sesión del usuario y obtener tokens de acceso en su nombre. Para iniciar la sesión del usuario, debe validar el token de identificador [exactamente como se describió](#validate-the-id-token). Para obtener tokens de acceso, siga los pasos descritos en nuestra [documentación del protocolo OAuth](active-directory-v2-protocols-oauth-code.md#request-an-access-token).
