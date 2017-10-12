---
title: "Azure AD: restablecer la contraseña | Microsoft Docs"
description: "Usar el autoservicio de restablecimiento de contraseña para recuperar el acceso a su cuenta de usuario profesional o educativa"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 1a5e97e2a6aca440c9643569014ffce41a614324
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="help-i-forgot-my-azure-ad-password"></a>Ayuda, he olvidado mi contraseña de Azure AD

Si ha olvidado la contraseña, nunca ha recibido ninguna de su equipo de TI, está bloqueada fuera de su cuenta o si desea cambiarla, podemos ayudarle. Si conoce la contraseña y solo necesita cambiarla, vaya a la sección [Cambio de mi contraseña](#change-my-password).

   > [!NOTE]
   > Si quiere volver a su cuenta personal como Xbox, hotmail.com o outlook.com, pruebe las [sugerencias que se encuentran en este artículo](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Restablecimiento o desbloqueo de mi contraseña en una cuenta profesional o educativa

Si no puede obtener acceso a su cuenta debido a una de las siguientes causas:

* La contraseña no funciona y quiere restablecerla.
* Conoce la contraseña pero la cuenta está bloqueada y le gustaría desbloquearla.

Siga los pasos siguientes para obtener acceso al autoservicio de restablecimiento de contraseña de Azure AD o, como nos gusta llamarlo, a SSPR, y vuelva a su cuenta.

1. En cualquier página de inicio de sesión profesional o educativa, haga clic en el vínculo **¿No puede acceder a su cuenta?** y en **Cuenta profesional o educativa**, o bien vaya directamente a la [página de restablecimiento de la contraseña](https://passwordreset.microsoftonline.com/).

    ![¿No puede tener acceso a su cuenta?][Login]

2. Escriba el **Id. de usuario** de su cuenta profesional o educativa, demuestre que no es un robot escribiendo los caracteres que aparecen en la pantalla y luego haga clic en **Siguiente**.

   > [!NOTE]
   > Si su personal de TI no ha habilitado esta funcionalidad, aparecerá un vínculo con el texto "Póngase en contacto con el administrador" para solicitar ayuda al personal de TI mediante correo electrónico o un portal web de su propiedad.
   >
   > Si necesita desbloquear la cuenta, elija la opción "Conozco la contraseña, pero aún así no puedo iniciar sesión".
   >

3. En función de cómo haya configurado el personal de TI el SSPR, verá una o varias de las siguientes opciones. El usuario o su personal de TI ya habrán rellenado parte de esta información antes de usar el artículo [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).

   * **Enviar un mensaje de correo electrónico a mi dirección alternativa**
   * **Enviar mensaje de texto a mi teléfono móvil**
   * **Llamar al teléfono móvil**
   * **Llamar al teléfono del trabajo** 
   * **Responder a mis preguntas de seguridad**

   Elija una opción, proporcione las respuestas correctas y haga clic en **Siguiente**.

   ![Compruebe los datos de autenticación][Verification].

4. Es posible que el personal de TI necesite realizar otra comprobación y tenga que repetir el paso 3 de nuevo con una opción diferente.
5. En la página **Elegir una nueva contraseña**, escriba la nueva contraseña, confírmela y luego haga clic en **Finalizar**. Se recomienda que la contraseña contenga entre 8 y 16 caracteres, caracteres en mayúsculas y minúsculas, números y caracteres especiales.
6. Cuando vea el mensaje **Se ha restablecido la contraseña**, puede iniciar sesión con la nueva contraseña.

    ![Se ha restablecido la contraseña][Complete]

Ahora ya debería tener acceso a su cuenta. En caso contrario, debe ponerse en contacto con el personal de TI de su organización para obtener más ayuda.

Es posible que reciba un correo electrónico de confirmación que procede de una cuenta como "Microsoft en nombre de \<su organización>". Si recibe un correo electrónico como ese y no había usado el autoservicio de restablecimiento de contraseña para recuperar el acceso a su cuenta, póngase en contacto con el administrador.

## <a name="change-my-password"></a>Cambio de mi contraseña

Si ya conoce la contraseña y quiere cambiarla, realice los pasos siguientes.

### <a name="change-your-password-from-the-office-365-portal"></a>Cambio de la contraseña desde el portal de Office 365

Utilice este método si accede normalmente a las aplicaciones mediante el portal de Office.

1. Inicie sesión en su [cuenta de Office 365](https://www.office.com) con la contraseña existente
2. Haga clic en el perfil, en la parte superior derecha, y en **Ver cuenta**.
3. Haga clic en **Seguridad y privacidad** > **Contraseña**
4. Escriba la contraseña anterior y establezca y confirme la nueva contraseña, y finalmente haga clic en **Enviar**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Cambio de la contraseña en el panel de acceso de Azure

Utilice este método si accede normalmente a las aplicaciones mediante el panel de acceso de Azure.

1. Inicie sesión en el [panel de acceso de Azure](https://myapps.microsoft.com/) usando su contraseña existente.
2. Haga clic en el perfil, en la parte superior derecha, y en **Perfil**.
3. Haga clic en **Cambiar contraseña**.
4. Escriba la contraseña anterior y establezca y confirme la nueva contraseña, y finalmente haga clic en **Enviar**.

## <a name="common-problems-and-their-solutions"></a>problemas comunes y sus soluciones

 A continuación se presentan algunos casos terror comunes y sus soluciones:

| Caso de error| ¿Qué tipo de error aparece?| Solución |
| --- | --- | --- |
| Al escribir mi identificador de usuario, aparece una página "Póngase en contacto con su administrador" | Póngase en contacto con el administrador <br> <br> Hemos detectado que la contraseña de su cuenta de usuario no está administrada por Microsoft. Como consecuencia, no podemos restablecer automáticamente su contraseña. <br> <br> Debe ponerse en contacto con su personal de TI para obtener ayuda adicional. | Está viendo este mensaje porque su personal de TI administra su contraseña en el entorno local y no le permite restablecerla desde el vínculo No se puede tener acceso a su cuenta. <br> <br> Para restablecer la contraseña, póngase en contacto directamente con su personal de TI para solicitar ayuda y hágales saber que quiere restablecer la contraseña para que le puedan habilitar esta característica.|
| Después de escribir mi identificador de usuario, recibo el error "Su cuenta no está habilitada para el restablecimiento de contraseña". | La cuenta no está habilitada para restablecer la contraseña <br> <br> Su personal de TI no ha configurado la cuenta para utilizarla con este servicio. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Está viendo este mensaje porque su personal de TI no ha habilitado el restablecimiento de contraseña para su organización desde el vínculo No se puede tener acceso a su cuenta, o bien no le ha autorizado para usar la característica. <br> <br> Para restablecer la contraseña, haga clic en el vínculo Póngase en contacto con el administrador para enviar un mensaje de correo electrónico al personal de TI de su empresa para que sepan que quiere restablecer su contraseña y le puedan habilitar esta característica. |
| Después de escribir mi identificador de usuario, recibo el error "No se pudo comprobar su cuenta". | No se ha podido comprobar su cuenta. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Esta viendo este mensaje porque está habilitado para el restablecimiento de contraseña, pero no se ha registrado para usar el servicio. Para registrarse para el restablecimiento de contraseña, vaya a http://aka.ms/ssprsetup después de haber recuperado el acceso a su cuenta. <br> <br> Para restablecer la contraseña, haga clic en el vínculo Póngase en contacto con un administrador para enviar un correo electrónico al personal de TI de su empresa. |

## <a name="next-steps"></a>Pasos siguientes

* [Cómo registrarse para usar el autoservicio de restablecimiento de la contraseña](active-directory-passwords-reset-register.md)
* [Página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
* [Portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/)
* [No puede iniciar sesión en su cuenta de Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de inicio: ¿No puede tener acceso a su cuenta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Compruebe los datos de autenticación"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Cambio de la contraseña"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Se ha restablecido la contraseña"
