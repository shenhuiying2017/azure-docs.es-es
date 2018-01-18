---
title: "Guía de implementación de restablecimiento de contraseña de autoservicio: Azure Active Directory"
description: "Sugerencia para el lanzamiento del autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 0ab45043e2f75ff07a2a1a48e69507be185b56ab
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Cómo implementar correctamente el lanzamiento del restablecimiento de contraseña de autoservicio

Para garantizar un lanzamiento fluido de la funcionalidad del restablecimiento de contraseña de autoservicio (SSPR) de Azure Active Directory (Azure AD), la mayoría de los clientes completan los pasos siguientes:

1. [Habilitación del restablecimiento de contraseña en el directorio](active-directory-passwords-getting-started.md).
2. [Configuración de los permisos de Active Directory local para la escritura diferida de contraseñas](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Configuración de la escritura diferida de contraseñas](active-directory-passwords-writeback.md#configure-password-writeback) para escribir contraseñas de Azure AD en su directorio local.
4. [Asignación y comprobación de las licencias necesarias](active-directory-passwords-licensing.md).
5. Determine si desea realizar un lanzamiento gradual. Si desea lanzar SSPR gradualmente, puede limitar el acceso a un grupo de usuarios para probar el programa con un grupo concreto. Para lanzárselo a un grupo concreto, establezca **Se habilitó el restablecimiento de contraseña del autoservicio** en **Seleccionado** y seleccione el grupo de seguridad que quiere que pueda usar el restablecimiento de contraseña. 
6. Rellene los [Datos de autenticación](active-directory-passwords-data.md) necesarios para que los usuarios se registren, como el teléfono del trabajo, el teléfono móvil y la dirección de correo electrónico alternativa.
7. [Personalización de la experiencia de inicio de sesión de Azure AD para que incluya la marca de la empresa](active-directory-passwords-customize.md).
8. Enseñar a los usuarios a utilizar SSPR. Envíe las instrucciones para mostrar a los usuarios cómo registrarse y cómo restablecer la contraseña.
9. Determinar cuándo desea forzar el registro. Puede elegir forzar el registro en cualquier momento. También puede solicitar que los usuarios vuelvan a confirmar la información de autenticación tras un tiempo determinado.
10. Use la funcionalidad de informes. Con el tiempo, puede revisar el registro de los usuarios y el uso con la [funcionalidad de informes que proporciona Azure AD](active-directory-passwords-reporting.md).
11. Habilitar restablecimiento de contraseña. Cuando esté listo, habilite el restablecimiento de contraseña para todos los usuarios al establecer **Se habilitó el restablecimiento de contraseña del autoservicio** en **Todos**. 

   > [!NOTE]
   > Cambiar esta opción de un grupo seleccionado a todos los usuarios no invalida los datos de autenticación existentes que un usuario haya registrado como parte de un grupo de prueba. Los usuarios que están configurados y tienen datos de autenticación válidos registrados pueden continuar funcionando.

12. [Permitir que los usuarios de Windows 10 restablezcan su contraseña en la pantalla de inicio de sesión](active-directory-passwords-login.md).

   > [!IMPORTANT]
   > Pruebe SSPR con un usuario en lugar de con un administrador, ya que Microsoft impone requisitos de autenticación estrictos para las cuentas de administrador de Azure. Para más información acerca de la directiva de contraseñas de administrador, consulte nuestro artículo sobre la [directiva de contraseñas](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>Implementación basada en correo electrónico

Muchos clientes opinan que una campaña de correo electrónico, con instrucciones sencillas, es la manera más fácil de hacer que los usuarios utilicen SSPR. [Hemos creado tres correos electrónicos simples que se pueden utilizar como plantilla para facilitar el lanzamiento](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16):

* **Próximamente**: Una plantilla de correo electrónico para usar las semanas o los días previos al lanzamiento con el fin de informar a los usuarios de que deben hacer algo.
* **Available Now** (Ya disponible): Una plantilla de correo electrónico para que use el día del lanzamiento del programa con el fin de dirigir a los usuarios al registro y a confirmar los datos de autenticación. Si los usuarios se registran ahora, tendrán SSPR disponible para cuando lo necesiten.
* **Recordatorio de registro**: Una plantilla de correo electrónico para los días o semanas previos a la implementación para recordar a los usuarios que se registren y confirmen los datos de autenticación.

![Correo electrónico][Email]

## <a name="create-your-own-password-portal"></a>Creación de su propio portal de contraseñas

Muchos clientes eligen hospedar una página web y crear una entrada DNS raíz, como https://passwords.contoso.com. Rellenan esta página con vínculos a la información siguiente:

* [Portal de restablecimiento de contraseña de Azure AD: https://aka.ms/sspr](https://aka.ms/sspr)
* [Portal de registro para el restablecimiento de contraseña de Azure AD: http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* [Portal para el cambio de contraseña de Azure AD: https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Otra información específica de la organización

En las comunicaciones por correo electrónico o los folletos que envíe, puede incluir una dirección URL de la marca fácil de recordar que los usuarios puedan visitar cuando necesiten usar los servicios. Para ayudarle, hemos creado una [página de ejemplo de restablecimiento de contraseña](https://github.com/ajamess/password-reset-page) que puede usar y personalizar para las necesidades de su organización.

## <a name="use-enforced-registration"></a>Uso del registro forzado

Si desea que los usuarios se registren para el restablecimiento de la contraseña, puede solicitar que se registren cuando inician sesión con Azure AD. Puede habilitar esta opción desde el panel **Restablecimiento de contraseña** del directorio; para ello, habilite la opción **Exigir a los usuarios que se registren al iniciar sesión** de la pestaña **Registro**.

Los administradores pueden solicitar a los usuarios que se vuelvan a registrar después de un período de tiempo específico. Pueden establecer la opción **Número de días que pasan hasta que se pide a los usuarios que vuelvan a confirmar su información de autenticación** de 0 a 730 días.

Después de habilitar esta opción, cuando los usuarios inicien sesión verán un mensaje que les informará de que el administrador les solicita que comprueben la información de autenticación.

## <a name="populate-authentication-data"></a>Rellenado de los datos de autenticación

Debe [Rellenar los datos de autenticación para los usuarios](active-directory-passwords-data.md). De este modo los usuarios no necesitan registrarse para restablecer la contraseña antes de poder usar SSPR. Siempre que los datos de autenticación proporcionados por los usuarios se correspondan con la directiva de restablecimiento de contraseña definida, se podrán restablecer las contraseñas.

## <a name="disable-self-service-password-reset"></a>Deshabilitación del autoservicio de restablecimiento de contraseña

Deshabilitar el autoservicio de restablecimiento de contraseña es fácil. Abra el inquilino de Azure AD y vaya a **Restablecimiento de contraseña** > **Propiedades** y seleccione **Ninguno** en **Se habilitó el restablecimiento de contraseña del autoservicio**.

## <a name="next-steps"></a>pasos siguientes

* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md)
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md)
* [¿Tiene alguna pregunta acerca de las licencias?](active-directory-passwords-licensing.md)
* [¿Qué datos usa SSPR y cuáles se deben rellenar en lugar de los usuarios?](active-directory-passwords-data.md)
* [¿Cuáles son las opciones de directiva con SSPR?](active-directory-passwords-policy.md)
* [¿Qué es la escritura diferida de contraseñas y por qué nos interesa?](active-directory-passwords-writeback.md)
* [¿Cómo se informa sobre la actividad de SSPR?](active-directory-passwords-reporting.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Creo que algo se ha roto. ¿Cómo se solucionan problemas en SSPR?](active-directory-passwords-troubleshoot.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "Personalice estas plantillas de correo electrónico para ajustarlas a sus necesidades organizativas"
