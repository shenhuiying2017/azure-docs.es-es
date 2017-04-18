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
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9b7735bbe8d3b2ffc78eba8f1489d70a02fe7342
ms.lasthandoff: 04/12/2017


---
# <a name="help-i-forgot-my-password"></a>Ayuda, he olvidado mi contraseña

Paso 1... tranquilo

Si se encuentra en alguno de los siguientes escenarios, podemos ayudarle.

* No está seguro de cómo acceder a su cuenta y no recuerda su contraseña.
* No se le ha asignado una contraseña y su administrador le envió aquí.

## <a name="unlock-your-account"></a>Desbloqueo de la cuenta

Si está aquí para desbloquear la cuenta, siga los pasos que aparecen a continuación. Cuando vea **Chose a new password** (Elegir una nueva contraseña) en el paso 6, puede desbloquear o cambiar la contraseña, con lo que se desbloqueará.

## <a name="reset-my-password"></a>Restablecimiento de mi contraseña

Para volver a entrar en tu cuenta, siga estos pasos.
1. En cualquier página de inicio de sesión profesional o educativa, haga clic en el vínculo **¿No puede acceder a su cuenta?** y en **Cuenta profesional o educativa**, o bien vaya directamente a la [página de restablecimiento de la contraseña](https://passwordreset.microsoftonline.com/).

    ![¿No puede tener acceso a su cuenta?][Login]

2. Escriba su **Id. de usuario** profesional o educativo y demuestre que no es un robot; para ello, especifique el texto que aparece en el desafío CAPTCHA y haga clic en **Siguiente**.
3. Según cómo su administrador haya configurado las cosas, verá una o varias de las siguientes opciones:
    * **Enviar un mensaje de correo electrónico a mi dirección alternativa**: envía un correo electrónico con un código de 6 dígitos a su correo electrónico alternativo o correo electrónico de autenticación (según elija).
    * **Enviar mensaje de texto a mi teléfono móvil**: envía un mensaje de texto con un código de 6 dígitos a su teléfono móvil o su teléfono de autenticación (según elija).
    * **Llamar al teléfono móvil**: llama a su teléfono móvil o teléfono de autenticación (según elija). Presione la tecla # para verificar la llamada.
    * **Llamar al teléfono del trabajo**: llama a su teléfono del trabajo. Presione la tecla # para verificar la llamada.
    * **Responder a mis preguntas de seguridad**: muestra las preguntas de seguridad registradas previamente para que responda.
4. Rellene los campos obligatorios con respuestas a la información de desafío y haga clic en **Siguiente**.

    ![Compruebe los datos de autenticación][Verification].

5. Es posible que el administrador solicite un paso de comprobación adicional y tenga que repetir el paso 4 con otra opción.
6. En la página **Elegir la nueva contraseña**, escriba una nueva contraseña que satisfaga las necesidades de las organizaciones, confirme la contraseña y haga clic en **Finalizar**.

    ![Cambio de la contraseña][Change]

7. Cuando vea el mensaje **Se ha restablecido la contraseña**, puede iniciar sesión con la nueva contraseña.

    ![Se ha restablecido la contraseña][Complete]

> [!NOTE]
> Si el administrador no ha habilitado esta funcionalidad, aparece un vínculo "Póngase en contacto con el administrador" para que el administrador puede proporcionar asistencia mediante correo electrónico o mediante su propio portal web.
>

Después de usar este método para desbloquear o restablecer la contraseña, recibirá un correo electrónico, en el que se confirma que se ha completado el proceso, procedente de una cuenta similar a "Microsoft en nombre de su organización aquí". Si recibe un correo electrónico como ese y no había restablecido la contraseña para recuperar el acceso a su cuenta, póngase en contacto con su administrador.

## <a name="change-my-password"></a>Cambio de mi contraseña

Si ya conoce la contraseña y necesita cambiarla, realice los pasos siguientes:

### <a name="change-your-password-from-the-office-365-portal"></a>Cambio de la contraseña desde el portal de Office 365

1. Haga clic en el perfil, en la parte superior derecha, y en **Ver cuenta**.
2. Haga clic en **Seguridad y privacidad**.
3. **Password**
4. Escriba la contraseña anterior y establezca y confirme la nueva contraseña.
5. Haga clic en **Enviar**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Cambio de la contraseña en el panel de acceso de Azure

1. Inicie sesión en el [panel de acceso de Azure](https://myapps.microsoft.com/) usando su contraseña existente.
2. Haga clic en el perfil, en la parte superior derecha, y en **Perfil**.
3. Haga clic en **Cambiar contraseña**.
4. Escriba la contraseña anterior y establezca y confirme la nueva contraseña.
5. Haga clic en **Enviar**.

## <a name="next-steps"></a>Pasos siguientes

* [Cómo registrarse para usar el autoservicio de restablecimiento de la contraseña](active-directory-passwords-reset-register.md)
* [Página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
* [Portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de inicio: ¿No puede tener acceso a su cuenta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Compruebe los datos de autenticación"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Cambio de la contraseña"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Se ha restablecido la contraseña"

