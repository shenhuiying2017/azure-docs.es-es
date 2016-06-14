<properties
	pageTitle="Control de errores en OAuth 2.0 | Microsoft Azure"
	description="En este artículo se describen las clases de errores comunes en OAuth 2.0 con Azure Active Directory y las prácticas recomendadas para controlarlos."
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
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# Control de errores en OAuth 2.0

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

En este artículo, obtendrá información sobre algunas prácticas recomendadas para controlar clases de errores comunes que pueden producirse al autorizar una aplicación con Azure Active Directory (Azure AD). Para obtener más información sobre el punto de conexión de autorización y el de emisión de tokens, consulte el [flujo de autenticación de aplicaciones en Azure AD](active-directory-protocols-oauth-code.md).

## Errores de puntos de conexión de autorización

En el primer paso del proceso de autorización, la aplicación cliente envía una solicitud al punto de conexión `/authorize` de Azure AD con la lista de permisos que necesita del usuario.

Los errores de puntos de conexión de autorización se originan en el punto de conexión `/authorize`. Además, se devuelven como errores HTTP 200 en una página web, o bien mediante un código de redireccionamiento HTTP 302 cuando una aplicación cliente está disponible para controlar el error.

Aquí verá una respuesta de error HTTP 302 de muestra del punto de conexión de autorización de Azure AD cuando falta el parámetro `response_type` en una solicitud.

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?error=invalid_request&error_description=AADSTS90014%3a+The+request+body+must+contain+the+following+parameter%3a+%27response_type%27.%0d%0aTrace+ID%3a+57f5cb47-2278-4802-a018-d05d9145daad%0d%0aCorrelation+ID%3a+570a9ed3-bf1d-40d1-81ae-63465cc25488%0d%0aTimestamp%3a+2013-12-31+05%3a51%3a35Z&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parámetro | Descripción |
|-----------|-------------|
| error | Valor de código de error definido en la sección 5.2 del [marco de autorización de OAuth 2.0](http://tools.ietf.org/html/rfc6749). En la tabla siguiente se describen los códigos de error que devuelve Azure AD. |
| error\_description | Descripción más detallada del error. Este mensaje no está diseñado para que el usuario final lo comprenda sin problemas. |
| state | El valor de estado es un valor no reutilizado y generado aleatoriamente que se envía en la solicitud y que se devuelve en la respuesta con el fin de evitar ataques de falsificación de solicitud entre sitios (CSRF). |

### Códigos de error correspondientes a errores de puntos de conexión de autorización

En la tabla siguiente se describen los distintos códigos de error que pueden devolverse en el parámetro `error` de la respuesta de error.

| Código de error | Descripción | Acción del cliente |
|------------|-------------|---------------|
| invalid\_request | Error de protocolo, como un parámetro obligatorio que falta. | Corrija el error y vuelva a enviar la solicitud. Se trata de un error de desarrollo que suele detectarse durante las pruebas iniciales.|
| unauthorized\_client | La aplicación cliente no puede solicitar un código de autorización. | Este error suele producirse cuando la aplicación cliente no está registrada en Azure AD o no se ha agregado al inquilino de Azure AD del usuario. La aplicación puede pedir al usuario consentimiento para instalar la aplicación y agregarla a Azure AD. |
| access\_denied | El propietario del recurso ha denegado el consentimiento. | La aplicación cliente puede notificar al usuario que no puede continuar, salvo que este dé su consentimiento. |
| unsupported\_response\_type | El servidor de autorización no admite el tipo de respuesta de la solicitud. | Corrija el error y vuelva a enviar la solicitud. Se trata de un error de desarrollo que suele detectarse durante las pruebas iniciales.|
|server\_error | El servidor ha detectado un error inesperado. | Vuelva a intentarlo. Estos errores pueden deberse a condiciones temporales. La aplicación cliente podría explicar al usuario que su respuesta se ha retrasado debido a un error temporal. |
| temporarily\_unavailable | De manera temporal, el servidor está demasiado ocupado para atender la solicitud. | Vuelva a intentarlo. La aplicación cliente podría explicar al usuario que su respuesta se ha retrasado debido a una condición temporal. |
| invalid\_resource |El recurso de destino no es válido porque no existe, Azure AD no lo encuentra o no está configurado correctamente.| Este error indica que el recurso, en caso de que exista, no se ha configurado en el inquilino. La aplicación puede pedir al usuario consentimiento para instalar la aplicación y agregarla a Azure AD. |

## Errores de puntos de conexión de emisión de tokens

Una vez que la aplicación reciba un código de autorización del punto de conexión `/authorize`, se encarga de pasarlo al punto de conexión `/token` con el fin de recibir un token de acceso para el recurso especificado.

Los errores de puntos de conexión de emisión de tokens se originan en el punto de conexión `/token`. Estos son los códigos de error HTTP, ya que el cliente llama directamente al punto de conexión de emisión de tokens. Además del código de estado HTTP, el punto de conexión de emisión de tokens de Azure AD también devuelve un documento JSON con objetos que describen el error.

Por ejemplo, si el parámetro `client_id` de la solicitud no es válido, el error podría tener el siguiente aspecto:

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8

{"error":"invalid_request","error_description":"AADSTS90011: Request is ambiguous, multiple application identifiers found. Application identifiers: '197451ec-ade4-40e4-b403-02105abd9049, 597451ec-ade4-40e4-b403-02105abd9049'.\r\nTrace ID: 4457d068-2a03-42b2-97f2-d55325289d86\r\nCorrelation ID: 6b3474d8-233e-463f-b0a3-86433d8ba889\r\nTimestamp: 2013-12-31 06:31:41Z","error_codes":[90011],"timestamp":"2013-12-31 06:31:41Z","trace_id":"4457d068-2a03-42b2-97f2-d55325289d86","correlation_id":"6b3474d8-233e-463f-b0a3-86433d8ba889"}
```
### Códigos de estado HTTP

En la tabla siguiente se enumeran los códigos de estado HTTP que devuelve el punto de conexión de emisión de tokens. En algunos casos, con el código de error basta para describir la respuesta, pero, si hay errores, se debe analizar el documento JSON que la acompaña y examinar su código de error.

| Código HTTP | Descripción |
|-----------|-------------|
| 400 | Código HTTP predeterminado. Se utiliza en la mayoría de los casos y suele originarse debido a una solicitud con formato incorrecto. Corrija el error y vuelva a enviar la solicitud. |
| 401 | Error de autenticación. Por ejemplo, la solicitud no tiene el parámetro client\_secret.|
| 403 | Error de autorización. Por ejemplo, el usuario no tiene permiso para acceder al recurso. |
| 500 | Se ha producido un error interno en el servicio. Vuelva a intentarlo. |

### Objetos de documento JSON de la respuesta de error

| Objeto JSON | Descripción |
|-------------|-------------|
| error | Valor de código de error definido en la sección 5.2 del [marco de autorización de OAuth 2.0](http://tools.ietf.org/html/rfc6749). En la tabla siguiente se describen los códigos de error que devuelve Azure AD. |
| error\_description | Descripción más detallada del error. Este mensaje no está diseñado para que el usuario final lo comprenda sin problemas. |
| timestamp | Fecha y hora en que se produjo el error en el horario universal coordinado (UTC). |
| trace\_id | Id. que identifica el seguimiento de errores. |
| correlation\_id | 	Valor que genera el cliente. Este id. se incluye en el documento de error JSON cuando la solicitud al punto de conexión de emisión de tokens incluye este valor en el encabezado `client-request-id`. Además, pueden usarlo otras llamadas en la misma sesión. |
| error\_codes | Contiene una lista de códigos de error que se asignan a distintas condiciones del servicio. No use estos códigos en la lógica de aplicación. Sin embargo, sí puede utilizarlos para diagnosticar un problema y buscar información en línea por código de error. |

### Valores de códigos de error de documento JSON

| Código de error | Descripción | Acción del cliente |
|------------|-------------|---------------|
| invalid\_request | Error de protocolo, como un parámetro obligatorio que falta. | Corrija el error y vuelva a enviar la solicitud. |
| invalid\_grant | La concesión de autorización (o sus parámetros) o el token de actualización no son válidos, expiraron o se revocaron. | Corrija el error y vuelva a enviar la solicitud. |
| unauthorized\_client | El cliente autenticado no está autorizado para usar este tipo de concesión de autorización. | Este error suele producirse cuando la aplicación cliente no está registrada en Azure AD o no se ha agregado al inquilino de Azure AD del usuario. La aplicación puede pedir al usuario consentimiento para instalar la aplicación y agregarla a Azure AD. |
| invalid\_client | Se produjo un error de autenticación de cliente. | Las credenciales del cliente no son válidas. Para corregirlo, el administrador de la aplicación actualiza las credenciales. |
| unsupported\_grant\_type | El servidor de autorización no admite el tipo de concesión de autorización. | Cambie el tipo de concesión de la solicitud. Este tipo de error solo debe producirse durante el desarrollo y detectarse en las pruebas iniciales. |
| invalid\_resource | El recurso de destino no es válido porque no existe, Azure AD no lo encuentra o no está configurado correctamente. | Este error indica que el recurso, en caso de que exista, no se ha configurado en el inquilino. La aplicación puede pedir al usuario consentimiento para instalar la aplicación y agregarla a Azure AD. |
| interaction\_required | La solicitud requiere la interacción del usuario. Por ejemplo, hay que realizar un paso de autenticación más. | Vuelva a tratar de realizar la solicitud con el mismo recurso. |
| temporarily\_unavailable | De manera temporal, el servidor está demasiado ocupado para atender la solicitud. | Vuelva a intentarlo. La aplicación cliente podría explicar al usuario que su respuesta se ha retrasado debido a una condición temporal.|

## Errores de los recursos protegidos

Estos son los errores que podría devolver un recurso protegido, como una API web, si implementa la especificación [RFC 6750](http://tools.ietf.org/html/rfc6750) del marco de autorización de OAuth 2.0.

Los recursos protegidos que implementan esta especificación emiten códigos de error HTTP. Si la solicitud no incluye las credenciales de autenticación o si falta el token, la respuesta incluirá un encabezado `WWW-Authenticate`. Cuando se produce un error en una solicitud, el servidor de recursos responde con un código de estado HTTP y uno de error.

A continuación, se muestra un ejemplo de una respuesta incorrecta cuando la solicitud de cliente no incluye el token de portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

## Parámetros de error

| Parámetro | Descripción |
|-----------|-------------|
| authorization\_uri | URI (punto de conexión físico) del servidor de autorización. Este valor también se utiliza como clave de búsqueda con el fin de obtener más información del servidor a partir de un punto de conexión de detección. <p><p> El cliente debe validar que el servidor de autorización sea de confianza. Cuando el recurso está protegido por Azure AD, basta con comprobar que la dirección URL comienza con https://login.windows.net u otro nombre de host que admita Azure AD. Los recursos específicos del inquilino siempre deben devolver un URI de autorización exclusivo del inquilino. |
| error | Valor de código de error definido en la sección 5.2 del [marco de autorización de OAuth 2.0](http://tools.ietf.org/html/rfc6749).|
| error\_description | Descripción más detallada del error. Este mensaje no está diseñado para que el usuario final lo comprenda sin problemas.|
| resource\_id | Devuelve el identificador único del recurso. La aplicación cliente puede utilizar este identificador como valor del parámetro `resource` cuando se solicita un token para el recurso. <p><p> Es muy importante que la aplicación cliente compruebe este valor; de lo contrario, un servicio malintencionado podría inducir un ataque de **elevación de privilegios**. <p><p> La estrategia recomendada para prevenir ataques consiste en comprobar que el parámetro `resource_id` coincide con la base de la dirección URL de la API web que tiene acceso. Por ejemplo, si https://service.contoso.com/data tiene acceso, `resource_id` puede ser htttps://service.contoso.com/. La aplicación cliente debe rechazar un parámetro `resource_id` que no empiece por la dirección URL base, salvo que exista una alternativa confiable para comprobar el id. |

## Códigos de error del esquema de portador

La especificación RFC 6750 define los siguientes errores de los recursos que utilizan el encabezado WWW-Authenticate y el esquema de portador de la respuesta.

| Código de estado HTTP | Código de error | Descripción | Acción del cliente |
|------------------|------------|-------------|---------------|
| 400 | invalid\_request | La solicitud no tiene el formato correcto. Por ejemplo, podría faltar un parámetro o que está utilizando el mismo parámetro dos veces. | Corrija el error y vuelva a tratar de realizar la solicitud. Este tipo de error solo debe producirse durante el desarrollo y detectarse en las pruebas iniciales. |
| 401 | invalid\_token | El token de acceso no es válido, falta o se ha revocado. El valor del parámetro error\_description proporciona más información. | Solicite un nuevo token desde el servidor de autorización. Si el nuevo token no funciona, significa que se ha producido un error inesperado. Envíe un mensaje de error para el usuario y vuelva a intentarlo después de los retrasos aleatorios. |
| 403 | insufficient\_scope | El token de acceso no contiene los permisos de suplantación necesarios para acceder al recurso. | Envíe una nueva solicitud de autorización al punto de conexión de autorización. Si la respuesta contiene el parámetro de ámbito, utilice el valor de ámbito de la solicitud en el recurso. |
| 403 | insufficient\_access | El asunto del token no tiene los permisos necesarios para acceder al recurso. | Pida al usuario que utilice una cuenta diferente o que solicite permisos al recurso especificado. |

<!---HONumber=AcomDC_0601_2016-->