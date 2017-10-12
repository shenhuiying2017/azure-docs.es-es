---
title: "Información acerca de los diferentes tipos de tokens y notificaciones compatibles con Azure AD | Microsoft Docs"
description: "Una guía en la que se describen y evalúan las notificaciones en los tokens SAML 2.0 y los tokens web JSON (JWT) emitidos por Azure Active Directory (AAD)"
documentationcenter: na
author: dstrockis
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: be28230b9c56dcbca4ba8f70e44741f65a447f73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-token-reference"></a>Referencia de tokens de Azure AD
Azure Active Directory (Azure AD) emite varios tipos de tokens de seguridad durante el procesamiento de cada flujo de autenticación. Este documento describe el formato, las características de seguridad y el contenido de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de tokens
Azure AD admite el [protocolo de autorización OAuth 2.0](active-directory-protocols-oauth-code.md), que usa access_tokens y refresh_tokens.  También admite la autenticación y el inicio de sesión a través de [OpenID Connect](active-directory-protocols-openid-connect-code.md), que introduce un tercer tipo de token, id_token.  Todos estos tokens se representan como un "token de portador".

Un token portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. En este sentido, el "portador" es cualquier parte que pueda presentar el token. Aunque se requiere autenticación con Azure AD para recibir un token de portador, deben realizarse una serie de pasos para proteger el token, con el fin de evitar interceptaciones imprevistas. Dado que los tokens de portador no disponen de ningún mecanismo integrado que evite que terceros no autorizadas los usen, se deben transportar en un canal seguro como, por ejemplo, Seguridad de la capa de transporte (HTTPS). Si un token portador se transmite sin cifrar, se puede utilizar un ataque del tipo "Man in the middle" para adquirir el token y obtener acceso no autorizado a un recurso protegido. Los mismos principios de seguridad se aplican al almacenamiento o almacenamiento en caché de tokens portadores para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura. Para otras consideraciones sobre la seguridad de los tokens portadores, consulte la [Sección 5 de RFC 6750](http://tools.ietf.org/html/rfc6750).

Muchos de los tokens emitidos por Azure AD se implementan como tokens web JSON o JWT.  Un JWT es un medio compacto y seguro de la dirección URL para transferir información entre dos partes.  La información contenida en los JWT se conoce como "notificaciones", o aserciones de información sobre el portador y el asunto del token.  Las notificaciones de JWT son los objetos JSON codificados y serializados para su transmisión.  Puesto que los JWT emitidos por Azure AD están firmados, pero no cifrados, puede inspeccionar fácilmente el contenido de un JWT con fines de depuración.  Para ello hay varias herramientas disponibles, como [jwt.ms](https://jwt.ms/). Para obtener más información sobre los JWT, consulte la [especificación de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_Tokens
Los id_tokens son una forma de token de seguridad de inicio de sesión que recibe la aplicación cuando la autenticación se realiza mediante [OpenID Connect](active-directory-protocols-openid-connect-code.md).  Se representan como [JWT](#types-of-tokens) y contienen notificaciones que se pueden usar para que el usuario inicie sesión en la aplicación.  Puede utilizar las notificaciones de un id_token como le convenga. Normalmente se usan para mostrar información de la cuenta o tomar decisiones de control de acceso en una aplicación.

En ese momento los id_tokens están firmados, pero no cifrados.  Cuando la aplicación recibe un id_token tiene que [validar la firma](#validating-tokens) para probar la autenticidad del token y validar algunas notificaciones en el token para probar su validez.  Las notificaciones validadas por una aplicación varían dependiendo de los requisitos de escenario, pero hay algunas [validaciones de notificación comunes](#validating-tokens) que la aplicación debe realizar en todos los escenarios.

Para obtener información sobre las notificaciones de id_tokens, así como un id_token de ejemplo, consulte la siguiente sección.  Tenga en cuenta que  las notificaciones de los id_tokens no se devuelven en ningún orden concreto.  Además, se pueden introducir nuevas notificaciones en los id_tokens en cualquier momento y no se debe interrumpir la aplicación cuando se introducen nuevas notificaciones.  La siguiente lista incluye las notificaciones que la aplicación puede interpretar de forma confiable en el momento de redactar este artículo.  Si es necesario, se pueden encontrar más detalles en la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Ejemplo de id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Para practicar, intente inspeccionar las notificaciones del token de identificador de ejemplo, pegándolo en [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>Notificaciones en los id_tokens
> [!div class="mx-codeBreakAll"]
| Notificación de JWT | Nombre | Description |
| --- | --- | --- |
| `appid` |Identificador de aplicación |Identifica la aplicación que usa el token para acceder a un recurso. La aplicación puede actuar por sí misma o en nombre de un usuario. Normalmente, el id. de aplicación representa un objeto de aplicación, pero también puede representar un objeto de entidad de servicio en Azure AD. <br><br> **Valor de JWT de ejemplo**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud` |Público |El destinatario previsto del token. La aplicación que recibe el token debe comprobar que el valor de la audiencia sea correcto y rechazar cualquier token destinado a una audiencia diferente. <br><br> **Valor de SAML de ejemplo**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Valor de JWT de ejemplo**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Referencia de clase de contexto de autenticación de aplicación |Indica cómo se autenticó el cliente. Para un cliente público, el valor es 0. Si se usan el id. de cliente y el secreto de cliente, el valor es 1. <br><br> **Valor de JWT de ejemplo**: <br> `"appidacr": "0"` |
| `acr` |Referencia de clase de contexto de autenticación |Indica cómo se autenticó el firmante, en lugar del cliente, como es el caso en la notificación de la referencia de clase de contexto de autenticación de aplicación. Un valor de "0" indica que la autenticación del usuario final no ha cumplido los requisitos de ISO/IEC 29115. <br><br> **Valor de JWT de ejemplo**: <br> `"acr": "0"` |
| Instante de autenticación |Registra la fecha y la hora de la autenticación. <br><br> **Valor de SAML de ejemplo**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Método de autenticación |Identifica cómo se autenticó el firmante del token. <br><br> **Valor de SAML de ejemplo**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Valor de JWT de ejemplo**: `“amr”: ["pwd"]` |
| `given_name` |Nombre |Proporciona el nombre de pila o "dado" del usuario, tal como se establece en el objeto de usuario de Azure AD. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"given_name": "Frank"` |
| `groups` |Grupos |Proporciona identificadores de objeto que representan la pertenencia al grupo del firmante. Estos valores son únicos (vea el id. de objeto) y se pueden usar de forma segura para administrar el acceso, por ejemplo, para exigir autorización para tener acceso a un recurso. Los grupos incluidos en la notificación de grupos se configuran por aplicación mediante la propiedad "groupMembershipClaims" del manifiesto de aplicación. Un valor null excluirá todos los grupos, un valor de "SecurityGroup" incluirá únicamente la pertenencia a grupos de seguridad de Active Directory y un valor de "All" incluirá grupos de seguridad y listas de distribución de Office 365. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` |Proveedor de identidades |Registra el proveedor de identidades que autenticó al firmante del token. Este valor es idéntico al valor de la notificación del emisor, a menos que la cuenta de usuario esté en un inquilino diferente que el emisor. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Almacena la hora a la que se emitió el token. A menudo se usa para medir la actualización de tokens. <br><br> **Valor de SAML de ejemplo**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Valor de JWT de ejemplo**: <br> `"iat": 1390234181` |
| `iss` |Emisor |Identifica el servicio de token de seguridad (STS) que construye y devuelve el token. En los tokens que devuelve Azure AD, el emisor es sts.windows.net. El GUID en el valor de notificación del emisor es el id. de inquilino del directorio de Azure AD. El id. de inquilino es un identificador inmutable y confiable del directorio. <br><br> **Valor de SAML de ejemplo**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Valor de JWT de ejemplo**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Apellido |Proporciona el apellido del usuario según está definido en el objeto de usuario de Azure AD. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"family_name": "Miller"` |
| `unique_name` |Nombre |Proporciona un valor en lenguaje natural que identifica al firmante del token. No se asegura que este valor sea único dentro de un inquilino y está diseñado para usarse solo con fines de visualización. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |Id. de objeto |Contiene un identificador único de un objeto en Azure AD. Este valor es inmutable y no se puede reasignar ni volver a usar. Use el id. de objeto para identificar un objeto en las consultas a Azure AD. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Roles |Representan todos los roles de aplicación que se han concedido al firmante directa e indirectamente a través de la pertenencia a grupos y se puede usar para exigir el control de acceso basado en roles. Los roles de aplicación se definen por aplicación mediante la propiedad `appRoles` del manifiesto de aplicación. La propiedad `value` de cada rol de aplicación es el valor que aparece en la notificación de roles. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Scope |Indica los permisos de suplantación concedidos a la aplicación cliente. El permiso predeterminado es `user_impersonation`. El propietario del recurso protegido puede registrar valores adicionales en Azure AD. <br><br> **Valor de JWT de ejemplo**: <br> `"scp": "user_impersonation"` |
| `sub` |Asunto |Identifica la entidad de seguridad sobre la que el token declara información como, por ejemplo, el usuario de una aplicación. Este valor es inmutable y no se puede reasignar o volver a usar, por lo que se puede usar para realizar comprobaciones de autorización de forma segura. Dado que el firmante siempre está presente en los tokens que emite Azure AD, se recomienda usar este valor en un sistema de autorización de propósito general. <br> `SubjectConfirmation` no es una notificación. Describe cómo se comprueba el firmante del token. `Bearer` indica que el firmante se confirma mediante su posesión del token. <br><br> **Valor de SAML de ejemplo**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Valor de JWT de ejemplo**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |Id. de inquilino |Identificador inmutable y no reutilizable que identifica al inquilino de directorio que emitió el token. Puede usar este valor para tener acceso a recursos de directorio específicos del inquilino en una aplicación multiempresa. Por ejemplo, puede usar este valor para identificar al inquilino en una llamada a API Graph. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Vigencia del token |Define el intervalo de tiempo dentro del cual es válido un token. El servicio que valida el token debe comprobar que la fecha actual está dentro de la vigencia del token; de lo contrario, debe rechazarlo. El servicio puede permitir que el intervalo de vigencia del token se supere en hasta cinco minutos para tener en cuenta las diferencias en la hora del reloj ("desfase temporal") entre Azure AD y el servicio. <br><br> **Valor de SAML de ejemplo**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Valor de JWT de ejemplo**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Nombre principal de usuario |Almacena el nombre de usuario de la entidad de seguridad del usuario.<br><br> **Valor de JWT de ejemplo**: <br> `"upn": frankm@contoso.com` |
| `ver` |Versión |Almacena el número de versión del token. <br><br> **Valor de JWT de ejemplo**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Tokens de acceso
Tras una autenticación correcta, Azure AD devuelve un token de acceso, que puede utilizarse para acceder a recursos protegidos. El token de acceso es un JSON Web Token (JWT) con codificación Base 64 y se puede inspeccionar su contenido pasándolo por un descodificador.

Si la aplicación solo *utiliza* tokens de acceso para obtener acceso a las API, puede (y debe) tratar los tokens de acceso como completamente opacos: son simplemente cadenas que su aplicación puede pasar a los recursos en las solicitudes HTTP.

Al solicitar un token de acceso, Azure AD también devuelve algunos metadatos sobre el token de acceso para el consumo de la aplicación.  Esta información incluye la hora de expiración del token de acceso y los ámbitos para los que es válido.  Esto permite a la aplicación realizar un almacenamiento inteligente en caché de los tokens de acceso sin tener que analizar y abrir el mismo token de acceso.

Si la aplicación es una API protegida con Azure AD que espera tokens de acceso en las solicitudes HTTP, debe llevar a cabo la validación y la inspección de los tokens que recibe. La aplicación debe realizar la validación del token de acceso antes de usarlo para acceder a los recursos. Para obtener más información sobre la validación, consulte [Validación de los tokens](#validating-tokens).  
Para obtener más información acerca de cómo hacer esto con. NET, consulte cómo [proteger una API web mediante tokens de portador de Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

## <a name="refresh-tokens"></a>Tokens de actualización

Los tokens de actualización son tokens de seguridad que la aplicación puede utilizar para adquirir nuevos tokens de acceso en un flujo de OAuth 2.0.  Permite a la aplicación obtener acceso a largo plazo a los recursos en nombre de un usuario sin necesidad de que intervenga el usuario.

Los tokens de actualización tienen varios recursos.  Es decir, un token de actualización recibido durante una solicitud de token para un recurso se puede canjear para los tokens de acceso a un recurso completamente diferente. Para ello, establezca el parámetro `resource` en la solicitud para el recurso de destino.

Los tokens de actualización son totalmente opacos para su aplicación. También son de larga duración, pero no se debe componer la aplicación esperando que un token de actualización dure por cualquier período de tiempo.  Los tokens de actualización pueden ser invalidados en cualquier momento por varios motivos.  La única forma para que su aplicación sepa si un token de actualización es válido es intentar canjearlo; para ello, realice una solicitud de token a un punto de conexión de token de Azure AD.

Al canjear un token de actualización por un nuevo token de acceso, recibirá un nuevo token de actualización en la respuesta del token.  Tienes que guardar el token de actualización recién emitido reemplazando el utilizado en la solicitud.  Esto garantizará que los tokens de actualización sigan siendo válidos mientras sea posible.

## <a name="validating-tokens"></a>Validación de los tokens

Para validar un id_token o un access_token, la aplicación tiene que validar tanto la firma como las notificaciones del token. Para validar los tokens de acceso, la aplicación también debe validar el emisor, la audiencia y los tokens de firmas. Deben validarse con los valores del documento de detección de OpenID. Por ejemplo, la versión independiente de inquilino del documento se encuentra en [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration). El middleware de Azure AD tiene funciones integradas para validar los tokens de acceso, y usted puede explorar nuestros [ejemplos](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-code-samples) para buscar uno en el idioma de su elección. Para obtener más información sobre cómo validar explícitamente un token JWT, consulte el [ejemplo de una validación manual de JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).  

Ofrecemos bibliotecas y ejemplos de código que muestran cómo realizar fácilmente la validación del token; la siguiente información se proporciona solo para quienes quieran entender el proceso subyacente.  También hay varias bibliotecas de código abierto de terceros para la validación de JWT; hay al menos una opción para casi cualquier plataforma e idioma. Para más información acerca de los ejemplos de código y las bibliotecas de autenticación de Azure AD, consulte [Bibliotecas de autenticación de Azure Active Directory](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Validación de la firma

Un JWT contiene tres segmentos, que están separados por el carácter `.` .  El primer segmento se conoce como el **encabezado**, el segundo como el **cuerpo** y el tercero como la **firma**.  El segmento de firma se puede utilizar para validar la autenticidad del token con el fin de que la aplicación pueda confiar en él.

Los tokens emitidos por Azure AD se firman con algoritmos de cifrado asimétrico estándar del sector, como RSA 256. El encabezado de JWT contiene información acerca de clave y el método de cifrado utilizados para firmar el token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

La notificación `alg` indica el algoritmo que se usó para firmar el token, mientras que la notificación `x5t` indica la clave pública concreta que se usó para firmar el token.

En cualquier momento, Azure AD puede firmar un id_token mediante cualquiera de un determinado conjunto de pares de claves pública y privada. Azure AD rota los posibles conjuntos de claves de forma periódica, por lo que su aplicación debería estar escrita para manejar esos cambios de clave automáticamente.  Una frecuencia razonable para comprobar las actualizaciones de las claves públicas usadas por Azure AD es cada 24 horas.

Puedes adquirir los datos de las claves de firmas necesarios para validar la firma utilizando el documento de metadatos de OpenID Connect, ubicado en:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Pruebe esta dirección URL en un explorador.
> 
> 

Este documento de metadatos es un objeto JSON que contiene varias piezas útiles de información, como la ubicación de los diferentes extremos necesarios para realizar la autenticación de OpenID Connect.  

También incluye un `jwks_uri`, que ofrece la ubicación del conjunto de claves públicas que se utilizan para firmar los tokens.  El documento JSON que se encuentra en `jwks_uri` contiene toda la información de clave pública en uso en ese momento concreto.  La aplicación puede usar la notificación `kid` en el encabezado de JWT para seleccionar la clave pública que se ha usado en este documento para firmar un determinado token.  Después, puede realizar la validación de la firma mediante la clave pública correcta y el algoritmo indicado.

La realización de la validación de la firma queda fuera del ámbito de este documento, pero hay muchas bibliotecas de código abierto disponibles para ayudarte a hacerlo si es necesario.

#### <a name="validating-the-claims"></a>Validación de las notificaciones

Cuando la aplicación recibe un token (un id_token al inicio de sesión de usuario o un token de acceso como token de portador en la solicitud HTTP), debe realizar algunas comprobaciones respecto a las notificaciones del token.  Estas incluyen, pero no se limitan a:

* Notificación **Audience** (Audiencia): comprueba que el token estuviera previsto para proporcionarlo a la aplicación.
* Notificaciones **Not Before** (No antes de) y **Fecha de expiración**: comprueban que el token no haya expirado.
* Notificación **Emisor** : comprueba que el token fue emitido realmente a la aplicación por Azure AD.
* **Nonce**: mitiga ataques de reproducción de tokens.
* y mucho más...

Para ver una lista completa de las validaciones de notificaciones que la aplicación debe realizar con los tokens de identificador, consulte la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). En la [sección sobre id_token](#id-tokens) anterior se proporciona información detallada sobre los valores esperados para estas notificaciones.

## <a name="sample-tokens"></a>Tokens de ejemplo

Esta sección muestra ejemplos de tokens SAML y JWT que Azure AD devuelve. Estos ejemplos le permiten ver las notificaciones en contexto.

### <a name="saml-token"></a>Token SAML

Este es un ejemplo de token SAML típico.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>Token JWT: suplantación de usuario
A continuación se muestra un ejemplo de token web JSON (JWT) típico que se usa en un flujo de concesión de códigos de autorización.
Además de las notificaciones, el token incluye un número de versión en **ver** y **appidacr**, la referencia de clase de contexto de autenticación, que indica cómo se autenticó el cliente. Para un cliente público, el valor es 0. Si se ha usado el id. de cliente o el secreto de cliente, el valor es 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Contenido relacionado
* Para más información acerca de la administración de la directiva de vigencia de token mediante la API de Azure AD Graph, consulte las [operaciones de directivas](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) y la [entidad de directiva](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) de Azure AD Graph.
* Para más información y ejemplos acerca de cómo administrar las directivas a través de los cmdlets de PowerShell, incluidos ejemplos, consulte [Configurable Token Lifetimes in Azure Active Directory](../active-directory-configurable-token-lifetimes.md) (Vigencias de tokens configurables en Azure Active Directory). 
