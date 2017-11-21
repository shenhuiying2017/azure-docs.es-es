---
title: "Autenticación de paso a través de Azure AD Connect: Preguntas más frecuentes | Microsoft Docs"
description: "Respuestas a las preguntas más frecuentes acerca de la autenticación de paso a través de Azure Active Directory."
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
ms.openlocfilehash: 821d70ec7236e165d4f60ed6217c49d10de1cfc3
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticación de paso a través de Azure Active Directory: Preguntas más frecuentes

En este artículo se ofrece respuesta a las preguntas más frecuentes acerca de la autenticación de paso a través de Azure Active Directory (Azure AD). Siga comprobando si hay contenido nuevo.

## <a name="which-of-the-azure-ad-sign-in-methods---pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs---should-i-choose"></a>¿Cuál de los métodos de inicio de sesión, autenticación de paso a través, sincronización de hash de contraseña y Servicios de federación de Active Directory (AD FS) de Azure AD debo elegir?

Depende de su entorno local y de los requisitos organizativos. Revise este artículo para conocer una [comparación entre los distintos métodos de inicio de sesión de Azure AD](active-directory-aadconnect-user-signin.md).

## <a name="is-pass-through-authentication-a-free-feature"></a>¿Es la autenticación de paso a través una característica gratuita?

Sí, es una característica gratuita y no es necesario tener ninguna versión de pago de Azure AD para usarla.

## <a name="is-pass-through-authentication-available-in-microsoft-cloud-germanyhttpwwwmicrosoftdecloud-deutschland-and-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>¿Está disponible la autenticación de paso a través en [Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) y en la [nube de Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

No, la autenticación de paso a través solo está disponible en la instancia mundial de Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>¿Funciona el [acceso condicional](../active-directory-conditional-access-azure-portal.md) con la autenticación de paso a través?

Sí, todas las funcionalidades de acceso condicional, incluida Azure Multi-Factor Authentication, funcionan con la autenticación de paso a través.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>¿Admite la autenticación de paso a través "Alternate ID" como nombre de usuario, en lugar de "userPrincipalName"?

Sí. La autenticación de paso a través admite `Alternate ID` como nombre de usuario cuando se configura en Azure AD Connect, tal como se muestra [aquí](active-directory-aadconnect-get-started-custom.md). No todas las aplicaciones de Office 365 admiten `Alternate ID`. Consulte la documentación de la aplicación específica para conocer la declaración de compatibilidad.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>¿Actúa la sincronización de hash de contraseña como una reserva de la autenticación de paso a través?

No, la autenticación de paso a través _no_ realiza una conmutación automática por error a la sincronización de hash de contraseña. Solo actúa como reserva para [escenarios en los que la autenticación de paso a través no se admite en la actualidad](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Para evitar errores de inicio de sesión de usuario, debe configurar la autenticación de paso a través para una [alta disponibilidad](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>¿Puedo instalar un conector del [proxy de aplicación de Azure AD](../active-directory-application-proxy-get-started.md) en el mismo servidor que un agente de autenticación de paso a través?

Sí, esta configuración es compatible con las nuevas versiones del agente de autenticación de paso a través (versiones 1.5.193.0 o posteriores).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>¿Qué versiones de Azure AD Connect y del agente de autenticación de paso a través necesita?

Necesita la versión 1.1.486.0 o posterior para Azure AD Connect y 1.5.58.0 o posterior para el agente de autenticación de paso a través para que esta característica funcione. Todo el software debe instalarse en servidores con Windows Server 2012 R2 o posterior.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-using-pass-through-authentication"></a>¿Qué ocurre si mi contraseña de usuario ha expirado y se intenta iniciar sesión mediante la autenticación de paso a través?

Si ha configurado la [escritura diferida de contraseñas](../active-directory-passwords-update-your-own-password.md) para un usuario concreto y, si el usuario inicia sesión mediante la autenticación de paso a través, puede cambiar o restablecer su contraseña. La contraseña se volverá a escribir en la instancia local de Active Directory, tal como cabría esperar.

Sin embargo, si no está configurada la escritura diferida de contraseñas para un usuario determinado o si este no tiene una licencia válida de Azure AD asignada, el usuario no podrá actualizar la contraseña en la nube. El usuario no puede actualizar la contraseña incluso aunque haya expirado. En su lugar, verá el siguiente mensaje: "La organización no le permite actualizar la contraseña en este sitio. Actualícela de acuerdo con el método que recomienda la organización o, si necesita ayuda, póngase en contacto con el administrador". El usuario o el administrador tendrá que restablecer su contraseña en la instancia de Active Directory local.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>¿Cómo protege la autenticación de paso a través frente a ataques de contraseña por fuerza bruta?

Lea [este artículo](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) para más información.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>¿Qué comunican los agentes de autenticación de paso a través mediante los puertos 80 y 443?

- Los agentes de autenticación realizan las solicitudes HTTPS a través del puerto 443 en todas las operaciones de esta característica.
- Los agentes de autenticación realizan solicitudes HTTP a través del puerto 80 para la descarga de listas de revocación de certificados SSL.

     >[!NOTE]
     >En las actualizaciones recientes se ha reducido el número de puertos que necesita la característica. Si tiene versiones anteriores de Azure AD Connect o del agente de autenticación, mantenga también estos puertos abiertos: 5671, 8080, 9090, 9091, 9350, 9352 y 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>¿Se pueden comunicar los agentes de autenticación de paso a través mediante un servidor de proxy web de salida?

Sí. Si WPAD (detección automática de proxy web) está habilitada en el entorno local, los agentes de autenticación intentarán automáticamente buscar y usar un servidor proxy web en la red.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>¿Puedo instalar dos o más agentes de autenticación de paso a través en el mismo servidor?

No, solo se puede instalar un agente de autenticación de paso a través en un único servidor. Si desea configurar la autenticación de paso a través para alta disponibilidad, siga las instrucciones de este [artículo](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) en su lugar.

## <a name="i-already-use-active-directory-federation-services-ad-fs-for-azure-ad-sign-in-how-do-i-switch-it-to-pass-through-authentication"></a>Ya utilizo los servicios de federación de Active Directory (AD FS) para el inicio de sesión de Azure AD. ¿Cómo se puede cambiar a la autenticación de paso a través?

Si ha configurado AD FS como su método de inicio de sesión mediante el Asistente de Azure AD Connect, cambie el método de inicio de sesión del usuario a autenticación de paso a través. Este cambio permite habilitar la autenticación de paso a través en el inquilino y convierte _todos_ los dominios federados en dominios administrados. Todas las solicitudes de inicio de sesión posteriores del inquilino se manejarán mediante la autenticación de paso a través. En la actualidad, no hay ninguna forma compatible en Azure AD Connect para poder usar una combinación de AD FS y la autenticación de paso a través en dominios diferentes.

Si AD FS se ha configurado como el método de inicio de sesión _fuera_ del Asistente de Azure AD Connect, cambie el método de inicio de sesión del usuario a la autenticación de paso a través (desde la opción "No configurar"). Este cambio permite la autenticación de paso a través en el inquilino. Sin embargo, todos los dominios federados seguirán usando AD FS para iniciar sesión. Use PowerShell para convertir manualmente todos o algunos de estos dominios federados a dominios administrados. Después de eso, todas las solicitudes de inicio de sesión en dominios administrados (_solo en ellos_) se controlarán mediante la autenticación de paso a través.

>[!IMPORTANT]
>La autenticación de paso a través no admite el inicio de sesión de usuarios de Azure AD solo en la nube.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-ad-environment"></a>¿Se puede usar la autenticación de paso a través en un entorno de bosques múltiples de AD?

Sí. Se admiten entornos de varios bosques si hay relaciones de confianza de bosque entre los bosques de AD y si el enrutamiento de sufijos de nombre está configurado correctamente.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>¿Cuántos agentes de autenticación de paso a través es necesario instalar?

La instalación de varios agentes de autenticación de paso a través garantiza una [alta disponibilidad](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Sin embargo no proporciona un equilibrio de carga determinista entre los agentes de autenticación.

Considere la carga máxima y la carga media de las solicitudes de inicio de sesión que espera ver en el inquilino. Como referencia, un solo agente de autenticación puede controlar entre 300 000 y 400 000 autenticaciones por segundo en un servidor estándar con CPU de 4 núcleos y 16 GB de RAM. Para la mayoría de los clientes, dos o tres agentes de autenticación en total son suficientes para obtener alta disponibilidad y capacidad.

Es recomendable instalar agentes de autenticación cerca de los controladores de dominio para mejorar la latencia de inicio de sesión.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>¿Puedo instalar el primer agente de autenticación de paso a través en un servidor diferente al que ejecuta Azure AD Connect?

No, este escenario _no_ se admite.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>¿Cuántos agentes de autenticación de paso a través se deben instalar?

Es recomendable que:

- Instale dos o tres agentes de autenticación en total. Esta configuración es suficiente para la mayoría de los clientes.
- Instale agentes de autenticación en los controladores de dominio (o lo más cerca posible de ellos) para mejorar la latencia de inicio de sesión.
- Se asegure de que los servidores (aquellos en los que se han instalado los agentes de autenticación) se han agregado al mismo bosque de AD que los usuarios cuyas contraseñas se deben validar.

>[!NOTE]
>Hay un límite de sistema de 12 agentes de autenticación por inquilino.

## <a name="how-can-i-disable-pass-through-authentication"></a>¿Se puede deshabilitar la autenticación de paso a través?

Vuelva a ejecutar al Asistente de Azure AD Connect y cambie el método de inicio de sesión del usuario de autenticación de paso a través a otro método. Este cambio deshabilita la autenticación de paso a través en el inquilino y desinstala el agente de autenticación del servidor. Tendrá que desinstalar manualmente los agentes de autenticación de los otros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>¿Qué ocurre cuando se desinstala un agente de autenticación de paso a través?

La desinstalación de un agente de autenticación de paso a través de un servidor hace que este deje de aceptar solicitudes de inicio de sesión. Asegúrese de que tiene otro agente de autenticación en ejecución antes de realizar esta operación para evitar interrumpir los inicios de sesión de los usuarios en el inquilino.

## <a name="next-steps"></a>Pasos siguientes
- [**Limitaciones actuales**](active-directory-aadconnect-pass-through-authentication-current-limitations.md): conozca qué escenarios son compatibles y cuáles no.
- [**Inicio rápido**](active-directory-aadconnect-pass-through-authentication-quick-start.md): desarrollo y ejecución de la autenticación de paso a través de Azure AD.
- [**Bloqueo inteligente**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): configuración de la funcionalidad Bloqueo inteligente en su inquilino para proteger las cuentas de usuario.
- [**Profundización técnica**](active-directory-aadconnect-pass-through-authentication-how-it-works.md): descripción del funcionamiento de esta característica.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): información para resolver problemas habituales de esta característica.
- [**Análisis a fondo de la seguridad**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): información técnica detallada adicional sobre la característica.
- [**SSO de conexión directa de Azure AD**](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para rellenar solicitudes de características nuevas.
