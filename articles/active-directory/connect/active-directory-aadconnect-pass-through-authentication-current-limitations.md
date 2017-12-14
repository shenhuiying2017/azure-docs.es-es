---
title: "Autenticación de paso a través de Azure AD: limitaciones actuales | Microsoft Docs"
description: "En este artículo se describen las limitaciones actuales de la autenticación de paso a través de Azure Active Directory (Azure AD)."
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
ms.date: 12/05/2017
ms.author: billmath
ms.openlocfilehash: a7edfd1939ad45dd3309fe5eaee2afa36086e9eb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticación de paso a través de Azure Active Directory: limitaciones actuales

>[!IMPORTANT]
>La autenticación de paso a través de Azure Active Directory (Azure AD) es una característica gratuita y no es necesario tener ninguna versión de pago de Azure AD para usarla. La autenticación de paso a través solo está disponible en la instancia mundial de Azure AD y no en la [nube Microsoft Azure Alemania](http://www.microsoft.de/cloud-deutschland) ni en la [nube Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Escenarios admitidos

Los siguientes escenarios son totalmente compatibles:

- Inicios de sesión de usuario en todas las aplicaciones basadas en explorador web.
- Inicios de sesión de usuario en las aplicaciones cliente de Office 365 que admitan la [autenticación moderna](https://aka.ms/modernauthga).
- Office 2016 y Office 2013 _con_ autenticación moderna.
- Uniones a un dominio de Azure AD para dispositivos Windows 10.
- Compatibilidad con Exchange ActiveSync.

## <a name="unsupported-scenarios"></a>Escenarios no admitidos

Los siguientes escenarios _no_ son compatibles:

- Inicios de sesión de usuario en las aplicaciones de cliente de Office heredadas: Office 2010 y Office 2013 _sin_ autenticación moderna. Se recomienda a las organizaciones que cambien a la autenticación moderna, en caso de que sea posible. La autenticación moderna es compatible con la autenticación de paso a través. También facilita la protección de las cuentas de usuario con características de [acceso condicional](../active-directory-conditional-access-azure-portal.md), como Azure Multi-Factor Authentication.
- Inicios de sesión de usuarios en aplicaciones cliente de Skype Empresarial, incluido Skype Empresarial 2016.
- Inicios de sesión de usuario en PowerShell 1.0. Se recomienda usar PowerShell 2.0.
- Azure Active Directory Domain Services.
- Contraseñas de aplicaciones de Multi-Factor Authentication.
- Detección de usuarios con [credenciales filtradas](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>Como solución alternativa _solo_ para escenarios no admitidos, habilite la sincronización de hash de contraseñas en la página [Características opcionales](active-directory-aadconnect-get-started-custom.md#optional-features) del asistente de Azure AD Connect.

>[!NOTE]
Habilitar la sincronización de hash de contraseña le proporciona la opción de autenticación mediante conmutación por error si se interrumpe la infraestructura local. Esta conmutación por error de la autenticación de paso a través a la sincronización de hash de contraseña de Active Directory no es automática. Deberá cambiar el método de inicio de sesión manualmente con Azure AD Connect. Si el servidor que ejecuta Azure AD Connect deja de funcionar, necesitará la ayuda de Soporte técnico de Microsoft para desactivar la autenticación de paso a través.

## <a name="next-steps"></a>Pasos siguientes
- [Inicio rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md): poner en marcha la autenticación de paso a través de Azure AD.
- [Bloqueo inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): obtenga información sobre cómo configurar la funcionalidad de bloqueo inteligente en el inquilino para proteger las cuentas de usuario.
- [Profundización técnica](active-directory-aadconnect-pass-through-authentication-how-it-works.md): conozca cómo funciona la característica de autenticación de paso a través.
- [Preguntas más frecuentes](active-directory-aadconnect-pass-through-authentication-faq.md): encuentre respuesta a las preguntas más frecuentes sobre la característica de autenticación de paso a través.
- [Solución de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): obtenga información sobre cómo resolver problemas comunes relacionados con la característica de autenticación de paso a través.
- [Análisis a fondo de la seguridad](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): obtenga información técnica sobre la característica de autenticación de paso a través.
- [SSO de conexión directa de Azure AD](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use el foro de Azure Active Directory para solicitar nuevas características.

