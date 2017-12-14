---
title: "Protocolos de autenticación de Azure Active Directory | Microsoft Docs"
description: "Información general sobre los protocolos de autenticación admitidos por Azure Active Directory (AD)."
documentationcenter: dev-center-name
author: priyamohanram
services: active-directory
manager: mtillman
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: 1387ce5f3f16301786cf4111a081a5486788fa77
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-authentication-protocols"></a>Protocolos de autenticación de Azure Active Directory
Azure Active Directory (Azure AD) admite varios de los protocolos de autenticación y autorización utilizados más comúnmente. En los temas de esta sección se describen los protocolos admitidos y su implementación en Azure AD. En los temas se incluye una revisión de los tipos de notificación compatibles, una introducción al uso de los metadatos de federación, documentación de referencia detallada sobre los protocolos OAuth 2.0. y SAML 2.0, y una sección de solución de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Artículos y referencia de protocolos de autenticación
* [Información importante acerca de la cadencia de sustitución de clave en Azure AD](active-directory-signing-key-rollover.md) : obtenga información sobre la cadencia de sustitución de clave de firma de Azure AD, los cambios que puede realizar para actualizar la clave automáticamente y una explicación sobre cómo actualizar los escenarios de aplicación más comunes.
* [Tipos de token y notificación compatibles](active-directory-token-and-claims.md): obtenga información sobre las notificaciones en los tokens que emite Azure AD.
* [Metadatos de federación](active-directory-federation-metadata.md) : obtenga información sobre cómo buscar e interpretar los documentos de metadatos que Azure AD genera.
* [OAuth 2.0 en Azure AD](active-directory-protocols-oauth-code.md) : obtenga información sobre la implementación de OAuth 2.0 en Azure AD.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) : obtenga información sobre cómo usar el protocolo de autorización OAuth 2.0 para la autenticación.
* [Llamadas de servicio a servicio con credenciales de cliente](active-directory-protocols-oauth-service-to-service.md): aprenda a usar el flujo de concesión de credenciales de cliente de OAuth 2.0 para las llamadas de servicio a servicio.
* [Llamadas de servicio a servicio con el flujo En nombre de](active-directory-protocols-oauth-on-behalf-of.md): aprenda a usar el flujo "En nombre de" de OAuth 2.0 en las llamadas de servicio a servicio.
* [Referencia del protocolo SAML](active-directory-saml-protocol-reference.md) : obtenga información sobre los perfiles SAML de inicio de sesión único y de cierre de sesión único de Azure AD.

## <a name="see-also"></a>Otras referencias
[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

[Ejemplos de código de Azure Active Directory](active-directory-code-samples.md)
