---
title: "Autenticación de paso a través de Azure AD Connect: Preguntas más frecuentes | Microsoft Docs"
description: "Respuestas a las preguntas más frecuentes sobre la autenticación de paso a través de Azure Active Directory."
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
ms.openlocfilehash: 077a60949b5eed24cb9a1c56008a0073693f121e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticación de paso a través de Azure Active Directory: Preguntas más frecuentes

En este artículo se ofrecen respuestas a las preguntas más frecuentes sobre la autenticación de paso a través de Azure Active Directory (Azure AD). Siga comprobando si hay contenido actualizado.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>¿Cuál de los métodos de inicio de sesión, autenticación de paso a través, sincronización de hash de contraseña y Servicios de federación de Active Directory (AD FS) de Azure AD debo elegir?

Depende de su entorno local y de los requisitos organizativos. Revise el artículo [Opciones para el inicio de sesión de los usuarios en Azure AD Connect](active-directory-aadconnect-user-signin.md) para consultar una comparación de los diferentes métodos de inicio de sesión de Azure AD.

## <a name="is-pass-through-authentication-a-free-feature"></a>¿Es la autenticación de paso a través una característica gratuita?

La autenticación de paso a través es una característica gratuita. Por tanto, no se necesita ninguna versión de pago de Azure AD para usarla.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>¿Está disponible la autenticación de paso a través en la [nube Microsoft Azure Alemania](http://www.microsoft.de/cloud-deutschland) y en la [nube Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Nº La autenticación de paso a través solo está disponible en la instancia mundial de Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>¿Funciona el [acceso condicional](../active-directory-conditional-access-azure-portal.md) con la autenticación de paso a través?

Sí. Todas las funcionalidades de acceso condicional, incluida Azure Multi-Factor Authentication, funcionan con la autenticación de paso a través.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>¿Admite la autenticación de paso a través "Alternate ID" como nombre de usuario, en lugar de "userPrincipalName"?

Sí. La autenticación de paso a través admite `Alternate ID` como nombre de usuario cuando se configura en Azure AD Connect. Para más información, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md). No todas las aplicaciones de Office 365 admiten `Alternate ID`. Consulte la documentación de la aplicación específica para conocer la declaración de compatibilidad.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>¿Actúa la sincronización de hash de contraseña como una reserva de la autenticación de paso a través?

Nº La autenticación de paso a través _no_ realiza una conmutación automática por error a la sincronización de hash de contraseña. Solo actúa como reserva para [escenarios en los que la autenticación de paso a través no se admite en la actualidad](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Para evitar errores de inicio de sesión de usuario, debe configurar la autenticación de paso a través para una [alta disponibilidad](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>¿Puedo instalar un conector del [proxy de aplicación de Azure AD](../active-directory-application-proxy-get-started.md) en el mismo servidor que un agente de autenticación de paso a través?

Sí. Esta configuración es compatible con las nuevas versiones del agente de autenticación de paso a través (versiones 1.5.193.0 o posteriores).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>¿Qué versiones de Azure AD Connect y del agente de autenticación de paso a través necesita?

Para que esta característica funcione, necesita la versión 1.1.486.0 o posterior para Azure AD Connect y 1.5.58.0 o posterior para el agente de autenticación de paso a través. Instale todo el software servidores con Windows Server 2012 R2 o posterior.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>¿Qué ocurre si mi contraseña de usuario ha expirado y se intenta iniciar sesión mediante la autenticación de paso a través?

Si ha configurado la [escritura diferida de contraseñas](../active-directory-passwords-update-your-own-password.md) para un usuario concreto y, si el usuario inicia sesión mediante la autenticación de paso a través, puede cambiar o restablecer su contraseña. La contraseña se volverá a escribir en la instancia local de Active Directory, tal como cabría esperar.

Si no ha configurado la escritura diferida de contraseñas para un usuario determinado o si este no tiene una licencia válida de Azure AD asignada, el usuario no podrá actualizar la contraseña en la nube. El usuario no puede actualizar la contraseña incluso aunque haya expirado. En su lugar, verá el siguiente mensaje: "La organización no le permite actualizar la contraseña en este sitio. Actualícela de acuerdo con el método que recomienda la organización o, si necesita ayuda, póngase en contacto con el administrador". El usuario o el administrador debe restablecer su contraseña en la instancia de Active Directory local.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>¿Cómo protege la autenticación de paso a través frente a ataques de contraseña por fuerza bruta?

Lea [Autenticación de paso a través de Azure Active Directory: Bloqueo inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) para más información.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>¿Qué comunican los agentes de autenticación de paso a través mediante los puertos 80 y 443?

- Los agentes de autenticación realizan las solicitudes HTTPS a través del puerto 443 en todas las operaciones de esta característica.
- Los agentes de autenticación realizan solicitudes HTTP a través del puerto 80 para la descarga de listas de revocación de certificados (CRL) SSL.

     >[!NOTE]
     >Las actualizaciones recientes redujeron el número de puertos que la característica necesita. Si tiene versiones anteriores de Azure AD Connect o del agente de autenticación, mantenga también estos puertos abiertos: 5671, 8080, 9090, 9091, 9350, 9352 y 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>¿Se pueden comunicar los agentes de autenticación de paso a través mediante un servidor de proxy web de salida?

Sí. Si la Detección automática de proxy web (WPAD) está habilitada en el entorno local, los agentes de autenticación intentarán automáticamente buscar y usar un servidor proxy web en la red.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>¿Puedo instalar dos o más agentes de autenticación de paso a través en el mismo servidor?

No, solo se puede instalar un agente de autenticación de paso a través en un único servidor. Si desea configurar la autenticación de paso a través para alta disponibilidad, siga las instrucciones descritas en [Autenticación de paso a través de Azure Active Directory: inicio rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>¿Cómo quito un agente de autenticación de paso a través?

Mientras se esté ejecutando un agente de autenticación de paso a través, permanecerá activo y controlará continuamente las solicitudes de inicio de sesión del usuario. Si desea desinstalar un agente de autenticación, vaya a **Panel de Control -> Programas -> Programas y características** y desinstale los programas **Agente de autenticación de Microsoft Azure AD Connect** y el **Agent Updater de Microsoft Azure AD Connect**.

Si activa la hoja Autenticación de paso a través en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) después de completar los pasos anteriores, verá que el agente de autenticación se muestra como **inactivo**. Se _espera_ que esto sea así. El agente de autenticación se quita automáticamente de la lista después de unos días.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Ya utilizo AD FS para iniciar sesión en Azure AD. ¿Cómo se puede cambiar a la autenticación de paso a través?

Si ha configurado AD FS como su método de inicio de sesión mediante el asistente de Azure AD Connect, cambie el método de inicio de sesión del usuario a la autenticación de paso a través. Este cambio permite habilitar la autenticación de paso a través en el inquilino y convierte _todos_ los dominios federados en dominios administrados. La autenticación de paso a través administra todas las solicitudes de inicio de sesión posteriores del inquilino. En la actualidad, no hay ninguna forma compatible en Azure AD Connect para poder usar una combinación de AD FS y la autenticación de paso a través en dominios diferentes.

Si se configuró AD FS como el método de inicio de sesión _fuera_ del asistente de Azure AD Connect, cambie el método de inicio de sesión del usuario a la autenticación de paso a través. Puede realizar este cambio en la opción **No configurar**. Este cambio habilita la autenticación de paso a través en el inquilino, pero todos los dominios federados continuarán usando AD FS para iniciar sesión. Use PowerShell para convertir manualmente todos o algunos de estos dominios federados a dominios administrados. Después de realizar este cambio, *solo* la autenticación de paso a través administra todas las solicitudes de inicio de sesión en los dominios administrados.

>[!IMPORTANT]
>La autenticación de paso a través no admite el inicio de sesión de usuarios de Azure AD solo en la nube.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>¿Se puede usar la autenticación de paso a través en un entorno de bosques múltiples de Active Directory?

Sí. Se admiten entornos de bosques múltiples si hay relaciones de confianza de bosque entre los bosques de Active Directory y si el enrutamiento de sufijos de nombre está configurado correctamente.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>¿Cuántos agentes de autenticación de paso a través es necesario instalar?

La instalación de varios agentes de autenticación de paso a través garantiza una [alta disponibilidad](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Sin embargo, no proporciona un equilibrio de carga determinista entre los agentes de autenticación.

Considere la carga máxima y la carga media de las solicitudes de inicio de sesión que espera ver en el inquilino. Como referencia, un solo agente de autenticación puede administrar entre 300 y 400 autenticaciones por segundo en un servidor estándar con CPU de 4 núcleos y 16 GB de RAM.

Para calcular el tráfico de red, use la guía sobre el tamaño siguiente:
- Cada solicitud tiene un tamaño de carga de trabajo de (0.5K + 1K * num_of_agents) bytes; es decir, los datos de Azure AD al Agente de autenticación. Aquí, "num_of_agents" indica el número de agentes de autenticación que hay registrados en su inquilino.
- Cada respuesta tiene un tamaño de carga de trabajo de 1K bytes; es decir, los datos del Agente de autenticación a Azure AD.

Para la mayoría de los clientes, dos o tres agentes de autenticación en total son suficientes para obtener alta disponibilidad y capacidad. Debe instalar agentes de autenticación cerca de los controladores de dominio para mejorar la latencia de inicio de sesión.

>[!NOTE]
>Hay un límite de sistema de 12 agentes de autenticación por inquilino.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>¿Puedo instalar el primer agente de autenticación de paso a través en un servidor diferente al que ejecuta Azure AD Connect?

No, este escenario _no_ se admite.

## <a name="how-can-i-disable-pass-through-authentication"></a>¿Se puede deshabilitar la autenticación de paso a través?

Vuelva a ejecutar al Asistente de Azure AD Connect y cambie el método de inicio de sesión del usuario de autenticación de paso a través a otro método. Este cambio deshabilita la autenticación de paso a través en el inquilino y desinstala el agente de autenticación del servidor. Debe desinstalar manualmente los agentes de autenticación de los otros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>¿Qué ocurre cuando se desinstala un agente de autenticación de paso a través?

Si desinstala un agente de autenticación de paso a través de un servidor, el servidor deja de aceptar las solicitudes de inicio de sesión. Para evitar anular la funcionalidad de inicio de sesión del usuario en el inquilino, asegúrese de que haya otro agente de autenticación en ejecución antes de desinstalar un agente de autenticación de paso a través.

## <a name="next-steps"></a>pasos siguientes
- [Limitaciones actuales](active-directory-aadconnect-pass-through-authentication-current-limitations.md): conozca qué escenarios son compatibles y cuáles no.
- [Inicio rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md): poner en marcha la autenticación de paso a través de Azure AD.
- [Bloqueo inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): obtenga información sobre cómo configurar la funcionalidad de bloqueo inteligente en el inquilino para proteger las cuentas de usuario.
- [Profundización técnica](active-directory-aadconnect-pass-through-authentication-how-it-works.md): conozca cómo funciona la característica de autenticación de paso a través.
- [Solución de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): obtenga información sobre cómo resolver problemas comunes relacionados con la característica de autenticación de paso a través.
- [Análisis a fondo de la seguridad](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): obtenga información técnica sobre la característica de autenticación de paso a través.
- [SSO de conexión directa de Azure AD](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use el foro de Azure Active Directory para solicitar nuevas características.

