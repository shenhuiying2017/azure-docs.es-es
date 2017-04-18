---
title: "Azure AD: Registro de restablecimiento de la contraseña de autoservicio | Microsoft Docs"
description: "Registro de datos de autenticación para restablecer la contraseña de autoservicio"
services: active-directory
keywords: "Administración de contraseñas de Active Directory, administración de contraseñas, restablecimiento de contraseñas con el autoservicio de Azure AD, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 0f10ad4904b10a9554e949e77f0192edcb8f624f
ms.lasthandoff: 04/12/2017


---
# <a name="register-for-self-service-password-reset"></a>Registro para el autoservicio de restablecimiento de contraseñas

Si el administrador lo ha habilitado como usuario final, puede restablecer su contraseña o desbloquear su cuenta sin necesidad de hablar con una persona que use el restablecimiento de la contraseña de autoservicio (SSPR). Para poder usar esta funcionalidad, debe registrar los métodos de autenticación o confirmar los métodos de autenticación predefinidos que ha rellenado el administrador.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registro o confirmación de los datos de autenticación con SSPR

1. Abra el explorador web en el dispositivo y vaya a la [página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
2. Escriba el nombre de usuario y la contraseña proporcionada por el administrador
3. Según las opciones que el administrador haya aprobado, verá uno o varios de los elementos siguientes para que los configure o compruebe que se usarán si es necesario restablecer la contraseña
    * Teléfono de la oficina: solo el administrador puede establecer esta opción
    * Número de teléfono de autenticación: esta opción debe establecerse en otro número de teléfono distinto al que tenga acceso como, por ejemplo, un teléfono móvil que pueda recibir texto o llamadas (el administrador puede rellenar esto automáticamente con su número de teléfono móvil si ya tiene su permiso para utilizar esa información)
    * Correo electrónico de autenticación: esta opción debe establecerse en una dirección de correo electrónico alternativa a la que pueda acceder sin la contraseña que tiene que restablecer
    * Preguntas de seguridad: esta opción le ofrece una lista de preguntas que el administrador ha aprobado para que las responda. No se puede usar la misma respuesta para más de una pregunta.
4. Proporcione y compruebe la información requerida por el administrador. Si hay más de una opción le sugerimos que registre varios métodos para tener flexibilidad en caso de que algún método no esté disponible (por ejemplo: durante sus viajes, cuando no puede acceder a su teléfono de la oficina)

    ![Registrar los métodos de autenticación y hacer clic en Finalizar][Register]

5. Cuando haya terminado con el paso 4 elija **Finalizar** y ya podrá usar el restablecimiento de la contraseña de autoservicio cuando lo necesite en el futuro.

Los datos que escriba en el número de teléfono o en el correo electrónico de autenticación no serán visibles en el directorio global. Las únicas personas que pueden ver estos datos son el propio usuario y los administradores. Solo el propio usuario puede ver las respuestas a las preguntas de seguridad.

Los administradores pueden requerir que confirme los métodos de autenticación después de un período de tiempo para asegurarse de que todavía tiene los métodos adecuados registrados.

## <a name="next-steps"></a>Pasos siguientes

* [Cómo cambiar la contraseña mediante el restablecimiento de la contraseña de autoservicio](active-directory-passwords-update-your-own-password.md)
* [Página de registro en el restablecimiento de contraseña](http://aka.ms/ssprsetup)
* [Portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro en el restablecimiento de contraseña que muestra los métodos registrados y botón Finalizar"

