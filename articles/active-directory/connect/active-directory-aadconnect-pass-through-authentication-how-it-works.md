---
title: "Azure AD Connect: funcionamiento de la autenticación de paso a través | Microsoft Docs"
description: "En este artículo se describe el funcionamiento de la autenticación de paso a través de Azure Active Directory."
services: active-directory
keywords: "Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: billmath
ms.openlocfilehash: cd42278048b8162a06af21de04397a959be33586
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticación de paso a través de Azure Active Directory: información técnica detallada
En este artículo se ofrece información general sobre el funcionamiento de la autenticación de paso a través de Azure Active Directory (Azure AD). Si desea información técnica y de seguridad detallada, consulte el artículo [Análisis a fondo de la seguridad](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md).

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Funcionamiento de la autenticación de paso a través de Azure Active Directory

Cuando un usuario intenta iniciar sesión en una aplicación protegida mediante Azure AD, y si la autenticación de paso a través está habilitada en el inquilino, se producen los pasos siguientes:

1. Un usuario intenta acceder a una aplicación (por ejemplo, [Outlook Web App](https://outlook.office365.com/owa/)).
2. Si el usuario todavía no ha iniciado sesión, se le redirige a la página **Inicio de sesión de usuario** de Azure AD.
3. El usuario escribe su nombre de usuario y contraseña en la página de inicio de sesión de Azure AD y hace clic en el botón **Iniciar sesión**.
4. Cuando Azure AD recibe la solicitud de inicio de sesión, pone el nombre de usuario y la contraseña (cifrada mediante una clave pública) en una cola.
5. Un agente de autenticación local recupera el nombre de usuario y la contraseña cifrada de la cola. Tenga en cuenta que el agente no sondea con frecuencia las solicitudes de la cola, sino que recupera las solicitudes a través de una conexión continua establecida previamente.
6. El agente descifra la contraseña con su clave privada.
7. El agente valida el nombre de usuario y la contraseña en Active Directory mediante las API de Windows, que es un mecanismo similar al que se usa en Servicios de federación de Active Directory (AD FS). El nombre de usuario puede ser el nombre de usuario predeterminado local (normalmente, `userPrincipalName`) u otro atributo (conocido como `Alternate ID`) configurado en Azure AD Connect.
8. El controlador de dominio (DC) de Active Directory local evalúa la solicitud y devuelve la respuesta adecuada (correcto, error, contraseña expirada o bloqueo de usuario) al agente.
9. El agente de autenticación, a su vez, devuelve esta respuesta a Azure AD.
10. Azure AD evalúa la respuesta y responde al usuario según corresponda. Por ejemplo, Azure AD inicia la sesión del usuario inmediatamente o envía una solicitud a Azure Multi-Factor Authentication.
11. Si el inicio de sesión del usuario se realiza correctamente, el usuario puede acceder a la aplicación.

En el diagrama siguiente se ilustran todos los componentes y los pasos implicados:

![Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>pasos siguientes
- [Limitaciones actuales](active-directory-aadconnect-pass-through-authentication-current-limitations.md): conozca qué escenarios son compatibles y cuáles no.
- [Inicio rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md): poner en marcha la autenticación de paso a través de Azure AD.
- [Bloqueo inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): configurar la funcionalidad de bloqueo inteligente en el inquilino para proteger las cuentas de usuario.
- [Preguntas más frecuentes](active-directory-aadconnect-pass-through-authentication-faq.md): encuentre respuestas a las preguntas más frecuentes.
- [Solución de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): obtenga información sobre cómo resolver problemas comunes relacionados con la característica de autenticación de paso a través.
- [Análisis a fondo de la seguridad](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): obtenga información técnica sobre la característica de autenticación de paso a través.
- [SSO de conexión directa de Azure AD](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use el foro de Azure Active Directory para solicitar nuevas características.

