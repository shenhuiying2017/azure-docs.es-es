---
title: "Azure Active Directory B2C: aplicaciones de una página con el flujo implícito | Microsoft Docs"
description: "Obtenga información sobre cómo compilar aplicaciones de una página directamente con el flujo implícito de OAuth 2.0 con Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
ms.openlocfilehash: 44ff168599e9078506e1afdd0f1dc4657ef0964d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Azure AD B2C: inicio de sesión de aplicaciones de una página con el flujo implícito de OAuth 2.0

> [!NOTE]
> Esta característica se encuentra en su versión preliminar.
> 

Muchas aplicaciones modernas tienen un front-end de aplicación de una página escrito principalmente en JavaScript. A menudo, la aplicación se escribe con un marco como AngularJS, Ember.js o Durandal. Las aplicaciones de una sola página y otras aplicaciones JavaScript que se ejecutan principalmente en un explorador tienen algunos retos adicionales para la autenticación:

* Las características de seguridad de estas aplicaciones son significativamente diferentes de las de las aplicaciones web tradicionales basadas en el servidor.
* Muchos proveedores de identidades y servidores de autorización no admiten solicitudes de uso compartido de recursos entre orígenes (CORS).
* Los redireccionamientos del explorador a una página completa fuera de la aplicación pueden ser especialmente invasivos para la experiencia del usuario.

Para admitir estas aplicaciones, Azure Active Directory B2C (Azure AD B2C) usa el flujo implícito de OAuth 2.0. El flujo de concesión implícito de autorización de OAuth 2.0 se describe en la [sección 4.2 de la especificación de este protocolo](http://tools.ietf.org/html/rfc6749). En el flujo implícito, la aplicación recibe tokens directamente del punto de conexión de autorización de Azure Active Directory (Azure AD), sin ningún intercambio de servidor a servidor. La lógica de autenticación y el control de sesiones tienen lugar por completo en el cliente de JavaScript, sin redireccionamientos de página adicionales.

Azure AD B2C extiende el flujo implícito de OAuth 2.0 estándar para realizar algo más que una autorización y autenticación simples. Azure AD B2C presenta el [parámetro de directiva](active-directory-b2c-reference-policies.md). Con el parámetro de directiva, puede usar OAuth 2.0 para agregar experiencias de usuario a su aplicación como registro, inicio de sesión y administración de perfiles. En este artículo, le mostramos cómo usar el flujo implícito y Azure AD para implementar cada una de estas experiencias en sus aplicaciones de una página. Vea nuestros ejemplos de [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) y [Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) que le ayudarán a empezar a trabajar.

En las solicitudes HTTP de ejemplo de este artículo se usa nuestro directorio de ejemplo de Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. También se usa nuestra propia aplicación y nuestras directivas de ejemplo. Puede probar las solicitudes por sí mismo con estos valores, o bien puede reemplazarlos por los suyos propios.
Obtenga información sobre cómo [obtener su propio directorio, aplicación y directivas de Azure AD B2C](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Diagrama de protocolo

El flujo de inicio de sesión implícito tiene un aspecto similar al de la figura siguiente. Cada paso se describe con detalle más adelante en este artículo.

![Calles OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Envío de solicitudes de autenticación
Cuando su aplicación web necesita autenticar al usuario y ejecutar la directiva, dirige al usuario al punto de conexión `/authorize`. Esta es la parte interactiva del flujo, donde el usuario realiza la acción, dependiendo de la directiva. El usuario obtiene un token de identificador del punto de conexión de Azure AD.

En esta solicitud, el cliente indica en el parámetro `scope` los permisos que debe obtener por parte del usuario. En el parámetro `p` indica la directiva que se debe ejecutar. Los siguientes tres ejemplos (con saltos de línea para facilitar la lectura) usan directivas diferentes. Para hacerse una idea de cómo funciona cada solicitud, intente pegar la solicitud en un explorador y volver a ejecutarlo.

### <a name="use-a-sign-in-policy"></a>Uso de una directiva de inicio de sesión
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Uso de una directiva de registro
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Uso de una directiva de edición de perfil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| client_id |Obligatorio |Identificador de aplicación asignado a la aplicación en [Azure Portal](https://portal.azure.com). |
| response_type |Obligatorio |Debe incluir `id_token` para el inicio de sesión en OpenID Connect. También puede incluir el tipo de respuesta `token`. Si usa `token`, la aplicación puede recibir un token de acceso inmediatamente desde el punto de conexión autorizado sin realizar una segunda solicitud para autorizar el punto de conexión.  Si usa el tipo de respuesta `token`, el parámetro `scope` debe contener un ámbito que indica para qué recurso se va a emitir el token. |
| redirect_uri |Recomendado |El URI de redireccionamiento de la aplicación, adonde la aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los URI de redireccionamiento que ha registrado en el portal, con la excepción de que debe estar codificado como URL. |
| response_mode |Recomendado |Especifica el método que se usará para enviar el token resultante de nuevo a la aplicación.  Para los flujos implícitos, use `fragment`. |
| ámbito |Obligatorio |Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión con el usuario y obtener los datos del usuario en forma de tokens de identificador. (Hablaremos sobre esto posteriormente en el artículo). El ámbito `offline_access` es opcional para las aplicaciones web. Indica que la aplicación necesita un token de actualización para un acceso de larga duración a los recursos. |
| state |Recomendado |Un valor incluido en la solicitud que también se devolverá en la respuesta del token. Puede ser una cadena de cualquier contenido que quiera usar. Se suele usar un valor único generado de manera aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página en la que estaban. |
| valor de seguridad |Obligatorio |Un valor incluido en la solicitud (generada por la aplicación), que se incluye en el token de identificador resultante como una notificación. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que puede usarse para identificar el origen de la solicitud. |
| p |Obligatorio |La directiva que se va a ejecutar. Es el nombre de una directiva que se crea en el inquilino de Azure AD B2C. El valor del nombre de directiva debe comenzar por **b2c\_1\_**. Para obtener más información, vea [Directivas integradas de Azure AD B2C](active-directory-b2c-reference-policies.md). |
| símbolo del sistema |Opcional |El tipo de interacción con el usuario que se necesita. Actualmente, el único valor válido es `login`. Esto obliga al usuario a escribir sus credenciales en esa solicitud. El inicio de sesión único no surtirá efecto. |

En este punto se pedirá al usuario que complete el flujo de trabajo de la directiva. Esto puede implicar que el usuario tenga que escribir su nombre de usuario y contraseña, iniciar sesión con una identidad social, registrarse en el directorio o realizar otros pasos. Las acciones del usuario dependerán de cómo se defina la directiva.

Cuando el usuario haya completado la directiva, Azure AD devolverá una respuesta a la aplicación en el valor que ha usado para `redirect_uri`. Usa el método especificado en el parámetro `response_mode`. La respuesta es exactamente la misma para cada uno de los escenarios de acción del usuario, independientemente de la directiva que se haya ejecutado.

### <a name="successful-response"></a>Respuesta correcta
Una respuesta correcta que usa `response_mode=fragment` y `response_type=id_token+token` es como la siguiente, con saltos de línea para mejorar la legibilidad:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parámetro | Description |
| --- | --- |
| access_token |El token de acceso que solicitó la aplicación.  El token de acceso no se debe descodificar o inspeccionar de otro modo. Puede tratarse como una cadena opaca. |
| token_type |El valor del tipo de token. El único tipo que admite Azure AD es portador. |
| expires_in |El período de validez del token de acceso (en segundos). |
| ámbito |Los ámbitos para los que el token es válido. También puede usar ámbitos para almacenar en caché los tokens para su posterior uso. |
| ID_token |El token de identificador que la aplicación solicitó. Puede usar el token de identificador para comprobar la identidad del usuario y comenzar una sesión con el usuario. Para obtener más información sobre los tokens de identificador y su contenido, vea [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md). |
| state |Si un parámetro `state` está incluido en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores `state` de la solicitud y de la respuesta sean idénticos. |

### <a name="error-response"></a>Respuesta de error
Las respuestas de error también se pueden enviar al URI de redireccionamiento para que la aplicación pueda controlarlas correctamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que se usa para clasificar los tipos de errores que se producen. También puede usar el código de error para el control de errores. |
| error_description |Un mensaje de error específico que puede ayudarlo a identificar la causa raíz de un error de autenticación. |
| state |Vea la descripción completa en la tabla anterior. Si un parámetro `state` está incluido en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores `state` de la solicitud y de la respuesta sean idénticos.|

## <a name="validate-the-id-token"></a>Validar el token de identificador
Recibir un token de identificador no es suficiente para autenticar al usuario. Debe validar la firma del token de identificador y comprobar las notificaciones en el token en función de los requisitos de la aplicación. Azure AD B2C usa [tokens web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y la criptografía de clave pública para firmar los tokens y comprobar que son válidos.

Muchas bibliotecas de código abierto están disponibles para validar los JWT, según su lenguaje preferido. Considere la posibilidad de explorar las bibliotecas de código abierto disponibles, en lugar de implementar su propia lógica de validación. Puede usar la información de este artículo para ayudarle a obtener información sobre cómo usar correctamente esas bibliotecas.

Azure AD B2C tiene un punto de conexión de metadatos OpenID Connect. Una aplicación puede usar el punto de conexión para obtener información sobre Azure AD B2C en tiempo de ejecución. En esta información se incluyen los extremos, los contenidos del token y las claves de firma de los token. Hay un documento de metadatos JSON para cada directiva en su inquilino de Azure AD B2C. Por ejemplo, el documento de metadatos para la directiva b2c_1_sign_in del inquilino fabrikamb2c.onmicrosoft.com se encuentra en:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una de las propiedades de este documento de configuración es `jwks_uri`. El valor de la misma directiva sería:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Para determinar qué directiva se ha usado para firmar un token de identificador (y de dónde obtener los metadatos), tiene dos opciones. En primer lugar, el nombre de la directiva se incluye en la notificación `acr` de `id_token`. Para obtener más información sobre cómo analizar las notificaciones de un token de identificador, vea [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md). La otra opción consiste en codificar la directiva en el valor del parámetro `state` al emitir la solicitud. Después, descodifique el parámetro `state` para determinar qué directiva se ha usado. Cualquiera de estos métodos es válido.

Cuando haya adquirido el documento de metadatos del punto de conexión de metadatos de OpenID Connect, podrá usar las claves públicas RSA-256 (que están ubicadas en este punto de conexión) para validar la firma del token de identificador. Podría haber varias claves enumeradas en este punto de conexión en cualquier momento, cada una identificada con una `kid`. El encabezado de `id_token` también contiene una notificación `kid`. Indica cuál de estas claves se ha usado para firmar el token de identificador. Para obtener más información, incluida la información para [validar los tokens](active-directory-b2c-reference-tokens.md#token-validation), vea [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Después de que valide la firma del token de identificador, varias notificaciones necesitan comprobarse. Por ejemplo:

* Valide la notificación `nonce` para evitar ataques de reproducción del token. Su valor debería ser el que especificó en la solicitud de inicio de sesión.
* Valide la notificación `aud` para asegurarse de que se ha generado el token de identificador para su aplicación. Su valor debería ser el identificador de la aplicación.
* Valide las notificaciones `iat` y `exp` para asegurarse de que el token de identificador no ha expirado.

Varias validaciones más que debe realizar se describen en detalle en la [especificación de OpenID Connect Core](http://openid.net/specs/openid-connect-core-1_0.html). Podría también querer validar notificaciones adicionales, en función de su escenario. Algunas validaciones comunes incluyen:

* Asegurarse de que la organización o el usuario se han registrado en la aplicación.
* Asegurarse de que el usuario tiene la autorización y los privilegios adecuados.
* Asegurarse de que se haya producido un determinado nivel de autenticación, como al usar Azure Multi-Factor Authentication.

Para obtener más información sobre las notificaciones en un token de identificador, vea [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md).

Una vez que valide completamente el token de identificador, puede comenzar una sesión con el usuario. Use las notificaciones del token de identificador para obtener información sobre el usuario en la aplicación. Esta información puede usarse para su visualización, registros, autorizaciones, etc.

## <a name="get-access-tokens"></a>Obtención de tokens de acceso
Si lo único que sus aplicaciones web necesitan hacer es ejecutar directivas, puede omitir las secciones siguientes. La información de las siguientes secciones se aplica solo a las aplicaciones web que necesitan realizar llamadas autenticadas a una API web y que están protegidas por Azure AD B2C.

Ahora que ha iniciado la sesión del usuario en su aplicación de una página, puede obtener tokens de acceso para llamar a las API web que están protegidas por Azure AD. Incluso si ya ha recibido un token mediante el tipo de respuesta `token`, puede usar este método para adquirir tokens para recursos adicionales sin redirigir al usuario para que vuelva a iniciar sesión.

En el flujo de aplicación web normal, podría hacer esto realizando una solicitud al punto de conexión `/token`.  En cambio, el punto de conexión no admite solicitudes CORS, así que las llamadas a AJAX para obtener y actualizar los tokens no son una opción. En su lugar, puede usar el flujo implícito en un elemento iframe HTML oculto para obtener nuevos tokens para otras API web. Aquí se muestra un ejemplo, con saltos de línea por motivos de legibilidad:

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| client_id |Obligatorio |Identificador de aplicación asignado a la aplicación en [Azure Portal](https://portal.azure.com). |
| response_type |Obligatorio |Debe incluir `id_token` para el inicio de sesión en OpenID Connect.  También puede incluir el tipo de respuesta `token`. Si usa `token` aquí, la aplicación puede recibir un token de acceso inmediatamente desde el punto de conexión autorizado sin realizar una segunda solicitud para autorizar el punto de conexión. Si usa el tipo de respuesta `token`, el parámetro `scope` debe contener un ámbito que indica para qué recurso se va a emitir el token. |
| redirect_uri |Recomendado |El URI de redireccionamiento de la aplicación, adonde la aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los URI de redireccionamiento que ha registrado en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito |Obligatorio |Una lista de ámbitos separada por espacios.  Para obtener los tokens, incluya todos los ámbitos que necesita para el recurso previsto. |
| response_mode |Recomendado |Especifica el método que se usará para enviar el token resultante de nuevo a la aplicación.  Puede ser `query`, `form_post` o `fragment`. |
| state |Recomendado |Un valor incluido en la solicitud que se devolverá en la respuesta del token.  Puede ser una cadena de cualquier contenido que quiera usar.  Se suele usar un valor único generado de manera aleatoria para evitar los ataques de falsificación de solicitudes entre sitios.  El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación. Por ejemplo, la página o la vista en la que ha estado el usuario. |
| valor de seguridad |Obligatorio |Un valor incluido en la solicitud, generada por la aplicación, que se incluirá en el token de identificador resultante como una notificación.  La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que puede identificar el origen de la solicitud. |
| símbolo del sistema |Obligatorio |Para actualizar y obtener tokens en un iframe oculto, use `prompt=none` para asegurarse de que el iframe no se bloquea en la página de inicio de sesión y se devuelve inmediatamente. |
| login_hint |Obligatorio |Para actualizar y obtener tokens en un iframe oculto, incluya el nombre de usuario del usuario en esta sugerencia para distinguir entre varias sesiones que el usuario pueda tener en un momento dado. Puede extraer el nombre de usuario de un inicio de sesión anterior mediante la notificación `preferred_username`. |
| domain_hint |Obligatorio |Puede ser `consumers` o `organizations`.  Para actualizar y obtener tokens en un iframe oculto, debe incluir el valor `domain_hint` en la solicitud.  Extraiga la notificación `tid` del token de identificador de un inicio de sesión anterior para determinar qué valor va a usar.  Si el valor de la notificación `tid` es `9188040d-6c67-4c5b-b112-36a304b66dad`, use `domain_hint=consumers`.  De lo contrario, use `domain_hint=organizations`. |

Al establecer el parámetro `prompt=none`, esta solicitud se realiza correctamente o produce un error inmediatamente, y vuelve a la aplicación.  Se envía una respuesta correcta a la aplicación en el URI de redireccionamiento indicado, mediante el método especificado en el parámetro `response_mode`.

### <a name="successful-response"></a>Respuesta correcta
Una respuesta correcta al usar `response_mode=fragment` tiene el siguiente aspecto:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parámetro | Description |
| --- | --- |
| access_token |El token que solicitó la aplicación. |
| token_type |El tipo de token siempre será un token de portador. |
| state |Si un parámetro `state` está incluido en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores `state` de la solicitud y de la respuesta sean idénticos. |
| expires_in |Durante cuánto tiempo es válido el token de acceso (en segundos). |
| ámbito |Los ámbitos para los que el token de acceso es válido. |

### <a name="error-response"></a>Respuesta de error
Las respuestas de error también se pueden enviar al URI de redireccionamiento para que la aplicación pueda controlarlas correctamente.  Para `prompt=none`, un error esperado tiene este aspecto:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que puede usarse para clasificar los tipos de errores que se producen. También puede usar la cadena para reaccionar frente a errores. |
| error_description |Un mensaje de error específico que puede ayudarlo a identificar la causa raíz de un error de autenticación. |

Si recibe este error en la solicitud de iframe, el usuario debe iniciar sesión de nuevo de manera interactiva para recuperar un nuevo token. Puede controlar esto de manera que tenga sentido para su aplicación.

## <a name="refresh-tokens"></a>Tokens de actualización
Los tokens de identificador y los tokens de acceso expiran tras un breve período de tiempo. La aplicación debe estar preparada para actualizar estos tokens periódicamente.  Para actualizar cualquier tipo de token, realice la misma solicitud de iframe oculto que hemos usado en un ejemplo anterior, mediante el parámetro `prompt=none` para controlar los pasos de Azure AD.  Para recibir un nuevo valor `id_token`, asegúrese de usar `response_type=id_token` y `scope=openid`, y un parámetro `nonce`.

## <a name="send-a-sign-out-request"></a>Envío de una solicitud de cierre de sesión
Cuando quiera iniciar sesión fuera de la aplicación, redirija al usuario a Azure AD para cerrar la sesión. Si no lo hace, el usuario podría autenticarse de nuevo en su aplicación sin volver a escribir sus credenciales. Esto se debe a que el usuario tendrá una sesión válida de inicio de sesión único con Azure AD.

Lo que puede hacer es simplemente redirigir al usuario al elemento `end_session_endpoint` que se enumera en el mismo documento de metadatos de OpenID Connect que se ha descrito en [Validar el token de identificador](#validate-the-id-token). Por ejemplo:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| p |Obligatorio |La directiva que se usará para cerrar la sesión del usuario de su aplicación. |
| post_logout_redirect_uri |Recomendado |La dirección URL a la que se debe redirigir al usuario después de un cierre de sesión correcto. Si no se incluye, Azure AD B2C muestra un mensaje genérico al usuario. |

> [!NOTE]
> Al dirigir al usuario a `end_session_endpoint` se borra parte del estado del inicio de sesión único del usuario con Azure AD B2C. En cambio, no cierra la sesión del proveedor de identidades sociales del usuario. Si el usuario selecciona el mismo proveedor de identidades durante un inicio de sesión posterior, este se vuelve a autenticar sin especificar sus credenciales. Si un usuario quiere cerrar sesión en su aplicación de Azure AD B2C, eso no significa necesariamente que quiera cerrar sesión completamente en su cuenta de Facebook, por ejemplo. En cambio, para las cuentas locales, la sesión del usuario se terminará correctamente.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>Usar su propio inquilino de Azure AD B2C
Para probar estas solicitudes, realice los siguientes tres pasos. Reemplace los valores de ejemplo que hemos usado en este artículo por los suyos propios:

1. [Crear un inquilino de Azure AD B2C](active-directory-b2c-get-started.md). Use el nombre del inquilino en las solicitudes.
2. [Crear una aplicación](active-directory-b2c-app-registration.md) para obtener un identificador de aplicación y un valor `redirect_uri`. Incluya una aplicación web o una API web en la aplicación. Opcionalmente, puede crear un secreto de aplicación.
3. [Crear directivas](active-directory-b2c-reference-policies.md) para obtener los nombres de las directivas.

## <a name="samples"></a>Muestras

* [Creación de una aplicación de una página mediante Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Creación de una aplicación de una página mediante .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

