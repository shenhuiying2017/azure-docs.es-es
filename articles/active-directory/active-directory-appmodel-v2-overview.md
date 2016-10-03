<properties
	pageTitle="Información general del modelo de aplicaciones versión 2.0 | Microsoft Azure"
	description="Introducción a la creación de aplicaciones con inicio de sesión de cuentas de Microsoft y de Azure Active Directory."
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
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Inicio de sesión de usuarios de cuentas de Microsoft y de Azure AD en una sola aplicación

Antes, un desarrollador de aplicaciones que deseara admitir cuentas de Microsoft y Azure Active Directory debía realizar la integración con dos sistemas independientes. Hemos incorporado una nueva versión de API de autenticación que permite el inicio de sesión de los usuarios con ambos tipos de cuentas mediante el sistema de Azure AD. Este sistema de autenticación convergente se conoce como **punto de conexión v2.0**. Con el punto de conexión v2.0, una integración sencilla le permite llegar a una audiencia que abarca millones de usuarios con cuentas personales, así como profesionales y educativas.

Las aplicaciones que usan el punto de conexión v2.0 también pueden usar las API de REST de [Microsoft Graph](https://graph.microsoft.io) y [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) con cualquier tipo de cuenta.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## Introducción
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Elija su plataforma favorita para compilar una aplicación mediante nuestras bibliotecas de código abierto y marcos. Como alternativa, puede usar la documentación del protocolo OAuth 2.0 y OpenID Connect para enviar y recibir directamente mensajes de protocolo sin usar una biblioteca de autenticación.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Novedades
La información conceptual que aquí se describe será útil para entender qué se puede y no se puede hacer con el punto de conexión v2.0.

- Si compiló una aplicación durante la versión preliminar de 2015 del punto de conexión 2.0, no olvide [informarse sobre los últimos cambios en los protocolos](active-directory-v2-preview-oidc-changes.md) que hemos realizado.
- Obtenga información sobre los [tipos de aplicaciones que puede compilar con la versión 2.0 del punto de conexión](active-directory-v2-flows.md).
- Los desarrolladores familiarizados con Azure Active Directory deben comprobar las [actualizaciones de nuestros protocolos y las diferencias de la versión 2.0 del punto de conexión](active-directory-v2-compare.md).
- Comprenda [las limitaciones y restricciones](active-directory-v2-limitations.md) de la versión 2.0 del punto de conexión.

## Referencia
Estos vínculos le servirán para explorar la plataforma en profundidad:

- Build 2016: [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) (Introducción a las identidades de Microsoft: inicio de sesión de categoría empresarial para sus aplicaciones)
- Obtenga ayuda acerca del desbordamiento de pila con las etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
- [Referencia de protocolos de v2.0](active-directory-v2-protocols.md)
- [Referencia de los tokens de v2.0](active-directory-v2-tokens.md)
- [Ámbitos y consentimiento en el punto de conexión v2.0](active-directory-v2-scopes.md)
- [Portal de registro para aplicaciones de Microsoft](https://apps.dev.microsoft.com)
- [Referencia de API de REST de Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- A continuación se muestran las bibliotecas de cliente de código abierto y ejemplos probados con el punto de conexión v2.0.

  - [Servidor de identidades de Java WSO2](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Federación Java Gluu](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [Cliente básico de PHP OpenID Connect](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Cliente de OAuth2 para iOS](https://github.com/nxtbgthng/OAuth2Client)
  - [Cliente de OAuth2 para Android](https://github.com/wuman/android-oauth-client)
  - [Cliente de OpenID Connect para Android](https://github.com/kalemontes/OIDCAndroidLib)

<!---HONumber=AcomDC_0921_2016-->