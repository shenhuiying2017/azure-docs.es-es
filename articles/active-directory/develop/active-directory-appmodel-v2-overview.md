---
title: "Punto de conexión v2.0 de Azure Active Directory | Documentos de Microsoft"
description: "Introducción a la creación de aplicaciones con inicio de sesión de cuentas de Microsoft y de Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
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
ms.openlocfilehash: 1e286044fb1a1b367fcac2dc14c47f68d5ed120d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Inicio de sesión de usuarios de cuentas de Microsoft y de Azure AD en una sola aplicación
Antes, un desarrollador de aplicaciones que deseara admitir cuentas de Microsoft personales y profesionales de Azure Active Directory debía realizar la integración con dos sistemas independientes.  El **punto de conexión v2.0 de Azure AD** incorpora una nueva versión de API de autenticación que le permite iniciar sesión con ambos tipos de cuentas mediante una sencilla integración.  Las aplicaciones que usan el punto de conexión v2.0 también pueden usar las API de REST de [Microsoft Graph](https://graph.microsoft.io) con cualquier tipo de cuenta.

## <a name="getting-started"></a>Introducción
Elija su plataforma favorita de la lista siguiente para compilar una aplicación mediante nuestros marcos y bibliotecas de código abierto.  Como alternativa, puede usar la documentación del protocolo OAuth 2.0 y OpenID Connect para enviar y recibir directamente mensajes de protocolo sin usar una biblioteca de autenticación.

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Novedades
La información que se describe aquí será útil para comprender qué se puede y no se puede hacer con el punto de conexión v2.0.

* Obtenga información sobre los [tipos de aplicaciones que puede compilar con la versión 2.0 del punto de conexión](active-directory-v2-flows.md).
* Conozca las [limitaciones y restricciones](active-directory-v2-limitations.md) de la versión 2.0 del punto de conexión.
* Eche un vistazo a este vídeo introductorio del punto de conexión v2.0:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>Referencia
Estos vínculos le servirán para explorar la plataforma en profundidad:

* [Referencia de protocolos de v2.0](active-directory-v2-protocols.md)
* [Referencia de los tokens de v2.0](active-directory-v2-tokens.md)
* [Referencia de la biblioteca de la versión 2.0](active-directory-v2-libraries.md)
* [Ámbitos y consentimiento en el punto de conexión v2.0](active-directory-v2-scopes.md)
* [Microsoft Graph](https://graph.microsoft.io)

## <a name="help--support"></a>Ayuda y soporte técnico
Estos son los mejores lugares para obtener ayuda con el desarrollo en Azure Active Directory.

* [`azure-active-directory` de Stack Overflow y etiquetas `adal`](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)
* [Comentarios sobre Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)


> [!NOTE]
> Si solo tiene que iniciar sesión en cuentas profesionales o educativas de Azure Active Directory, debería comenzar con nuestra [Guía del desarrollador de Azure AD](active-directory-developers-guide.md).  El punto de conexión v2.0 está diseñado para que lo usen desarrolladores que deban iniciar sesión explícitamente en cuentas personales de Microsoft.

