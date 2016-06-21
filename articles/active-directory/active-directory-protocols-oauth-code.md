<properties
	pageTitle="Información general sobre el protocolo .NET de Azure AD | Microsoft Azure"
	description="En este artículo se describe cómo utilizar mensajes HTTP para autorizar el acceso a aplicaciones y API web en su inquilino con Azure Active Directory y OAuth 2.0."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>


# Autorización del acceso a aplicaciones web mediante OAuth 2.0 y Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Azure Active Directory (Azure AD) utiliza el protocolo OAuth 2.0 para poder autorizar el acceso a aplicaciones y API web en su inquilino de Azure AD. En esta guía, que es independiente del lenguaje, describe cómo enviar y recibir mensajes HTTP sin usar ninguna de nuestras bibliotecas de código abierto.

El flujo de código de autorización de OAuth 2.0 se describe en la [sección 4.1 de la especificación de OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Se usa para realizar la autenticación y autorización en la mayoría de los tipos de aplicación, incluidas las aplicaciones web y las aplicaciones instaladas de forma nativa.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## Flujo de autorización de OAuth 2.0

A nivel general, el flujo de autorización completo de una aplicación tiene un aspecto similar al siguiente:

![Flujo de código de autenticación de OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## Solicitud de un código de autorización

El flujo del código de autorización comienza con el cliente dirigiendo al usuario al punto de conexión `/authorize`. En esta solicitud, el cliente indica los permisos que necesita obtener del usuario. Puede obtener los puntos de conexión de OAuth 2.0 desde la página de la aplicación del Portal de Azure clásico, en el botón **Ver extremos** del cajón inferior.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| tenant | requerido | El valor de `{tenant}` de la ruta de acceso de la solicitud se puede usar para controlar quién puede iniciar sesión en la aplicación. Los valores permitidos son los identificadores de inquilino; por ejemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` o `common` para los tokens independientes del inquilino. |
| client\_id | requerido | Identificador de aplicación asignado a la aplicación cuando se registra en Azure AD. Puede encontrarlo en el Portal de administración de Azure. Haga clic en **Active Directory**, el directorio, la aplicación y, luego, **Configurar**. |
| response\_type | requerido | Debe incluir `code` para el flujo de código de autorización. |
| redirect\_uri | recomendado | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. En el caso de las aplicaciones nativas y móviles, debe utilizar el valor predeterminado de `urn:ietf:wg:oauth:2.0:oob`. |
| ámbito | requerido | Lista separada por espacios de [ámbitos](active-directory-v2-scopes.md) que desea que el usuario dé su consentimiento. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el token resultante de nuevo a la aplicación. Puede ser `query` o `form_post`. |
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para [evitar los ataques de falsificación de solicitudes entre sitios](http://tools.ietf.org/html/rfc6749#section-10.12). El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. Los únicos valores válidos en este momento son 'login', 'none' y 'consent'. `prompt=login` obligará al usuario a escribir sus credenciales en esa solicitud, negando el inicio de sesión único. En el lado opuesto, `prompt=none` se asegurará de que al usuario no se le presenta ninguna solicitud interactiva del tipo que sea. Si no se puede completar la solicitud sin notificaciones mediante el inicio de sesión único, el punto de conexión v2.0 devolverá un error. `prompt=consent` desencadenará el cuadro de diálogo de consentimiento de OAuth después de que el usuario inicia sesión, y solicitará a este que conceda permisos a la aplicación. |
| login\_hint | opcional | Puede usarse para rellenar previamente el campo de nombre de usuario y dirección de correo electrónico de la página de inicio de sesión del usuario, si sabe su nombre de usuario con antelación. A menudo, las aplicaciones usarán este parámetro durante la reautenticación, dado que ya han extraído el nombre de usuario de un inicio de sesión anterior mediante la notificación `preferred_username`. |
| domain\_hint | opcional | Puede ser uno de `consumers` o `organizations`. Si se incluye, omitirá el proceso de detección basado en correo electrónico por el que pasa el usuario en la página de inicio de sesión de v2.0, con lo que la experiencia de usuario será ligeramente más sencilla. A menudo, las aplicaciones usarán este parámetro durante la reautenticación extrayendo `tid` de un inicio de sesión anterior. Si el valor de la notificación `tid` es `9188040d-6c67-4c5b-b112-36a304b66dad`, debe usar `domain_hint=consumers`. De lo contrario, use `domain_hint=organizations`. |

> [AZURE.NOTE] Si el usuario forma parte de una organización, un administrador de la organización puede dar su consentimiento o rechazar en nombre del usuario, o bien permitir que el usuario dé su consentimiento. El usuario tiene la opción de dar su consentimiento solo cuando el administrador lo permite.

En este punto, se pedirá al usuario que escriba sus credenciales y dé su consentimiento a los permisos indicados en el parámetro de consulta `scope`. Una vez que el usuario se autentica y da su consentimiento, Azure AD envía una respuesta a la aplicación en la dirección `redirect_uri` de la solicitud.

### Respuesta correcta

Una respuesta correcta podría tener el siguiente aspecto:

```
http://localhost:12345/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&state=12345
&session_state=733ad279-b681-49c3-9215-951abf94d2c5
```

| Parámetro | Descripción |
| -----------------------| --------------- |
| admin\_consent | El valor es True si un administrador ha dado su consentimiento a una solicitud de consentimiento.|
| código | El código de autorización que solicitó la aplicación. La aplicación puede utilizar el código de autorización con el fin de solicitar un token de acceso para el recurso de destino. |
| session\_state | Un valor único que identifica la sesión del usuario actual. Este valor es un GUID, pero se debe tratar como un valor opaco que se pasa sin analizar. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores de estado de la solicitud y la respuesta son idénticos.
Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. Se recomienda que la aplicación compruebe que los valores de estado de la solicitud y la respuesta son idénticos.

### Respuesta de error

Las respuestas de error también pueden enviarse al elemento `redirect_uri` para que la aplicación pueda controlarlas adecuadamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

## Uso del código de autorización para solicitar un token de acceso

Ahora que ha obtenido un código de autorización y el usuario le ha concedido permiso, puede canjear el código por un token de acceso al recurso deseado mediante el envío de una solicitud POST al punto de conexión `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: client_secret only required for web apps
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | requerido | El valor de `{tenant}` de la ruta de acceso de la solicitud se puede usar para controlar quién puede iniciar sesión en la aplicación. Los valores permitidos son los identificadores de inquilino; por ejemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` o `common` para los tokens independientes del inquilino. |
| client\_id | requerido | Identificador de aplicación asignado a la aplicación cuando se registra en Azure AD. Puede encontrarlo en el Portal de Azure clásico. Haga clic en **Active Directory**, el directorio, la aplicación y, luego, **Configurar**. |
| grant\_type | requerido | Debe ser `authorization_code` para el flujo de código de autorización. |
| código | requerido | El `authorization_code` que obtuvo en la sección anterior. |
| redirect\_uri | requerido | El mismo valor de `redirect_uri` que usó para obtener `authorization_code`. |
| client\_secret | necesario para las aplicaciones web | El secreto de la aplicación que creó en el portal de registro de aplicaciones para su aplicación. No debería utilizarse en una aplicación nativa, porque los client\_secrets no se pueden almacenar de forma confiable en los dispositivos. Es necesario para aplicaciones y API web, que tienen la capacidad de almacenar el `client_secret` de forma segura en el servidor. |


### Respuesta correcta

Una respuesta correcta podría tener el siguiente aspecto:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar a los recursos protegidos, como una API web. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es portador. Para obtener más información sobre los tokens de portador, consulte [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) (Marco de autorización de OAuth2.0: uso del token de portador [RFC 6750]). |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |
| ámbito | Permisos de suplantación concedidos a la aplicación cliente. El permiso predeterminado es `user_impersonation`. El propietario del recurso protegido puede registrar valores adicionales en Azure AD.|
| refresh\_token | Un token de actualización de OAuth 2.0. La aplicación puede utilizar este token para obtener más tokens de acceso una vez que expire el token de acceso actual. Los tokens de actualización son de larga duración y pueden usarse para conservar el acceso a los recursos durante largos periodos. |
| ID\_token | Un token web JSON (JWT) sin firmar. La aplicación puede descodificar base64Url en los segmentos de este token para solicitar información acerca del usuario que ha iniciado sesión. La aplicación puede almacenar en caché los valores y mostrarlos, pero no debe confiar en ellos para cualquier autorización o límite de seguridad. |

### Notificaciones de token JWT
El token JWT del valor del parámetro `id_token` se puede descodificar en las siguientes notificaciones:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

El parámetro `id_token` incluye los siguientes tipos de notificación. Para obtener más información sobre los tokens web JSON, consulte la [especificación de borrador de JWT IETF](http://go.microsoft.com/fwlink/?LinkId=392344). Para obtener más información sobre los tipos de tokens y notificaciones, consulte [Tipos de notificaciones y tokens admitidos](active-directory-token-and-claims.md).

| Tipo de notificación | Descripción |
|------------|-------------|
| aud | Se trata de la audiencia del token. Cuando se emite el token a una aplicación cliente, la audiencia es el `client_id` del cliente.
| exp | Se trata de la fecha de expiración. La hora a la que expira el token. Para que el token sea válido, la fecha y hora actual debe ser menor o igual que el valor de `exp`. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento en que se emitió el token. |
| family\_name | Se trata de los apellidos del usuario. La aplicación puede mostrar este valor. |
| given\_name | Se trata del nombre del usuario. La aplicación puede mostrar este valor. |
| iat | Significa que el token se emite en la hora. La hora a la que se generó el token JWT. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento en que se emitió el token. |
| iss | Identifica al emisor del token. |
| nbf | Significa que el token no se emite antes de la hora. La hora a la que el token surte efecto. Para que el token sea válido, la fecha y hora actual debe ser menor o igual que el valor de Nbf. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento en que se emitió el token. |
| oid | Se trata del identificador de objeto (id.) del objeto de usuario en Azure AD. |
| sub | Se trata del identificador de asunto del token. Es un identificador persistente e inmutable del usuario que describe el token. Use este valor en la lógica de caché. |
| tid | Se trata del identificador (id.) del inquilino de Azure AD que emitió el token. |
| unique\_name | Se trata de un identificador único que se puede mostrar al usuario. Por lo general, suele ser un nombre principal de usuario (UPN). |
| upn | Se trata del nombre principal del usuario. |
| ver | Se trata de la versión. Es la versión del token JWT, normalmente, la 1.0. |

### Respuesta de error

Una respuesta de error de muestra podría tener este aspecto:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |
| error\_codes | Una lista de los códigos de error específicos de STS que pueden ayudar en los diagnósticos. |
| timestamp | La hora a la que se produjo el error. |
| trace\_id | Un identificador exclusivo para la solicitud que puede ayudar en los diagnósticos. |
| correlation\_id | Un identificador exclusivo para la solicitud que puede ayudar en los diagnósticos entre componentes.|

## Uso del token de acceso para acceder al recurso

Ahora que ha obtenido correctamente un `access_token`, puede usar el token en solicitudes a las API web mediante su inclusión en el encabezado `Authorization`. En la especificación [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) se explica cómo utilizar los tokens de portador en solicitudes HTTP para acceder a recursos protegidos.

### Solicitud de muestra

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

## Actualización de los tokens de acceso

Los tokens de acceso tienen una duración breve y debe actualizarlos una vez expiren para seguir teniendo acceso a los recursos. Puede hacerlo el `access_token` enviando otra solicitud `POST` al punto de conexión `/token`, pero esta vez proporcionando el `refresh_token` en lugar del `code`.

No se proporciona la duración de un token de actualización y varía en función de la configuración de directiva. Además, Azure AD revoca la hora de concesión del código de la autorización. La aplicación debe esperar y controlar los casos en los que se produce un error en la solicitud de un nuevo token de acceso. En este caso, debe devolver el código que solicita un nuevo token de acceso.

Una solicitud de muestra al punto de conexión **específico del inquilino** (también puede utilizar el punto de conexión **común**) para obtener un nuevo token de acceso con un token de actualización similar al siguiente:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Parámetro | Descripción |
|-----------|-------------|
| access\_token | El nuevo token de acceso que se solicitó.|
| expires\_in | Duración restante del token en segundos. Un valor típico es 3600 (una hora). |
| expires\_on | La fecha y hora en que expira el token. La fecha se representa como el número de segundos desde 1970-01-01T0:0:0Z UTC hasta la fecha de expiración. |
| refresh\_token | Nuevo refresh\_token de OAuth 2.0 que puede usarse para solicitar nuevos tokens de acceso cuando expira en esta respuesta. |
| resource | Identifica el recurso protegido que el token de acceso puede utilizar para acceder. |
| ámbito | Permisos de suplantación concedidos a la aplicación cliente nativa. El permiso predeterminado es **user\_impersonation**. El propietario del recurso de destino puede registrar valores alternativos en Azure AD. |
| token\_type | El tipo de token. El único valor admitido es **bearer**. |

### Respuesta correcta

Una respuesta de token correcta tendrá un aspecto similar al siguiente:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### Respuesta de error

Una respuesta de error de muestra podría tener este aspecto:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

<!---HONumber=AcomDC_0608_2016-->