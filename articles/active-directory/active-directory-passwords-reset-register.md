---
title: 'Azure AD: registro de SSPR | Microsoft Docs'
description: "Registro de datos de autenticación de autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: bd0175558a94a146a365e8c1a32522f1e54c184e
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---
# <a name="register-for-self-service-password-reset"></a>Registro para el autoservicio de restablecimiento de contraseñas

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

## <a name="next-steps"></a>Pasos siguientes

* [Cómo cambiar la contraseña mediante el restablecimiento de la contraseña de autoservicio](active-directory-passwords-update-your-own-password.md)
* [Página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
* [Portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/)
* [No puede iniciar sesión en su cuenta de Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro en el restablecimiento de contraseña que muestra los métodos registrados y botón Finalizar"


