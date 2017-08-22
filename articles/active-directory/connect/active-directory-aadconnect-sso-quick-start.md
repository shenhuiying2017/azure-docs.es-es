---
title: "Inicio de sesión único de conexión directa de Azure AD Connect: Quía de inicio rápido | Microsoft Docs"
description: "En este artículo se describe cómo empezar a trabajar con el inicio de sesión único de conexión directa de Azure Active Directory."
services: active-directory
keywords: "qué es Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: 977108687734a5eb7f7a30419de2a6bdef184d0e
ms.contentlocale: es-es
ms.lasthandoff: 08/07/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Inicio de sesión único de conexión directa de Azure Active Directory: Guía de inicio rápido

## <a name="how-to-deploy-seamless-sso"></a>Implementación de SSO de conexión directa

El inicio de sesión único de conexión directa de Azure Active Directory (SSO de conexión directa de Azure AD) permite iniciar sesión automáticamente a los usuarios en equipos de escritorio corporativos conectados a la red de la empresa. Esta característica proporciona a los usuarios un acceso sencillo a las aplicaciones en la nube sin necesidad de otros componentes locales.

>[!IMPORTANT]
>La característica de SSO de conexión directa está actualmente en versión preliminar.

Para implementar SSO de conexión directa, debe seguir estos pasos:

## <a name="step-1-check-prerequisites"></a>Paso 1: Comprobación de los requisitos previos

Asegúrese de que se cumplen los siguientes requisitos previos:

1. El servidor de Azure AD Connect está configurado: si usa la [autenticación de paso a través](active-directory-aadconnect-pass-through-authentication.md) como método de inicio de sesión, no es necesaria realizar ninguna otra acción. Si va a usar la [sincronización de hash de contraseña](active-directory-aadconnectsync-implement-password-synchronization.md) como método de inicio de sesión y hay un firewall entre Azure AD Connect y Azure AD, asegúrese de que:
- Está usando la versión 1.1.484.0 de Azure AD Connect o una posterior.
- Azure AD Connect puede comunicarse con las direcciones URL de `*.msappproxy.net` y a través del puerto 443. Este requisito solo es aplicable cuando se habilita la característica, no para los inicios de sesión de usuarios reales.
- Azure AD Connect también puede crear conexiones IP directas con los [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653). De nuevo, este requisito solo es aplicable cuando se habilita la característica.
2. Tendrá que especificar las credenciales de administrador de dominio para cada bosque de AD que sincronice con Azure AD (a través de Azure AD Connect) y para cuyos usuarios desee habilitar SSO de conexión directa.

## <a name="step-2-enable-the-feature"></a>Paso 2: Habilitación de la característica

SSO de conexión directa se puede habilitar a través de [Azure AD Connect](active-directory-aadconnect.md).

Si va a realizar una instalación nueva de Azure AD Connect, elija la [ruta de acceso de instalación personalizada](active-directory-aadconnect-get-started-custom.md). En la página "Inicio de sesión de usuario", active la opción "Habilitar el inicio de sesión único".

![Azure AD Connect: Inicio de sesión de usuario](./media/active-directory-aadconnect-sso/sso8.png)

Si ya tiene una instalación de Azure AD Connect, elija "Change user sign-in page" (Cambiar página de inicio de sesión del usuario) en Azure AD Connect y haga clic en "Siguiente". A continuación, active la opción "Habilitar el inicio de sesión único".

![Azure AD Connect: Change user sign-in page (Cambiar página de inicio de sesión del usuario)](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Continúe con el asistente hasta llegar a la página "Habilitar el inicio de sesión único". Especifique las credenciales de administrador de dominio para cada bosque de AD que sincronice con Azure AD (a través de Azure AD Connect) y para cuyos usuarios desee habilitar SSO de conexión directa. 

Cuando haya finalizado con el asistente, el SSO de conexión directa estará habilitado en su inquilino.

>[!NOTE]
> Las credenciales de administrador de dominio no se almacenan en Azure AD Connect ni en Azure AD, sino que solo se usan para habilitar la característica.

Siga estas instrucciones para verificar que ha habilitado SSO de conexión directa correctamente:

1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con las credenciales de administrador global del inquilino.
2. Seleccione **Azure Active Directory** en la barra de navegación de la izquierda.
3. Seleccione **Azure AD Connect**.
4. Verifique que la característica de **Inicio de sesión único de conexión directa** aparece como **Habilitado**.

![Azure Portal: hoja Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Paso 3: Implementación de la característica

Para implementar la característica para los usuarios, debe agregar dos direcciones URL de Azure AD (https://autologon.microsoftazuread-sso.com y https://aadg.windows.net.nsatc.net) en la configuración de zona de intranet de los usuarios a través de una directiva de grupo en Active Directory.

>[!NOTE]
> Las siguientes instrucciones solo funcionan en Internet Explorer y Google Chrome en Windows (si comparte el mismo conjunto de direcciones URL de sitios de confianza que Internet Explorer). Lea la sección siguiente para obtener instrucciones para configurar Mozilla Firefox y Google Chrome en Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>¿Por qué es necesario modificar la configuración de zona de Intranet de los usuarios?

De forma predeterminada, el explorador calcula automáticamente la zona correcta (Internet o intranet) de la dirección URL. Por ejemplo, http://contoso/ se asignará a la zona de intranet, mientras que http://intranet.contoso.com/ se asignará a la zona de Internet (porque la dirección URL contiene un punto). Los exploradores no envían vales Kerberos a un punto de conexión en la nube, como las dos direcciones URL de Azure, a menos que su dirección URL se agregue explícitamente a la zona de intranet del explorador.

### <a name="detailed-steps"></a>Pasos detallados

1. Abra las herramientas de administración de directivas de grupo.
2. Edite la directiva de grupo que se aplica a algunos o todos los usuarios. En este ejemplo, se usa la **directiva de dominio predeterminada**.
3. Vaya a **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page** y seleccione **Site to Zone Assignment List** (Lista de asignación de sitio a zona).
![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso6.png)  
4. Habilite la directiva y escriba los siguientes valores (direcciones URL de Azure AD desde donde se reenvían los vales de Kerberos) y los datos (*1* indica la zona de Intranet) en el cuadro de diálogo.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
>[!NOTE]
> Si no desea permitir que algunos usuarios usen SSO de conexión directa (por ejemplo, si estos usuarios inician sesión en quioscos compartidos) establezca los valores anteriores en *4*. De este modo, agrega las direcciones URL de AD Azure a la zona Sitios restringidos y el SSO de conexión directa no puede conectar a esos usuarios.

5. Haga clic en **Aceptar** y en **Aceptar** de nuevo.

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso7.png)

### <a name="browser-considerations"></a>Consideraciones sobre el explorador

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox no realiza automáticamente la autenticación Kerberos. Cada usuario tiene que agregar manualmente las direcciones URL de Azure AD a su configuración de Firefox siguiendo estos pasos:
1. Ejecute Firefox y escriba `about:config` en la barra de direcciones. Descarte las notificaciones que vea.
2. Busque la preferencia **network.negotiate-auth.trusted-URI**. Esta preferencia enumera los sitios de confianza de Firefox para la autenticación Kerberos.
3. Haga clic con el botón derecho y seleccione "Modificar".
4. Escriba "https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net" en el campo.
5. Haga clic en "Aceptar" y vuelva a abrir el explorador.

#### <a name="safari-on-mac-os"></a>Safari en Mac OS

Asegúrese de que la máquina que ejecuta Mac OS se ha unido a AD. Consulte instrucciones sobre cómo hacerlo [aquí](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf).

#### <a name="google-chrome-on-mac-os"></a>Google Chrome en Mac OS

En el caso de Google Chrome para Mac OS y otras plataformas distintas de Windows, consulte [este artículo](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obtener información sobre cómo incluir en la lista blanca las direcciones URL de Azure AD para la autenticación integrada.

El uso de las extensiones de directiva de grupo de Active Directory de terceros para implementar las direcciones URL de AD Azure de Firefox y Google Chrome para los usuarios de Mac está fuera del ámbito de este artículo.

#### <a name="known-limitations"></a>Limitaciones conocidas

SSO de conexión directa no funciona en modo de exploración privada en los navegadores Firefox y Edge. Tampoco funciona en Internet Explorer si el navegador se ejecuta en modo de protección mejorada.

>[!IMPORTANT]
>Recientemente se ha revertido la compatibilidad para Edge, a fin de investigar los problemas notificados por los clientes.

## <a name="step-4-test-the-feature"></a>Paso 4: Prueba de la característica

Para probar la característica con un usuario específico, asegúrese de que se cumplen _todas los_ las condiciones siguientes:
  - El usuario inicia sesión en un escritorio corporativo.
  - El dispositivo se ha unido previamente a su dominio de Active Directory (AD).
  - El dispositivo tiene una conexión directa a su controlador de dominio (DC), en la red con cable o inalámbrica de la empresa, o a través de una conexión de acceso remoto, como una conexión VPN.
  - Ha [implantado la característica](##step-3-roll-out-the-feature) para este usuario mediante la directiva de grupo.

Para probar el escenario en el que el usuario escribe solo su nombre de usuario, pero no la contraseña:
- Inicie sesión en *https://myapps.microsoft.com/* en una nueva sesión privada del explorador.

Para probar el escenario en el que el usuario no tiene que escribir ni su nombre de usuario ni su contraseña: 
- Inicie sesión en *https://myapps.microsoft.com/contoso.onmicrosoft.com* en una nueva sesión privada del explorador. Reemplace "*contoso*" con el nombre de su inquilino.
- O bien, inicie sesión en *https://myapps.microsoft.com/contoso.com* en una nueva sesión privada del explorador. Reemplace "*contoso.com*" con un dominio comprobado (no un dominio federado) en el inquilino.

## <a name="step-5-roll-over-keys"></a>Paso 5: Sustitución de claves

En el paso 2, Azure AD Connect crea cuentas de equipo (que representan a Azure AD) en todos los bosques de AD en que se ha habilitado SSO de conexión directa. Obtenga información más detallada [aquí](active-directory-aadconnect-sso-how-it-works.md). Para mejorar la seguridad, se recomienda sustituir con frecuencia las claves de descifrado de Kerberos de estas cuentas de equipo.

>[!IMPORTANT]
>No es necesario realizar este paso _inmediatamente_ después de haber habilitado la característica. Sustituya las claves de descifrado de Kerberos al menos cada 30 días.

## <a name="next-steps"></a>Pasos siguientes

- [**Profundización técnica** ](active-directory-aadconnect-sso-how-it-works.md): descripción del funcionamiento de esta característica.
- [**Preguntas más frecuentes**](active-directory-aadconnect-sso-faq.md): obtenga respuestas a las preguntas más frecuentes.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-sso.md): aprenda a resolver problemas comunes de esta característica.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para la tramitación de solicitudes de nuevas características.

