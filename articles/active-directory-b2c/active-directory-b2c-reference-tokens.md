---
title: 'Referencia de tokens: Azure AD B2C | Microsoft Docs'
description: Tipos de tokens emitidos en Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: parakhj
ms.openlocfilehash: ce82fcc82cf411d1596fea56ff368d96eceeff38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: referencia de tokens

Azure Active Directory B2C (Azure AD B2C) emite varios tipos de tokens de seguridad a medida que procesa cada [flujo de autenticación](active-directory-b2c-apps.md). Este documento describe el formato, las características de seguridad y el contenido de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de tokens
Azure AD B2C admite el [protocolo de autorización de OAuth 2.0](active-directory-b2c-reference-protocols.md), que usa tanto tokens de acceso como tokens de actualización. También admite la autenticación y el inicio de sesión a través de [OpenID Connect](active-directory-b2c-reference-protocols.md), que introduce un tercer tipo de token, token de identificador. Todos estos tokens se representan como un token de portador.

Un token de portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. El "portador" es cualquier parte que pueda presentar el token. Para que una parte pueda recibir un token de portador, es necesario que Azure AD B2C la autentique previamente. Pero si no se realizan los pasos necesarios para proteger el token durante la transmisión y el almacenamiento, puede ser interceptado y usado por usuarios no previstos. Algunos tokens de seguridad disponen de un mecanismo integrado para evitar que usuarios no autorizados puedan usarlos, pero los tokens de portador no tienen este mecanismo. Deberán ser transportados en un canal seguro, como Seguridad de la capa de transporte (HTTPS).

Si un token de portador se transmite fuera de un canal seguro, un usuario malintencionado puede utilizar un ataque de tipo "man in the middle" para adquirir el token y usarlo para obtener acceso sin autorización a un recurso protegido. Los mismos principios de seguridad se aplican cuando los tokens de portador se almacenan o guardan en caché para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura.

Para ver más consideraciones de seguridad relativas a los tokens de portador, consulte la [sección 5 de RFC 6750](http://tools.ietf.org/html/rfc6750).

Muchos de los tokens emitidos por Azure AD B2C se implementan como tokens web JSON (JWT). Un JWT es un medio compacto y seguro de la dirección URL para transferir información entre dos partes. Los JWT contienen información conocida como notificaciones. Se trata de aserciones de información sobre el portador y el sujeto del token. En JWT, las notificaciones son objetos JSON que se codifican y serializan para su transmisión. Como los JWT emitidos por Azure AD B2C están firmados pero no cifrados, puede inspeccionar fácilmente el contenido de un JWT para depurarlo. Hay varias herramientas para hacerlo, entre ellas [jwt.ms](https://jwt.ms). Para más información acerca de los JWT, consulte las [especificaciones de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokens de identificador

Los tokens de identificador son una forma de token de seguridad que la aplicación recibe de los puntos de conexión `/authorize` y `/token` de Azure AD B2C. Los tokens de identificador se representan como si fueran [JWT](#types-of-tokens)y contienen notificaciones que se pueden usar para identificar a los usuarios de la aplicación. Cuando se adquieren los tokens de identificador del punto de conexión `/authorize`, el procedimiento se realizan utilizando el [flujo implícito](active-directory-b2c-reference-spa.md), que se usa a menudo para que los usuarios inicien sesión en aplicaciones web basadas en javascript. Cuando se adquieren los tokens de identificador del punto de conexión `/token`, el procedimiento se realizan utilizando el [flujo de código confidencial](active-directory-b2c-reference-oidc.md), que mantiene el token oculto al explorador. Esto permite que el token se envíe de forma segura en las solicitudes HTTP para la comunicación entre dos componentes de la misma aplicación o servicio. Puede usar las notificaciones en un token de identificador como considere oportuno. Normalmente se suelen usar para mostrar información sobre la cuenta o para tomar decisiones sobre control de acceso en una aplicación.  

Los tokens de identificador están firmados, pero actualmente no están cifrados. Cuando una aplicación o API recibe un token de identificador, debe [validar la firma](#token-validation) para demostrar que es auténtico. La aplicación o la API también debe validar algunas notificaciones del token para demostrar que es válido. En función de los requisitos del escenario, las notificaciones validadas por una aplicación varían, pero la aplicación debe realizar algunas [validaciones de notificación comunes](#token-validation) en todos los escenarios.

#### <a name="sample-id-token"></a>Token de identificador de ejemplo
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Tokens de acceso

Los tokens de acceso también son una forma de token de seguridad que la aplicación recibe de los puntos de conexión `/authorize` y `/token` de Azure AD B2C. Los tokens de acceso también se representan como [JWT](#types-of-tokens) y contienen notificaciones que puede usar para identificar los permisos concedidos a las API. Los tokens de acceso están firmados, pero actualmente no están cifrados. Los tokens de acceso se deberían utilizar para proporcionar acceso a servidores de recursos y API. Obtenga más información sobre cómo [usar los tokens de acceso](active-directory-b2c-access-tokens.md). 

Cuando una API recibe un token de acceso, debe [validar la firma](#token-validation) para demostrar que es auténtico. La API también debe validar algunas notificaciones del token para demostrar que es válido. En función de los requisitos del escenario, las notificaciones validadas por una aplicación varían, pero la aplicación debe realizar algunas [validaciones de notificación comunes](#token-validation) en todos los escenarios.

### <a name="claims-in-id-and-access-tokens"></a>Notificaciones en los tokens de identificador y de acceso

Con Azure AD B2C, tendrá un control preciso sobre el contenido de los tokens. Se puede configurar [directivas](active-directory-b2c-reference-policies.md) para enviar ciertos conjuntos de datos de usuario en las notificaciones que la aplicación necesita para sus operaciones. Dichas notificaciones pueden incluir propiedades estándar, tales como los valores de `displayName` y `emailAddress` del usuario. También pueden incluir [atributos de usuario personalizados](active-directory-b2c-reference-custom-attr.md) que se pueden definir en el directorio de B2C. Todos los tokens de identificador y de acceso que reciba contienen un conjunto concreto de notificaciones relacionadas con la seguridad. Las aplicaciones pueden usar estas notificaciones para autenticar usuarios y solicitudes de manera segura.

Tenga en cuenta que  las notificaciones de los tokens de identificador no se devuelven en ningún orden concreto. Además, se pueden agregar nuevas notificaciones en tokens de identificador en cualquier momento. No se debe interrumpir la aplicación cuando se agreguen nuevas notificaciones. Estas son las notificaciones que se espera que existan en los tokens de identificador y de acceso que emite Azure AD B2C. Las directivas determinan otras notificaciones adicionales. Para practicar, intente inspeccionar las notificaciones del token de identificador de ejemplo, pegándolo en [jwt.ms](https://jwt.ms). Puede encontrar más información al respecto en la [especificación OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

| Name | Notificación | Valor de ejemplo | Descripción |
| --- | --- | --- | --- |
| Público |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Una notificación de audiencia identifica al destinatario previsto del token. En el caso de Azure AD B2C, la audiencia es el identificador de la aplicación asignado a su aplicación en el portal de registro de aplicaciones. La aplicación tiene que validar este valor y rechazar el token si no coincide. |
| Emisor |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |La notificación identifica el servicio de token de seguridad (STS) que construye y devuelve el token. También identifica el directorio de Azure AD en el que se autenticó el usuario. La aplicación tiene que validar la notificación del emisor para asegurarse de que el token proviene del punto de conexión de Azure Active Directory 2.0. |
| Emitido a las |`iat` |`1438535543` |Esta notificación es la hora a la que se emitió el token, representada en tiempo de época. |
| Fecha de expiración |`exp` |`1438539443` |Esta notificación es la hora de expiración a la que el token deja de ser válido, representada en tiempo de época. La aplicación tiene que usar esta notificación para comprobar la validez de la duración del token. |
| No antes de |`nbf` |`1438535543` |Esta notificación es la hora a la que el token pasa a ser válido, representada en tiempo de época. Suele ser la misma hora a la que se emitió el token. La aplicación tiene que usar esta notificación para comprobar la validez de la duración del token. |
| Versión |`ver` |`1.0` |Versión del token de identificador, definida por Azure AD. |
| Código hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |El código hash se incluye en los tokens de identificador solo cuando el token se emite junto con un código de autorización de OAuth 2.0. Los códigos hash se pueden usar para validar la autenticidad de un código de autorización. Consulte la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para obtener más información sobre cómo llevar a cabo esta validación.  |
| Hash de token de acceso |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |El hash de token de acceso se incluye en los tokens de identificador solo cuando el token se emite junto con un token de acceso de OAuth 2.0. El hash de token de acceso se puede usar para validar la autenticidad de un token de acceso. Consulte la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para obtener más información sobre cómo llevar a cabo esta validación.  |
| Valor de seguridad |`nonce` |`12345` |El valor de seguridad es una estrategia que se usa para mitigar los ataques de reproducción de tokens. La aplicación puede especificar un valor de seguridad en una solicitud de autorización mediante el parámetro de consulta `nonce` . El valor que se proporcione en la solicitud solo se emitirá sin modificar en la notificación `nonce` de un token de identificador. Esto permite a la aplicación comprobar el valor con respecto al valor que especifica en la solicitud, que asocia la sesión de la aplicación con un token de identificador determinado. La aplicación tiene que realizar esta validación durante el proceso de validación del token de identificador. |
| Asunto |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Esta es la entidad de seguridad sobre la que el token declara información como, por ejemplo, el usuario de una aplicación. Este valor es inmutable y no se puede reasignar ni volver a usar. Se puede usar para realizar comprobaciones de autorización de forma segura, por ejemplo, cuando el token se usa para acceder a un recurso. De manera predeterminada, la notificación del asunto se rellena con el identificador de objeto del usuario del directorio. Para obtener más información, consulte [Azure Active Directory B2C: configuración de tokens, sesión e inicio de sesión único](active-directory-b2c-token-session-sso.md). |
| Referencia de clase de contexto de autenticación |`acr` |No aplicable |No se usa actualmente, salvo en caso de directivas anteriores. Para obtener más información, consulte [Azure Active Directory B2C: configuración de tokens, sesión e inicio de sesión único](active-directory-b2c-token-session-sso.md). |
| Directiva de marco de confianza |`tfp` |`b2c_1_sign_in` |Nombre de la directiva que se usó para emitir el token de identificador. |
| Hora de autenticación |`auth_time` |`1438535543` |Esta notificación es la hora a la que un usuario especificó sus credenciales por última vez, representada en tiempo de época. |

### <a name="refresh-tokens"></a>Tokens de actualización
Los tokens de actualización son tokens de seguridad que la aplicación puede usar para adquirir nuevos tokens de identificador y tokens de acceso en un flujo de OAuth 2.0. Permite a la aplicación obtener acceso a largo plazo a los recursos en nombre de los usuarios sin necesidad de interacción con los usuarios.

Para recibir un token de actualización en la respuesta de un token, la aplicación debe solicitar el ámbito `offline_acesss` . Para obtener más información sobre el ámbito `offline_access` , consulte la [referencia de protocolo de Azure AD B2C](active-directory-b2c-reference-protocols.md).

Los tokens de actualización son, y siempre serán, totalmente opacos para tu aplicación. Los emite Azure AD y solo Azure AD los puede inspeccionar e interpretar. Son de larga duración, pero la aplicación no se debe escribir esperando que un token de actualización dure un período de tiempo especificado. Los tokens de actualización pueden invalidarse en cualquier momento por varios motivos. La única forma para que tu aplicación sepa si un token de actualización es válido es intentar canjearlo mediante una solicitud de token a Azure AD.

Cuando se canjea un token de actualización por un token nuevo (y si se ha otorgado el ámbito `offline_access` a la aplicación), se recibe un nuevo token de actualización en la respuesta del token. Debe guardar el token de actualización recién emitido. Reemplazará al token de actualización que usó anteriormente en la solicitud. Esto ayuda a garantizar que los tokens de actualización sigan siendo válidos mientras sea posible.

## <a name="token-validation"></a>Validación de tokens
Para validar un token, la aplicación debe comprobar tanto la firma como las notificaciones del token.

Hay muchas bibliotecas de código abierto disponibles para validar los JWT según su lenguaje preferido. Se recomienda explorar esas opciones en lugar de implementar su propia lógica de validación. La información de esta guía puede ayudarle a aprender a usar correctamente esas bibliotecas.

### <a name="validate-the-signature"></a>validar la firma
Un JWT contiene tres segmentos, separados por el carácter `.` . El primer segmento es el *encabezado*, el segundo el *cuerpo* y el tercero la *firma*. El segmento de firma se puede utilizar para validar la autenticidad del token con el fin de que la aplicación pueda confiar en él.

Los tokens de Azure AD B2C se firman con algoritmos de cifrado asimétrico estándar del sector, como RSA 256. El encabezado del token contiene información acerca del método de cifrado y la clave utilizados para firmar el token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

La notificación `alg` indica el algoritmo que se usó para firmar el token. La notificación `kid` indica la clave pública particular que se usó para firmar el token.

En cualquier momento, Azure AD puede firmar un token mediante cualquiera de un determinado conjunto de pares de claves pública y privada. Azure AD rota los posibles conjuntos de claves de forma periódica, por lo que la aplicación debería estar escrita para tratar esos cambios de clave automáticamente. Una frecuencia razonable para comprobar las actualizaciones de las claves públicas usadas por Azure AD es cada 24 horas.

Azure AD B2C tiene un punto de conexión de metadatos OpenID Connect. Esto permite a las aplicaciones obtener información acerca de Azure AD B2C en tiempo de ejecución. En esta información se incluyen los extremos, los contenidos del token y las claves de firma de los token. Su directorio B2C contiene un documento de metadatos JSON para cada directiva. Por ejemplo, el documento de metadatos de la directiva `b2c_1_sign_in` en `fabrikamb2c.onmicrosoft.com` se encuentra en:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` es el directorio de B2C que se usa para autenticar el usuario, mientras que `b2c_1_sign_in` es la directiva que se emplea para adquirir el token. Para determinar qué directiva se usó para firmar un token (y dónde obtener los metadatos), tiene dos opciones. En primer lugar, el nombre de la directiva se incluye en la notificación `acr` del token. Las notificaciones se pueden analizar fuera del cuerpo del JWT; para ello, descodifique la descodificación en base 64 del cuerpo y deserialice la cadena JSON resultante. La notificación `acr` será el nombre de la directiva que se usó para emitir el token.  La otra opción consiste en codificar la directiva en el valor del parámetro `state` al emitir la solicitud y descodificarla para determinar qué directiva se ha usado. Cualquiera de estos métodos es válido.

El documento de metadatos es un objeto JSON que contiene varias piezas de información útiles. Estas son la ubicación de los puntos de conexión necesarios para realizar la autenticación de OpenID Connect. También incluye un `jwks_uri`, que ofrece la ubicación del conjunto de claves públicas que se usan para firmar los tokens. La ubicación se proporciona aquí, pero es mejor capturarla dinámicamente mediante el documento de metadatos y el análisis de `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

El documento JSON que se encuentra en esta dirección URL contiene toda la información de clave pública en uso en ese momento determinado. La aplicación puede usar la notificación `kid` en el encabezado de JWT para seleccionar en el documento JSON la clave pública que se ha usado en este documento para firmar un token concreto. Después, puede realizar la validación de la firma mediante la clave pública correcta y el algoritmo indicado.

La descripción de cómo realizar la validación de la firma queda fuera del ámbito de este documento. Hay muchas bibliotecas de código abierto disponibles para ayudarle con esto, si lo necesita.

### <a name="validate-the-claims"></a>Validación de las notificaciones
Cuando una aplicación o una API reciben un token de identificador, también es preciso realizar varias comprobaciones de las notificaciones en el token de identificador. Estas incluyen, pero no se limitan a:

* Notificación de **audiencia** : comprueba que está previsto que el token de identificador se proporcione a tu aplicación.
* Notificaciones **no antes de** y **hora de expiración**: comprueban que el token de identificador no expiró.
* Notificación **emisor** : comprueba que Azure AD emitió el token para la aplicación.
* **Valor de seguridad**: es una estrategia para mitigar los ataques de reproducción de tokens.

Para ver una lista completa de las validaciones que la aplicación debe llevar a cabo, consulte la [especificación de OpenID Connect](https://openid.net). En la anterior [sección sobre tipos de token](#types-of-tokens)se incluye información detallada de los valores que se esperan para estas notificaciones.  

## <a name="token-lifetimes"></a>Vigencia de los tokens
Las siguientes vigencias de los tokens se proporcionan para ampliar sus conocimientos. Pueden ayudarle a desarrollar y depurar aplicaciones. Tenga en cuenta que las aplicaciones no se deben escribir esperando que estas vigencias permanezcan constantes. Pueden cambiar y lo harán. Obtenga más información sobre la [personalización de la duración de los tokens](active-directory-b2c-token-session-sso.md) en Azure AD B2C.

| SWT | Vigencia | Descripción |
| --- | --- | --- |
| Tokens de identificador |Una hora |Los tokens de identificador normalmente son válidos durante una hora. La aplicación web puede usar esta vigencia para mantener sus propias sesiones con los usuarios (recomendado). También puede elegir una vigencia de sesión diferente. Si la aplicación necesita obtener un nuevo token de identificador, solo tiene que realizar una nueva solicitud de inicio de sesión a Azure AD. Si el usuario tiene una sesión de explorador válida con Azure AD, es posible que el usuario no tenga que volver a escribir sus credenciales. |
| Tokens de actualización |Hasta 14 días |Un token de actualización solo es válido durante un máximo de 14 días. Pero un token de actualización puede dejar de ser válido en cualquier momento por una serie de motivos. La aplicación debe continuar intentando usar un token de actualización hasta que se produce un error en la solicitud o hasta que la aplicación reemplaza el token de actualización por uno nuevo. Un token de actualización también puede dejar de ser válido si transcurren 90 días desde que el usuario especificó sus credenciales por última vez. |
| Códigos de autorización |Cinco minutos |Los códigos de autorización son de corta duración intencionadamente. Deben canjearse inmediatamente por tokens de acceso, tokens de identificador o tokens de actualización cuando se reciben. |

