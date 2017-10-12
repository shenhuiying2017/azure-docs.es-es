---
title: 'Azure AD: registro de SSPR | Microsoft Docs'
description: "Registro de datos de autenticación de autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 87c92265d4f377e76d3697e6957c5f739413f2b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="register-for-self-service-password-reset"></a>Registro para el autoservicio de restablecimiento de contraseñas

> [!IMPORTANT]
> **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md).

Como usuario final, puede restablecer su contraseña o desbloquear su cuenta sin necesidad de hablar con una persona que use el autoservicio de restablecimiento de contraseña (SSPR). Para poder usar esta funcionalidad, debe registrar los métodos de autenticación o confirmar los métodos de autenticación predefinidos que ha rellenado el administrador.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registro o confirmación de los datos de autenticación con SSPR

1. Abra el explorador web en el dispositivo y vaya a la [página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
2. Escriba el nombre de usuario y la contraseña proporcionada por el administrador
3. Según cómo haya realizado la configuración su personal de TI, una o varias de las opciones siguientes estarán a su disposición para que lleve a cabo la configuración y comprobación. El administrador puede rellenar por usted parte de esto si cuenta con permiso para usar la información.
    * Solo el administrador puede establecer el teléfono del trabajo
    * Teléfono de autenticación debe establecerse en otro número de teléfono al que tendría acceso, como un teléfono móvil que puede recibir un mensaje de texto o una llamada.
    * Correo electrónico de autenticación debe establecerse en una dirección de correo electrónico alternativa a la que pueda tener acceso sin la contraseña que debe restablecer.
    * Preguntas de seguridad le ofrece una lista de preguntas que el administrador ha aprobado para que las responda. No se puede usar la misma pregunta o respuesta más de una vez.
4. Proporcione y compruebe la información requerida por el administrador. Si hay más de una opción, le sugerimos que registre varios métodos para tener flexibilidad en caso de que algún método no esté disponible (por ejemplo: durante sus viajes, cuando no puede tener acceso a su teléfono del trabajo)

    ![Registrar los métodos de autenticación y hacer clic en Finalizar][Register]

5. Cuando haya terminado con el paso 4 elija **Finalizar** y ya podrá usar el restablecimiento de la contraseña de autoservicio cuando lo necesite en el futuro.

Los datos que escriba en el número de teléfono o en el correo electrónico de autenticación no serán visibles en el directorio global. Las únicas personas que pueden ver estos datos son el propio usuario y los administradores. Solo el propio usuario puede ver las respuestas a las preguntas de seguridad.

Los administradores pueden requerir que confirme los métodos de autenticación después de un período de tiempo para asegurarse de que todavía tiene los métodos adecuados registrados.

## <a name="common-problems-and-their-solutions"></a>problemas comunes y sus soluciones

 A continuación se presentan algunos casos terror comunes y sus soluciones:

| Caso de error| ¿Qué tipo de error aparece?| Solución |
| --- | --- | --- |
| Al escribir mi identificador de usuario, aparece una página "Póngase en contacto con su administrador" | Póngase en contacto con el administrador <br> <br> Hemos detectado que la contraseña de su cuenta de usuario no está administrada por Microsoft. Como consecuencia, no podemos restablecer automáticamente su contraseña. <br> <br> Debe ponerse en contacto con su personal de TI para obtener ayuda adicional. | Está viendo este mensaje porque su personal de TI administra su contraseña en el entorno local y no le permite restablecerla desde el vínculo No se puede tener acceso a su cuenta. <br> <br> Para restablecer la contraseña, póngase en contacto directamente con su personal de TI para solicitar ayuda y hágales saber que quiere restablecer la contraseña para que le puedan habilitar esta característica.|
| Después de escribir mi identificador de usuario, recibo el error "Su cuenta no está habilitada para el restablecimiento de contraseña". | La cuenta no está habilitada para restablecer la contraseña <br> <br> Su personal de TI no ha configurado la cuenta para utilizarla con este servicio. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Está viendo este mensaje porque su personal de TI no ha habilitado el restablecimiento de contraseña para su organización desde el vínculo No se puede tener acceso a su cuenta, o bien no le ha autorizado para usar la característica. <br> <br> Para restablecer la contraseña, haga clic en el vínculo Póngase en contacto con el administrador para enviar un mensaje de correo electrónico al personal de TI de su empresa para que sepan que quiere restablecer su contraseña y le puedan habilitar esta característica. |
| Después de escribir mi identificador de usuario, recibo el error "No se pudo comprobar su cuenta". | No se ha podido comprobar su cuenta. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Esta viendo este mensaje porque está habilitado para el restablecimiento de contraseña, pero no se ha registrado para usar el servicio. Para registrarse para el restablecimiento de contraseña, vaya a http://aka.ms/ssprsetup después de haber recuperado el acceso a su cuenta. <br> <br> Para restablecer la contraseña, haga clic en el vínculo Póngase en contacto con un administrador para enviar un correo electrónico al personal de TI de su empresa. |

## <a name="next-steps"></a>Pasos siguientes

* [Cómo cambiar la contraseña mediante el restablecimiento de la contraseña de autoservicio](active-directory-passwords-update-your-own-password.md)
* [Página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
* [Portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/)
* [No puede iniciar sesión en su cuenta de Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro en el restablecimiento de contraseña que muestra los métodos registrados y botón Finalizar"
