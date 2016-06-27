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
	ms.date="06/06/2016"
	ms.author="priyamo"/>

# Protocolos de autenticación de Azure Active Directory

Azure Active Directory (Azure AD) admite varios de los protocolos de autenticación y autorización utilizados más comúnmente.

En este conjunto de artículos, analizaremos algunos de los protocolos admitidos y su implementación en Azure AD. Mostraremos solicitudes y respuestas de muestra. Además, como la integración se va a realizar directamente con los protocolos, estos artículos son, en gran parte, independientes del lenguaje.

- [Autorización del acceso a aplicaciones web mediante OAuth 2.0 y Azure Active Directory](active-directory-protocols-oauth-code.md): aprenda sobre la concesión de autorización de "código de autorización" de OAuth2.0" y su implementación en Azure AD.
- [Descripción del flujo de concesión implícita de OAuth2 de Azure Active Directory (AD)](active-directory-dev-understanding-oauth2-implicit-grant.md): aprenda sobre la concesión de autorización "implícita" de OAuth 2.0, y si es adecuada para su aplicación.
- [Autorización del acceso a aplicaciones web con OpenID Connect y Azure Active Directory](active-directory-protocols-openid-connect-code.md): aprenda a usar el protocolo de autenticación OpenID Connect de Azure AD.
- [Cómo Azure Active Directory usa el protocolo SAML](active-directory-saml-protocol-reference.md): conozca cómo usar el protocolo SAML para admitir [inicio](active-directory-single-sign-on-protocol-reference.md) y [cierre de sesión único](active-directory-single-sign-out-protocol-reference.md) en Azure AD.


## Referencia y solución de problemas

En este conjunto de artículos se ofrece información adicional que podría ser útil para solucionar problemas de las aplicaciones de Azure AD, así como para ayudarlo a entender los conceptos más avanzados de Azure AD.

- [Metadatos de federación](active-directory-federation-metadata.md): sepa cómo buscar e interpretar los documentos de metadatos que Azure AD genera.
- [Tipos de notificaciones y tokens admitidos](active-directory-token-and-claims.md): aprenda sobre las distintas notificaciones de los tokens que emite Azure AD.
- [Sustitución de claves de firma de Azure Active Directory](active-directory-signing-key-rollover.md): aprenda sobre la cadencia de sustitución de claves de firma de Azure AD y sobre cómo actualizar la clave en los escenarios de aplicación más comunes.
- [Control de errores en OAuth 2.0](active-directory-error-handling.md): conozca cómo interpretar y solucionar los errores más comunes al usar OAuth 2.0 y Azure AD.
- [Prácticas recomendadas de OAuth 2.0 en Azure Active Directory](active-directory-oauth-best-practices.md): conozca los procedimientos recomendados al usar OAuth 2.0 en Azure AD y evitar errores habituales.

<!---HONumber=AcomDC_0615_2016-->