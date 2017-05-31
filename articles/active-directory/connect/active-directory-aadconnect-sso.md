---
title: "Azure AD Connect: inicio de sesión único de conexión directa | Microsoft Docs"
description: "En este tema se describe el inicio de sesión único de conexión directa con Azure Active Directory (Azure AD) y cómo permite proporcionar un verdadero inicio de sesión único a los usuarios de escritorio corporativo dentro de la red de la empresa."
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
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2601850f99188445cf63a6a4f185bdc4ebb92c29
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Inicio de sesión único de conexión directa con Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>¿Qué es el inicio de sesión único de conexión directa con Azure Active Directory?

El inicio de sesión único de conexión directa con Azure Active Directory (SSO de conexión directa con Azure AD) proporciona un verdadero inicio de sesión único para los usuarios que inician sesión en equipos de escritorio corporativos conectados a la red de la empresa. Si se habilita, no será preciso que los usuarios escriban la contraseña para iniciar sesión en Azure AD y, en la mayoría de los casos, ni siquiera los nombres de usuario. Esta característica proporciona a los usuarios un acceso sencillo a los servicios en la nube sin necesidad de otros componentes locales.

SSO de conexión directa se puede habilitar a través de Azure AD Connect y se puede combinar con [la sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md) o [la autenticación de paso a través](active-directory-aadconnect-pass-through-authentication.md).

>[!NOTE]
>Esta característica NO se puede aplicar a Active Directory Federation Services (ADFS), que ya ofrece esta funcionalidad.

Para que esta característica funcione para un usuario concreto, deben cumplirse las siguientes condiciones:

- El usuario inicia sesión en un escritorio corporativo.
- El escritorio se ha unido previamente a su dominio de Active Directory (AD).
- El escritorio tiene una conexión directa a su controlador de dominio (DC), en la red con cable o inalámbrica de la empresa, o a través de una conexión de acceso remoto, como una conexión VPN.
- Nuestros puntos de conexión de servicio se han incluido en la zona de la intranet del explorador.

Si no se cumple alguna de las condiciones anteriores, al usuario se le pedirá que escriba su nombre de usuario y contraseña como antes.

![Inicio de sesión único de conexión directa](./media/active-directory-aadconnect-sso/sso1.png)

Las demás funciones del inicio de sesión único (SSO) de conexión directa son las siguientes:

- Si una solicitud de inicio de sesión de Azure AD incluye el parámetro `domain_hint` o `login_hint` (iniciado por una aplicación en el inquilino), SSO de conexión directa la usará y el usuario no tendrá que escribir su nombre de usuario y contraseña.
- El SSO de conexión directa admite el nombre de usuario que sea el nombre de usuario predeterminado local (normalmente, "userPrincipalName") u otro atributo configurado en Azure AD Connect (conocido como "Id. alternativo").
- SSO de conexión directa es una característica oportunista, lo que significa que, si por algún motivo genera un error, la experiencia de inicio de sesión del usuario se revierte a su comportamiento habitual; es decir, el usuario deberá escribir su contraseña en la página de inicio de sesión.

## <a name="whats-available-during-preview"></a>¿Que está disponible en la versión preliminar?

>[!NOTE]
>SSO de conexión directa con Azure AD está actualmente en versión preliminar. Es una característica gratuita y no es necesario usar ninguna edición de pago de Azure AD para usarla.

La compatibilidad con SSO de conexión directa se realiza a través de clientes con explorador web y de Office que admiten la [autenticación moderna](https://aka.ms/modernauthga) en escritorios que pueden usar la autenticación Kerberos, como los equipos de escritorio con Windows. En la tabla siguiente, se proporcionan detalles de los clientes basados en el explorador en distintos sistemas operativos.

| SO\Explorador |Internet Explorer|Google Chrome|Mozilla Firefox|perimetral
| --- | --- |--- | --- | --- |
|Windows 10|Sí|Sí|Sí\*|No
|Windows 8.1|Sí|Sí|Sí\*|N/D
|Windows 8|Sí|Sí|Sí\*|N/D
|Windows 7|Sí|Sí|Sí\*|N/D
|Mac OS X|N/D|Sí\*|Sí\*|N/D

\*Requiere configuración adicional.

>[!NOTE]
>En el caso de Windows 10, la recomendación es usar [Azure AD Join](../active-directory-azureadjoin-overview.md) para que la experiencia con Azure AD sea óptima.

## <a name="how-does-azure-ad-seamless-sso-work"></a>¿Cómo funciona SSO de conexión directa con Azure AD?

SSO de conexión directa se puede habilitar en Azure AD Connect como se muestra [a continuación](#how-to-enable-azure-ad-seamless-sso?). Una vez que se habilita, se crea una cuenta de equipo denominada AZUREADSSOACCT en la instancia local de Active Directory (AD) y su clave de descifrado de Kerberos se comparte de forma segura con Azure AD. Además, se crean dos nombres de entidad de servicio (SPN) de Kerberos que representan las dos direcciones URL de servicio que se usan en el inicio de sesión de Azure AD.

>[!NOTE]
> Se deben crear la cuenta de equipo y los SPN de Kerberos en cada bosque de AD que sincronice con Azure AD (a través de Azure AD Connect) y para cuyos usuarios desee habilitar SSO de conexión directa. Si el bosque de AD tiene unidades organizativas (UO) para cuentas de equipo, después de habilitar la característica SSO de conexión directa, mueva la cuenta de equipo AZUREADSSOACCT a una UI para asegurarse de que no se elimine y que se administra del mismo modo que otras cuentas de equipo.

Una vez completada la instalación, el inicio de sesión en Azure AD funcionará de la misma forma que cualquier otro inicio de sesión que use la autenticación integrada de Windows (IWA). El proceso de SSO de conexión directa funciona del siguiente modo:

Supongamos que un usuario intenta acceder a un recurso en la nube que está protegido por Azure AD, como SharePoint Online. SharePoint Online redirige el explorador del usuario a Azure AD para el inicio de sesión.

Si la solicitud de inicio de sesión a Azure AD incluye los parámetros `domain_hint` (identifica un inquilino de Azure AD; por ejemplo, contoso.onmicrosoft.com) o `login_hint` (identifica el nombre del usuario; por ejemplo, user@contoso.onmicrosoft.com o user@contoso.com), se dan los pasos 1 al 5.

Si cualquiera de estos dos parámetros no se incluyen en la solicitud, se le pedirá al usuario que especifique su nombre de usuario en la página de inicio de sesión de Azure AD. Los pasos 1 al 5 solo se dan una vez que el usuario sale del campo de nombre de usuario o hace clic en el botón "Continuar".

1. Azure AD solicita al cliente, con una respuesta 401 No autorizado, que proporcione un vale de Kerberos.
2. El cliente solicita un vale desde Active Directory para Azure AD (representado por la cuenta del equipo que se ha configurado antes).
3. Active Directory busca la cuenta de equipo y devuelve al cliente un vale Kerberos cifrado con el secreto de la cuenta del equipo. El vale incluye la identidad del usuario que inició sesión en el escritorio.
4. El cliente envía el vale de Kerberos que adquirió desde Active Directory a Azure AD.
5. Azure AD descifra el vale Kerberos mediante la clave antes compartida. Si lo realiza correctamente, Azure AD devuelve un token o pide al usuario que proporcione las pruebas adicionales, como la autenticación multifactor, que solicite el recurso.

SSO de conexión directa es una característica oportunista, lo que significa que, si por algún motivo genera un error, la experiencia de inicio de sesión del usuario se revierte a su comportamiento habitual; es decir, el usuario deberá escribir su contraseña en la página de inicio de sesión.

El proceso también se muestra en el diagrama siguiente:

![Inicio de sesión único de conexión directa](./media/active-directory-aadconnect-sso/sso2.png)

## <a name="how-to-enable-azure-ad-seamless-sso"></a>¿Cómo se habilita SSO de conexión directa con Azure AD?

### <a name="pre-requisites"></a>Requisitos previos

Si va a habilitar SSO de conexión directa con autenticación de paso a través, no hay más requisitos previos que los que se requieren para la característica de autenticación de paso a través.

Si va a habilitar SSO de conexión directa con sincronización de contraseñas y si hay un firewall entre Azure AD Connect y Azure AD, asegúrese de que:

- El servidor de Azure AD Connect pueda comunicarse con las direcciones URL de `*.msappproxy.net`.
- Azure AD Connect (versiones 1.1.484.0 o superiores) puede realizar solicitudes HTTPS a Azure AD a través del puerto 443. Esto solo se utiliza para habilitar la característica, no para los inicios de sesión de usuario reales.
- Azure AD Connect también puede crear conexiones IP directas con los [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653). Repetimos que esto solo se usa para habilitar la característica.

>[!NOTE]
> Las versiones anteriores de Azure AD Connect (anteriores a la 1.1.484.0) necesitan poder comunicarse con Azure AD a través del puerto 9090.

### <a name="enabling-the-azure-ad-seamless-sso-feature"></a>Habilitación de la característica de SSO de conexión directa con Azure AD

SSO de conexión directa con Azure AD puede habilitarse a través de Azure AD Connect.

Si va a realizar una instalación nueva de Azure AD Connect, elija la [ruta de acceso de instalación personalizada](active-directory-aadconnect-get-started-custom.md). En la página "Inicio de sesión de usuario", active la opción "Habilitar el inicio de sesión único".

![Azure AD Connect: Inicio de sesión de usuario](./media/active-directory-aadconnect-sso/sso8.png)

Si ya tiene una instalación de Azure AD Connect, utilice la ruta de acceso de la [instalación rápida](active-directory-aadconnect-get-started-express.md) o de la [instalación personalizada](active-directory-aadconnect-get-started-custom.md), elija "Change user sign-in page" (Cambiar página de inicio de sesión del usuario) en Azure AD Connect y haga clic en "Siguiente". A continuación, active la opción "Habilitar el inicio de sesión único".

![Azure AD Connect: Change user sign-in page (Cambiar página de inicio de sesión del usuario)](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Continúe con el asistente hasta llegar a la página "Habilitar el inicio de sesión único". Tendrá que especificar las credenciales de administrador de dominio de cada bosque de AD que sincronice con Azure AD (a través de Azure AD Connect) y para cuyos usuarios desee habilitar SSO de conexión directa. Tenga en cuenta que las credenciales de administrador de dominio no se almacenan en Azure AD Connect ni en Azure AD, solo se usan para crear la cuenta del equipo y configurar los SPN de Kerberos como ya se ha descrito.

Cuando haya finalizado con el asistente, el SSO de conexión directa estará habilitado en su inquilino. Tenga en cuenta que todavía se deben completar los pasos descritos en la sección siguiente antes de los usuarios puede beneficiarse de esta característica.

## <a name="rolling-the-feature-out-to-your-users"></a>Implementación de la característica a los usuarios

Para implementar la característica SSO de conexión directa en los usuarios, deberá agregar dos direcciones URL de Azure AD (https://autologon.microsoftazuread-sso.com y https://aadg.windows.net.nsatc.net) en la configuración de zona de intranet de los usuarios a través de una directiva de grupo en Active Directory. Tenga en cuenta que esto solo funciona en Internet Explorer y Google Chrome (si comparte el mismo conjunto de direcciones URL de sitios de confianza que Internet Explorer). Deberá realizar otra configuración para Mozilla Firefox.

### <a name="why-do-you-need-this"></a>¿Por qué es necesario esto?

De forma predeterminada, los exploradores no envían vales Kerberos a un punto de conexión en la nube, salvo que su dirección URL se defina como parte de la zona de intranet del explorador. El explorador calcula automáticamente la zona correcta (Internet o intranet) de la dirección URL. Por ejemplo, http://contoso/ se asignará a la zona de intranet, mientras que http://intranet.contoso.com/ se asignará a la zona de Internet (porque la dirección URL contiene un punto).

Dado que las direcciones URL de Azure AD que se usan para SSO de conexión directa contienen un punto, se deben agregar explícitamente a la configuración de la zona de intranet de cada explorador. Esto hace que el explorador envíe automáticamente a Azure AD los vales Kerberos del usuario que ha iniciado sesión. Aunque esta operación se puede realizar manualmente en cada equipo, la manera más fácil de agregar las direcciones URL necesarias a la zona de intranet de todos los usuarios es crear una directiva de grupo en Active Directory.

### <a name="detailed-steps"></a>Pasos detallados

1. Abra las herramientas de administración de directivas de grupo.
2. Edite la directiva de grupo que se aplica a todos los usuarios, por ejemplo, la **directiva de dominio predeterminada**.
3. Vaya a **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page** y seleccione **Site to Zone Assignment List** (Lista de asignación de sitio a zona).
![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso6.png)  
4. Habilite la directiva y escriba los valores/datos siguientes en el cuadro de diálogo. Estas son las direcciones URL de AD Azure a las que se envían los vales de Kerberos.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
5. Haga clic en **Aceptar** y en **Aceptar** de nuevo.

Debería ser parecido a este:

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso7.png)

>[!NOTE]
>De forma predeterminada, Chrome usa el mismo conjunto de direcciones URL de sitios de confianza que Internet Explorer. Si tiene una configuración distinta para Chrome, será preciso que la actualice de manera independiente.

## <a name="disabling-azure-ad-seamless-sso"></a>Deshabilitación del SSO de conexión directa de Azure AD

El SSO de conexión directa de Azure AD puede deshabilitarse a través de Azure AD Connect.

Ejecute Azure AD Connect, elija "Change user sign-in page" (Cambiar página de inicio de sesión de usuario) y haga clic en "Siguiente". A continuación, desactive la opción "Habilitar el inicio de sesión único". Continúe con el asistente. Cuando haya finalizado con el asistente, el SSO de conexión directa estará deshabilitado en su inquilino. Sin embargo, verá un mensaje en pantalla en el que se anuncia lo siguiente:

"El inicio de sesión único ya está deshabilitado, pero es necesario completar otros pasos manualmente para finalizar el proceso. Más información".

Estos son los pasos manuales que necesita:

- Obtención de la lista de bosques de AD en la que se ha habilitado el SSO de conexión directa
  - En PowerShell, llame a `New-AzureADSSOAuthenticationContext`. Se mostrará un cuadro emergente para escribir las credenciales de administrador de su inquilino de Azure AD.
  - Llame a `Get-AzureADSSOStatus`. Aparecerá la lista de bosques de AD (examine la lista "Dominios") en la que se ha habilitado esta característica.
- Elimine manualmente la cuenta de equipo AZUREADSSOACCT de cada bosque de AD que encuentre en la lista anterior.

## <a name="next-steps"></a>Pasos siguientes

- Lea nuestra [guía de solución de problemas](active-directory-aadconnect-troubleshoot-sso.md) para obtener información sobre cómo resolver problemas comunes con SSO de conexión directa de Azure AD.

## <a name="feedback"></a>Comentarios

Sus comentarios son importantes. Si tiene alguna pregunta, utilice la siguiente sección de comentarios. Utilice nuestro [foro de UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) para las nuevas solicitudes de características.

