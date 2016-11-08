---
title: Azure Active Directory B2C | Microsoft Docs
description: Creación de aplicaciones directamente mediante los protocolos que admite Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock

---
# Azure AD B2C: protocolos de autenticación
Azure Active Directory (Azure AD) B2C proporciona identidad como servicio para sus aplicaciones gracias a la compatibilidad con dos protocolos estándar del sector: OpenID Connect y OAuth 2.0. Aunque el servicio cumple con la norma, pueden existir diferencias sutiles entre dos implementaciones cualesquiera de estos protocolos. La información de esta guía resultará útil si el código se escribe mediante el envío y el control directo de solicitudes HTTP, en lugar de utilizar una biblioteca de código abierto. Se recomienda que lea esta página antes de profundizar en los detalles de cada protocolo específico. No obstante, si ya está familiarizado con Azure AD B2C, puede ir directamente a [las guías de referencia de protocolo](#protocols).

<!-- TODO: Need link to libraries above -->

## Conceptos básicos
Todas las aplicaciones que usen Azure AD B2C deben estar registrada en su directorio B2C del [Portal de Azure](https://portal.azure.com). El proceso de registro de la aplicación recopila y asigna algunos valores a la aplicación:

* Un **identificador de la aplicación** que identifica la aplicación de forma única.
* Un **URI de redirección** o **identificador de paquete** que se puede utilizar para dirigir las respuestas de nuevo a la aplicación.
* Algunos otros valores específicos de cada escenario. Para más información, consulte [Cómo registrar una aplicación con el punto de conexión v2.0](active-directory-b2c-app-registration.md).

Una vez registrada, la aplicación se comunica con Azure AD mediante el envío de solicitudes al punto de conexión v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

En casi todos los flujos de OAuth y OpenID Connect hay cuatro partes implicadas en el intercambio:

![Funciones de OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* El **servidor de autorización** es el punto de conexión v2.0 de Azure AD. Controla de manera segura todo lo relacionado con el acceso y la información de usuario. También controla las relaciones de confianza entre las partes de un flujo. Es responsable de garantizar la identidad del usuario, conceder y revocar el acceso a los recursos y emitir tokens. También es conocido como proveedor de identidades.
* El **propietario del recurso** suele ser el usuario final. Es la parte que posee los datos y tiene la capacidad de permitir que terceros tengan acceso a esos datos o recursos.
* El **cliente OAuth** es su aplicación. Se identifica mediante su identificador de aplicación. Suele ser la entidad con la que interactúan los usuarios finales. También solicita tokens del servidor de autorizaciones. El propietario del recurso debe conceder al cliente el permiso para tener acceso al recurso.
* El **servidor de recursos** es el lugar en que residen el recurso o los datos. Confía en el servidor de autorizaciones para autenticar y autorizar de forma segura al cliente OAuth. También usa tokens de acceso de portador para asegurarse de que se puede conceder el acceso a un recurso.

## Directivas
Puede decirse que las directivas de Azure AD B2C son las características más importantes del servicio. Azure AD B2C amplía los protocolos estándar de OAuth 2.0 y OpenID Connect mediante la introducción de las directivas. Estas permiten que Azure AD B2C realice tareas que van más allá de la mera autenticación y autorización. Las directivas describen por completo las experiencias de identidad del consumidor como el registro, el inicio de sesión y la edición de perfil. Las directivas se pueden definir en una interfaz de usuario administrativa. Se pueden ejecutar mediante un parámetro de consulta especial en solicitudes de autenticación de HTTP. Las directivas no son una característica estándar de OAuth 2.0 y OpenID Connect, así que debe dedicar tiempo a entenderlas. Para más información, consulte [Versión preliminar de Azure Active Directory B2C: marco de directiva extensible](active-directory-b2c-reference-policies.md).

## Tokens
La implementación de Azure AD B2C de OAuth 2.0 y OpenID Connect hace un uso generalizado de tokens de portador, incluidos los representados como tokens web JSON (JWT). Un token de portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. El "portador" es cualquier parte que pueda presentar el token. Para que una parte pueda recibir un token de portador, es necesario que Azure AD la autentique previamente. Sin embargo, si no se siguen los pasos necesarios para proteger el token en la transmisión y el almacenamiento, este podría interceptarse y utilizarse por un tercero.

Algunos tokens de seguridad tienen un mecanismo integrado que impide que partes no autorizadas puedan usarlos, pero los tokens de portador no disponen de este mecanismo. Deben ser transportados en un canal seguro, como la seguridad de la capa de transporte (HTTPS). Si un token de portador se transmite fuera de un canal seguro, un usuario malintencionado puede utilizar un ataque de tipo "man in the middle" para adquirir el token y usarlo para obtener acceso sin autorización a un recurso protegido. Los mismos principios de seguridad se aplican cuando los tokens de portador se almacenan o guardan en caché para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura.

Para ver más consideraciones de seguridad relativas a los tokens de portador, consulte la [sección 5 de RFC 6750](http://tools.ietf.org/html/rfc6750).

En [Versión preliminar de Azure AD B2C: referencia de tokens](active-directory-b2c-reference-tokens.md), encontrará más información sobre los distintos tipos de tokens que se usan en Azure AD B2C.

## Protocolos
Cuando esté listo para revisar algunas solicitudes de ejemplo, puede comenzar con uno de los siguientes tutoriales. Cada uno de ellos corresponde a un escenario de autenticación determinado. Si necesita ayuda para determinar qué flujo es el más apropiado en su caso, consulte [Versión preliminar de Azure Active Directory B2C: tipos de aplicaciones](active-directory-b2c-apps.md).

* [Creación de aplicaciones móviles nativas mediante OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Creación de aplicaciones web mediante OpenID Connect](active-directory-b2c-reference-oidc.md)

<!---HONumber=AcomDC_0727_2016-->