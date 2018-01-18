---
title: "Azure AD: restablecer la contraseña | Microsoft Docs"
description: "Usar el autoservicio de restablecimiento de contraseña para recuperar el acceso a su cuenta de usuario profesional o educativa"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: a2ba6c6b59ba4decdfd621a62ec07ae04bfb98cb
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="reset-your-work-or-school-password"></a>Restablecimiento de la contraseña profesional o educativa

Si ha olvidado la contraseña, nunca ha recibido ninguna del soporte técnico de su compañía, está bloqueado en su cuenta o quiere cambiarla, podemos ayudarle. Si conoce la contraseña y solo necesita cambiarla, vaya a la sección [Cambiar la contraseña](#change-my-password).

   > [!NOTE]
   > Si quiere volver a su cuenta personal, como Xbox, hotmail.com o outlook.com, pruebe las sugerencias que se encuentran en el artículo [Cuando no puedes iniciar sesión en tu cuenta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Restablecimiento o desbloqueo de mi contraseña en una cuenta profesional o educativa

Es posible que no pueda obtener acceso a su cuenta de Azure Active Directory (Azure AD) debido a una de las siguientes razones:

* La contraseña no funciona y quiere restablecerla.
* Conoce la contraseña pero la cuenta está bloqueada y le quiere desbloquearla.

Realice los pasos siguientes para tener acceso al autoservicio de restablecimiento de contraseña (SSPR) de Azure AD y volver a su cuenta.

1. En cualquier página de **inicio de sesión** profesional o educativa, seleccione el vínculo **¿No puede acceder a su cuenta?** y seleccione **Cuenta profesional o educativa** o vaya directamente a la página [Restablecimiento de contraseña](https://passwordreset.microsoftonline.com/).

    ![¿No puede tener acceso a su cuenta?][Login]

2. Escriba el **Id. de usuario** de la cuenta profesional o educativa, demuestre que no es un robot escribiendo los caracteres que aparecen en la pantalla y luego seleccione **Siguiente**.

   > [!NOTE]
   > Si el personal de TI no ha habilitado esta funcionalidad, aparecerá un vínculo con el texto "Póngase en contacto con el administrador" para solicitar ayuda al personal de TI por correo electrónico o a través de un portal web de su propiedad.
   >
   > Si necesita desbloquear la cuenta, seleccione la opción **Conozco la contraseña, pero aún así no puedo iniciar sesión**.
   >

3. En función de cómo el personal de TI haya configurado SSPR, verá uno o varios de los siguientes métodos de autenticación. El usuario o el personal de TI ya habrán rellenado parte de esta información siguiendo los pasos indicados en el artículo [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).

   * **Enviar un mensaje de correo electrónico a mi dirección alternativa**
   * **Enviar mensaje de texto a mi teléfono móvil**
   * **Llamar al teléfono móvil**
   * **Llamar al teléfono del trabajo** 
   * **Responder a mis preguntas de seguridad**

   Elija una opción, dé las respuestas correctas y luego seleccione **Siguiente**.

   ![Compruebe los datos de autenticación][Verification].

4. Puede que el personal de TI tenga que realizar otra comprobación y haya que repetir el paso 3 con una opción distinta.
5. En la página **elegir una contraseña nueva**, escriba la nueva contraseña, confírmela y seleccione **Finalizar**. Puede que la contraseña profesional o educativa tenga algunos requisitos que debe cumplir. Se recomienda elegir una contraseña que tenga entre 8 y 16 caracteres e incluya caracteres en mayúsculas y minúsculas, un número y un carácter especial.
6. Cuando vea el mensaje **Se ha restablecido la contraseña**, puede iniciar sesión con la nueva contraseña.

    ![Se ha restablecido la contraseña][Complete]

Ahora ya debería tener acceso a su cuenta. En caso contrario, debe ponerse en contacto con el personal de TI de la organización para obtener más ayuda.

Es posible que reciba un correo electrónico de confirmación que procede de una cuenta como "Microsoft en nombre de \<su organización>". Si recibe un correo electrónico como ese y no ha usado el autoservicio de restablecimiento de contraseña para recuperar el acceso a su cuenta, póngase en contacto con el personal de TI de su organización.

## <a name="change-my-password"></a>Cambio de mi contraseña

Si ya conoce la contraseña y quiere cambiarla, realice los pasos siguientes.

### <a name="change-your-password-from-the-office-365-portal"></a>Cambio de la contraseña desde el portal de Office 365

Use este método si normalmente obtiene acceso a las aplicaciones mediante el Portal de Office:

1. Inicie sesión en su [cuenta de Office 365](https://www.office.com) con la contraseña existente.
2. Seleccione el perfil que se encuentra en la parte superior derecha y luego elija **Ver cuenta**.
3. Seleccione **Seguridad y privacidad** > **Contraseña**.
4. Escriba la contraseña anterior, establezca la nueva contraseña y confírmela y luego seleccione **Enviar**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Cambio de la contraseña en el panel de acceso de Azure

Use este método si normalmente obtiene acceso a las aplicaciones mediante el panel de acceso de Azure (MyApps):

1. Inicie sesión en el [panel de acceso de Azure](https://myapps.microsoft.com/) con su contraseña existente.
2. Seleccione el perfil que se encuentra en la parte superior derecha y luego elija **Perfil**.
3. Seleccione **Cambiar contraseña**.
4. Escriba la contraseña anterior, establezca la nueva contraseña y confírmela y luego seleccione **Enviar**.

## <a name="reset-password-at-sign-in"></a>Restablecer la contraseña en el inicio de sesión

Si el administrador ha habilitado la funcionalidad, es posible que ahora pueda ver un vínculo a **Restablecer contraseña** en la pantalla de inicio de sesión de Windows 10 Fall Creators Update.

![Pantalla de inicio de sesión][LoginScreen]

Seleccione el vínculo **Restablecer contraseña** para abrir la experiencia de SSPR en la pantalla de inicio de sesión y poder restablecer la contraseña sin tener que iniciar sesión para tener acceso a la experiencia normal basada en web.

1. Confirme su identificador de usuario y seleccione **Siguiente**.
2. Seleccione un método de contacto para la comprobación y confírmelo. Puede que el personal de TI tenga que realizar otra comprobación y haya que repetir este paso con una opción distinta.

   ![Método de contacto][ContactMethod]

3. En la página **Crear una nueva contraseña**, escriba la nueva contraseña, confírmela y luego seleccione **Siguiente**. Se recomienda que la contraseña tenga entre 8 y 16 caracteres e incluya caracteres en mayúsculas y minúsculas, números y caracteres especiales.

   ![Restablecimiento de contraseñas][ResetPassword]

4. Cuando vea el mensaje **Se ha restablecido la contraseña**, seleccione **Finalizar**.

Ahora ya debería tener acceso a su cuenta. Si no es así, póngase en contacto con el personal de TI de la organización para obtener más ayuda.

## <a name="common-problems-and-their-solutions"></a>problemas comunes y sus soluciones

 A continuación se presentan algunos casos terror comunes y sus soluciones:

| Caso de error| ¿Qué tipo de error aparece?| Solución |
| --- | --- | --- |
| Veo un error al intentar cambiar la contraseña. | Lamentablemente, la contraseña contiene una palabra, frase o patrón que permite adivinar la contraseña fácilmente. Vuelva a intentarlo con una contraseña diferente. | Elija una contraseña que sea más difícil de adivinar. |
| Al escribir mi identificador de usuario, aparece una página "Póngase en contacto con el administrador". | Póngase en contacto con el administrador. <br> <br> Hemos detectado que la contraseña de su cuenta de usuario no está administrada por Microsoft. Como consecuencia, no podemos restablecer automáticamente su contraseña. <br> <br> Tiene que ponerse en contacto con el personal de TI para obtener más ayuda. | Si ve este mensaje es que el personal de TI administra la contraseña en su entorno local. No puede restablecer la contraseña desde el vínculo "No se puede tener acceso a su cuenta". <br> <br> Para restablecer la contraseña, póngase en contacto directamente con el personal de TI para solicitar ayuda y dígales que quiere restablecer la contraseña para que le puedan habilitar esta característica.|
| Después de escribir mi identificador de usuario, recibo el error "Su cuenta no está habilitada para el restablecimiento de contraseña". | La cuenta no está habilitada para restablecer la contraseña. <br> <br> Lamentablemente, el personal de TI no ha configurado la cuenta para usar este servicio. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Si ve este mensaje es que el personal de TI no ha habilitado el restablecimiento de contraseña para su organización desde el vínculo "No se puede tener acceso a su cuenta", o bien no le ha autorizado para usar la característica. <br> <br> Para restablecer la contraseña, seleccione el vínculo "Póngase en contacto con un administrador" para enviar un correo electrónico al personal de TI de la compañía y decirles que quiere restablecer su contraseña para que puedan habilitarle esta característica. |
| Después de escribir mi identificador de usuario, recibo el error "No se pudo comprobar su cuenta". | No se ha podido comprobar su cuenta. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Si ve este mensaje es que está habilitado para el restablecimiento de contraseña, pero no se ha registrado para usar el servicio. Para registrarse para el restablecimiento de contraseña, vaya a http://aka.ms/ssprsetup después de haber recuperado el acceso a su cuenta. <br> <br> Para restablecer la contraseña, seleccione el vínculo "Póngase en contacto con un administrador" para enviar un correo electrónico al personal de TI de la compañía. |

## <a name="next-steps"></a>pasos siguientes

* [Cómo registrarse para usar el autoservicio de restablecimiento de la contraseña](active-directory-passwords-reset-register.md)
* [Página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
* [Portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/)
* [No puede iniciar sesión en su cuenta de Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de inicio: ¿No puede tener acceso a su cuenta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Compruebe los datos de autenticación"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Cambio de la contraseña"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Se ha restablecido la contraseña"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Vínculo para el restablecimiento de la contraseña en la página de inicio de sesión de Windows 10 Fall Creators Update"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Compruebe los datos de autenticación"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Cambio de la contraseña"
