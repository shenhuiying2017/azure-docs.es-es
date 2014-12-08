<properties pageTitle="Delegación de registros de usuario y suscripciones a producto" metaKeywords="" description="Learn how to delegate user registration and product subscription to a third party in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to delegate user registration and product subscription in Azure API Management" authors="antonba" solutions="" manager="dwrede" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="antonba" />

# Delegación de registros de usuario y suscripciones a producto

La delegación le permite usar su sitio web actual para controlar el inicio de sesión y la suscripción de los desarrolladores, así como sus suscripciones a productos, en contraposición al uso de la funcionalidad integrada en el portal para desarrolladores. Esto habilita su sitio web como propietario de los datos de usuario para poder realizar la validación de estos pasos de forma personalizada.


## En este tema

-   [Delegación de inicios de sesión y suscripciones de desarrolladores][]
-   [Delegación de suscripciones a productos][]

## <a name="delegate-signin-up"> </a>Delegación de inicios de sesión y suscripciones de desarrolladores

Para delegar el inicio de sesión y la suscripción de los desarrolladores a su sitio web actual, deberá crear un extremo especial de delegación en el sitio que funcione como punto de entrada de cualquier solicitud de este tipo que se inicie en el portal para desarrolladores de Administración de API.

El flujo de trabajo final será el siguiente:

1. El desarrollador hace clic en el enlace de inicio de sesión o de suscripción que se encuentra en el portal para desarrolladores de Administración de API.
2. El explorador se redirige al extremo de delegación.
3. Como respuesta, el extremo de delegación se redirige a la interfaz de usuario o la presenta para pedir al usuario que inicie sesión o se suscriba.
4. Una vez conseguido, se redirige de nuevo al usuario a la página del portal para desarrolladores de Administración de API de la que partió.


Para empezar, configuremos primero Administración de API para que dirija las solicitudes a través del extremo de delegación. En el portal del publicador de administración de API, haga clic en **Seguridad** y, a continuación, haga clic en la pestaña **Delegación**. Haga clic en la casilla para activar "Delegar inicio de sesión y suscripción".

![Delegation page][api-management-delegation-signin-up]

* Determine cuál será la URL del extremo especial de delegación y escríbala en el campo**URL de extremo de delegación**. 

* En el campo **Clave de autenticación de delegación**, introduzca el secreto que se usará para procesar una firma suministrada para su comprobación con objeto de garantizar que la solicitud procede efectivamente de Administración de API de Azure. Puede hacer clic en el botón **Generar** para que la administración de API genere de forma aleatoria una clave en su lugar.

Ahora debe crear el **extremo de delegación**. Este tiene que realizar varias acciones:

1. Recibir una solicitud de la forma siguiente:

	> *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL de la página de origen}&salt={cadena}&sid={cadena}*

	Parámetros de consulta en el caso de inicio de sesión o suscripción:
	- **operation**: identifica el tipo de solicitud de delegación de que se trata: solo puede ser "SignIn" en este caso.
	- **returnUrl**: dirección URL de la página en donde el usuario hizo clic en el vínculo de suscripción o de inicio de sesión.
	- **salt**: cadena salt especial que se usa para procesar un hash de seguridad
	- **sig**: hash de seguridad procesado que se comparará con su propio hash procesado

2. Compruebe que la solicitud procede de Administración de API de Azure (opcional, pero especialmente recomendado por motivos de seguridad).

	* Calcule un hash HMAC-SHA512 de una cadena según los parámetros de consulta **returnUrl** y **salt** ([se proporciona código de ejemplo a continuación]):
        > HMAC(**salt** + '\n' + **returnUrl**)
		 
	* Compare el hash procesado anteriormente con el valor del parámetro de consulta **sig**. Si los dos hashes coinciden, vaya a paso siguiente; de lo contrario, deniegue la solicitud.

2. Compruebe que se recibe una solicitud de inicio de sesión o suscripción: el parámetro de consulta **operation** se establecerá en "**SignIn**".

3. Presente al usuario la interfaz de usuario para que inicie sesión o se suscriba.

4. Si el usuario se suscribe, hay que crear la cuenta correspondiente en Administración de API. [Cree un usuario] con la API REST de Administración de API. Al hacerlo, asegúrese de que el identificador de usuario se establece en el mismo que existe en su almacén de usuario o en un identificador al que pueda realizar el seguimiento.

5. Cuando el usuario se autentique correctamente:

	* [solicite un token de inicio de sesión único (SSO)] a través de la API de REST de Administración de API

	* anexe un parámetro de consulta returnUrl a la URL de SSO que se recibió de la llamada de API anterior:
		> por ejemplo, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

	* redirija al usuario a la URL producida anteriormente


## <a name="delegate-product-subscription"> </a>Delegación de suscripciones a productos

La delegación de una suscripción a productos funciona de forma similar a la delegación de inicio de sesión y suscripción de usuario. El flujo de trabajo final sería el siguiente:

1. El desarrollador selecciona un producto en el portal para desarrolladores de Administración de API y hace clic en el botón Suscribir.
2. El explorador se redirige al extremo de delegación.
3. El extremo de delegación realiza los pasos necesarios para la suscripción al producto: esto depende de usted, y puede que implique la redirección a otra página para solicitar información de facturación, la formulación de otras preguntas o simplemente el almacenamiento de la información sin que se requiera ninguna acción del usuario.
4. Una vez conseguido, se redirige de nuevo al usuario a la página del portal para desarrolladores de Administración de API de la que partió.

Para habilitar la funcionalidad, en la página **Delegación**, haga clic en **Delegar suscripción de productos**.

A continuación, asegúrese de que el extremo de delegación realiza las siguientes acciones:


1. Recibir una solicitud de la forma siguiente:

	> *http://www.yourwebsite.com/apimdelegation?operation={operación}&productId={producto al que se suscribe}&userId={usuario que realiza la solicitud}&salt={cadena}&sid={cadena}*

	Parámetros de consulta en el caso de suscripción a producto:
	- **operation**: identifica el tipo de solicitud de delegación de que se trata. En las solicitudes de suscripción a producto las opciones válidas son:
		- "Subscribe": solicitud de suscripción del usuario a un determinado producto con el identificador especificado (consulte a continuación)
		- "Unsubscribe": solicitud de cancelación de la suscripción de un usuario a un producto
		- "Renew": solicitud de renovación de una suscripción (por ejemplo, que pueda expirar)
	- **productId**: identificador del producto al que el usuario solicitó suscribirse
	- **userId**: identificador del usuario para el que se realiza la solicitud
	- **salt**: cadena salt especial que se usa para procesar un hash de seguridad
	- **sig**: hash de seguridad procesado que se comparará con su propio hash procesado


2. Compruebe que la solicitud procede de Administración de API de Azure (opcional, pero especialmente recomendado por motivos de seguridad).

	* Procesar un hash HMAC-SHA512 de una cadena en función de los parámetros de consulta **productId**,**userId** y **salt**:
		> HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
		 
	* Compare el hash procesado anteriormente con el valor del parámetro de consulta **sig**. Si los dos hashes coinciden, vaya a paso siguiente; de lo contrario, deniegue la solicitud.
	
3. Realice cualquier procesamiento de la suscripción a producto en función del tipo de operación solicitada en **operation**; por ejemplo, facturación, preguntas adicionales, etc.

4. Tras la correcta suscripción del usuario al producto por su parte, suscriba el usuario al producto de Administración de API [llamando a la API de REST para la suscripción del producto].

5. Redirija de nuevo al usuario a la **returnUrl** especificada al recibir la solicitud.

## <a name="delegate-example-code"> </a> Código de ejemplo ##

Estos ejemplos de código muestran cómo aprovechar la *clave de validación de delegación*, que se establece en la pantalla de delegación del portal de administración de API para crear un HMAC que se utiliza para validar la firma, probando la validez del valor returnUrl que se ha pasado. El mismo código funciona para productId y userId con pequeñas modificaciones.

**Código C# para generar el hash de returnUrl**

	using System.Security.Cryptography;

	string key = "delegation validation key";
	string returnUrl = "returnUrl query parameter";
	string salt = "salt query parameter";
	string signature;
	using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
	{
		signature = encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl));
		// change to (salt + "\n" + productId + "\n" + userId) for point 2 above
	}

**Código NodeJS para generar el hash de returnUrl**

	var crypto = require('crypto');
	
	var key = 'delegation validation key'; 
	var returnUrl = 'returnUrl query parameter';
	var salt = 'salt query parameter';
	
	var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
	var digest = hmac.update(salt + '\n' + returnUrl).digest();
	// change to (salt + '\n' + productId + '\n' + userId) for point 2 above
	
	var signature = digest.toString('base64');

[Delegación de inicios de sesión y suscripciones de desarrolladores]: #delegate-signin-up
[Delegación de suscripciones a productos]: #delegate-product-subscription
[solicitar un token de inicio de sesión único (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[crear un usuario]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[llamar a la API de REST de suscripción de producto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Pasos siguientes]: #next-steps
[código de ejemplo proporcionado a continuación]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
