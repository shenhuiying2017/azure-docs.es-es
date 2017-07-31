---
title: "Azure AD Connect: funcionamiento de la autenticación de paso a través | Microsoft Docs"
description: "En este artículo se describe el funcionamiento de la autenticación de paso a través de Azure Active Directory."
services: active-directory
keywords: "Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 294ffde02fdf6b54e03837b2ace77b2940441459
ms.contentlocale: es-es
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticación de paso a través de Azure Active Directory: información técnica detallada

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Funcionamiento de la autenticación de paso a través de Azure Active Directory

Cuando un usuario intenta iniciar sesión en una aplicación protegida mediante Azure Active Directory (Azure AD), y si la autenticación de paso a través está habilitada en el inquilino, se producen los pasos siguientes:

1. El usuario intenta acceder a una aplicación (por ejemplo, Outlook Web App en https://outlook.office365.com/owa/).
2. Si el usuario todavía no inicia sesión, se le redirige a la página de inicio de sesión de Azure AD.
3. El usuario escribe su nombre de usuario y contraseña en la página de inicio de sesión de Azure AD y hace clic en el botón "Iniciar sesión".
4. Cuando Azure AD recibe la solicitud de inicio de sesión, pone el nombre de usuario y la contraseña (cifrada mediante una clave pública) en una cola.
5. Un agente de autenticación de paso a través local hace una llamada saliente a la cola y recupera el nombre de usuario y la contraseña cifrada.
6. El agente descifra la contraseña con su clave privada.
7. Después, el agente valida el nombre de usuario y la contraseña en Active Directory mediante las API de Windows (un mecanismo similar al que se usa en Servicios de federación de Active Directory). El nombre de usuario puede ser el nombre de usuario predeterminado local (normalmente, `userPrincipalName`) u otro atributo (conocido como `Alternate ID`) configurado en Azure AD Connect.
8. Luego, el controlador de dominio (DC) de Active Directory local evalúa la solicitud y devuelve la respuesta adecuada (correcto, error, contraseña expirada o bloqueo de usuario) al agente
9. que, a su vez, la devuelve a Azure AD.
10. Azure AD evalúa la respuesta y responde al usuario según corresponda, por ejemplo, inicia la sesión del usuario de inmediato o solicita la autenticación multifactor (MFA).
11. Si el inicio de sesión del usuario se realiza correctamente, el usuario puede acceder a la aplicación.

En el diagrama siguiente se ilustran todos los componentes y los pasos implicados.

![Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Pasos siguientes
- [**Limitaciones actuales**](active-directory-aadconnect-pass-through-authentication-current-limitations.md): esta característica actualmente está en su versión preliminar. Obtenga información acerca de qué escenarios se admiten y cuáles no.
- [**Inicio rápido** ](active-directory-aadconnect-pass-through-authentication-quick-start.md): desarrollo y ejecución de la autenticación de paso a través de Azure AD.
- [**Preguntas más frecuentes**](active-directory-aadconnect-pass-through-authentication-faq.md): obtenga respuestas a las preguntas más frecuentes.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): aprenda a resolver problemas comunes de esta característica.
- [**SSO de conexión directa de Azure AD**](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para la tramitación de solicitudes de nuevas características.

