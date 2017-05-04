---
title: "Azure AD: restablecer la contraseña | Microsoft Docs"
description: "Usar el restablecimiento de contraseña para recuperar el acceso a su cuenta"
services: active-directory
keywords: "administración de contraseñas de Active Directory, administración de contraseñas, restablecimiento de contraseñas con el autoservicio de Azure AD, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c95f52f028cdfaed35821275d816a24035ff02fb
ms.lasthandoff: 04/26/2017


---
# <a name="help-i-forgot-my-azure-ad-password"></a>Ayuda, he olvidado mi contraseña de Azure AD

Si ha olvidado la contraseña, nunca ha recibido ninguna de su equipo de TI, está bloqueada fuera de su cuenta o si desea cambiarla, podemos ayudarle.

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Restablecimiento o desbloqueo de mi contraseña en una cuenta profesional o educativa

Para entrar en su cuenta profesional o educativa, siga los pasos siguientes para acceder al autoservicio de restablecimiento de contraseña de Azure AD o, como nos gusta llamarlo, a SSPR.

1. En cualquier página de inicio de sesión profesional o educativa, haga clic en el vínculo **¿No puede acceder a su cuenta?** y en **Cuenta profesional o educativa**, o bien vaya directamente a la [página de restablecimiento de la contraseña](https://passwordreset.microsoftonline.com/).

   > [!NOTE]
   > Si quiere volver a una cuenta personal como hotmail.com o outlook.com, pruebe las [sugerencias que se encuentran en este artículo](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >
    ![¿No puede tener acceso a su cuenta?][Login]

2. Escriba el **Id. de usuario** de su cuenta profesional o educativa, demuestre que no es un robot escribiendo los caracteres que aparecen en la pantalla y luego haga clic en **Siguiente**.

   > [!NOTE]
   > Si su personal de TI no ha habilitado esta funcionalidad, aparecerá un vínculo con el texto "Póngase en contacto con el administrador" para solicitar ayuda al personal de TI mediante correo electrónico o un portal web de su propiedad.
   >

3. En función de cómo haya configurado el personal de TI el SSPR, verá una o varias de las siguientes opciones. El usuario o su personal de TI ya habrán rellenado parte de esta información antes de usar la información que se encuentra [aquí](active-directory-passwords-reset-register.md).
    * **Enviar un mensaje de correo electrónico a mi dirección alternativa**
    * **Enviar mensaje de texto a mi teléfono móvil**
    * **Llamar al teléfono móvil**
    * **Llamar al teléfono del trabajo** 
    * **Responder a mis preguntas de seguridad**

    Elija una opción, proporcione las respuestas correctas y haga clic en **Siguiente**.

    ![Compruebe los datos de autenticación][Verification].

4. Es posible que el personal de TI necesite realizar otra comprobación y tenga que repetir el paso 3 de nuevo con una opción diferente.
5. En la página **Elegir una nueva contraseña**, escriba la nueva contraseña, confírmela y luego haga clic en **Finalizar**. Se recomienda que la contraseña contenga entre 8 y 16 caracteres, caracteres en mayúsculas y minúsculas, números y caracteres especiales.

   > [!NOTE]
   > Si necesita desbloquear la cuenta, en este punto elija la opción solo para desbloquear, o cambie la contraseña y, después, desbloquee.
   >

6. Cuando vea el mensaje **Se ha restablecido la contraseña**, puede iniciar sesión con la nueva contraseña.

    ![Se ha restablecido la contraseña][Complete]

Ahora ya debería tener acceso a su cuenta. En caso contrario, debe ponerse en contacto con el personal de TI de su organización para obtener más ayuda.

Es posible que reciba un correo electrónico de confirmación que procede de una cuenta como "Microsoft en nombre de \<su organización>\". Si recibe un correo electrónico como ese y no había usado el autoservicio de restablecimiento de contraseña para recuperar el acceso a su cuenta, póngase en contacto con el administrador.

## <a name="change-my-password"></a>Cambio de mi contraseña

Si ya conoce la contraseña y quiere cambiarla, realice los pasos siguientes.

### <a name="change-your-password-from-the-office-365-portal"></a>Cambio de la contraseña desde el portal de Office 365

Utilice este método si accede normalmente a las aplicaciones mediante el portal de Office.

1. Inicie sesión en su [cuenta de Office 365](https://www.office.com).
2. Haga clic en el perfil, en la parte superior derecha, y en **Ver cuenta**.
3. Haga clic en **Seguridad y privacidad** > **Contraseña**
4. Escriba la contraseña anterior y establezca y confirme la nueva contraseña, y finalmente haga clic en **Enviar**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Cambio de la contraseña en el panel de acceso de Azure

Utilice este método si accede normalmente a las aplicaciones mediante el panel de acceso de Azure.

1. Inicie sesión en el [panel de acceso de Azure](https://myapps.microsoft.com/) usando su contraseña existente.
2. Haga clic en el perfil, en la parte superior derecha, y en **Perfil**.
3. Haga clic en **Cambiar contraseña**.
4. Escriba la contraseña anterior y establezca y confirme la nueva contraseña, y finalmente haga clic en **Enviar**.

## <a name="next-steps"></a>Pasos siguientes

* [Cómo registrarse para usar el autoservicio de restablecimiento de la contraseña](active-directory-passwords-reset-register.md)
* [Página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
* [Portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/)
* [No puede iniciar sesión en su cuenta de Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de inicio: ¿No puede tener acceso a su cuenta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Compruebe los datos de autenticación"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Cambio de la contraseña"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Se ha restablecido la contraseña"

