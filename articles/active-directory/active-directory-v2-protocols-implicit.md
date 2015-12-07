<properties
	pageTitle="Flujo implícito del modelo de aplicaciones v2.0 | Microsoft Azure"
	description="Creación de aplicaciones web mediante la implementación del flujo implícito de Azure AD para aplicaciones de una sola página."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="dastrock"/>

# Versión preliminar del modelo de aplicaciones v2.0: protocolos, flujo implícito
Con el modelo de aplicaciones v2.0 de Azure AD, puede iniciar la sesión de los usuarios en sus aplicaciones de una página tanto con cuentas de Microsoft personales como profesionales o educativas. Las aplicaciones de una sola página y otras aplicaciones JavaScript que se ejecutan principalmente en un explorador, se enfrentan con algunos retos interesantes por lo que se refiere a la autenticación:

- Las características de seguridad de estas aplicaciones son significativamente diferentes de las de las aplicaciones web tradicionales basadas en el servidor.
- Muchos proveedores de identidades y servidores de autorización no admiten solicitudes CORS por razones de seguridad bien documentadas.
- Los redireccionamientos del explorador a una página completa fuera de la aplicación se vuelven especialmente invasivos para la experiencia del usuario.

Para estas aplicaciones (por ejemplo, AngularJS, Ember.js, React.js, etc.), Azure AD admite el flujo de concesión implícita de OAuth 2.0. El flujo implícito se describe en la [especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). Su principal ventaja es que hace posible que la aplicación obtenga tokens de Azure AD sin necesidad de realizar un intercambio de credenciales con el servidor back-end. De esta forma, la aplicación puede iniciar la sesión del usuario, mantenerla y recibir tokens para otras API web, y todo dentro del código de cliente de JavaScript. Existen algunas consideraciones de seguridad importantes que se deben tener en cuenta al usar el flujo implícito, especialmente en lo referente a la [suplantación de identidad del cliente](http://tools.ietf.org/html/rfc6749#section-10.3) y el [usuario](http://tools.ietf.org/html/rfc6749#section-10.3).

Si quiere usar el flujo implícito y Azure AD para agregar autenticación a la aplicación JavaScript, se recomienda el uso de nuestra biblioteca JavaScript de código abierto, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). Puede encontrar [aquí](active-directory-appmodel-v2-overview.md#getting-started) algunos tutoriales de AngularJS que le ayudarán a empezar.

Sin embargo, si prefiere no usar una biblioteca en su aplicación de una página y enviar mensajes de protocolo usted mismo, siga estos pasos generales.

> [AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para ver instrucciones sobre cómo realizar la integración con el servicio Azure AD, disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

## Enviar la solicitud de inicio de sesión

Al principio, para iniciar la sesión del usuario en la aplicación, puede enviar una solicitud de autorización [OpenID Connect](active-directory-v2-protocols-oidc.md) y obtener un `id_token` del punto de conexión v2.0:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=openid%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
&state=12345
&nonce=678910
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| response\_type | requerido | Debe incluir `id_token` para el inicio de sesión en OpenID Connect. También puede incluir otros elementos response\_type como `code`. |
| redirect\_uri | requerido | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Asegúrese de que incluye el ámbito `openid` para OpenID Connect, lo que se traduce en el permiso de "inicio de sesión y lectura de su perfil" en la IU de consentimiento. También puede incluir otros ámbitos en esta solicitud para solicitar el consentimiento. |
| valor de seguridad | requerido | Un valor incluido en la solicitud, generada por la aplicación, que se incluirá en el id\_token resultante como una notificación. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que puede utilizarse para identificar el origen de la solicitud. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el authorization\_code resultante de nuevo a tu aplicación. Puede ser uno de 'query', 'form\_post' o 'fragment'.  
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. Los únicos valores válidos en este momento son 'login', 'none' y 'consent'. `prompt=login` obligará al usuario a escribir sus credenciales en esa solicitud, negando el inicio de sesión único. En el lado opuesto, `prompt=none` se asegurará de que al usuario no se le presenta ninguna solicitud interactiva del tipo que sea. Si no se puede completar la solicitud sin notificaciones mediante el inicio de sesión único, el punto de conexión v2.0 devolverá un error. `prompt=consent` desencadenará el cuadro de diálogo de consentimiento de OAuth después de que el usuario inicia sesión, y solicitará a este que conceda permisos a la aplicación. |
| login\_hint | opcional | Puede usarse para rellenar previamente el campo de nombre de usuario y dirección de correo electrónico de la página de inicio de sesión del usuario. |

En este punto, se le pedirá al usuario que escriba sus credenciales y que complete la autenticación. El punto de conexión v2.0 también garantiza que el usuario ha dado su consentimiento a los permisos indicados en el parámetro de la consulta `scope`. Si el usuario no ha dado su consentimiento a alguno de esos permisos, se le solicitará al usuario su consentimiento para los permisos necesarios. Aquí puede encontrar información detallada sobre los [permisos, el consentimiento y las aplicaciones multiempresa](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y otorga su consentimiento, el punto de conexión v2.0 devuelve una respuesta a su aplicación en el `redirect_uri` indicado mediante el método especificado en el parámetro `response_mode`.

Una respuesta correcta al usar `response_mode=fragment` tiene el siguiente aspecto:

```
GET https://localhost/myapp/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&id_token_expires_in=3600

```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| ID\_token | El id\_token que solicitó la aplicación. Puede usar el id\_token para comprobar la identidad del usuario y comenzar una sesión con el usuario. Se incluyen más detalles sobre id\_tokens y su contenido en la [referencia a los tokens del punto de conexión v2.0](active-directory-v2-tokens.md). |
| session\_state | Un valor único que identifica la sesión del usuario actual. Este valor es un GUID, pero se debe tratar como un valor opaco que se pasa sin analizar. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |
| id\_token\_expires\_in | Durante cuánto tiempo es válido el token de id. (en segundos). |

Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

## Validar el id\_token
Recibir un solo id\_token no es suficiente para autenticar al usuario; debe validar la firma del id\_token y comprobar las notificaciones en el token según los requisitos de su aplicación. El punto de conexión v2.0 usa [tokens web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y criptografía de clave pública para firmar los tokens y comprobar que son válidos.

El modelo de aplicaciones v2.0 tiene un extremo de metadatos OpenID Connect, que permite a una aplicación obtener información sobre el modelo de aplicaciones v2.0 en tiempo de ejecución. En esta información se incluyen los extremos, los contenidos del token y las claves de firma de los token. El extremo de metadatos contiene un documento JSON ubicado en:

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Una de las propiedades de este documento de configuración es el `jwks_uri`, cuyo valor para el modelo de aplicaciones v2.0 será:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

Puede usar las claves públicas RSA256 ubicadas en este extremo para validar la firma del id\_token. Hay varias claves enumeradas en este punto de conexión en cualquier momento dado, cada una identificada con un `kid`. El encabezado del id\_token también contiene una notificación `kid`, que indica cuál de estas claves se usó para firmar el id\_token.

Consulte la [referencia a los tokens del modelo de aplicaciones v2.0](active-directory-v2-tokens.md) para más información, por ejemplo, los puntos [Validación de tokens](active-directory-v2-tokens.md#validating-tokens) e [Información importante sobre la sustitución de la clave de firma](active-directory-v2-tokens.md#validating-tokens). <!--TODO: Improve the information on this-->

Puede elegir validar el `id_token` en el código de cliente, pero una práctica común es enviar el `id_token` a un servidor back-end y realizar ahí las validaciones. Existen muchas bibliotecas disponibles para analizar y validar los tokens JWT. Una vez haya validado la firma del id\_token, hay algunas notificaciones que tendrá que comprobar:

- Debería validar la notificación `nonce` para evitar ataques de reproducción de token. Su valor debería ser el que especificó en la solicitud de inicio de sesión.
- Debería validar la notificación `aud` para asegurarse de que se ha emitido el id\_token para su aplicación. Su valor debería ser el `client_id` de su aplicación.
- Debería validar las notificaciones `iat` y `exp` para asegurarse de que el id\_token no ha expirado.

Se recomienda que valide notificaciones adicionales según su escenario. Algunas validaciones comunes incluyen:

- Asegurarse de que la organización/el usuario se ha registrado en la aplicación.
- Asegurarse de que el usuario tiene la autorización/los privilegios adecuados
- Asegurarse de que se haya producido un determinado nivel de autenticación, como la autenticación multifactor.

Para más información sobre las notificaciones en un id\_token, consulte la [referencia a los tokens del modelo de aplicaciones v2.0](active-directory-v2-tokens.md).

Una vez que haya validado completamente el id\_token, puede iniciar una sesión con el usuario y usar las notificaciones en el id\_token para obtener información sobre el usuario en su aplicación. Esta información puede utilizarse para su visualización, registros, autorizaciones, etc.

## Obtención de tokens de acceso

Ahora que ha iniciado la sesión del usuario en su aplicación de una página, puede obtener tokens de acceso para llamar a las API web protegidas por Azure AD, como las de [Microsoft Graph](https://graph.microsoft.io). En el flujo normal de OpenID Connect y OAuth, haría esto mediante una solicitud al punto de conexión v2.0 `/token`. Sin embargo, el punto de conexión v2.0 no admite solicitudes CORS, así que las llamadas a AJAX para obtener y actualizar los tokens están fuera de nuestro alcance. En su lugar, puede usar el flujo implícito en un iframe oculto para obtener nuevos tokens para otras API web:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read  // The space separated scope(s) you want an access token for
&state=12345
&prompt=none
&login_hint=joe.user@outlook.com
&domain_hint=consumers
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| response\_type | requerido | Debe ser `token` para el flujo implícito. |
| redirect\_uri | requerido | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista ámbitos separados por espacios que necesita en el token de acceso. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el authorization\_code resultante de nuevo a tu aplicación. Puede ser uno de 'query', 'form\_post' o 'fragment'.  
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| símbolo del sistema | requerido | Aquí usamos `prompt=none` para especificar que Azure AD no debe presentar ninguna interfaz de usuario. Dado que esta solicitud se produce en un iframe oculto, querrá asegurarse de que si el token no se puede adquirir sin notificaciones, Azure AD debe devolver un error en lugar de pedir al usuario que inicie sesión. |
| login\_hint | requerido | Necesario para la autenticación sin notificaciones. Esta sugerencia ayuda a Azure AD a distinguir entre varias cuentas si el usuario tiene una sesión activa con más de una. El valor proporcionado aquí puede ser el valor exacto que recibe en la notificación `preferred_uesrname` del id\_token. |
| domain\_hint | requerido | Necesario para la autenticación sin notificaciones. En este flujo, domain\_hint puede ser uno de dos valores: `consumers` o `organizations`. Si el valor de la notificación `tid` en el `id_token` que recibió era exactamente '9188040d-6c67-4c5b-b112-36a304b66dad', use `consumers` como domain\_hint. De lo contrario, use `organizations`. |

Gracias al parámetro `prompt=none`, esta solicitud tendrá éxito o dará error inmediatamente y volverá a la aplicación. Se enviará una respuesta correcta a la aplicación en el `redirect_uri` indicado, mediante el método especificado en el parámetro `response_mode`.

Una respuesta correcta al usar `response_mode=fragment` tiene el siguiente aspecto:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the access_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&expires_in=3600
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read

```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | El token que solicitó la aplicación. |
| session\_state | Un valor único que identifica la sesión del usuario actual. Este valor es un GUID, pero se debe tratar como un valor opaco que se pasa sin analizar. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |
| ámbito | Los ámbitos para los que el token de acceso es válido. |

Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente. En el caso de `prompt=none`, un error esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

Si recibe este error en la solicitud de iframe, el usuario debe iniciar sesión de nuevo de manera interactiva para recuperar un nuevo token. Puede elegir tratar este caso de la manera que más sentido tenga para su aplicación.

## Actualización de tokens

Tanto los elementos `id_token` como `access_token` caducan tras un corto período de tiempo, así que su aplicación debe estar preparada para actualizar estos tokens de manera periódica. Para actualizar cualquier tipo de token, puede realizar la misma solicitud de iframe oculto que antes mediante el parámetro `prompt=none` para controlar el comportamiento de Azure AD. Si quiere recibir un nuevo `id_token`, asegúrese de usar `response_type=id_token` y `scope=openid`, así como un parámetro `nonce`.


## Enviar una solicitud de cierre de sesión

Actualmente, la versión preliminar del modelo de aplicaciones v2.0 no admite el `end_session_endpoint` de OpenIdConnect. Esto significa que la aplicación no puede enviar una solicitud al extremo v2.0 para finalizar una sesión de usuario y borrar las cookies establecidas por el extremo v2.0. Para cerrar la sesión de un usuario, la aplicación puede simplemente finalizar su propia sesión con el usuario y dejar la sesión del usuario con el extremo v2.0 intacta. La próxima vez que el usuario intente iniciar sesión, verá una página de "elegir cuenta", en la que se enumeran sus cuentas con inicio de sesión activo. En esa página, el usuario puede elegir cerrar la sesión de cualquier cuenta, finalizando así la sesión con el extremo v2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

<!---HONumber=AcomDC_1125_2015-->