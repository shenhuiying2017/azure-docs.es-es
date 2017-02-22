---
title: Azure Active Directory B2C | Microsoft Docs
description: "En este artículo se explica cómo crear aplicaciones de una página directamente con el flujo implícito."
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
translationtype: Human Translation
ms.sourcegitcommit: f2eb1f67c5687ac9bd8b94f6480617e6dc87ac9f
ms.openlocfilehash: ace35adc1cd2745ea11cb60429a1e7892d1f66d3


---
# <a name="azure-active-directory-b2c-sign-in-for-single-page-apps-using-oauth-20-implicit-flow"></a>Azure Active Directory B2C: Inicio de sesión de las aplicaciones de una página con el flujo implícito de OAuth 2.0

> [!NOTE]
> Esta característica aún sigue en la fase de versión preliminar.
> 

Muchas aplicaciones modernas tienen un front-end de aplicación de una página escrito principalmente en JavaScript. A menudo, se escribe con un marco como AngularJS, Ember.js o Durandal.js. Las aplicaciones de una sola página y otras aplicaciones JavaScript que se ejecutan principalmente en un explorador, se enfrentan con algunos retos interesantes por lo que se refiere a la autenticación:

* Las características de seguridad de estas aplicaciones son significativamente diferentes de las de las aplicaciones web tradicionales basadas en el servidor.
* Muchos proveedores de identidades y servidores de autorización no admiten solicitudes CORS.
* Los redireccionamientos del explorador a una página completa fuera de la aplicación se vuelven especialmente invasivos para la experiencia del usuario.

Para admitir estas aplicaciones, Azure AD B2C usa el flujo implícito de OAuth 2.0.  El flujo de concesión implícito de autorización de OAuth 2.0 se describe en la [sección 4.2 de la especificación de este protocolo](http://tools.ietf.org/html/rfc6749).  En este flujo, la aplicación recibe tokens directamente del punto de conexión de autorización de Azure AD, sin necesidad de realizar ningún intercambio de servidor a servidor. Toda la lógica de autenticación y el control de sesiones tiene lugar por completo en el cliente de JavaScript, sin redireccionamientos de página adicionales.

Azure AD B2C extiende los flujos implícitos de OAuth 2.0 estándar para realizar algo más que una autorización y autenticación simples. Introduce el [**parámetro de directiva**](active-directory-b2c-reference-policies.md), que le permite usar OAuth 2.0 para agregar experiencias de usuario a su aplicación como registro, inicio de sesión y administración de perfiles. En este artículo mostramos cómo usar el flujo implícito y Azure AD para implementar cada una de estas experiencias en sus aplicaciones de una página.  También puede buscar en nuestros ejemplos de [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) o [.NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) que lo ayudarán a empezar a trabajar.

Las siguientes solicitudes HTTP de ejemplo usarán nuestro directorio de ejemplo de B2C, **fabrikamb2c.onmicrosoft.com**, así como nuestras directivas y la aplicación de ejemplo. Puede probar las solicitudes por sí mismo con estos valores, o bien puede reemplazarlos por los suyos propios.
Obtenga más información acerca de cómo [obtener su propio directorio de B2C, aplicación y directivas](#use-your-own-b2c-tenant).

## <a name="protocol-diagram"></a>Diagrama de protocolo
El conjunto del flujo de inicio de sesión implícito tiene un aspecto similar a lo que se indica a continuación. Cada uno de los pasos se describe en detalle más adelante.

![Calles OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Envío de solicitudes de autenticación
Cuando su aplicación web necesita autenticar al usuario y ejecutar la directiva, dirige al usuario al punto de conexión `/authorize`. Esta es la parte interactiva del flujo, donde el usuario actuará realmente de acuerdo con la directiva, y obtiene un elemento `id_token` del punto de conexión de Azure AD.

En esta solicitud, el cliente indica los permisos que necesita adquirir del usuario en el parámetro `scope` y la directiva que se va a ejecutar en el parámetro `p`. A continuación se proporcionan tres ejemplos (con saltos de línea para facilitar la lectura), cada uno con una directiva diferente. Para hacerse una idea de cómo funciona cada solicitud, intente pegar la solicitud en un explorador y volver a ejecutarlo.

#### <a name="use-a-sign-in-policy"></a>Uso de una directiva de inicio de sesión
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

#### <a name="use-a-sign-up-policy"></a>Uso de una directiva de registro
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

#### <a name="use-an-edit-profile-policy"></a>Uso de una directiva de edición de perfil
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

| Parámetro |  | Description |
| --- | --- | --- |
| client_id |Obligatorio |El identificador de aplicación que el [Portal de Azure](https://portal.azure.com/) asignó a la aplicación. |
| response_type |requerido |Debe incluir `id_token` para el inicio de sesión en OpenID Connect.  También puede incluir el `token` response_type. El uso de `token` aquí permite a la aplicación recibir un token de acceso inmediatamente desde el punto de conexión autorizado sin tener que realizar una segunda solicitud para autorizar el punto de conexión.  Si usa el `token` response_type, el parámetro `scope` debe contener un ámbito que indica para qué recurso se va a emitir el token. |
| redirect_uri |Recomendado |El redirect_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los elementos redirect_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| response_mode |recomendado |Especifica el método que debe usarse para enviar el token resultante de nuevo a la aplicación.  Debe ser `fragment` para el flujo implícito. |
| ámbito |Obligatorio |Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión el usuario y obtener los datos del usuario en forma de **id_tokens** (más adelante encontrará más información al respecto). El ámbito `offline_access` es opcional para las aplicaciones web. Indica que la aplicación necesitará un **refresh_token** para un acceso de larga duración a los recursos. |
| state |Recomendado |Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria  para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página en la que estaban. |
| valor de seguridad |Obligatorio |Un valor incluido en la solicitud, generada por la aplicación, que se incluirá en el elemento id_token resultante como una notificación. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que puede utilizarse para identificar el origen de la solicitud. |
| p |Obligatorio |La directiva que se va a ejecutar. Es el nombre de una directiva que se crea en el inquilino B2C. El valor del nombre de directiva debe comenzar por "b2c\_1\_". Más información sobre las directivas en [Vista previa de Azure Active Directory B2C: marco de directiva extensible](active-directory-b2c-reference-policies.md). |
| símbolo del sistema |Opcional |El tipo de interacción necesaria con el usuario. El único valor válido en este momento es 'login', lo que obliga al usuario a escribir sus credenciales en esa solicitud. El inicio de sesión único no surtirá efecto. |

En este punto, se pedirá al usuario que complete el flujo de trabajo de la directiva. Esto puede implicar que el usuario tenga que escribir su nombre de usuario y contraseña, iniciar sesión con una identidad social, registrarse para el directorio o realizar cualquier otro número de pasos, en función de cómo esté definida la directiva.

Una vez que el usuario complete la directiva, Azure AD devolverá una respuesta a su aplicación en el elemento `redirect_uri` indicado, para lo que se usa el método que se especifica en el parámetro `response_mode`. La respuesta será exactamente la misma para cada uno de los casos anteriores, independientemente de la directiva que se ejecute.

#### <a name="successful-response"></a>Respuesta correcta
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
| access_token |El token de acceso que solicitó la aplicación.  El token de acceso no se debe descodificar o inspeccionar en forma alguna, se puede tratar como una cadena opaca. |
| token_type |El valor del tipo de token. El único tipo que admite Azure AD es portador. |
| expires_in |El período de validez del elemento access_token (en segundos). |
| ámbito |Los ámbitos para los que el token es válido, que se pueden usar para almacenar en caché los tokens para su uso posterior. |
| ID_token |El id_token que solicitó la aplicación. Puede usar el id_token para comprobar la identidad del usuario y comenzar una sesión con el usuario. [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md) incluye más información acerca de id_tokens y su contenido. |
| state |Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

#### <a name="error-response"></a>Respuesta de error
Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| . | Description |
| --- | --- |
| error |Una cadena de código de error que se usa para clasificar los tipos de errores que se producen. El código de error se puede usar para controlar errores. |
| error_description |Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |
| state |Vea la descripción completa en la tabla anterior. Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos.|

## <a name="validate-the-idtoken"></a>Validar el id_token
Recibir un solo elemento id_token no es suficiente para autenticar al usuario; debe validar la firma del elemento id_token y comprobar las notificaciones en el token según los requisitos de su aplicación. Azure AD B2C usa [tokens web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y la criptografía de clave pública para firmar los tokens y comprobar que son válidos.

Hay muchas bibliotecas de código abierto disponibles para validar los JWT, según el lenguaje de preferencia. Plantéese explorar esas opciones en lugar de implementar su propia lógica de validación. Esta información será útil para averiguar cómo usar adecuadamente dichas bibliotecas.

Azure AD B2C tiene un extremo de metadatos OpenID Connect, que permite a una aplicación obtener información sobre Azure AD B2C en tiempo de ejecución. En esta información se incluyen los extremos, los contenidos del token y las claves de firma de los token. Hay un documento de metadatos JSON para cada directiva en su inquilino de B2C. Por ejemplo, el documento de metadatos de la directiva `b2c_1_sign_in` en `fabrikamb2c.onmicrosoft.com` se encuentra en:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una de las propiedades de este documento de configuración es `jwks_uri`, cuyo valor para la misma directiva sería:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Para determinar qué directiva se usó en la firma de un id_token (y de dónde obtener los metadatos), tiene dos opciones. En primer lugar, se incluye el nombre de la directiva en la notificación `acr` en id_token. Para más información sobre cómo analizar las notificaciones de un id_token, consulte [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md). La otra opción consiste en codificar la directiva en el valor del parámetro `state` al emitir la solicitud y descodificarla para determinar qué directiva se ha usado. Cualquiera de los métodos es perfectamente válido.

Cuando haya adquirido el documento de metadatos del punto de conexión de metadatos de OpenID Connect, podrá usar las claves públicas RSA de 256 bits (que están ubicadas en este punto de conexión) para validar la firma del elemento id_token. Hay varias claves enumeradas en este extremo en cualquier momento, cada una identificada con un `kid`. El encabezado del id_token también contiene una notificación `kid`, que indica cuál de estas claves se usó para firmar el id_token. Consulte la [referencia del token de Azure AD B2C](active-directory-b2c-reference-tokens.md) para más información, por ejemplo, para saber [cómo validar los tokens](active-directory-b2c-reference-tokens.md#token-validation).
<!--TODO: Improve the information on this-->

Después de validar la firma de id_token, también hay que comprobar varias notificaciones, por ejemplo:

* Valide la notificación `nonce` para evitar ataques de reproducción del token. Su valor debería ser el que especificó en la solicitud de inicio de sesión.
* Valide la notificación `aud` para asegurarse de que se ha generado el elemento id_token para su aplicación. Su valor debería ser el identificador de la aplicación.
* Valide las notificaciones `iat` y `exp` para asegurarse de que el elemento id_token no ha expirado.

También hay varias validaciones más que debe realizar, que se describen en detalle en la [especificación de OpenID Connect Core](http://openid.net/specs/openid-connect-core-1_0.html).  Podría también querer validar notificaciones adicionales, en función de su escenario. Algunas validaciones comunes incluyen:

* Asegurarse de que la organización o el usuario se han registrado en la aplicación.
* Asegurarse de que el usuario tiene la autorización o los privilegios adecuados.
* Asegurarse de que se haya producido un determinado nivel de autenticación, como Azure Multi-Factor Authentication.

Para más información sobre las notificaciones en un id_token, consulte [Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md).

Después de que haya validado completamente el elemento id_token, puede iniciar una sesión con el usuario y usar las notificaciones en el elemento id_token para obtener información sobre el usuario en su aplicación. Esta información puede usarse para su visualización, registros, autorizaciones, etc.

## <a name="get-access-tokens"></a>Obtención de tokens de acceso
Si todo lo que su aplicación web necesita hacer es ejecutar directivas, puede pasar por alto las secciones siguientes. Estas secciones solo son aplicables a las aplicaciones web que necesitan realizar llamadas autenticadas a una API web y también están protegidas por Azure AD B2C.

Ahora que ha iniciado la sesión del usuario en su aplicación de una página, puede obtener tokens de acceso para llamar a las API web protegidas por Azure AD.  Incluso si ya recibió un token mediante el `token` response_type, puede usar este método para adquirir tokens para recursos adicionales sin tener que redirigir al usuario para que vuelva a iniciar sesión.

En el flujo de aplicación web normal, podría hacer esto realizando una solicitud al punto de conexión `/token`.  Sin embargo, el punto de conexión no admite solicitudes CORS, así que las llamadas a AJAX para obtener y actualizar los tokens están fuera de nuestro alcance.  En su lugar, puede usar el flujo implícito en un iframe oculto para obtener nuevos tokens para otras API web:

```
// Line breaks for legibility only

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

| Parámetro |  | Description |
| --- | --- | --- |
| client_id |Obligatorio |El identificador de aplicación que el [Portal de Azure](https://portal.azure.com/) asignó a la aplicación. |
| response_type |requerido |Debe incluir `id_token` para el inicio de sesión en OpenID Connect.  También puede incluir el `token` response_type. El uso de `token` aquí permitirá a la aplicación recibir un token de acceso inmediatamente desde el punto de conexión autorizado sin tener que realizar una segunda solicitud para autorizar el punto de conexión.  Si usa el `token` response_type, el parámetro `scope` debe contener un ámbito que indica para qué recurso se va a emitir el token. |
| redirect_uri |recomendado |El redirect_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación.  Debe coincidir exactamente con uno de los redirect_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito |requerido |Una lista de ámbitos separada por espacios.  Para obtener los tokens, incluya todos los ámbitos que necesita para el recurso de interés. |
| response_mode |recomendado |Especifica el método que se usará para enviar el token resultante de nuevo a la aplicación.  Puede ser `query`, `form_post` o `fragment`. |
| state |recomendado |Un valor incluido en la solicitud que se devolverá en la respuesta del token.  Puede ser una cadena de cualquier contenido que desee.  Se utiliza normalmente un valor único generado de forma aleatoria  para evitar los ataques de falsificación de solicitudes entre sitios.  El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| valor de seguridad |requerido |Un valor incluido en la solicitud, generada por la aplicación, que se incluirá en el id_token resultante como una notificación.  La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token.  Normalmente, el valor es una cadena única aleatoria que puede identificar el origen de la solicitud. |
| símbolo del sistema |requerido |Para actualizar y obtener tokens en un iframe oculto, use `prompt=none` para asegurarse de que el iframe no se bloquea en la página de inicio de sesión y se devuelve inmediatamente. |
| login_hint |requerido |Para actualizar y obtener tokens en un iframe oculto, tiene que incluir el nombre de usuario en esta sugerencia para distinguir entre varias sesiones que el usuario pueda tener en un momento dado. Puede extraer el nombre de usuario de un inicio de sesión anterior mediante la notificación `preferred_username`. |
| domain_hint |requerido |Puede ser `consumers` o `organizations`.  Para actualizar y obtener tokens en un iframe oculto, tiene que incluir el domain_hint en la solicitud.  Extraiga la notificación `tid` de id_token de un inicio de sesión anterior para determinar qué valor va a usar.  Si el valor de la notificación `tid` es `9188040d-6c67-4c5b-b112-36a304b66dad`, debe usar `domain_hint=consumers`.  De lo contrario, use `domain_hint=organizations`. |

Al establecer el parámetro `prompt=none`, esta solicitud se realizará correctamente o dará error inmediatamente y volverá a la aplicación.  Se enviará una respuesta correcta a la aplicación en el `redirect_uri` indicado, mediante el método especificado en el parámetro `response_mode`.

#### <a name="successful-response"></a>Respuesta correcta
Una respuesta correcta al usar `response_mode=fragment` tiene el siguiente aspecto:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| . | Description |
| --- | --- |
| access_token |El token que solicitó la aplicación. |
| token_type |Siempre será `Bearer`. |
| state |Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |
| expires_in |Durante cuánto tiempo es válido el token de acceso (en segundos). |
| ámbito |Los ámbitos para los que el token de acceso es válido. |

#### <a name="error-response"></a>Respuesta de error
Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente.  En el caso de `prompt=none`, un error esperado será:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parámetro | Description |
| --- | --- |
| error |Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error_description |Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

Si recibe este error en la solicitud de iframe, el usuario debe iniciar sesión de nuevo de manera interactiva para recuperar un nuevo token.  Puede elegir tratar este caso de la manera que más sentido tenga para su aplicación.

## <a name="refreshing-tokens"></a>Actualización de tokens
Tanto los elementos `id_token` como `access_token` expiran tras un corto periodo, así que su aplicación debe estar preparada para actualizar estos tokens de manera periódica.  Para actualizar cualquier tipo de token, realice la misma solicitud de iframe oculto que antes mediante el parámetro `prompt=none` para controlar el comportamiento de Azure AD.  Para recibir un nuevo elemento `id_token`, asegúrese de usar `response_type=id_token` y `scope=openid`, así como un parámetro `nonce`.

## <a name="send-a-sign-out-request"></a>Envío de una solicitud de cierre de sesión
Cuando quiera iniciar sesión fuera de la aplicación, redirija al usuario a Azure AD para cerrar la sesión. Si no lo hace, el usuario podría autenticarse de nuevo en su aplicación sin volver a escribir sus credenciales. Esto se debe a que el usuario tendrá una sesión válida de inicio de sesión único con Azure AD.

Lo que puede hacer es simplemente redirigir al usuario al elemento `end_session_endpoint` que se enumera en el mismo documento de metadatos de OpenID Connect que se ha descrito en la sección anterior "Validar el id_token":

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parámetro | ¿Necesario? | Description |
| --- | --- | --- |
| p |Obligatorio |La directiva que se usará para cerrar la sesión del usuario de su aplicación. |
| post_logout_redirect_uri |Recomendado |La dirección URL a la que se debe redirigir al usuario después de un cierre de sesión correcto. Si no se incluye, Azure AD B2C mostrará un mensaje genérico al usuario. |

> [!NOTE]
> Aunque el hecho de dirigir al usuario a `end_session_endpoint` borrará algún estado de inicio de sesión único del usuario con Azure AD B2C, no se cerrará la sesión del proveedor de identidades social (IDP) del usuario. Si el usuario selecciona el mismo IDP durante un inicio de sesión posterior, se volverá a autenticar sin especificar sus credenciales. Si un usuario desea cerrar sesión en su aplicación B2C, eso no significa necesariamente que quiera cerrar sesión en su cuenta de Facebook. Sin embargo, en el caso de las cuentas locales, la sesión del usuario se terminará correctamente.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Uso de un inquilino de B2C propio
Si quiere probar estas solicitudes por sí mismo, primero debe realizar estos tres pasos y, luego, reemplazar los valores del ejemplo anterior por los suyos propios:

* [Crear un inquilino de B2C](active-directory-b2c-get-started.md)y utilizar el nombre del inquilino en las solicitudes.
* [Crear una aplicación](active-directory-b2c-app-registration.md) para obtener un identificador de aplicación y un elemento redirect_uri. Deseará incluir una **aplicación web/api web** en su aplicación y, opcionalmente, crear un **secreto de aplicación**.
* [Crear directivas](active-directory-b2c-reference-policies.md) para obtener los nombres de las directivas.

## <a name="samples"></a>Muestras

* [Creación de una aplicación de una página mediante Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Creación de una aplicación de una página mediante .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)




<!--HONumber=Feb17_HO2-->


