<properties
	pageTitle="Protocolos del modelo de aplicación v2.0 | Microsoft Azure"
	description="Los protocolos admitidos por la vista previa pública del modelo de aplicaciones v2.0 de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Vista previa del modelo de aplicaciones v2.0: protocolos - OAuth 2.0 y OpenID Connect

El modelo de aplicaciones v2.0 proporciona identidad como servicio para sus aplicaciones al admitir protocolos de normalización industrial, OpenID Connect y OAuth 2.0. Aunque el servicio sea compatible con el estándar, puede haber diferencias sutiles entre cualquiera de las dos implementaciones de estos protocolos. Esta información será útil si decide escribir código enviando y administrando solicitudes HTTP directamente en lugar de usar una de nuestras bibliotecas de código abierto. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrarse en el servicio de Azure AD, disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

## Tokens
La implementación de OAuth 2.0 y OpenID Connect en el modelo de aplicaciones v2.0 hacen un uso generalizado de tokens de portador, incluidos los representados como JWT. Un token portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. En este sentido, el "portador" es cualquier parte que pueda presentar el token. Aunque una parte debe autenticarse primero con Azure AD para recibir el token portador, si no se realizan los pasos necesarios para asegurar el token en la transmisión y el almacenamiento, este puede interceptarse y ser utilizado por un usuario no deseado. Mientras que algunos tokens de seguridad disponen de un mecanismo integrado para evitar ser usados por partes no autorizadas, los tokens portadores no tienen este mecanismo y deben transportarse en un canal seguro como, por ejemplo, la seguridad de la capa de transporte (HTTPS). Si un token portador se transmite sin cifrar, un usuario malintencionado puede utilizar un ataque de tipo "Man in the middle" para adquirir el token y usarlo para obtener acceso sin autorización a un recurso protegido. Los mismos principios de seguridad se aplican al almacenamiento o almacenamiento en caché de tokens portadores para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura. Para otras consideraciones sobre la seguridad de los tokens portadores, consulte la [Sección 5 de RFC 6750](http://tools.ietf.org/html/rfc6750).

Más detalles sobre los diferentes tipos de token que se usan en el modelo de aplicaciones v2.0 disponibles en [la referencia de token del modelo de aplicaciones v2.0](active-directory-v2-tokens.md).

## Conceptos básicos
Todas las aplicaciones que utilicen el modelo de aplicaciones v2.0 tendrán que registrarse en [apps.dev.microsoft.com](https://apps.dev.microsoft.com). El proceso de registro de la aplicación recopilará y asignará algunos valores a la aplicación:

- Un **Id. de aplicación** que identifica de forma única su aplicación
- Un **URI de redireccionamiento** o **identificador de paquete** que puede utilizarse para dirigir las respuestas de nuevo a la aplicación
- Algunos otros valores específicos de cada escenario. Para obtener más detalles, aprenda cómo [registrar una aplicación](active-directory-v2-app-registration.md).

Una vez registrada, la aplicación le comunica a Azure AD mis solicitudes de envío al extremo v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

En casi todos los flujos de OAuth y OpenID Connect hay cuatro partes implicadas en el intercambio:

![Funciones de OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- El **servidor de autorización** es el extremo v2.0. Es responsable de garantizar la identidad del usuario, conceder y revocar el acceso a los recursos y emitir tokens. También se le conoce como proveedor de identidad: controla de forma segura todo lo que tenga que ver con la información del usuario, su acceso y las relaciones de confianza entre las partes de un flujo.
- El **propietario del recurso** suele ser el usuario final. Es la parte que posee los datos y tiene la capacidad de permitir que terceros tengan acceso a esos datos o recursos.
- El **cliente de OAuth** es su aplicación, identificada por su Id. de aplicación. Suele ser la parte con la que interactúa el usuario final y solicita tokens del servidor de autorización. El cliente debe contar con el permiso del propietario del recurso para acceder a este.
- El **servidor de recursos** es donde residen el recurso o los datos. Confía en el servidor de autorización para autenticar y autorizar al cliente de OAuth de forma segura y usa access\_tokens de portador para garantizar que se puede conceder el acceso a un recurso.

## Flujo de código de autorización de OAuth2
El flujo de código de autorización de OAuth 2.0 se describe en la [sección 4.1 de la especificación OAuth 2.0](http://tools.ietf.org/html/rfc6749). Se utiliza para realizar la autenticación y la autorización en la mayoría de los tipos de aplicación, incluidas las [aplicaciones web](active-directory-v2-flows.md#web-apps) y las [aplicaciones instaladas](active-directory-v2-flows.md#mobile-and-native-apps). Permite que las aplicaciones adquieran de forma segura access\_tokens que se puedan utilizar para acceder a los recursos protegidos mediante el modelo de aplicación v2.0.

Aquí está el flujo completo para una aplicación nativa; cada solicitud se detalla en las secciones siguientes: ![Flujo de código de autenticación de OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

#### Solicitar un código de autorización
El flujo del código de autorización comienza con el cliente dirigiendo al usuario al `/authorize`extremo. En esta solicitud, el cliente indica los permisos que necesita adquirir del usuario:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=											   // See table below for scope parameter details.
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value provided by your app
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | requerido | El Id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| response\_type | requerido | Debe incluir `code` para el flujo de código de autorización. |
| redirect\_uri | requerido | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Un único valor de ámbito indica al extremo v2.0 tanto el recurso como los permisos para que se solicite ese recurso. Los ámbitos adoptan la forma `<app identifier URI>/<scope value>`. En el ejemplo anterior, se utiliza el identificador de la aplicación para la API de Azure AD Graph, `https://graph.windows.net`, y se solicitan dos permisos: `directory.read` y `directory.write`. Para obtener una explicación más detallada de los ámbitos, consulte c. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el authorization\_code resultante de nuevo a tu aplicación. Puede ser uno de 'query', 'form\_post' o 'fragment'.
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. El único valor válido en este momento es 'login', lo que obliga al usuario a escribir sus credenciales en esa solicitud. El inicio de sesión único no surtirá efecto. |

En este punto, se le pedirá al usuario que escriba sus credenciales y que complete la autenticación. El extremo v2.0 también garantizará que el usuario haya dado su consentimiento a los permisos indicados en el parámetro de la consulta `scope`. Si el usuario no ha dado su consentimiento a alguno de esos permisos, se le solicitará al usuario su consentimiento para los permisos necesarios. Los detalles sobre los [permisos, el consentimiento y las aplicaciones de varios inquilinos se proporcionan aquí](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y otorga su consentimiento, el extremo v2.0 devolverá una respuesta a su aplicación en el `redirect_uri` indicado utilizando el método especificado en el parámetro `response_mode`.

Una respuesta correcta al usar `response_mode=query` tiene el siguiente aspecto:

```
GET https://localhost/myapp/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  							// the value provided in the request
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| código | El authorization\_code que solicitó la aplicación. La aplicación puede utilizar el código de autorización para solicitar un token de acceso para el recurso de destino. Los authorization\_codes son de muy corta duración, normalmente expiran después de unos 10 minutos. |
| session\_state | Un valor único que identifica la sesión del usuario actual. Este valor es un GUID, pero se debe tratar como un valor opaco que se pasa sin analizar. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

Las respuestas de error también pueden enviarse a la `redirect_uri` para que la aplicación pueda controlarlos adecuadamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

#### Solicitar un token de acceso
Ahora que ha adquirido un authorization\_code y el usuario le ha concedido permiso, puede canjear el `code` para un `access_token` al recurso deseado mediante el envío de una solicitud de `POST` al extremo `/token`:

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
| client\_id | requerido | El Id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
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
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar a los recursos protegidos, como una API web. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es el portador |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |
| expires\_on | La hora a la que expira el token de acceso. La fecha se representa como el número de segundos desde el tiempo de época. |
| ámbito | Los ámbitos para los que el access\_token es válido. |
| refresh\_token | Un token de actualización de OAuth 2.0. La aplicación puede utilizar este token para adquirir tokens de acceso adicionales una vez que expire el token de acceso actual. Los refresh\_tokens son de larga duración y pueden usarse para conservar el acceso a los recursos durante largos períodos de tiempo. Para obtener más información, consulte la [referencia del token v2.0](active-directory-v2-tokens.md). |
| ID\_token | Un token web JSON (JWT) sin firmar. La aplicación puede descodificar base64Url en los segmentos de este token para solicitar información acerca del usuario que ha iniciado sesión. La aplicación puede almacenar en caché los valores y mostrarlos, pero no debe confiar en ellos para cualquier autorización o límite de seguridad. Para obtener más información sobre los id\_tokens, consulte la [referencia de token del modelo de aplicaciones v2.0](active-directory-v2-tokens.md). |

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

#### Use el token de acceso
Ahora que ha adquirido correctamente una `access_token`, puede utilizar el token en solicitudes a las API web mediante su inclusión en el `Authorization` encabezado:

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

#### Actualizar el token de acceso
Los Access\_tokens tienen una duración breve y debe actualizarlos una vez expiren para seguir teniendo acceso a los recursos. Puede hacerlo mediante el envío de otra solicitud de `POST` al extremo `/authorize`, esta vez proporcionando el `refresh_token` en lugar del `code`:

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
| client\_id | requerido | El Id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
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
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar a los recursos protegidos, como una API web. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es el portador |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |
| expires\_on | La hora a la que expira el token de acceso. La fecha se representa como el número de segundos desde el tiempo de época. |
| ámbito | Los ámbitos para los que el access\_token es válido. |
| refresh\_token | Un nuevo token de actualización de OAuth 2.0. Debe reemplazar el token de actualización antiguo con este token de actualización recientemente adquirido para asegurar que los tokens de actualización siguen siendo válidos durante tanto tiempo como sea posible. |
| ID\_token | Un token web JSON (JWT) sin firmar. La aplicación puede descodificar base64Url en los segmentos de este token para solicitar información acerca del usuario que ha iniciado sesión. La aplicación puede almacenar en caché los valores y mostrarlos, pero no debe confiar en ellos para cualquier autorización o límite de seguridad. Para obtener más información sobre los id\_tokens, consulte la [referencia de token del modelo de aplicaciones v2.0](active-directory-v2-tokens.md). |

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






## Flujo de inicio de sesión OpenID Connect
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) amplía el protocolo de *autorización* de OAuth 2.0 para su uso como un protocolo de *autenticación*, lo que le permite realizar inicios de sesión únicos mediante OAuth. Introduce el concepto de un `id_token`, que es un token de seguridad que permite al cliente comprobar la identidad del usuario y obtener información del perfil básica sobre el usuario.

OpenID Connect para el modelo de aplicaciones v2.0 es la forma recomendada para implementar el inicio de sesión para una [aplicación web](active-directory-v2-flows.md#web-apps). El flujo de inicio de sesión más básico contiene los siguientes pasos:

![Calles OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

#### Enviar la solicitud de inicio de sesión
Cuando su aplicación web necesita autenticar al usuario, puede dirigir al usuario al extremo `/authorize`. Esta solicitud es similar al primer segmento del [flujo de código de autorización de OAuth 2.0](#oauth2-authorization-code-flow) con algunas diferencias importantes: - La solicitud debe incluir el ámbito `openid` en el parámetro `scope`. - El parámetro `response_type` debe incluir `id_token` - La solicitud debe incluir el parámetro `nonce`

```
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
| client\_id | requerido | El Id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| response\_type | requerido | Debe incluir `id_token` para el inicio de sesión en OpenID Connect. También puede incluir otros response\_types, como se describe en la sección [OpenID Connect con flujo de código de OAuth](#OpenID-Connect-with-OAuth-Code-Flow). |
| redirect\_uri | requerido | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Asegúrese de que incluye el ámbito `openid` para OpenID Connect, lo que se traduce en el permiso de "inicio de sesión y lectura de su perfil" en la IU de consentimiento. También puede incluir otros ámbitos en esta solicitud de consentimiento del solicitante, como se describe en la sección [OpenID Connect con flujo de código de OAuth](#OpenID-Connect-with-OAuth-Code-Flow). |
| valor de seguridad | requerido | Un valor incluido en la solicitud, generada por la aplicación, que se incluirá en el id\_token resultante como una notificación. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que puede utilizarse para identificar el origen de la solicitud. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el authorization\_code resultante de nuevo a tu aplicación. Puede ser uno de 'query', 'form\_post' o 'fragment'.  
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. El único valor válido en este momento es 'login', lo que obliga al usuario a escribir sus credenciales en esa solicitud. El inicio de sesión único no surtirá efecto. |

En este punto, se le pedirá al usuario que escriba sus credenciales y que complete la autenticación. El extremo v2.0 también garantizará que el usuario haya dado su consentimiento a los permisos indicados en el parámetro de la consulta `scope`. Si el usuario no ha dado su consentimiento a alguno de esos permisos, se le solicitará al usuario su consentimiento para los permisos necesarios. Los detalles sobre los [permisos, el consentimiento y las aplicaciones de varios inquilinos se proporcionan aquí](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y otorga su consentimiento, el extremo v2.0 devolverá una respuesta a su aplicación en el `redirect_uri` indicado utilizando el método especificado en el parámetro `response_mode`.

Una respuesta correcta al usar `response_mode=query` tiene el siguiente aspecto:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| ID\_token | El id\_token que solicitó la aplicación. Puede usar el id\_token para comprobar la identidad del usuario y comenzar una sesión con el usuario. Se incluyen más detalles sobre id\_tokens y su contenido en la [referencia del token de extremo v2.0](active-directory-v2-tokens.md). |
| session\_state | Un valor único que identifica la sesión del usuario actual. Este valor es un GUID, pero se debe tratar como un valor opaco que se pasa sin analizar. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

Las respuestas de error también pueden enviarse a la `redirect_uri` para que la aplicación pueda controlarlos adecuadamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

#### Validar el id\_token
Recibir un solo id\_token no es suficiente para autenticar al usuario; debe validar la firma del id\_token y comprobar las notificaciones en el token según los requisitos de su aplicación. El extremo v2.0 usa [tokens web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y criptografía de clave pública para firmar los tokens y comprobar que son válidos.

El modelo de aplicaciones v2.0 tiene un extremo de metadatos OpenID Connect, que permite a una aplicación obtener información sobre el modelo de aplicaciones v2.0 en tiempo de ejecución. En esta información se incluyen los extremos, los contenidos del token y las claves de firma de los token. El extremo de metadatos contiene un documento JSON ubicado en:

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Una de las propiedades de este documento de configuración es el `jwks_uri`, cuyo valor para el modelo de aplicaciones v2.0 será:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

Puede usar las claves públicas RSA256 ubicadas en este extremo para validar la firma del id\_token. Hay varias claves enumeradas en este extremo en cualquier momento, cada una identificada con un `kid`. El encabezado del id\_token también contiene una notificación de `kid`, que indica cuál de estas claves se utilizó para firmar el id\_token.

Vea la [referencia de token del modelo de aplicaciones v2.0](active-directory-v2-tokens.md) para obtener más información, por ejemplo, [Validar tokens](active-directory-v2-tokens.md#validating-tokens) e [ Información importante sobre la sustitución de la clave de firma](active-directory-v2-tokens.md#validating-tokens). <!--TODO: Improve the information on this-->

Una vez haya validado la firma del id\_token, hay algunas notificaciones que tendrá que comprobar:

- Debería validar la notificación de `nonce` para evitar ataques de reproducción de token. Su valor debería ser el que especificó en la solicitud de inicio de sesión.
- Debería validar la notificación de `aud` para asegurarse de que se ha emitido el id\_token para su aplicación. Su valor debería ser el `client_id` de su aplicación.
- Debería validar las notificaciones de `iat` y `exp` para asegurarse de que el id\_token no ha expirado.

Se recomienda que valide notificaciones adicionales según su escenario. Algunas validaciones comunes incluyen:

- Asegurarse de que la organización/el usuario se ha registrado en la aplicación.
- Asegurarse de que el usuario tiene la autorización/los privilegios adecuados
- Asegurarse de que se haya producido un determinado nivel de autenticación, como la autenticación multifactor.

Para obtener más información sobre las notificaciones en un id\_token, consulte la [referencia de token del modelo de aplicaciones v2.0](active-directory-v2-tokens.md).

Una vez que haya validado completamente el id\_token, puede iniciar una sesión con el usuario y usar las notificaciones en el id\_token para obtener información sobre el usuario en su aplicación. Esta información puede utilizarse para su visualización, registros, autorizaciones, etc.

#### Enviar una solicitud de cierre de sesión

La vista previa del modelo de aplicaciones v2.0 no admite actualmente el OpenIdConnect `end_session_endpoint`. Esto significa que la aplicación no puede enviar una solicitud al extremo v2.0 para finalizar una sesión de usuario y borrar las cookies establecidas por el extremo v2.0. Para cerrar la sesión de un usuario, la aplicación puede simplemente finalizar su propia sesión con el usuario y dejar la sesión del usuario con el extremo v2.0 intacta. La próxima vez que el usuario intente iniciar sesión, verá una página de "elegir cuenta", en la que se enumeran sus cuentas con inicio de sesión activo. En esa página, el usuario puede elegir cerrar la sesión de cualquier cuenta, finalizando así la sesión con el extremo v2.0.

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

## OpenID Connect con el flujo de código de OAuth
Muchas aplicaciones web requieren que el usuario inicie sesión y luego obtenga acceso a un servicio web en nombre del usuario mediante OAuth. Este escenario combina las dos secciones anteriores: usa OpenID Connect para la autenticación de usuario mientras adquiere simultáneamente un authorization\_code que puede utilizarse para obtener access\_tokens usando el flujo de código de autorización de OAuth:

![Calles OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Este flujo difiere ligeramente de las secciones anteriores en la forma de enviar la solicitud de inicio de sesión.

#### Enviar la solicitud de inicio de sesión
Cuando su aplicación web necesita autenticar al usuario y obtener los permisos necesarios para acceder a los recursos, puede dirigir al usuario al extremo `/authorize`. En este caso, asegúrese de que su aplicación solicita tanto un `id_token` como un `code` en la respuesta:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20										 // Include both 'openid' and scopes your app needs  
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | ----------------- |
| client\_id | requerido | El Id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| response\_type | requerido | Debe incluir `id_token` y `code`para este escenario. |
| redirect\_uri | requerido | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Asegúrese de que incluye el ámbito `openid` para OpenID Connect, lo que se traduce en el permiso de "inicio de sesión y lectura de su perfil" en la IU de consentimiento. Asegúrese de incluir también los ámbitos para los recursos para los que su aplicación requiere acceso. Para obtener una explicación más detallada de los ámbitos, consulte [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md). |
| valor de seguridad | requerido | Un valor incluido en la solicitud, generada por la aplicación, que se incluirá en el id\_token resultante como una notificación. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que puede utilizarse para identificar el origen de la solicitud. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el authorization\_code resultante de nuevo a tu aplicación. Puede ser uno de 'query', 'form\_post' o 'fragment'.  
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. El único valor válido en este momento es 'login', lo que obliga al usuario a escribir sus credenciales en esa solicitud. El inicio de sesión único no surtirá efecto. |

En este punto, se le pedirá al usuario que escriba sus credenciales y que complete la autenticación. El extremo v2.0 también garantizará que el usuario haya dado su consentimiento a los permisos indicados en el parámetro de la consulta `scope`. Si el usuario no ha dado su consentimiento a alguno de esos permisos, se le solicitará al usuario su consentimiento para los permisos necesarios. Los detalles sobre los [permisos, el consentimiento y las aplicaciones de varios inquilinos se proporcionan aquí](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y otorga su consentimiento, el extremo v2.0 devolverá una respuesta a su aplicación en el `redirect_uri` indicado utilizando el método especificado en el parámetro `response_mode`.

Una respuesta correcta al usar `response_mode=query` tiene el siguiente aspecto:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| ID\_token | El id\_token que solicitó la aplicación. Puede usar el id\_token para comprobar la identidad del usuario y comenzar una sesión con el usuario. Se incluyen más detalles sobre id\_tokens y su contenido en la [referencia de token del modelo de aplicaciones v2.0](active-directory-v2-tokens.md). |
| código | El authorization\_code que solicitó la aplicación. La aplicación puede utilizar el código de autorización para solicitar un token de acceso para el recurso de destino. Los authorization\_codes son de muy corta duración, normalmente expiran después de unos 10 minutos. |
| session\_state | Un valor único que identifica la sesión del usuario actual. Este valor es un GUID, pero se debe tratar como un valor opaco que se pasa sin analizar. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

Las respuestas de error también pueden enviarse a la `redirect_uri` para que la aplicación pueda controlarlos adecuadamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

#### Validar el id\_token
Este proceso es exactamente el mismo al descrito anteriormente en el [flujo de inicio de sesión de OpenID Connect](#OpenID-Connect-Sign-In-Flow).

#### Enviar una solicitud de cierre de sesión
Este proceso es exactamente el mismo al descrito anteriormente en el [flujo de inicio de sesión de OpenID Connect](#OpenID-Connect-Sign-In-Flow).

#### Solicitar un token de acceso
Este proceso es exactamente el mismo al descrito anteriormente en el [flujo de código de autorización de OAuth 2.0](#oauth2-authorization-code-flow).

#### Use el token de acceso
Este proceso es exactamente el mismo al descrito anteriormente en el [flujo de código de autorización de OAuth 2.0](#oauth2-authorization-code-flow).

#### Actualizar el token de acceso
Este proceso es exactamente el mismo al descrito anteriormente en el [flujo de código de autorización de OAuth 2.0](#oauth2-authorization-code-flow).





## Flujo de concesión de credenciales de cliente de OAuth2
La concesión de credenciales de cliente de OAuth 2.0 se describe en la [especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4). Es útil para procesos de larga duración y otros escenarios de servidor a servidor, donde la aplicación puede autenticar un recurso mediante su propia "identidad de aplicación", en lugar de la identidad delegada de un usuario.

La vista previa del modelo de aplicaciones v2.0 no admite actualmente este flujo. Para ver cómo funciona en el servicio Azure AD, disponible con carácter general, consulte [este ejemplo de código de Azure AD](https://github/com/AzureADSamples/Daemon-DotNet).

## Flujo de OAuth2 implícito
El flujo implícito de OAuth 2.0 se describe en la [especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). A menudo se utiliza para aplicaciones de una página/javascript que se ejecutan en un explorador.

La vista previa del modelo de aplicaciones v2.0 no admite actualmente este flujo. Para ver cómo funciona en el servicio Azure AD, disponible con carácter general, consulte [este ejemplo de código de Azure AD](active-directory-devquickstarts-angular.md).

## Concesión de credenciales de contraseña de propietario del recurso de OAuth2
La concesión de credenciales de contraseña de propietario del recurso de OAuth 2.0 se describe en la [especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.3). Permite a una aplicación adquirir access\_tokens proporcionando el nombre de usuario y la contraseña de un usuario en una solicitud de token.

La vista previa del modelo de aplicaciones v2.0 no admite actualmente este flujo. Para ver cómo funciona en el servicio Azure AD, disponible con carácter general, consulte [este ejemplo de código de Azure AD](https://github.com/AzureADSamples/NativeClient-Headless-DotNet).

## OAuth2 en nombre de flujo
Se describe la concesión de credenciales en nombre del flujo o del portador JWT en este [borrador de concesión de la extensión de OAuth 2.0](https://tools.ietf.org/html/draft-ietf-oauth-jwt-bearer-12). Permite que una API web que recibe un access\_token use ese access\_token como una credencial para la adquisición de access\_tokens para otros recursos. Esto permite que una API web llame a otra API web de nivel inferior de forma segura.

La vista previa del modelo de aplicaciones v2.0 no admite actualmente este flujo. Para ver cómo funciona en el servicio Azure AD, disponible con carácter general, consulte [este ejemplo de código de Azure AD](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=Oct15_HO3-->