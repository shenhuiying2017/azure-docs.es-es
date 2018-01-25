---
title: "Registro para el autoservicio de restablecimiento de contraseña: Azure Active Directory"
description: "Registro de datos de autenticación de autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user;seohack1
ms.openlocfilehash: ffdffc0c471970e5a7e7a0bb291658cefe99ed71
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="register-for-self-service-password-reset"></a>Registro para el autoservicio de restablecimiento de contraseñas

> [!IMPORTANT]
> ¿Está aquí porque no puede iniciar sesión? Si es así, consulte [Restablecimiento de la contraseña profesional o educativa](active-directory-passwords-update-your-own-password.md).

Como usuario final, puede restablecer su contraseña o desbloquear su cuenta con el autoservicio de restablecimiento de contraseñas (SSPR) de Azure Active Directory (Azure AD). Para poder usar esta funcionalidad, debe registrar los métodos de autenticación o confirmar los predefinidos que haya rellenado el administrador.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registro o confirmación de los datos de autenticación con SSPR

1. Abra el explorador web en el dispositivo y vaya a la [página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup).
2. Escriba el nombre de usuario y la contraseña proporcionados por el administrador.
3. Según el modo en que el personal de TI haya realizado la configuración, una o varias de las opciones siguientes estarán a su disposición para que lleve a cabo la configuración y la verificación. Si el administrador tiene permiso para usar su información, puede rellenar una parte en su lugar.
    * **Teléfono del trabajo**: solo el administrador puede establecer esta opción.
    * **Teléfono de autenticación**: establezca esta opción en otro número de teléfono al que tenga acceso. Un ejemplo es un teléfono móvil que pueda recibir un mensaje de texto o una llamada.
    * **Correo electrónico de autenticación**: establezca esta opción en una dirección de correo electrónico alternativa a la que pueda tener acceso sin usar la contraseña que desea restablecer.
    * **Preguntas de seguridad**: el administrador ha aprobado esta lista de preguntas para que las responda. No puede usar la misma pregunta o respuesta más de una vez.
4. Proporcione la información requerida por el administrador y compruébela. Si hay más de una opción, se recomienda registrar varios métodos. Así se proporciona flexibilidad cuando uno de los métodos no esté disponible. Un ejemplo es cuando viaja y no puede tener acceso a su teléfono del trabajo.

    ![Registre los métodos de autenticación y seleccione Finalizar][Register]

5. Seleccione **Finalizar**. Ahora puede usar SSPR cuando lo necesite.

Los datos que escriba en el **número de teléfono** o en el **correo electrónico de autenticación** no serán visibles en el directorio global. Las únicas personas que pueden ver estos datos son el propio usuario y los administradores. Solo el propio usuario puede ver las respuestas a las preguntas de seguridad.

Los administradores pueden requerir que confirme los métodos de autenticación después de un período de tiempo para asegurarse de que todavía tiene los métodos adecuados registrados.

## <a name="common-problems-and-their-solutions"></a>problemas comunes y sus soluciones

 A continuación se presentan algunos casos terror comunes y sus soluciones:

| Caso de error| ¿Qué tipo de error aparece?| Solución |
| --- | --- | --- |
| Al escribir mi identificador de usuario, aparece una página "Póngase en contacto con su administrador" | Póngase en contacto con el administrador. <br> <br> Hemos detectado que la contraseña de su cuenta de usuario no está administrada por Microsoft. Como consecuencia, no podemos restablecer automáticamente su contraseña. <br> <br> Póngase en contacto con el personal de TI para obtener ayuda adicional. | Está viendo este mensaje porque el personal de TI administra su contraseña en el entorno local y no le permite restablecerla desde el vínculo **No se puede tener acceso a su cuenta**. <br> <br> Para restablecer la contraseña, póngase en contacto directamente con el personal de TI para obtener ayuda. Hágales saber que desea restablecer la contraseña para que puedan habilitar esta característica para usted.|
| Después de escribir mi identificador de usuario, recibo el error "Su cuenta no está habilitada para el restablecimiento de contraseña". | La cuenta no está habilitada para restablecer la contraseña. <br> <br> Su personal de TI no ha configurado la cuenta para utilizarla con este servicio. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Si ve este mensaje, es que el personal de TI no ha habilitado el restablecimiento de contraseña para su organización desde el vínculo **No se puede tener acceso a su cuenta** o bien no le han autorizado para usar la característica. <br> <br> Para restablecer la contraseña, seleccione el vínculo **Póngase en contacto con un administrador**. Se enviará un correo electrónico al personal de TI de su empresa. El correo electrónico les permite saber que desea restablecer la contraseña para que puedan habilitar esta característica para usted. |
| Después de escribir mi identificador de usuario, recibo el error "No se pudo comprobar su cuenta". | No se ha podido comprobar su cuenta. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Si ve este mensaje, es que está habilitado para usar el restablecimiento de contraseña, pero no se ha registrado para usar el servicio. Para registrarse para el restablecimiento de contraseña, vaya a la [página de registro de restablecimiento de contraseña](http://aka.ms/ssprsetup) después de haber recuperado el acceso a su cuenta. <br> <br> Para restablecer la contraseña, seleccione el vínculo **Póngase en contacto con un administrador** para enviar un correo electrónico al personal de TI de la compañía. |

## <a name="next-steps"></a>pasos siguientes

* [Cambiar la contraseña mediante el autoservicio de restablecimiento de contraseñas](active-directory-passwords-update-your-own-password.md)
* [Página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
* [Portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/)
* [Cuando no puedes iniciar sesión en tu cuenta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro en el restablecimiento de contraseña que muestra los métodos registrados y botón Finalizar"

