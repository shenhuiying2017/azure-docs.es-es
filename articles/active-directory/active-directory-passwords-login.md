---
title: "SSPR de Azure AD desde la pantalla de inicio de sesión de Windows 10 | Microsoft Docs"
description: "Configuración del restablecimiento de contraseña de Azure AD de la pantalla de inicio de sesión de Windows 10 y olvido del PIN"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: d38d2790ae73d147209bb0ccdbbbe20bf880daa4
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>Restablecimiento de la contraseña de Azure AD desde la pantalla de inicio de sesión

Ya ha implementado el restablecimiento de contraseña de autoservicio de Azure AD (SSPR), pero los usuarios siguen llamando al departamento de soporte técnico cuando olvidan la contraseña. Llaman porque no llegan a un explorador web para acceder a SSPR.

Con la nueva actualización Windows 10 Fall Creators Update, los usuarios con dispositivos unidos a Azure AD verán un vínculo "Restablecer contraseña" en la pantalla de inicio de sesión. Al hacer clic en este vínculo, se incorporan a la misma experiencia de autoservicio de restablecimiento de contraseña (SSPR) con la que están familiarizados.

Para permitir a los usuarios restablecer su contraseña de Azure AD desde la pantalla de inicio de sesión de Windows 10, deben cumplirse los siguientes requisitos:

* Windows 10, versión 1709 o un cliente posterior que se ha [unido a Azure AD](device-management-azure-portal.md).
* El autoservicio de restablecimiento de contraseña de Azure AD debe estar habilitado.
* Configure e implemente los valores para habilitar el vínculo de restablecimiento de contraseña a través de uno de los métodos siguientes:
   * [Perfil de configuración de dispositivo de Intune](active-directory-passwords-login.md#configure-reset-password-link-using-intune)
   * [Clave del Registro](active-directory-passwords-login.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Configuración del vínculo de restablecimiento de contraseña con Intune

### <a name="create-a-device-configuration-policy-in-intune"></a>Creación de una directiva de configuración de dispositivo en Intune

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y haga clic en **Intune**.
2. Cree un nuevo perfil de configuración de dispositivo; para ello, vaya a **Configuración del dispositivo** > **Perfiles** > **Crear perfil**
   * Escriba un nombre descriptivo para el perfil
   * Opcionalmente, proporcione una descripción detallada del perfil
   * Plataforma **Windows 10 y versiones posteriores**
   * Tipo de perfil **Personalizado**

   ![CreateProfile][CreateProfile]

3. Configuración de los **valores**
   * **Agregue** la siguiente configuración de OMA-URI para habilitar el vínculo de restablecimiento de contraseña
      * Proporcione un nombre descriptivo para explicar lo que hace el valor de configuración
      * Opcionalmente, proporcione una descripción detallada del valor
      * **OMA-URI** establecido en `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * **Tipo de datos** establecido en **Entero**
      * **Valor** establecido en **1**
      * Haga clic en **Aceptar**
   * Haga clic en **Aceptar**
4. Haga clic en **Crear**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Asignación de una directiva de configuración de dispositivo en Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Cree un grupo para aplicar la directiva de configuración de dispositivo y haga lo siguiente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y haga clic en **Azure Active Directory**.
2. Vaya a **Usuarios y grupos** > **Todos los grupos** > **Nuevo grupo**
3. Proporcione un nombre para el grupo y, en **Tipo de pertenencia** elija **Asignado** 
   * En **Miembros**, elija los dispositivos con Windows 10 unidos a Azure AD a los que desea aplicar la directiva.
   * Haga clic en **Seleccionar**
4. Haga clic en **Crear**

Para más información sobre cómo crear grupos, consulte el artículo [Administración del acceso a recursos con grupos de Azure Active Directory](active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Asignación de la directiva de configuración de dispositivo al grupo de dispositivos

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y haga clic en **Intune**.
2. Identifique el perfil de configuración de dispositivo creado anteriormente; para ello, vaya a **Configuración del dispositivo** > **Perfiles** > haga clic en el perfil que creó anteriormente
3. Asigne el perfil a un grupo de dispositivos 
   * Haga clic en **Asignaciones** > en **Incluir** > **Seleccionar grupos para incluir**
   * Seleccione el grupo que creó anteriormente y haga clic en **Seleccionar**
   * Haga clic en **Guardar**

   ![Asignación][Assignment]

Ahora ha creado y asignado una directiva de configuración de dispositivo para habilitar el vínculo de restablecimiento de contraseña en la pantalla de inicio de sesión con Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Configuración del vínculo de restablecimiento de contraseña con el registro

Se recomienda usar este método solo para probar el cambio de configuración.

1. Inicie sesión en el dispositivo unido a Azure AD con credenciales administrativas
2. Ejecute **regedit** como administrador
3. Establezca la siguiente clave del Registro
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Lo que ven los usuarios

Ahora que la directiva está configurada y asignada, ¿qué cambia para el usuario? ¿Cómo sabe que puede restablecer su contraseña en la pantalla de inicio de sesión?

![LoginScreen][LoginScreen]

Cuando los usuarios intenten iniciar sesión, verán ahora un vínculo de restablecimiento de contraseña que abre la experiencia de autoservicio de restablecimiento de contraseña en la pantalla de inicio de sesión. Esta funcionalidad permite a los usuarios restablecer su contraseña sin tener que usar otro dispositivo para acceder a un explorador web.

Los usuarios encontrarán instrucciones para usar esta característica en el artículo sobre el [restablecimiento de la contraseña profesional o educativa](active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

## <a name="common-issues"></a>Problemas comunes

Al probar esta funcionalidad con Hyper-V, el vínculo "Restablecer contraseña" no aparece.

* Vaya a la máquina virtual que usa para la prueba, haga clic en **Ver** y desactive la opción **Sesión mejorada**.

Al probar esta funcionalidad con Escritorio remoto, el vínculo "Restablecer contraseña" no aparece.

* El restablecimiento de contraseña no se admite actualmente desde Escritorio remoto.

## <a name="next-steps"></a>pasos siguientes
Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [¿Cómo se implementa SSPR?](active-directory-passwords-best-practices.md)
* [¿Cómo se habilita el restablecimiento del PIN desde la pantalla de inicio de sesión?](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [Más información acerca de las directivas de autenticación de MDM](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/active-directory-passwords-login/create-profile.png "Creación de un perfil de configuración de dispositivo de Intune para habilitar el vínculo de restablecimiento de contraseña en la pantalla de inicio de sesión de Windows 10"
[Assignment]: ./media/active-directory-passwords-login/profile-assignment.png "Asignación de la directiva de configuración de dispositivo de Intune a un grupo de dispositivos con Windows 10"
[LoginScreen]: ./media/active-directory-passwords-login/logon-reset-password.png "Vínculo de restablecimiento de contraseña en la pantalla de inicio de sesión de Windows 10"
