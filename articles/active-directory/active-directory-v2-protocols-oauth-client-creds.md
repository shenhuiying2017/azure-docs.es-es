
<properties
	pageTitle="Flujo de credenciales de cliente de la versión 2.0 de OAuth de Azure | Microsoft Azure"
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
	ms.date="09/26/2016"
	ms.author="dastrock"/>

# Protocolos de la versión 2.0: flujo de credenciales de cliente de OAuth 2.0

La [concesión de credenciales de cliente de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4), a veces denominada "OAuth de dos vías", puede utilizarse para acceder a los recursos hospedados en la web mediante el uso la identidad de una aplicación. Normalmente, se utiliza para las interacciones de servidor a servidor que deben ejecutarse en segundo plano sin la presencia inmediata de un usuario final. Estos tipos de aplicaciones suelen denominarse "**demonios**" o "**cuentas de servicio**".

> [AZURE.NOTE]
	No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe utilizar la versión 2.0 del punto de conexión, obtenga información sobre las [limitaciones de esta versión](active-directory-v2-limitations.md).

En el escenario más típico tres OAuth de tres vías, una aplicación cliente tiene permiso para acceder a un recurso en nombre de un usuario determinado. El permiso se **delega** del usuario a aplicación, normalmente, durante el proceso de [consentimiento](active-directory-v2-scopes.md). Sin embargo, en el flujo de credenciales de cliente, los permisos se conceden **directamente** en la propia aplicación. Cuando la aplicación presenta un token a un recurso, este exige que la propia aplicación tenga autorización para realizar alguna acción, y no algún usuario.

## Diagrama de protocolo
El flujo entero de credenciales de cliente tiene un aspecto similar a lo que se indica a continuación. Cada uno de los pasos se describe en detalle más adelante.

![Flujo de credenciales de cliente](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## Obtención de autorización directa 
Hay dos maneras de que una aplicación obtenga normalmente autorización directa para acceder a un recurso: a través de una lista de control de acceso en el recurso o asignando permisos de aplicación en Azure AD. Existen otras formas un recurso de que un recurso pueda autorizar a sus clientes, y de cada servidor de recursos pueda elegir el método que más convenga a su aplicación. Estos dos métodos son las más comunes en Azure AD y se recomiendan para los clientes y recursos que quieren realizar el flujo de credenciales de cliente.

### Listas de control de acceso
Un proveedor de recursos determinado podría exigir una comprobación de autorización basada en una lista de identificadores de aplicación que conoce y concede niveles de acceso concretos. Cuando el recurso recibe un token del punto de conexión v2.0, puede descodificar el token y extraer el identificador de la aplicación del cliente desde las notificaciones `appid` y `iss`. Después, puede comparar esto en la aplicación en la que mantenga alguna lista de control de acceso (ACL). La granularidad y el método de la lista de control de acceso pueden variar considerablemente de un recurso en recurso.

Un caso de uso común para dicha ACL es ejecutar pruebas de aplicaciones o API web. La API web solo puede conceder un subconjunto de sus permisos completos a sus diversos clientes. Sin embargo, para poder ejecutar pruebas de extremo a extremo en la API, se crea un cliente de prueba que adquiere tokens desde el punto de conexión v2.0 y los envía a la API. La API puede agregar a la ACL el identificador de la aplicación del cliente de prueba para tener acceso completo a todas las funcionalidades de la API. Tenga en cuenta que si tiene una lista de estas características en su servicio, no solo debe asegurarse de que validen al elemento `appid` del llamador, sino también de que el elemento `iss` del token sea confiable.

Este tipo de autorización es común para las cuentas de servicio y los demonios que necesitan tener acceso a datos que pertenecen a los usuarios consumidores con cuentas Microsoft. Para los datos que pertenecen a las organizaciones, se recomienda obtener la autorización necesaria a través de los permisos de aplicación.

### Permisos de aplicación
En lugar de utilizar las ACL, las API pueden exponer un conjunto de **permisos de aplicación** que se pueden conceder a una aplicación. El administrador de una organización concede un permiso de aplicación y solo puede utilizarse para tener acceso a datos que pertenecen a dicha organización y sus empleados. Por ejemplo, Microsoft Graph expone varios permisos de aplicación:

- Lectura correo en todos los buzones de correo
- Lectura y escritura en todos los buzones de correo
- Envío de correo como cualquier usuario
- Lectura de datos de directorio
- [Y mucho más](https://graph.microsoft.io)

Para obtener estos permisos en su aplicación, puede realizar los pasos siguientes.

#### Solicitud de los permisos en el portal de registro de aplicaciones

- Vaya a la aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o [cree una aplicación](active-directory-v2-app-registration.md) si no lo ha hecho aún. Tendrá que asegurarse de que la aplicación ha creado, al menos, un secreto de aplicación.
- Busque la sección de **permisos directos de aplicación** y agregue los permisos que requiere la aplicación.
- No se olvide de **guardar** el registro de aplicaciones

#### Recomendación: iniciar la sesión del en la aplicación

Normalmente, al compilar una aplicación que usa permisos de aplicación, esta tendrá que tener una página o vista que permita la administración para aprobar los permisos de la aplicación. Esta página puede formar parte del flujo de registro de la aplicación, de la configuración de la aplicación o de un flujo de conexión específico. En muchos casos, tiene sentido que la aplicación muestre esta vista de conexión solo después de que un usuario haya iniciado sesión con una cuenta Microsoft profesional o educativa.

Iniciar la sesión del usuario en la aplicación permite identificar la organización a la que pertenece el usuario antes de pedirle aprobar los permisos de aplicación. Aunque no es estrictamente necesario, puede ayudarlo a crear una experiencia más intuitiva para los usuarios de la organización. Para iniciar la sesión del usuario, siga nuestros [tutoriales del protocolo de la versión 2.0](active-directory-v2-protocols.md).

#### Solicitud de los permisos de un administrador de directorios

Cuando esté listo para solicitar permisos del administrador de la empresa, puede redirigir al usuario a la versión 2.0 del **punto de conexión de consentimiento del administrador**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissons
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parámetro | | Description |
| ----------------------- | ------------------------------- | --------------- |
| tenant | requerido | El inquilino de directorio al que quiere solicitar permiso. Se pueden proporcionar en formato de nombre descriptivo o GUID. Si no sabe a qué inquilino pertenece el usuario y quiere permitirle que inicie sesión con cualquier inquilino, utilice `common`. |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| redirect\_uri | requerido | El elemento redirect\_uri donde desea que se envíe la respuesta para que se controle la aplicación. Debe coincidir exactamente con uno de los elementos redirect\_uri que registró en el portal, con la excepción de que debe estar codificado como URL y que pueden tener segmentos de ruta adicionales. |
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. El estado se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |

En este momento, Azure AD exigirá que solo un administrador de inquilinos pueda iniciar sesión para completar la solicitud. Se pedirá al administrador que apruebe todos los permisos de aplicación directos que ha solicitado para la aplicación en el portal de registro.

##### Respuesta correcta
Si el administrador aprueba los permisos para la aplicación, la respuesta correcta será la siguiente:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parámetro | Description |
| ----------------------- | ------------------------------- | --------------- |
| tenant | El inquilino de directorio que concedió los permisos solicitados, en formato GUID. |
| state | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. El estado se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| admin\_consent | Se establecerá en `True`. |


##### Respuesta de error
Si el administrador no aprueba los permisos de la aplicación, la respuesta error será la siguiente:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parámetro | Description |
| ----------------------- | ------------------------------- | --------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error. |

Una vez que haya recibido una respuesta correcta del punto de conexión de aprovisionamiento de la aplicación, esta habrá obtenida los permisos de aplicación directos que solicitó. Ahora puede pasar a solicitar un token para el recurso deseado.

## Obtención de un token
Una vez que haya obtenido la autorización necesaria para la aplicación, puede continuar con el proceso de obtención de tokens de acceso para las API. Para obtener un token mediante la concesión de credenciales de cliente, envíe una solicitud POST al punto de conexión v2.0 `/token`:

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parámetro | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| ámbito | requerido | El valor pasado para el parámetro `scope` en esta solicitud debe ser el identificador de recursos (el URI de id. de la aplicación) del recurso deseado, con el sufijo `.default`. Por tanto, para el ejemplo de Microsoft Graph, el valor debe ser `https://graph.microsoft.com/.default`. Este valor indica el punto de conexión v2.0 de todos los permisos de aplicación directos que ha configurado para la aplicación, debe emitir un token para aquellos relacionados con el recurso deseado. |
| client\_secret | requerido | El secreto de aplicación que creó en el portal de registro de aplicaciones para su aplicación. |
| grant\_type | requerido | Debe ser `client_credentials`. | 

#### Respuesta correcta
Una respuesta correcta adoptará el siguiente formato:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parámetro | Description |
| ----------------------- | ------------------------------- |
| access\_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar a los recursos protegidos, como una API web. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es `Bearer`. |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |

#### Respuesta de error
Una respuesta de error tendrá el siguiente formato:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parámetro | Description |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |
| error\_codes | Una lista de los códigos de error específicos de STS que pueden ayudar en los diagnósticos. |
| timestamp | La hora a la que se produjo el error. |
| trace\_id | Un identificador exclusivo para la solicitud que puede ayudar en los diagnósticos. |
| correlation\_id | Un identificador exclusivo para la solicitud que puede ayudar en los diagnósticos entre componentes. |

## Uso de un token
Ahora que ha obtenido un token, puede utilizarlo para realizar solicitudes al recurso. Cuando expira el token, basta con repetir la solicitud al punto de conexión `/token` para obtener un token de acceso nuevo.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## Código de ejemplo
Para ver un ejemplo de una aplicación que implementa la concesión de client\_credentials mediante el punto de conexión de consentimiento del administrador, consulte nuestro [ejemplo de código de demonios de versión 2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

<!---HONumber=AcomDC_0928_2016-->