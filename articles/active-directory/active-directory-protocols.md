<properties
	pageTitle="Protocolos de autenticación de Azure Active Directory | Microsoft Azure"
	description="En este artículo proporciona una introducción a los diversos protocolos de autenticación y autorización que admite Azure Active Directory."
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

# Protocolos de autenticación de Azure Active Directory

Azure Active Directory (Azure AD) admite varios de los protocolos de autenticación y autorización utilizados más comúnmente.

En este conjunto de artículos, analizaremos algunos de los protocolos admitidos y su implementación en Azure AD. Mostraremos solicitudes y respuestas de muestra. Además, como la integración se va a realizar directamente con los protocolos, estos artículos son, en gran parte, independientes del lenguaje.

- [OAuth 2.0 en Azure AD](active-directory-protocols-oauth-code.md): obtenga información sobre el flujo de concesión de autorización de OAuth2.0 y su implementación en Azure AD.
- [Open ID Connect 1.0](active-directory-protocols-openid-connect-code.md): aprenda a usar el protocolo de autenticación OpenID Connect de Azure AD.
- [Referencia del protocolo SAML](active-directory-saml-protocol-reference.md): obtenga información sobre cómo usar el protocolo SAML para admitir [inicio](active-directory-single-sign-on-protocol-reference.md) y [cierre de sesión único](active-directory-single-sign-out-protocol-reference.md) en Azure AD.


## Referencia y solución de problemas

En este conjunto de artículos se ofrece información adicional que podría ser útil para solucionar problemas de las aplicaciones de Azure AD, así como para ayudarlo a entender los conceptos más avanzados de Azure AD.

- [Metadatos de federación](active-directory-federation-metadata.md): obtenga información sobre cómo buscar e interpretar los documentos de metadatos que Azure AD genera.
- [Tipos de token y notificación compatibles](active-directory-token-and-claims.md): obtenga información sobre las notificaciones de los tokens que emite Azure AD.
- [Sustitución de claves de firma en Azure AD](active-directory-signing-key-rollover.md): obtenga información sobre la cadencia de sustitución de claves de firma de Azure AD y sobre cómo actualizar la clave en los escenarios de aplicación más comunes.
- [Solución de problemas de protocolos de autenticación](active-directory-error-handling.md): obtenga información sobre cómo interpretar y solucionar los errores más comunes al usar OAuth 2.0 y Azure AD.
- [Prácticas recomendadas de OAuth 2.0 en Azure AD](active-directory-oauth-best-practices.md): conozca los procedimientos que se recomiendan a la hora de usar OAuth 2.0 en Azure AD y evitar errores habituales.

<!---HONumber=AcomDC_0601_2016-->