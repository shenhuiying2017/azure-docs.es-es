
<properties
	pageTitle="Protocolo OAuth del modelo de aplicaciones v2.0 | Microsoft Azure"
	description="Creación de aplicaciones web mediante la implementación del protocolo de autenticación OAuth 2.0 de Azure AD."
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

# Versión preliminar del modelo de aplicaciones v2.0: protocolos, flujo de código de autorización de OAuth 2.0

La concesión de un código de autorización de OAuth 2.0 se puede usar en aplicaciones que se instalan en un dispositivo para obtener acceso a recursos protegidos, como las API web. Mediante la implementación de OAuth 2.0 del modelo de aplicaciones v2.0, puede agregar inicio de sesión y acceso a API a las aplicaciones móviles y de escritorio. Esta guía, que es independiente del lenguaje, describe cómo enviar y recibir mensajes HTTP sin usar ninguna de nuestras bibliotecas de código abierto.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrarse en el servicio de Azure AD, disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

El flujo de código de autorización de OAuth 2.0 se describe en la [sección 4.1 de la especificación OAuth 2.0](http://tools.ietf.org/html/rfc6749). Se usa para realizar la autenticación y la autorización en la mayoría de los tipos de aplicación, incluidas las [aplicaciones web](active-directory-v2-flows.md#web-apps) y las [aplicaciones instaladas de forma nativa](active-directory-v2-flows.md#mobile-and-native-apps). Permite que las aplicaciones adquieran de forma segura access\_tokens que se puedan utilizar para acceder a los recursos protegidos mediante el modelo de aplicación v2.0.



## Solicitar un código de autorización
El flujo del código de autorización comienza con el cliente dirigiendo al usuario al punto de conexión `/authorize`. En esta solicitud, el cliente indica los permisos que necesita adquirir del usuario:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		  // Your registered Application Id
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob 	  // Your registered Redirect Uri, url encoded
&response_mode=query							        // 'query', 'form_post', or 'fragment'
&scope=											     // See table below
openid%20
offline_access%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				   // Any value provided by your app
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| response\_type | requerido | Debe incluir `code` para el flujo de código de autorización. |
| redirect\_uri | requerido | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Un único valor de ámbito indica al extremo v2.0 tanto el recurso como los permisos para que se solicite ese recurso. Los ámbitos adoptan la forma `<app identifier URI>/<scope value>`. En el ejemplo anterior, se usa el identificador de la aplicación para la API de Azure AD Graph, `https://graph.windows.net`, y se solicitan dos permisos: `directory.read` y `directory.write`. Para obtener una explicación más detallada de los ámbitos, consulte la [referencia al ámbito del modelo de aplicaciones v2.0](active-directory-v2-scopes.md). |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el authorization\_code resultante de nuevo a tu aplicación. Puede ser uno de 'query', 'form\_post' o 'fragment'.
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. Los únicos valores válidos en este momento son 'login', 'none' y 'consent'. `prompt=login` obligará al usuario a escribir sus credenciales en esa solicitud, negando el inicio de sesión único. En el lado opuesto, `prompt=none` se asegurará de que al usuario no se le presenta ninguna solicitud interactiva del tipo que sea. Si no se puede completar la solicitud sin notificaciones mediante el inicio de sesión único, el punto de conexión v2.0 devolverá un error. `prompt=consent` desencadenará el cuadro de diálogo de consentimiento de OAuth después de que el usuario inicia sesión, y solicitará a este que conceda permisos a la aplicación. |
| login\_hint | opcional | Puede usarse para rellenar previamente el campo de nombre de usuario y dirección de correo electrónico de la página de inicio de sesión del usuario. |

En este punto, se le pedirá al usuario que escriba sus credenciales y que complete la autenticación. El punto de conexión v2.0 también garantiza que el usuario ha dado su consentimiento a los permisos indicados en el parámetro de la consulta `scope`. Si el usuario no ha dado su consentimiento a alguno de esos permisos, se le solicitará al usuario su consentimiento para los permisos necesarios. Aquí puede encontrar información detallada sobre los [permisos, el consentimiento y las aplicaciones multiempresa](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y otorga su consentimiento, el punto de conexión v2.0 devolverá una respuesta a la aplicación en el `redirect_uri` indicado mediante el método especificado en el parámetro `response_mode`.

Una respuesta correcta al usar `response_mode=query` tiene el siguiente aspecto:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												      // the value provided in the request
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| código | El authorization\_code que solicitó la aplicación. La aplicación puede utilizar el código de autorización para solicitar un token de acceso para el recurso de destino. Los authorization\_codes son de muy corta duración, normalmente expiran después de unos 10 minutos. |
| session\_state | Un valor único que identifica la sesión del usuario actual. Este valor es un GUID, pero se debe tratar como un valor opaco que se pasa sin analizar. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

## Solicitar un token de acceso
Ahora que ha adquirido un código de autorización y el usuario le ha concedido permiso, puede canjear el `code` por un `access_token` al recurso deseado mediante el envío de una solicitud de `POST` al punto de conexión `/token`:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------------- |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| grant\_type | requerido | Debe ser `authorization_code` para el flujo de código de autorización. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Los ámbitos solicitados en esta fase deben ser un subconjunto de los ámbitos solicitados en el primer segmento o un equivalente de este. Si los ámbitos especificados en esta solicitud abarcan varios servidores de recursos, el extremo v2.0 devolverá un token para el recurso especificado en el primer ámbito. Para obtener una explicación más detallada de los ámbitos, consulte [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md). |
| código | requerido | El authorization\_code que adquirió en el primer segmento del flujo. |
| client\_secret | necesario para las aplicaciones web | El secreto de la aplicación que creó en el portal de registro de aplicaciones para su aplicación. No debería utilizarse en una aplicación nativa, porque los client\_secrets no se pueden almacenar de forma confiable en los dispositivos. Es necesario para aplicaciones web y las API web, que tienen la capacidad de almacenar el client\_secret de forma segura en el lado del servidor. |

Una respuesta de token correcta tendrá un aspecto similar al siguiente:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
	"id_token_expires_in": "3599"
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar a los recursos protegidos, como una API web. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es el portador |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |
| ámbito | Los ámbitos para los que el access\_token es válido. |
| refresh\_token | Un token de actualización de OAuth 2.0. La aplicación puede utilizar este token para adquirir tokens de acceso adicionales una vez que expire el token de acceso actual. Los refresh\_tokens son de larga duración y pueden usarse para conservar el acceso a los recursos durante largos períodos de tiempo. Para más información, consulte la [referencia a los tokens v2.0](active-directory-v2-tokens.md). |
| ID\_token | Un token web JSON (JWT) sin firmar. La aplicación puede descodificar base64Url en los segmentos de este token para solicitar información acerca del usuario que ha iniciado sesión. La aplicación puede almacenar en caché los valores y mostrarlos, pero no debe confiar en ellos para cualquier autorización o límite de seguridad. Para más información sobre los id\_tokens, consulte la [referencia a los tokens del modelo de aplicaciones v2.0](active-directory-v2-tokens.md). |
| id\_token\_expires\_in | Durante cuánto tiempo es válido el token de id. (en segundos). |


Las respuestas de error tendrán un aspecto similar al siguiente:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

## Use el token de acceso
Ahora que ha adquirido correctamente un `access_token`, puede usar el token en solicitudes a las API web mediante su inclusión en el encabezado `Authorization`:

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Actualizar el token de acceso
Los Access\_tokens tienen una duración breve y debe actualizarlos una vez expiren para seguir teniendo acceso a los recursos. Puede hacerlo mediante el envío de otra solicitud `POST` al punto de conexión `/token`, esta vez proporcionando el `refresh_token` en lugar del `code`:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | -------- |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| grant\_type | requerido | Debe ser `refresh_token` para este segmento del flujo de código de autorización. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Los ámbitos solicitados en este segmento deben ser un subconjunto de los ámbitos solicitados en el segmento de la solicitud del authorization\_code original o un equivalente de este. Si los ámbitos especificados en esta solicitud abarcan varios servidores de recursos, el extremo v2.0 devolverá un token para el recurso especificado en el primer ámbito. Para obtener una explicación más detallada de los ámbitos, consulte [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md). |
| refresh\_token | requerido | El refresh\_token que adquirió en el segundo segmento del flujo. |
| client\_secret | necesario para las aplicaciones web | El secreto de la aplicación que creó en el portal de registro de aplicaciones para su aplicación. No debería utilizarse en una aplicación nativa, porque los client\_secrets no se pueden almacenar de forma confiable en los dispositivos. Es necesario para aplicaciones web y las API web, que tienen la capacidad de almacenar el client\_secret de forma segura en el lado del servidor. |

Una respuesta de token correcta tendrá un aspecto similar al siguiente:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
	"id_token_expires_in": "3599"
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar a los recursos protegidos, como una API web. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es el portador |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |
| ámbito | Los ámbitos para los que el access\_token es válido. |
| refresh\_token | Un nuevo token de actualización de OAuth 2.0. Debe reemplazar el token de actualización antiguo con este token de actualización recientemente adquirido para asegurar que los tokens de actualización siguen siendo válidos durante tanto tiempo como sea posible. |
| ID\_token | Un token web JSON (JWT) sin firmar. La aplicación puede descodificar base64Url en los segmentos de este token para solicitar información acerca del usuario que ha iniciado sesión. La aplicación puede almacenar en caché los valores y mostrarlos, pero no debe confiar en ellos para cualquier autorización o límite de seguridad. Para más información sobre los id\_tokens, consulte la [referencia a los tokens del modelo de aplicaciones v2.0](active-directory-v2-tokens.md). |
| id\_token\_expires\_in | Durante cuánto tiempo es válido el token de id. (en segundos). |

Las respuestas de error tendrán un aspecto similar al siguiente:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

## Resumen
En un nivel alto, el flujo de autenticación completo para una aplicación nativa o móvil es algo parecido a esto:

![Flujo de código de autenticación de OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

<!---HONumber=AcomDC_1203_2015-->