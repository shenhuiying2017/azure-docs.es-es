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
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 42a6d7156a28aaeadea96f9134b0becc004816ba
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticación de paso a través de Azure Active Directory: limitaciones actuales

>[!IMPORTANT]
>La autenticación de paso a través de Azure AD es una característica gratuita y no es necesario tener ninguna versión de pago de Azure AD para usarla. La autenticación de paso a través solo está disponible en la instancia mundial de Azure AD y no en [Microsoft Cloud Alemania](http://www.microsoft.de/cloud-deutschland) ni en [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Escenarios admitidos

Los siguientes escenarios son totalmente compatibles:

- Inicios de sesión de usuario en todas las aplicaciones basadas en explorador web.
- Inicios de sesión de usuario en las aplicaciones cliente de Office 365 que admitan la [autenticación moderna](https://aka.ms/modernauthga): Office 2016 y Office 2013 _con_ autenticación moderna.
- Azure AD Join para dispositivos con Windows 10.
- Compatibilidad con Exchange ActiveSync.

## <a name="unsupported-scenarios"></a>Escenarios no admitidos

Los siguientes escenarios _no_ son compatibles:

- Inicios de sesión de usuario en las aplicaciones de cliente de Office de heredadas (Office 2010 y Office 2013) _sin_ autenticación moderna. Se recomienda a las organizaciones que cambien a la autenticación moderna, en caso de que sea posible. La autenticación moderna permite la compatibilidad de la autenticación de paso a través, pero también le ayuda a proteger sus cuentas de usuario mediante características de [acceso condicional](../active-directory-conditional-access.md) como Multi-Factor Authentication (MFA).
- Inicios de sesión de usuarios en aplicaciones cliente de Skype Empresarial, incluido Skype Empresarial 2016.
- Inicios de sesión de usuario en PowerShell v1.0. Se recomienda que use PowerShell v2.0 en su lugar.
- Azure AD Domain Services.
- Contraseñas de aplicación para MFA.
- Detección de usuarios con [credenciales filtradas](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>Como solución alternativa _solo_ para escenarios no admitidos, habilite la sincronización de hash de contraseñas en la página [Características opcionales](active-directory-aadconnect-get-started-custom.md#optional-features) del asistente de Azure AD Connect. Al habilitar la sincronización de hash de contraseña también le proporciona la opción de autenticación mediante conmutación por error si se interrumpe la infraestructura local. Esta conmutación por error de la autenticación de paso a través a la sincronización de hash de contraseña no es automática, tiene que realizarse con la ayuda del Soporte técnico de Microsoft.

## <a name="next-steps"></a>Pasos siguientes
- [**Inicio rápido**](active-directory-aadconnect-pass-through-authentication-quick-start.md): desarrollo y ejecución de la autenticación de paso a través de Azure AD.
- [**Bloqueo inteligente**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): configuración de la funcionalidad Bloqueo inteligente en su inquilino para proteger las cuentas de usuario.
- [**Profundización técnica**](active-directory-aadconnect-pass-through-authentication-how-it-works.md): descripción del funcionamiento de esta característica.
- [**Preguntas más frecuentes**](active-directory-aadconnect-pass-through-authentication-faq.md): obtenga respuestas a las preguntas más frecuentes.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): información para resolver problemas habituales de esta característica.
- [**Análisis a fondo de la seguridad**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): información técnica detallada adicional sobre la característica.
- [**SSO de conexión directa de Azure AD**](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para rellenar solicitudes de características nuevas.
