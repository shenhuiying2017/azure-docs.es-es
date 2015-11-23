<properties
	pageTitle="Referencia de token del modelo de aplicación v2 | Microsoft Azure"
	description="Los tipos de tokens y notificaciones emitidos por el extremo de la versión 2.0"
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
	ms.date="11/10/2015"
	ms.author="dastrock"/>

# Vista previa de la versión 2.0 del modelo de aplicaciones: referencia de token

El extremo de la versión 2.0 emite varios tipos de tokens de seguridad en el procesamiento de cada [flujo de autenticación](active-directory-v2-flows.md). Este documento describe el formato, las características de seguridad y el contenido de cada tipo de token.

> [AZURE.NOTE]Esta información se aplica a la vista previa pública de la versión 2.0 del modelo de aplicaciones. Para obtener instrucciones sobre cómo integrar con el servicio de Azure AD disponible con carácter general, consulta la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

## Tipos de tokens

El extremo de la versión 2.0 admite el [Protocolo de autorización de OAuth 2.0](active-directory-v2-protocols.md), que utiliza access\_tokens y refresh\_tokens. También admite la autenticación e inicio de sesión a través de [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), que presenta un tercer tipo de token, el id\_token. Todos estos tokens se representan como un "token de portador".

Un token portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. En este sentido, el "portador" es cualquier parte que pueda presentar el token. Aunque una parte debe autenticarse primero con Azure AD para recibir el token portador, si no se realizan los pasos necesarios para asegurar el token en la transmisión y el almacenamiento, este puede interceptarse y ser utilizado por un usuario no deseado. Mientras que algunos tokens de seguridad disponen de un mecanismo integrado para evitar ser usados por partes no autorizadas, los tokens portadores no tienen este mecanismo y deben transportarse en un canal seguro como, por ejemplo, la seguridad de la capa de transporte (HTTPS). Si un token portador se transmite sin cifrar, un usuario malintencionado puede utilizar un ataque de tipo "Man in the middle" para adquirir el token y usarlo para obtener acceso sin autorización a un recurso protegido. Los mismos principios de seguridad se aplican al almacenamiento o almacenamiento en caché de tokens portadores para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura. Para otras consideraciones sobre la seguridad de los tokens portadores, consulte la [Sección 5 de RFC 6750](http://tools.ietf.org/html/rfc6750).

Muchos de los tokens emitidos por el extremo de la versión 2.0 se implementan como Tokens Web Json o JWT. Un JWT es un medio compacto y seguro de la dirección URL para transferir información entre dos partes. La información contenida en los JWT se conoce como "notificaciones", o aserciones de información sobre el portador y el asunto del token. Las notificaciones de JWT son los objetos JSON codificados y serializados para su transmisión. Como los JWT emitidos por el extremo de la versión 2.0 están firmados, pero no cifrados, puedes inspeccionar fácilmente el contenido de un JWT con fines de depuración. Hay varias herramientas disponibles para hacerlo, como [calebb.net](https://calebb.net). Para más información sobre los JWT, consulta la [especificación de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Id\_Tokens

Los Id\_tokens son una forma de token de seguridad de inicio de sesión que recibe tu aplicación al llevar a cabo la autenticación mediante [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Se representan como [JWT](#types-of-tokens) y contienen notificaciones que puedes usar que el usuario inicie sesión en la aplicación. Puedes utilizar las notificaciones de un id\_token como te convenga. Normalmente se usan para mostrar información de la cuenta o tomar decisiones de control de acceso en una aplicación. El extremo de la versión 2.0 solo emite un tipo de id\_token, que tiene un conjunto coherente de notificaciones sin tener en cuenta el tipo de usuario que ha iniciado sesión. Es decir, que el formato y el contenido de los id\_tokens será el mismo tanto para los usuarios de una cuenta personal de Microsoft como para una cuenta profesional o educativa.

En ese momento los id\_tokens están firmados, pero no cifrados. Cuando la aplicación recibe un id\_token tiene que [validar la firma](#validating-tokens) para probar la autenticidad del token y validar algunas notificaciones en el token para demostrar su validez. Las notificaciones validadas por una aplicación varían dependiendo de los requisitos de escenario, pero hay algunas [validaciones de notificación comunes](#validating-tokens) que la aplicación debe realizar en todos los escenarios.

A continuación se muestra información detallada sobre las notificaciones de los id\_tokens, así como un ejemplo de un id\_token. Ten en cuenta que las notificaciones de los id\_tokens no se devuelven en ningún orden concreto. Además, se pueden introducir nuevas notificaciones en los id\_tokens en cualquier momento y no se debe interrumpir la aplicación cuando se introducen nuevas notificaciones. La siguiente lista incluye las notificaciones que la aplicación puede interpretar de forma confiable en el momento de redactar este artículo. Para la práctica, intenta inspeccionar las notificaciones del id\_token de ejemplo pegándolo en [calebb.net](https://calebb.net). Si es necesario, se pueden encontrar más detalles en la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Ejemplo de un Id\_Token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ
19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI0OTIxMDI1My0wYmExLTRhOWEtYTQyNC02MTY5OTlmYWI2MjAiL
CJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTAzMTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMW
YzNzZlL3YyLjAvIiwiaWF0IjoxNDM4NTM1NTQzLCJuYmYiOjE0Mzg1MzU1NDMsImV4cCI6MTQzODUzOTQ0MywidmVyIjoiMi4
wIiwidGlkIjoiYjk0MTAzMTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlIiwib2lkIjoiYTFlYmRkZTgtZTRmOS00NTcx
LWFkOTMtMzA1OWUzNzUwZDIzIiwicHJlZmVycmVkX3VzZXJuYW1lIjoic2FtcGxlLmFkbWluQHN0cm9ja2lzZGV2Lm9ubWljc
m9zb2Z0LmNvbSIsInN1YiI6IjJvMmQ5SVBGVzI5MGo0RVkySXg0RUdoaEtlWnVGaC1LcFhHS2tuZkNxRWMiLCJuYW1lIjoiU2
FtcGxlIEFkbWluIiwibm9uY2UiOiIxMjM0NSIsImNfaGFzaCI6IngxeU92VTZRaXE0Y1lVcVIxeDBvM2cifQ.Qk9exyv04I6a
P6Sju2xNG9O2sj8dG-aEoJeS5dmnjdLo8k1ZzgZd7w-6yCrKXgPh4FJ1YY-08DZnHNmP3oxm3zmEv3RUIBEyTmo3598PRYLWl
vttis1KD5PoNgAyKfHqiOCL5q_Owd0m9oAKDagbJhRVZOS89phllA0AQnaI6hJOKvMsbOYJt-w00y6TXf1Nkzp_Yey8EmRiwN
7gqvudL1UfZ7_UbST2DBjPIyZsv0gT8gpApz6CecCOyX1NNWpUg8ZRkNnOjGL-IMhq4okPCTTfYriOo93z9Y9v6NmaJxV5bBN
V-DIguXSzLVKnnflfSLyvhinsjLKCnu9L3oXHxw
```

#### Notificaciones de los Id\_Tokens
| Nombre | Notificación | Valor de ejemplo | Descripción |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Público | `aud` | `49210253-0ba1-4a9a-a424-616999fab620` | Identifica al destinatario previsto del token. En los id\_tokens, la audiencia es el Id. de aplicación de la aplicación, como se asigna a tu aplicación en el portal de registro de la aplicación. La aplicación tiene que validar este valor y rechazar el token si no coincide. |
| Emisor | `iss` | `https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0/` | Identifica el servicio de token de seguridad (STS) que construye y devuelve el token, así como el inquilino de Azure AD en el que se autenticó al usuario. La aplicación tiene que validar la notificación del emisor para asegurarse de que el token proviene del extremo de la versión 2.0. También puede usar la parte guid de la notificación para restringir el conjunto de los inquilinos que tienen permiso para iniciar sesión en la aplicación. |
| Emitido a las | `iat` | `1438535543` | La hora en que se emitió el token, que se representa en tiempo de época. |
| Fecha de expiración | `exp` | `1438539443` | La hora en que el token deja de ser válido, que se representa en tiempo de época. La aplicación tiene que usar esta notificación para comprobar la validez de la duración del token. |
| Versión | `ver` | `2.0` | La versión del id\_token, tal como se define por Azure AD. Para la versión 2.0 del modelo de aplicaciones, el valor será `2.0`. |
| Identificador de inquilino | `tid` | `b9410318-09af-49c2-b0c3-653adc1f376e` | Un guid que representa el inquilino de Azure AD de donde procede el usuario. Para las cuentas profesionales y educativas, el guid será el identificador del inquilino inmutable de la organización a la que pertenece el usuario. Para las cuentas personales, el valor será `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Código Hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | El código hash se incluye en los id\_tokens solo cuando se emite el id\_token junto con un código de autorización de OAuth 2.0. Se puede usar para validar la autenticidad de un código de autorización. Consulta la [especificación OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para más información sobre cómo realizar esta validación. |
| Hash de Token de acceso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | El hash de token de acceso se incluye en los id\_tokens solo cuando se emite el id\_token junto con un código de autorización de OAuth 2.0. Se puede usar para validar la autenticidad de un token de acceso. Consulta la [especificación OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para más información sobre cómo realizar esta validación. |
| Valor de seguridad | `nonce` | `12345` | El valor de seguridad es una estrategia para mitigar los ataques de reproducción de los tokens. La aplicación puede especificar un valor de seguridad en una solicitud de autorización mediante el parámetro de consulta `nonce`. El valor que se proporciona en la solicitud se emitirá en la notificación `nonce` del id\_token, sin modificaciones. Esto permite a la aplicación comprobar el valor con respecto al valor que especifica en la solicitud, que asocia la sesión de la aplicación con un id\_token determinado. La aplicación tiene que realizar esta validación durante el proceso de validación del id\_token. |
| Nombre | `name` | `Leonardo DaVinci` | La notificación de nombre proporciona un valor en lenguaje natural que identifica al firmante del token. No se asegura que este valor sea único, es mutable y está diseñado para usarse solo con fines de visualización. |
| Nombre de usuario preferido | `preferred_username` | `leo@outlook.com` | El nombre de usuario principal que se utiliza para representar al usuario en el extremo de la versión 2.0. Puede ser una dirección de correo electrónico, un número de teléfono o un nombre de usuario genérico sin un formato especificado. Su valor es mutable y puede cambiar a un usuario determinado con el tiempo. |
| Asunto | `sub` | `AAAAAAAAAAAAAAAAAAAAAOUtxUJsxQtHuMcFCIA1NC0` | La entidad de seguridad sobre la que el token declara información como, por ejemplo, el usuario de una aplicación. Este valor es inmutable y no se puede reasignar o volver a usar, por lo que se puede usar para realizar comprobaciones de autorización de forma segura, por ejemplo, cuando se usa el token para tener acceso a un recurso. Dado que el firmante siempre está presente en los tokens que emite Azure AD, se recomienda usar este valor en un sistema de autorización de propósito general. |
| ObjectId | `oid` | `27cb5cec-7c0c-40b4-a69a-22500b6ea853` | El Id. de objeto de la cuenta profesional o educativa del sistema de Azure AD. Esta notificación no se emitirá para cuentas personales de Microsoft. |

<!---
| Not Before | `nbf` | `1438535543` |  The time at which the token becomes valid, represented in epoch time. It is usually the same as the issuance time.  Your app should use this claim to verify the validity of the token lifetime.  |
-->



## Tokens de acceso

Los tokens de acceso emitidos por el extremo de la versión 2.0 tienen dos formatos diferentes. Los tokens de acceso que se emiten en nombre de las cuentas profesionales o educativas son JWT, similares a un id\_token. Los tokens de acceso que se emiten en nombre de una cuenta personal de Microsoft están en un formato que se conoce como "vales compactos". Por este motivo, en el desarrollo puedes encontrar un formato de cadena diferente para los tokens de acceso emitidos por el extremo de la versión 2.0. Con el tiempo, esta diferencia de tokens de acceso se eliminará del extremo de la versión 2.0.

Dicho esto, los tokens de acceso emitidos por el extremo de la versión 2.0 solo los puede utilizar Microsoft Services en este momento. Tus aplicaciones no necesitan realizar ninguna validación o inspección de los tokens de acceso para ninguno de los escenarios admitidos actualmente. Puedes tratar los tokens de acceso como totalmente opacos, ya que simplemente son cadenas que tu aplicación puede pasar a Microsoft en las solicitudes HTTP.

En un futuro próximo, el extremo de la versión 2.0 podrá hacer que la aplicación reciba los tokens de acceso de otros clientes. En ese momento, esta información se actualizará con la información que necesita la aplicación para realizar la validación del token de acceso y otras tareas similares.

Cuando solicitas un token de acceso desde el extremo de la versión 2.0, el extremo de la versión 2.0 también devuelve algunos metadatos sobre el token de acceso para el consumo de la aplicación. Esta información incluye la hora de expiración del token de acceso y los ámbitos para los que es válido. Esto permite a la aplicación realizar un almacenamiento inteligente en caché de los tokens de acceso sin tener que analizar y abrir el mismo token de acceso.

## Tokens de actualización

Los tokens de actualización son tokens de seguridad que la aplicación puede utilizar para adquirir nuevos tokens de acceso en un flujo de OAuth 2.0. Permite a la aplicación obtener acceso a largo plazo a los recursos en nombre de un usuario sin necesidad de que intervenga el usuario.

Los tokens de actualización tienen varios recursos. Es decir, un token de actualización recibido durante una solicitud de token para un recurso se puede canjear para los tokens de acceso a un recurso completamente diferente.

Para recibir una actualización en una respuesta de token, la aplicación tiene que solicitar y obtener la concesión del ámbito `offline_acesss`. Para más información sobre el ámbito `offline_access`, consulta el [artículo de consentimiento & ámbitos aquí](active-directory-v2-scopes.md).

Los tokens de actualización son, y siempre serán, totalmente opacos para tu aplicación. Además, los emite el extremo de la versión 2.0 de Azure AD y solo los puede inspeccionar e interpretar el extremo de la versión 2.0. También son de larga duración, pero no se debe componer la aplicación esperando que un token de actualización dure por cualquier período de tiempo. Los tokens de actualización pueden ser invalidados en cualquier momento por varios motivos. La única forma para que tu aplicación sepa si un token de actualización es válido es intentar canjearlo realizando una solicitud de token al extremo de la versión 2.0.

Cuando canjeas un token de actualización por un nuevo token de acceso (y si se concedió el ámbito `offline_access` a la aplicación), recibirás un nuevo token de actualización en la respuesta del token. Tienes que guardar el token de actualización recién emitido reemplazando el utilizado en la solicitud. Esto garantizará que los tokens de actualización sigan siendo válidos mientras sea posible.

## Validación de los tokens

En este momento, la única validación de tokens que deberían realizar tus aplicaciones es la validación de id\_tokens. Para validar un id\_token, la aplicación tiene que validar la firma del id\_token y las notificaciones del id\_token.

<!-- TODO: Link -->
Ofrecemos bibliotecas y ejemplos de código que muestran cómo hacer fácilmente la validación del token y la siguiente información simplemente se proporciona para quienes quieran entender el proceso subyacente. También hay varias bibliotecas de código abierto de terceros para la validación de JWT y hay al menos una opción para prácticamente cualquier plataforma e idioma.

#### Validación de la firma
Un JWT contiene tres segmentos, que están separados por el carácter `.`. El primer segmento se conoce como el **encabezado**, el segundo como el **cuerpo** y el tercero como la **firma**. El segmento de firma se puede utilizar para validar la autenticidad del id\_token para que la aplicación pueda confiar en él.

Los id\_tokens se firman con algoritmos de cifrado asimétrico estándar del sector, como RSA 256. El encabezado del id\_token contiene información sobre el método de cifrado y la clave utilizados para firmar el token:

```
{
		typ: "JWT",
		alg: "RS256",
		x5t: "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

La notificación `alg` indica el algoritmo que se utilizó para firmar el token, mientras las notificaciones `kid` y `x5t` indican la clave pública concreta que se utilizó para firmar el token.

En cualquier momento, el extremo de la versión 2.0 puede firmar un id\_token utilizando uno de un determinado conjunto de pares de claves pública y privada. El extremo de la versión 2.0 gira el posible conjunto de claves de forma periódica, por lo que tu aplicación debería estar redactada para manejar esos cambios de claves automáticamente. Una frecuencia razonable para comprobar las actualizaciones de las claves públicas usadas por el extremo de la versión 2.0 es aproximadamente 24 horas.

Puedes adquirir los datos de las claves de firmas necesarios para validar la firma utilizando el documento de metadatos de OpenID Connect, ubicado en:

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Este documento de metadatos es un objeto JSON que contiene varias piezas útiles de información, como la ubicación de los diferentes extremos necesarios para realizar la autenticación de OpenID Connect. También incluye un `jwks_uri`, que ofrece la ubicación del conjunto de claves públicas que se utilizan para firmar los tokens. A continuación se muestra esa ubicación, pero es mejor capturar esa ubicación dinámicamente utilizando el documento de metadatos y analizando el `jwks_uri`:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`

El documento JSON que se encuentra en esta dirección url contiene toda la información de clave pública en uso en ese momento en particular. La aplicación puede utilizar las notificaciones `kid` y `x5t` en el encabezado de JWT para seleccionar la clave pública que se ha utilizado en este documento para firmar un token concreto. Después, puede realizar la validación de la firma mediante la clave pública correcta y el algoritmo indicado.

La realización de la validación de la firma queda fuera del ámbito de este documento, pero hay muchas bibliotecas de código abierto disponibles para ayudarte a hacerlo si es necesario.

#### Validación de las notificaciones
Cuando tu aplicación recibe un id\_token al inicio de sesión del usuario, también tiene que realizar algunas comprobaciones de las notificaciones del id\_token. Entre ellas se incluyen las siguientes:

- La notificación de **Audiencia**: para comprobar que el id\_token se proporcionaría a tu aplicación.
- Las notificaciones de **Emitido en** y **Hora de expiración**: para comprobar que el id\_token no ha expirado.
- La notificación de **Emisor**: para comprobar que el token fue emitido realmente a la aplicación por el extremo de la versión 2.0.
- El **Valor de seguridad**: para mitigar ataques de reproducción de tokens.

En la [sección id\_token](#id_tokens) situada arriba se incluyen los detalles de los valores esperados para estas notificaciones.


## Vigencia de los tokens

Las siguientes vigencias de tokens solo se ofrecen para tu conocimiento, ya que pueden ayudar a desarrollar y depurar aplicaciones. Las aplicaciones no se deben redactar permitiendo que cualquiera de estas duraciones permanezca constante ya que pueden cambiar y lo harán en algún momento.

| Se necesita el cifrado de tokens | Vigencia | Descripción |
| ----------------------- | ------------------------------- | ------------ |
| Id\_tokens (cuentas profesionales o educativas) | 1 hora | Los id\_tokens normalmente son válidos durante una hora. Tu aplicación web puede utilizar este mismo período de tiempo en el mantenimiento de su propia sesión con el usuario (recomendado), o elegir una duración para la sesión completamente diferente. Si tu aplicación necesita obtener un nuevo id\_token, solo tiene que realizar una nueva solicitud de inicio de sesión en el extremo de autorización de la versión 2.0. Si el usuario tiene una sesión de explorador válida con el extremo de la versión 2.0, no tendrá que volver a escribir sus credenciales. |
| Id\_tokens (cuentas personales) | 24 horas | Los id\_tokens para cuentas personales normalmente son válidos durante 24 horas. Tu aplicación web puede utilizar este mismo período de tiempo en el mantenimiento de su propia sesión con el usuario (recomendado), o elegir una duración para la sesión completamente diferente. Si tu aplicación necesita obtener un nuevo id\_token, solo tiene que realizar una nueva solicitud de inicio de sesión en el extremo de autorización de la versión 2.0. Si el usuario tiene una sesión de explorador válida con el extremo de la versión 2.0, no tendrá que volver a escribir sus credenciales. |
| Tokens de acceso (cuentas profesionales o educativas) | 1 hora | Se indican en las respuestas de tokens como parte de los metadatos de token. |
| Tokens de acceso (cuentas personales) | 1 hora | Se indican en las respuestas de tokens como parte de los metadatos de token. Los access\_tokens emitidos en nombre de las cuentas personales se pueden configurar para que tengan una duración distinta, pero lo habitual suele ser una hora. |
| Tokens de actualización (cuentas profesionales o educativas) | Hasta 14 días | Un token de actualización solo es válido durante un máximo de 14 días. Sin embargo, el token de actualización puede dejar de ser válido en cualquier momento por varias razones, por lo que tu aplicación tiene que continuar probando y usando un token de actualización hasta que se produzca un error o hasta que la aplicación lo sustituya por un nuevo token de actualización. Un token de actualización también dejará de ser válido si han pasado 90 días desde que el usuario introdujo sus credenciales. |
| Tokens de actualización (cuentas personales) | Hasta 1 año | Un token de actualización solo es válido durante un máximo de 1 año. Sin embargo, el token de actualización puede dejar de ser válido en cualquier momento por varias razones, por lo que tu aplicación tiene que continuar probando y usando un token de actualización hasta que se produzca un error. |
| Códigos de autorización (cuentas profesionales o educativas) | 10 minutos | Los códigos de autorización son de corta duración a propósito y se deben canjear inmediatamente por access\_tokens y refresh\_tokens cuando se reciben. |
| Códigos de autorización (cuentas personales) | 5 minutos | Los códigos de autorización son de corta duración a propósito y se deben canjear inmediatamente por access\_tokens y refresh\_tokens cuando se reciben. Los códigos de autorización emitidos en nombre de las cuentas personales también son de un solo uso. |

<!---HONumber=Nov15_HO3-->