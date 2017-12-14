---
title: Referencia de tokens de Azure Active Directory v2.0 | Microsoft Docs
description: "Los tipos de tokens y notificaciones emitidos por el punto de conexión de Azure AD v2.0"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 01994e067bd7ce0343f12ec3334a91bd062251a8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Referencia de tokens de Azure Active Directory v2.0
El punto de conexión de Azure Active Directory (Azure AD) v2.0 emite varios tipos de tokens de seguridad en cada [flujo de autenticación](active-directory-v2-flows.md). Esta referencia describe el formato, las características de seguridad y el contenido de cada tipo de token.

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con la versión 2.0 del punto de conexión. Para determinar si debe usar la versión 2.0 del punto de conexión, obtenga información sobre las [limitaciones de esta versión](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Tipos de tokens
El punto de conexión v2.0 admite el [protocolo de autorización OAuth 2.0](active-directory-v2-protocols.md), que usa tanto tokens de acceso como tokens de actualización. El punto de conexión de v2.0 también admite la autenticación y el inicio de sesión a través de [OpenID Connect](active-directory-v2-protocols.md). OpenID Connect presenta un tercer tipo de token: el token de identificador. Todos estos tokens se representan como un token de *portador*.

Un token de portador es un token de seguridad ligero que concede al portador acceso a un recurso protegido. El "portador" es cualquier parte que pueda presentar el token. Aunque una parte debe autenticarse con Azure AD para recibir el token de portador, si no se realizan los pasos para asegurar el token durante la transmisión y el almacenamiento, este puede interceptarse y ser usado por un usuario no deseado. Algunos tokens de seguridad tienen un mecanismo integrado que impide que partes no autorizadas puedan usarlos, pero los tokens de portador no disponen de este mecanismo. Los tokens de portador deben ser transportados en un canal seguro, como Seguridad de la capa de transporte (HTTPS). Si un token de portador se transmite sin este tipo de seguridad, una parte malintencionada puede usar un ataque de tipo "Man in the middle" para adquirir el token y usarlo a fin de obtener acceso sin autorización a un recurso protegido. Los mismos principios de seguridad se aplican al almacenamiento o almacenamiento en caché de tokens portadores para su uso posterior. Siempre debe asegurarse de que la aplicación transmite y almacena los tokens de portador de manera segura. Para otras consideraciones de seguridad sobre los tokens de portador, consulte la [sección 5 de RFC 6750](http://tools.ietf.org/html/rfc6750).

Muchos de los tokens emitidos por el punto de conexión de la versión 2.0 se implementan como JSON Web Tokens (JWT). Un JWT es una manera compacta y segura de la dirección URL para transferir información entre dos partes. La información contenida en un JWT se denomina *notificación*. Se trata de una aserción de información sobre el portador y el sujeto del token. En un JWT, las notificaciones son objetos de notación de objetos JavaScript (JSON) que se codifican y serializan para su transmisión. Como los JWT que emite el punto de conexión de la versión 2.0 están firmados pero no cifrados, puede inspeccionar fácilmente el contenido de un JWT con fines de depuración. Para más información sobre los JWT, consulte la [especificación de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokens de identificador
Un token de identificador es una forma de token de seguridad de inicio de sesión que recibe la aplicación cuando realiza la autenticación mediante [OpenID Connect](active-directory-v2-protocols.md). Los tokens de identificador se representan como si fueran [JWT](#types-of-tokens) y contienen notificaciones que se pueden usar para iniciar sesión del usuario en la aplicación. Puede usar las notificaciones en un token de identificador de varias maneras. Habitualmente, los administradores usan los tokens de identificador para mostrar información sobre la cuenta o para tomar decisiones relacionadas con el control de acceso en una aplicación. El punto de conexión de la versión 2.0 solo emite un tipo de token de identificador, que tiene un conjunto coherente de notificaciones, independientemente del tipo de usuario que inició sesión. El formato y el contenido de los tokens de identificador es el mismo, tanto para los usuarios de cuentas personales de Microsoft como para las cuentas profesionales o educativas.

Actualmente, los tokens de identificador están firmados pero no cifrados. Cuando la aplicación recibe un token de identificador, debe [validar la firma](#validating-tokens) para probar la autenticidad del token y validar algunas notificaciones en el token para probar su validez. Las notificaciones validadas por una aplicación varían en función de los requisitos del escenario, pero la aplicación debe realizar algunas [validaciones de notificación comunes](#validating-tokens) en todos los escenarios.

En las secciones siguientes, además de un token de identificador de ejemplo, se muestran todos los detalles sobre las notificaciones en los tokens de identificador. Tenga en cuenta que las notificaciones en los tokens de identificador no se devuelven en ningún orden específico. Además, se pueden agregar nuevas notificaciones en tokens de identificador en cualquier momento. La aplicación no se debe interrumpir cuando se agregan notificaciones nuevas. La lista siguiente incluye las notificaciones que la aplicación puede interpretar de manera confiable en el momento de redactar este artículo. Puede encontrar más detalles en la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Token de identificador de ejemplo
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Como práctica, para inspeccionar las notificaciones en el token de identificador de ejemplo, pegue el token de identificador de ejemplo en [calebb.net](http://calebb.net/).
>
>

#### <a name="claims-in-id-tokens"></a>Notificaciones en tokens de identificador
| Nombre | Notificación | Valor de ejemplo | Descripción |
| --- | --- | --- | --- |
| audience |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identifica al destinatario previsto del token. En los tokens de identificador, la audiencia es el identificador de aplicación de su aplicación, que se asigna a la aplicación en el portal de registro de aplicaciones de Microsoft. La aplicación debe validar este valor y rechazar el token si el valor no coincide. |
| issuer |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identifica el servicio de token de seguridad (STS) que construye y devuelve el token, así como el inquilino de Azure AD en el que se autenticó al usuario. La aplicación tiene que validar la notificación del emisor para asegurarse de que el token proviene del extremo de la versión 2.0. También debe usar la parte del GUID de la notificación para restringir el conjunto de inquilinos que pueden iniciar sesión en la aplicación. El GUID que indica que el usuario es un usuario consumidor desde una cuenta de Microsoft es `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| issued at |`iat` |`1452285331` |La hora en que se emitió el token, que se representa en tiempo de época. |
| expiration time |`exp` |`1452289231` |La hora en que el token deja de ser válido, que se representa en tiempo de época. La aplicación tiene que usar esta notificación para comprobar la validez de la duración del token. |
| not before |`nbf` |`1452285331` |Hora a la que el token pasa a ser válido, representada en tiempo de época. Normalmente es la misma que la hora de emisión. La aplicación tiene que usar esta notificación para comprobar la validez de la duración del token. |
| versión |`ver` |`2.0` |La versión del token de identificador, tal como lo define Azure AD. En el caso del punto de conexión de la versión 2.0, el valor es `2.0`. |
| tenant ID |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Un GUID que representa el inquilino de Azure AD de donde proviene el usuario. En el caso de las cuentas profesionales y educativas, el GUID es el identificador del inquilino inmutable de la organización a la que pertenece el usuario. En el caso de las cuentas personales, el valor es `9188040d-6c67-4c5b-b112-36a304b66dad`. El ámbito `profile` es necesario para recibir esta notificación. |
| code hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |El hash de código se incluye en los tokens de identificador solo cuando se emite el token de identificador con un código de autorización de OAuth 2.0. Se puede usar para validar la autenticidad de un código de autorización. Para detalles sobre cómo realizar esta validación, consulte la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| access token hash |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |El hash de token de acceso se incluye en los tokens de identificador solo cuando el token de identificador se emite con un token de acceso de OAuth 2.0. Se puede usar para validar la autenticidad de un token de acceso. Para detalles sobre cómo realizar esta validación, consulte la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| valor de seguridad |`nonce` |`12345` |El valor de seguridad es una estrategia para mitigar los ataques de reproducción de los tokens. La aplicación puede especificar un valor de seguridad en una solicitud de autorización mediante el parámetro de consulta `nonce` . El valor que proporciona en la solicitud se emite en la notificación `nonce` del token de identificador, sin modificaciones. La aplicación puede comprobar el valor con respecto al valor que especifica en la solicitud, que asocia la sesión de la aplicación con un token de identificador específico. La aplicación tiene que realizar esta validación durante el proceso de validación del token de identificador. |
| name |`name` |`Babe Ruth` |La notificación de nombre proporciona un valor en lenguaje natural que identifica al firmante del token. No se asegura que el valor sea único, es mutable y está diseñado para usarse solo con fines de visualización. El ámbito `profile` es necesario para recibir esta notificación. |
| email |`email` |`thegreatbambino@nyy.onmicrosoft.com` |La dirección de correo electrónico principal asociada con la cuenta de usuario, si existe. Su valor es mutable y podría cambiar en el tiempo. El ámbito `email` es necesario para recibir esta notificación. |
| preferred username |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |El nombre de usuario principal que representa al usuario en el punto de conexión de la versión 2.0. Puede ser una dirección de correo electrónico, un número de teléfono o un nombre de usuario genérico sin un formato especificado. Su valor es mutable y podría cambiar en el tiempo. Puesto que es mutable, este valor no debe usarse para tomar decisiones de autorización. El ámbito `profile` es necesario para recibir esta notificación. |
| subject |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | La entidad de seguridad sobre la que el token declara información como, por ejemplo, el usuario de una aplicación. Este valor es inmutable y no se puede reasignar ni volver a usar. Se puede usar para realizar comprobaciones de autorización de forma segura, por ejemplo, cuando el token se usa para acceder a un recurso, y se puede usar como clave en tablas de base de datos. Dado que el firmante siempre está presente en los tokens que emite Azure AD, se recomienda usar este valor en un sistema de autorización de propósito general. El asunto es, sin embargo, un identificador en pares (es único para un id. de aplicación determinado).  Por lo tanto, si un usuario inicia sesión en dos aplicaciones diferentes con dos identificadores de cliente diferente, esas aplicaciones recibirán dos valores diferentes para la notificación de asunto.  Esto puede ser o no deseable dependiendo de los requisitos de arquitectura y privacidad. |
| object ID |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | El identificador inmutable de un objeto en el sistema de identidades Microsoft, en este caso, una cuenta de usuario.  También se puede usar para realizar comprobaciones de autorización de forma segura y como clave en tablas de base de datos. Este identificador identifica de forma única el usuario entre aplicaciones: dos aplicaciones diferentes que inician sesión con el mismo usuario recibirán el mismo valor en la notificación `oid`.  Esto significa que puede usarse al realizar consultas en Microsoft Online Services, como Microsoft Graph.  Microsoft Graph devuelve este identificador como la propiedad `id` para una cuenta de usuario determinada.  Dado que la notificación `oid` permite que varias aplicaciones pongan en correlación a los usuarios, se requiere el ámbito `profile` para recibir esta notificación. Tenga en cuenta que si un usuario existe en varios inquilinos, el usuario contendrá un identificador de objeto distinto en cada inquilino, se consideran cuentas diferentes, incluso si el usuario inicia sesión en todas las cuentas con las mismas credenciales. |

### <a name="access-tokens"></a>Tokens de acceso
Actualmente, solo los servicios Microsoft pueden consumir los tokens de acceso que emite el punto de conexión de la versión 2.0. Las aplicaciones no necesitan realizar ninguna validación o inspección de los tokens de acceso para ninguno de los escenarios admitidos actualmente. Los tokens de acceso se pueden tratar como completamente opacos. Solo son cadenas que la aplicación puede pasar a Microsoft en las solicitudes HTTP.

En un futuro próximo, el extremo de la versión 2.0 podrá hacer que la aplicación reciba los tokens de acceso de otros clientes. En ese momento, la información que aparece en este tema de referencia se actualizará con la información que necesita para que la aplicación realice la validación del token de acceso y otras tareas similares.

Cuando solicitas un token de acceso desde el punto de conexión de la versión 2.0, este punto de acceso también devuelve metadatos sobre el token de acceso para el uso de la aplicación. Esta información incluye la hora de expiración del token de acceso y los ámbitos para los que es válido. La aplicación usa estos metadatos para realizar un almacenamiento en caché inteligente de los tokens de acceso sin tener que analizar y abrir el token de acceso mismo.

### <a name="refresh-tokens"></a>Tokens de actualización
Los tokens de actualización son tokens de seguridad que la aplicación puede usar para obtener nuevos tokens de acceso en un flujo de OAuth 2.0. La aplicación puede usar tokens de actualización para obtener acceso a largo plazo a los recursos en nombre de un usuario sin necesidad de interacción con el usuario.

Los tokens de actualización tienen varios recursos. Un token de actualización recibido durante una solicitud de token para un recurso se puede canjear para los tokens de acceso a un recurso completamente diferente.

Para recibir una actualización en la respuesta de un token, la aplicación debe solicitar el ámbito `offline_acesss` y obtener su concesión. Para más información sobre el ámbito `offline_access`, consulte el artículo sobre [consentimiento y ámbitos](active-directory-v2-scopes.md).

Los tokens de actualización son, y siempre serán, totalmente opacos para la aplicación. Los emite el punto de conexión de Azure AD v2.0 y solo los puede inspeccionar e interpretar el punto de conexión v2.0. También son de larga duración, pero no se debe componer la aplicación esperando que un token de actualización dure por cualquier período de tiempo. Los tokens de actualización pueden invalidarse en cualquier momento por diversos motivos. La única forma para que la aplicación sepa si un token de actualización es válido es intentar canjearlo realizando una solicitud de token al extremo de la versión 2.0.

Cuando se canjea un token de actualización por un nuevo token de acceso (y si se concedió el ámbito `offline_access` a la aplicación), se recibe un nuevo token de actualización en la respuesta del token. Debe guardar el token de actualización recién emitido; para ello, reemplace el que usó en la solicitud. Esto garantiza que los tokens de actualización sigan siendo válidos mientras sea posible.

## <a name="validating-tokens"></a>Validación de los tokens
En este momento, la única validación de tokens que deberían realizar las aplicaciones es la validación de tokens de identificador. Para validar un token de identificador, la aplicación debe validar tanto la firma del token de identificador como las notificaciones en el token de identificador.

<!-- TODO: Link -->
Microsoft proporciona bibliotecas y ejemplos de código que le muestran cómo controlar fácilmente la validación de token. En las secciones siguientes, se describe el proceso subyacente. Varias bibliotecas de código abierto de terceros también están disponibles para validación de JWT. Hay al menos una opción de biblioteca para casi cualquier plataforma y lenguaje.

### <a name="validate-the-signature"></a>validar la firma
Un JWT contiene tres segmentos, que están separados por el carácter `.` . El primer segmento se conoce como el *encabezado*, el segundo, como el *cuerpo*, y el tercero como la *firma*. El segmento de firma se puede usar para validar la autenticidad del token de identificador para que la aplicación pueda confiar en él.

Los tokens de identificador se firman con algoritmos de cifrado asimétrico estándar del sector, como RSA 256. El encabezado del token de identificador tiene información sobre el método de cifrado y la clave que se usan para firmar el token. Por ejemplo:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

La notificación `alg` indica el algoritmo que se usó para firmar el token. La notificación `kid` indica la clave pública que se usó para firmar el token.

En cualquier momento, el punto de conexión v2.0 puede firmar un token de identificador mediante cualquiera de un determinado conjunto de pares de claves pública y privada. El punto de conexión v2.0 gira el conjunto de claves posible de manera periódica, por lo que la aplicación debería estar escrita de manera de controlar automáticamente esos cambios de clave. Una frecuencia razonable para comprobar las actualizaciones de las claves públicas que usa el punto de conexión v2.0 es cada 24 horas.

Puede adquirir los datos de la clave de firma que necesita para validar la firma usando el documento de metadatos de OpenID Connect, ubicado en:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Pruebe la dirección URL en un explorador.
>
>

Este documento de metadatos es un objeto JSON que tiene varias piezas útiles de información, como la ubicación de los diferentes puntos de conexión que se necesitan para la autenticación de OpenID Connect.  El documento también incluye un *jwks_uri*, que ofrece la ubicación del conjunto de claves públicas que se usan para firmar los tokens. El documento JSON que se encuentra en el jwks_uri tiene toda la información de la clave pública que actualmente está en uso. La aplicación puede usar la notificación `kid` en el encabezado de JWT para seleccionar la clave pública que se usó en este documento para firmar un token. Luego realiza la validación de la firma mediante la clave pública correcta y el algoritmo indicado.

Cómo se realiza la validación de la firma queda fuera del ámbito de este documento. Hay muchas bibliotecas de código abierto disponibles para ayudarle con esto.

### <a name="validate-the-claims"></a>Validación de las notificaciones
Cuando la aplicación recibe un token de identificador al inicio de sesión del usuario, también debe realizar algunas comprobaciones de las notificaciones del token de identificador. Estas incluyen, pero no se limitan a:

* Notificación **audience**, para comprobar que estaba previsto proporcionar el token de identificador a la aplicación.
* Notificaciones **not before** y **expiration time**, para comprobar que el token de identificador no expiró.
* Notificación **issuer**, para comprobar que el punto de conexión v2.0 emitió el token a la aplicación.
* **nonce**, como mitigación de ataques de reproducción de tokens.

Para ver una lista completa de las validaciones de notificaciones que debe realizar la aplicación, consulte la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

En la sección sobre [tokens de identificador](# ID tokens) se incluyen detalles sobre los valores esperados para estas notificaciones.

## <a name="token-lifetimes"></a>Vigencia de los tokens
Proporcionamos la siguiente vigencia de los tokens solo como información. La información puede ayudarle a desarrollar y depurar aplicaciones. Las aplicaciones no se deben escribir esperando que estas vigencias permanezcan constantes. La vigencia de los tokens puede cambiar en cualquier momento, y así será.

| SWT | Vigencia | Descripción |
| --- | --- | --- |
| Tokens de identificador (cuentas profesionales o educativas) |1 hora |Habitualmente, los tokens de identificador son válidos durante 1 hora. La aplic. web puede usar esta misma vigencia para mantener su propia sesión con el usuario (recomendado), o bien puede elegir una vigencia de sesión completamente distinta. Si la aplicación necesita obtener un nuevo token de identificador, debe realizar una nueva solicitud de inicio de sesión en el punto de conexión de autorización v2.0. Si el usuario tiene una sesión de explorador válida con el punto de conexión v2.0, no se le solicitará que vuelva a escribir sus credenciales. |
| Tokens de identificador (cuentas personales) |24 horas |Habitualmente, los tokens de identificador para cuentas personales son válidos durante 24 horas. La aplic. web puede usar esta misma vigencia para mantener su propia sesión con el usuario (recomendado), o bien puede elegir una vigencia de sesión completamente distinta. Si la aplicación necesita obtener un nuevo token de identificador, debe realizar una nueva solicitud de inicio de sesión en el punto de conexión de autorización v2.0. Si el usuario tiene una sesión de explorador válida con el punto de conexión v2.0, no se le solicitará que vuelva a escribir sus credenciales. |
| Tokens de acceso (cuentas profesionales o educativas) |1 hora |Se indican en las respuestas de tokens como parte de los metadatos de token. |
| Tokens de acceso (cuentas personales) |1 hora |Se indican en las respuestas de tokens como parte de los metadatos de token. Los tokens de acceso que se emiten en nombre de las cuentas personales se pueden configurar para que tengan una vigencia distinta, pero lo habitual suele ser 1 hora. |
| Tokens de actualización (cuentas profesionales o educativas) |Hasta 14 días |Un token de actualización solo es válido durante un máximo de 14 días. Sin embargo, el token de actualización puede dejar de ser válido en cualquier momento por varias razones, por lo que la aplicación tiene que continuar probando y usando un token de actualización hasta que se produzca un error o hasta que la aplicación lo sustituya por un nuevo token de actualización. Un token de actualización también deja de ser válido si han pasado 90 días desde que el usuario escribió sus credenciales. |
| Tokens de actualización (cuentas personales) |Hasta 1 año |Un token de actualización solo es válido durante un máximo de 1 año. Sin embargo, el token de actualización puede dejar de ser válido en cualquier momento por varias razones, por lo que la aplicación debe continuar probando y usando un token de actualización hasta que se produzca un error. |
| Códigos de autorización (cuentas profesionales o educativas) |10 minutos |Los códigos de autorización son de corta duración a propósito y se deben canjear inmediatamente por tokens de acceso y tokens de actualización cuando se reciban. |
| Códigos de autorización (cuentas personales) |5 minutos |Los códigos de autorización son de corta duración a propósito y se deben canjear inmediatamente por tokens de acceso y tokens de actualización cuando se reciban. Los códigos de autorización emitidos en nombre de las cuentas personales son de un solo uso. |
