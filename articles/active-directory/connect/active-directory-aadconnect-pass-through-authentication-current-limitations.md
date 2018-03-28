---
title: 'Autenticación de paso a través de Azure AD: limitaciones actuales | Microsoft Docs'
description: En este artículo se describen las limitaciones actuales de la autenticación de paso a través de Azure Active Directory (Azure AD).
services: active-directory
keywords: Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 3e533b8b23c095a3de845d9b26a96aea9d8ee086
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticación de paso a través de Azure Active Directory: limitaciones actuales

>[!IMPORTANT]
>La autenticación de paso a través de Azure Active Directory (Azure AD) es una característica gratuita y no es necesario tener ninguna versión de pago de Azure AD para usarla. La autenticación de paso a través solo está disponible en la instancia mundial de Azure AD y no en la [nube Microsoft Azure Alemania](http://www.microsoft.de/cloud-deutschland) ni en la [nube Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Escenarios admitidos

Los siguientes escenarios son totalmente compatibles:

- Inicios de sesión de usuario en todas las aplicaciones basadas en explorador web.
- Inicios de sesión de usuario en aplicaciones de Office que admitan la [autenticación moderna](https://aka.ms/modernauthga): Office 2016 y Office 2013 _con_ autenticación moderna.
- El usuario inicia sesión en clientes Outlook mediante protocolos heredados como Exchange ActiveSync, SMTP, POP e IMAP.
- Inicios de sesión de usuario en Skype Empresarial que admitan la autenticación moderna, incluidas las topologías híbridas y en línea. Obtenga más información acerca de las topologías admitidas [aquí](https://technet.microsoft.com/library/mt803262.aspx).
- Uniones a dominios de Azure AD para dispositivos Windows 10.
- Contraseñas de aplicaciones de Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Escenarios no admitidos

Los siguientes escenarios _no_ son compatibles:

- Inicios de sesión de usuario en aplicaciones cliente de Office heredadas, excluido Outlook (consulte anteriormente los **escenarios admitidos**): Office 2010 y Office 2013 _sin_ autenticación moderna. Se recomienda a las organizaciones que cambien a la autenticación moderna, en caso de que sea posible. La autenticación moderna es compatible con la autenticación de paso a través. También facilita la protección de las cuentas de usuario con características de [acceso condicional](../active-directory-conditional-access-azure-portal.md), como Azure Multi-Factor Authentication.
- Acceso a uso compartido del calendario e información de disponibilidad en entornos híbridos de Exchange solo en Office 2010.
- Inicios de sesión de usuarios en aplicaciones cliente de Skype Empresarial _sin_ autenticación moderna.
- Inicios de sesión de usuario en PowerShell 1.0. Se recomienda usar PowerShell 2.0.
- Detección de usuarios con [credenciales filtradas](../active-directory-reporting-risk-events.md#leaked-credentials).
- Azure AD Domain Services necesita tener habilitada la sincronización de hash de contraseñas en el inquilino. Por lo tanto, los inquilinos que usan la autenticación de paso a través _únicamente_ no funcionan en escenarios que necesitan Azure AD Domain Services.
- La autenticación de paso a través no se viene integrada en [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- El Programa de inscripción de dispositivos de Apple (DEP de Apple) mediante el Asistente para configuración de iOS no admite la autenticación moderna. Al inscribir dispositivos de DEP de Apple en Intune para dominios administrados que usan la autenticación de paso a través, se producirá un error. Como alternativa, considere la posibilidad de usar la [aplicación de Portal de empresa](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/).

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
