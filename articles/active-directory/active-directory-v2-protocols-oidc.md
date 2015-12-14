
<properties
	pageTitle="Protocolo de OpenID Connect del modelo de aplicaciones v2.0 | Microsoft Azure"
	description="Creación de aplicaciones web mediante la implementación del protocolo de autenticación OpenID Connect de Azure AD."
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

# Versión preliminar del modelo de aplicaciones v2.0: protocolos, OpenID Connect
OpenID Connect es un protocolo de autenticación basado en OAuth 2.0 que puede usarse para que los usuarios inicien sesión de forma segura en las aplicaciones web. Mediante la implementación de OpenID Connect del modelo de aplicaciones v2.0, puede agregar inicio de sesión y acceso a API a las aplicaciones basadas en web. Esta guía le enseñará a hacerlo de manera independiente del lenguaje, y describe cómo enviar y recibir mensajes HTTP sin usar ninguna de nuestras bibliotecas de código abierto.

> [AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrar con el servicio de Azure AD disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) amplía el protocolo de *autorización* de OAuth 2.0 para usarlo como un protocolo de *autenticación*, lo que le permite realizar inicios de sesión únicos mediante OAuth. Presenta el concepto de un `id_token`, que es un token de seguridad que permite al cliente comprobar la identidad del usuario y obtener información del perfil básica sobre el usuario. Puesto que amplía OAuth 2.0, también permite que las aplicaciones adquieran de forma segura **access\_tokens** que se pueden usar para acceder a los recursos protegidos mediante un [servidor de autorización](active-directory-v2-protocols.md#the-basics). Nosotros recomendamos OpenID Connect si va a crear una [aplicación web](active-directory-v2-flows.md#web-apps) que está hospedada en un servidor y a la que se accede mediante un explorador.

## Enviar la solicitud de inicio de sesión
Cuando su aplicación web deba autenticar al usuario, puede dirigirlo al punto de conexión `/authorize`. Esta solicitud es similar al primer segmento del [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md), con algunas diferencias importantes:

- La solicitud debe incluir el ámbito `openid` en el parámetro `scope`.
- El parámetro `response_type` debe incluir `id_token`.
- La solicitud debe incluir el parámetro `nonce`.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid										 // Translates to the 'Read your profile' permission
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
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

Una respuesta correcta al usar `response_mode=query` tiene el siguiente aspecto:

```
GET https://localhost/myapp/?
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
GET https://localhost/myapp/?
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

Consulte la [referencia a los tokens del modelo de aplicaciones v2.0](active-directory-v2-tokens.md) para más información, por ejemplo, los puntos [Validación de tokens](active-directory-v2-tokens.md#validating-tokens) e [ Información importante sobre la sustitución de la clave de firma](active-directory-v2-tokens.md#validating-tokens). <!--TODO: Improve the information on this-->

Una vez haya validado la firma del id\_token, hay algunas notificaciones que tendrá que comprobar:

- Debería validar la notificación `nonce` para evitar ataques de reproducción de token. Su valor debería ser el que especificó en la solicitud de inicio de sesión.
- Debería validar la notificación `aud` para asegurarse de que se ha emitido el id\_token para su aplicación. Su valor debería ser el `client_id` de su aplicación.
- Debería validar las notificaciones `iat` y `exp` para asegurarse de que el id\_token no ha expirado.

Se recomienda que valide notificaciones adicionales según su escenario. Algunas validaciones comunes incluyen:

- Asegurarse de que la organización/el usuario se ha registrado en la aplicación.
- Asegurarse de que el usuario tiene la autorización/los privilegios adecuados
- Asegurarse de que se haya producido un determinado nivel de autenticación, como la autenticación multifactor.

Para más información sobre las notificaciones en un id\_token, consulte la [referencia a los tokens del modelo de aplicaciones v2.0](active-directory-v2-tokens.md).

Una vez que haya validado completamente el id\_token, puede iniciar una sesión con el usuario y usar las notificaciones en el id\_token para obtener información sobre el usuario en su aplicación. Esta información puede utilizarse para su visualización, registros, autorizaciones, etc.

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

## Diagrama de resumen de inicio de sesión
El flujo de inicio de sesión más básico contiene los siguientes pasos:

![Calles OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## Obtención de tokens de acceso
Muchas aplicaciones web no solo deben iniciar la sesión del usuario, sino también obtener acceso a un servicio web en nombre de ese usuario mediante OAuth. En este escenario se combina OpenID Connect para la autenticación de usuario, al tiempo que se adquiere a la vez un authorization\_code que puede usarse para obtener access\_tokens mediante el flujo de código de autorización de OAuth. Para adquirir tokens de acceso, debe modificar ligeramente la solicitud anterior de inicio de sesión:


```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20										 
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

Con la inclusión de los ámbitos de permiso en la solicitud y el uso de `response_type=code+id_token`, el punto de conexión v2.0 garantizará que el usuario ha dado su consentimiento a los permisos indicados en el parámetro de consulta `scope`, y devolverá a la aplicación un código de autorización para intercambiar por un token de acceso.

Una respuesta correcta al usar `response_mode=query` tiene el siguiente aspecto:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&id_token_expires_in=3599

```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| ID\_token | El id\_token que solicitó la aplicación. Puede usar el id\_token para comprobar la identidad del usuario y comenzar una sesión con el usuario. Se incluyen más detalles sobre id\_tokens y su contenido en la [referencia a los tokens del modelo de aplicaciones v2.0](active-directory-v2-tokens.md). |
| código | El authorization\_code que solicitó la aplicación. La aplicación puede utilizar el código de autorización para solicitar un token de acceso para el recurso de destino. Los authorization\_codes son de muy corta duración, normalmente expiran después de unos 10 minutos. |
| session\_state | Un valor único que identifica la sesión del usuario actual. Este valor es un GUID, pero se debe tratar como un valor opaco que se pasa sin analizar. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |
| id\_token\_expires\_in | Durante cuánto tiempo es válido el token de id. (en segundos). |

Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

Una vez que ha obtenido un `code` de autorización y un `id_token`, puede iniciar la sesión del usuario y obtener tokens de acceso en su nombre. Para iniciar la sesión del usuario, debe validar el `id_token` exactamente como se ha descrito [anteriormente](#validating-the-id-token). Para obtener tokens de acceso, puede seguir los pasos que se describen en nuestra [documentación del protocolo OAuth](active-directory-v2-protocols-oidc.md#request-an-access-token).

## Diagrama de resumen de adquisición de tokens

Como referencia, el flujo completo de adquisición de tokens e inicio de sesión de OpenID Connect es parecido a este:

![Calles OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

<!---HONumber=AcomDC_1203_2015-->