---
title: "Punto de conexión v2.0 de Azure Active Directory | Documentos de Microsoft"
description: "Introducción a la creación de aplicaciones con inicio de sesión de cuentas Microsoft y de Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 446e39f57448cbc0cd9a6f93a388159d7eca5863
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Inicio de sesión de usuarios de cuentas Microsoft y Azure Active Directory en una sola aplicación
Antes, los desarrolladores de aplicaciones que querían admitir cuentas de Microsoft personales y profesionales de Azure Active Directory debían realizar la integración con dos sistemas independientes. El punto de conexión de la versión 2.0 de Azure Active Directory (Azure AD) presenta una nueva versión de la API de autenticación que simplifica este proceso. El punto de conexión de la versión 2.0 de Azure AD permite iniciar sesión con ambos tipos de cuenta mediante el uso de una única integración. Las aplicaciones que usan el punto de conexión de la versión 2.0 de Azure AD también pueden usar las API de REST de [Microsoft Graph API](https://graph.microsoft.io) con cualquier tipo de cuenta.

## <a name="getting-started"></a>Introducción
Elija su plataforma favorita de la lista siguiente para crear una aplicación mediante los marcos y bibliotecas de código abierto de Microsoft. También puede usar los protocolos OAuth 2.0 y OpenID Connect para enviar y recibir mensajes de protocolo directamente sin utilizar una biblioteca de autenticación.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Obtener más información sobre el punto de conexión de la versión 2.0 de Azure AD
Obtener información sobre lo que puede hacer con el punto de conexión de la versión 2.0 de Azure AD:

* Descubra los [tipos de aplicaciones que se pueden crear con el punto de conexión de la versión 2.0 de Azure AD](active-directory-v2-flows.md).
* Conozca las [limitaciones y restricciones](active-directory-v2-limitations.md) del punto de conexión de la versión 2.0 de Azure AD.
* Vea este vídeo para una introducción al punto de conexión de la versión 2.0 de Azure AD:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Recursos adicionales
Explore información detallada sobre la plataforma del punto de conexión de la versión 2.0 de Azure AD:

* [Referencia de protocolos de la versión 2.0 de Azure AD](active-directory-v2-protocols.md)
* [Referencia de tokens de la versión 2.0 de Azure AD](active-directory-v2-tokens.md)
* [Referencia de las bibliotecas de autenticación de la versión 2.0 de Azure AD](active-directory-v2-libraries.md)
* [Ámbitos y consentimiento en el punto de conexión de la versión 2.0 de Azure AD](active-directory-v2-scopes.md)
* [Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Si solo necesita iniciar sesión en cuentas profesionales o educativas desde Azure Active Directory, comience con la [Guía del desarrollador de Azure AD](active-directory-developers-guide.md). El punto de conexión de la versión 2.0 de Azure AD está diseñado para que lo usen desarrolladores que deban iniciar sesión explícitamente en cuentas personales de Microsoft.

[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
