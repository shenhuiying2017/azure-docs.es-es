---
title: "Inicio de sesión único de conexión directa de Azure AD Connect: Guía de inicio rápido | Microsoft Docs"
description: "En este artículo se describe cómo empezar a trabajar con el inicio de sesión único de conexión directa de Azure Active Directory."
services: active-directory
keywords: "qué es Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: billmath
ms.openlocfilehash: b533df58d24b3bc76a229ad09c682d1d8aeaf741
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Inicio de sesión único de conexión directa de Azure Active Directory: Guía de inicio rápido

## <a name="deploy-seamless-single-sign-on"></a>Implementación del inicio de sesión único de conexión directa

El inicio de sesión único de conexión directa (SSO de conexión directa) de Azure Active Directory (Azure AD) permite iniciar sesión automáticamente a los usuarios en equipos de escritorio corporativos conectados a la red de la empresa. El inicio de sesión único de conexión directa proporciona a los usuarios un acceso sencillo a las aplicaciones en la nube sin necesidad de otros componentes locales.

Para implementar SSO de conexión directa, siga estos pasos.

## <a name="step-1-check-the-prerequisites"></a>Paso 1: Comprobar los requisitos previos

Asegúrese de que se cumplen los siguientes requisitos previos:

* **Configure el servidor de Azure AD Connect**: si usa la [autenticación de paso a través](active-directory-aadconnect-pass-through-authentication.md) como método de inicio de sesión, no es necesaria ninguna otra comprobación de requisitos previos. Si va a usar la [sincronización de hash de contraseña](active-directory-aadconnectsync-implement-password-synchronization.md) como método de inicio de sesión y hay un firewall entre Azure AD Connect y Azure AD, asegúrese de que:
   - Usa Azure AD Connect 1.1.644.0 o cualquier versión posterior. 
   - Si el firewall o el proxy permiten la creación de listas de permitidos con DNS, cree una lista de permitidos para las conexiones a las direcciones URL **\*.msappproxy.net** en el puerto 443. En caso contrario, permita el acceso a los [intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653), que se actualizan cada semana. Este requisito solo es aplicable cuando se habilita la característica. No es necesario para los inicios de sesión de usuarios reales.

    >[!NOTE]
    >Las versiones de Azure AD Connect 1.1.557.0, 1.1.558.0, 1.1.561.0 y 1.1.614.0 tienen un problema relacionado con la sincronización de hash de contraseña. Si _no_ tiene pensado utilizar la sincronización de hash de contraseña junto con la autenticación de paso a través, consulte las [notas del historial de versiones de Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) para más información.

* **Configure las credenciales del administrador de dominio**: debe tener credenciales de administrador de dominio para cada bosque de Active Directory que:
    * Sincronice en Azure AD a través de Azure AD Connect.
    * Contenga usuarios para los que desea habilitar el SSO de conexión directa.

## <a name="step-2-enable-the-feature"></a>Paso 2: Habilitación de la característica

Habilite el SSO de conexión directa a través de [Azure AD Connect](active-directory-aadconnect.md).

Si va a realizar una instalación nueva de Azure AD Connect, elija la [ruta de acceso de instalación personalizada](active-directory-aadconnect-get-started-custom.md). En la página **Inicio de sesión de usuario**, active la opción **Habilitar el inicio de sesión único**.

![Azure AD Connect: Inicio de sesión de usuario](./media/active-directory-aadconnect-sso/sso8.png)

Si ya tiene una instalación de Azure AD Connect, seleccione la página **Cambiar inicio de sesión de usuario** en Azure AD Connect y después seleccione **Siguiente**.

![Azure AD Connect: Cambiar inicio de sesión de usuario](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Continúe con el asistente hasta llegar a la página **Habilitar el inicio de sesión único**. Proporcione las credenciales de administrador de dominio para cada bosque de Active Directory que:
    * Sincronice en Azure AD a través de Azure AD Connect.
    * Contenga usuarios para los que desea habilitar el SSO de conexión directa.

Cuando haya finalizado con el asistente, el SSO de conexión directa estará habilitado en su inquilino.

>[!NOTE]
> Las credenciales de administrador de dominio no se almacenan en Azure AD Connect ni en Azure AD. Solo se usan para habilitar la característica.

Siga estas instrucciones para verificar que ha habilitado SSO de conexión directa correctamente:

1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con las credenciales de administrador global del inquilino.
2. Seleccione **Azure Active Directory** en el panel izquierdo.
3. Seleccione **Azure AD Connect**.
4. Verifique que la característica de **Inicio de sesión único de conexión directa** aparece como **Habilitado**.

![Azure Portal: panel Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Paso 3: Implementación de la característica

Para implantar la característica a los usuarios, debe agregar las siguientes direcciones URL de Azure AD a la configuración de la zona de intranet de los usuarios mediante la directiva de grupo en Active Directory:

- https://autologon.microsoftazuread-sso.com
- https://aadg.windows.net.nsatc.net

Además, tiene que habilitar a una configuración de directiva de zona de intranet denominada **Permitir actualizaciones en la barra de estado a través de script** mediante la directiva de grupo. 

>[!NOTE]
> Las siguientes instrucciones solo funcionan en Internet Explorer y Google Chrome en Windows (si comparte el mismo conjunto de direcciones URL de sitios de confianza que Internet Explorer). Lea la sección siguiente para obtener instrucciones sobre cómo configurar Mozilla Firefox y Google Chrome en Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>¿Por qué es necesario modificar la configuración de zona de Intranet de los usuarios?

De forma predeterminada, el explorador calcula automáticamente la zona correcta (Internet o intranet) de una dirección URL específica. Por ejemplo, "http://contoso/" se asigna a la zona de intranet, mientras que "http://intranet.contoso.com/" se asigna a la zona de Internet (porque la dirección URL contiene un punto). Los exploradores no envían vales Kerberos a un punto de conexión en la nube, como las dos direcciones URL de Azure AD, a menos que agregue explícitamente la dirección URL a la zona de intranet del explorador.

### <a name="detailed-steps"></a>Pasos detallados

1. Abra la herramienta Editor de administración de directivas de grupo.
2. Edite la directiva de grupo que se aplica a algunos o todos los usuarios. En este ejemplo se usa la **directiva de dominio predeterminada**.
3. Vaya a **Configuración de usuario** > **Plantillas administrativas** > **Componentes de Windows** > **Internet Explorer** > **Panel de control de Internet** > **Página de seguridad**. A continuación, seleccione **Lista de asignación de sitio a zona**.
    ![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso6.png)
4. Habilite la directiva y escriba los valores siguientes en el cuadro de diálogo:
   - **Nombre de valor**: estas son las direcciones URL de Azure AD a las que se reenvían los vales Kerberos.
   - **Valor** (datos): **1** indica la zona de intranet.

   El resultado se parecerá al siguiente:

    Value: https://autologon.microsoftazuread-sso.com
  
    Data 1
        
   Value: https://aadg.windows.net.nsatc.net

    Data 1

   >[!NOTE]
   > Si no desea permitir que algunos usuarios usen SSO de conexión directa (por ejemplo, si estos usuarios inician sesión en quioscos compartidos) establezca los valores anteriores en **4**. De este modo, agrega las direcciones URL de Azure AD a la zona Sitios restringidos y el SSO de conexión directa no puede conectar a esos usuarios.
   >

5. Seleccione **Aceptar** y, después, otra vez **Aceptar**.

    ![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso7.png)

6. Vaya a **Configuración de usuario** > **Plantillas administrativas** > **Componentes de Windows** > **Internet Explorer** > **Panel de control de Internet** > **Página de seguridad** > **Zona de intranet**. Después, seleccione **Permitir actualizaciones en la barra de estado a través de script**.

    ![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso11.png)

7. Habilite la configuración de directiva y después seleccione **Aceptar**.

    ![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Consideraciones sobre el explorador

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas las plataformas)

Mozilla Firefox no realiza automáticamente la autenticación Kerberos. Cada usuario debe agregar manualmente las direcciones URL de Azure AD a su configuración de Firefox a través de estos pasos:
1. Ejecute Firefox y escriba `about:config` en la barra de direcciones. Descarte las notificaciones que vea.
2. Busque la preferencia **network.negotiate-auth.trusted-URI**. Esta preferencia enumera los sitios de confianza de Firefox para la autenticación Kerberos.
3. Haga clic con el botón derecho y seleccione **Modificar**.
4. Escriba https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net en el campo.
5. Seleccione **Aceptar** y después vuelva a abrir el explorador.

#### <a name="safari-mac-os"></a>Safari (Mac OS)

Asegúrese de que la máquina que ejecuta Mac OS se ha unido a Azure AD. Para obtener instrucciones sobre la unión a Azure AD, vea [Best Practices for Integrating OS X with Active Directory](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf) (Procedimientos recomendados para integrar OS X con Active Directory).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (todas las plataformas)

Si ha reemplazado la configuración de las directivas [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) o [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) en su entorno, asegúrese de agregar también a estas directivas las direcciones URL de Azure AD (https://autologon.microsoftazuread-sso.com y https://aadg.windows.net.nsatc.net).

#### <a name="google-chrome-mac-os-only"></a>Google Chrome (solo Mac OS)

En el caso de Google Chrome para Mac OS y otras plataformas distintas de Windows, consulte la [lista de directivas de proyecto de Chromium](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obtener información sobre cómo incluir en la lista blanca las direcciones URL de Azure AD para la autenticación integrada.

El uso de las extensiones de directiva de grupo de Active Directory de terceros para implementar las direcciones URL de AD Azure de Firefox y Google Chrome para los usuarios de Mac está fuera del ámbito de este artículo.

#### <a name="known-browser-limitations"></a>Limitaciones de exploradores conocidos

SSO de conexión directa no funciona en modo de exploración privada en los navegadores Firefox y Edge. Tampoco funciona en Internet Explorer si el navegador se ejecuta en modo de protección mejorada.

## <a name="step-4-test-the-feature"></a>Paso 4: Prueba de la característica

Para probar la característica con un usuario específico, asegúrese de que se cumplen todas las condiciones siguientes:
  - El usuario inicia sesión en un dispositivo corporativo.
  - El dispositivo se une a su dominio de Active Directory.
  - El dispositivo tiene una conexión directa a su controlador de dominio (DC), en la red con cable o inalámbrica de la empresa o a través de una conexión de acceso remoto, como una conexión VPN.
  - Ha [implantado la característica](##step-3-roll-out-the-feature) para este usuario mediante la directiva de grupo.

Para probar el escenario en el que el usuario escribe solo su nombre de usuario, pero no la contraseña:
   - Inicie sesión en https://myapps.microsoft.com/ en una nueva sesión privada del explorador.

Para probar el escenario en el que el usuario no tiene que escribir ni su nombre de usuario ni su contraseña, realice alguno de estos pasos: 
   - Inicie sesión en https://myapps.microsoft.com/contoso.onmicrosoft.com en una nueva sesión privada del explorador. Reemplace *contoso* con el nombre de su inquilino.
   - Inicie sesión en https://myapps.microsoft.com/contoso.com en una nueva sesión privada del explorador. Reemplace *contoso.com* con un dominio comprobado (no un dominio federado) en el inquilino.

## <a name="step-5-roll-over-keys"></a>Paso 5: Sustitución de claves

En el paso 2, Azure AD Connect crea cuentas de equipo (que representan a Azure AD) en todos los bosques de Active Directory en que se ha habilitado SSO de conexión directa. Para más información, vea [Inicio de sesión único de conexión directa de Azure Active Directory: información técnica detallada](active-directory-aadconnect-sso-how-it-works.md). Para mejorar la seguridad, se recomienda sustituir periódicamente las claves de descifrado de Kerberos de estas cuentas de equipo. Para obtener instrucciones sobre cómo sustituir las claves, vea [Preguntas más frecuentes sobre el inicio de sesión único de conexión directa de Azure Active Directory](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>No es necesario realizar este paso _inmediatamente_ después de haber habilitado la característica. Sustituya las claves de descifrado de Kerberos al menos cada treinta días.

## <a name="next-steps"></a>Pasos siguientes

- [Información técnica detallada](active-directory-aadconnect-sso-how-it-works.md): obtenga información sobre cómo funciona la característica de inicio de sesión único de conexión directa.
- [Preguntas más frecuentes](active-directory-aadconnect-sso-faq.md): obtenga respuestas a las preguntas más frecuentes sobre el inicio de sesión único de conexión directa.
- [Solución de problemas](active-directory-aadconnect-troubleshoot-sso.md): obtenga información sobre cómo resolver problemas comunes con la característica de inicio de sesión único de conexión directa.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use el foro de Azure Active Directory para solicitar nuevas características.
