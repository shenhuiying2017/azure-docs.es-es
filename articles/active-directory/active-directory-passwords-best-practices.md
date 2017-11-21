---
title: 'Lanzamiento: SSPR de Azure AD | Microsoft Docs'
description: "Sugerencia para el lanzamiento del autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 0be1616f5df915e566dc73c15dbea2e53177aa1c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="how-to-successfully-rollout-self-service-password-reset"></a>Lanzamiento del restablecimiento de contraseña de autoservicio

La mayoría de los clientes siguen estos pasos para garantizar que la funcionalidad SSPR se lanza sin problemas.

1. [Habilitación del restablecimiento de contraseña en el directorio](active-directory-passwords-getting-started.md).
2. [Configuración de los permisos de AD local para la escritura diferida de contraseñas](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Configuración de la escritura diferida de contraseñas](active-directory-passwords-writeback.md#configuring-password-writeback) para escribir contraseñas de Azure AD en su directorio local.
4. [Asignación y comprobación de las licencias necesarias](active-directory-passwords-licensing.md).
5. Si desea lanzar SSPR gradualmente, puede limitar el acceso a un grupo de usuarios para probar por primera vez con un grupo concreto. Para ello, cambie **Se habilitó el restablecimiento de contraseña del autoservicio** a **Seleccionado** y seleccione el grupo de seguridad para habilitar el restablecimiento de contraseña. 
6. Rellene los [Datos de autenticación](active-directory-passwords-data.md) para los usuarios, como el teléfono del trabajo, el teléfono móvil y la dirección de correo electrónico alternativa.
7. [Personalización de la experiencia de inicio de sesión de Azure AD para que incluya la marca de la empresa](active-directory-passwords-customize.md).
8. Enseñe a los usuarios usar SSPR; envíeles instrucciones para mostrarles el registro y el restablecimiento.
9. Puede elegir aplicar el registro en cualquier momento y solicitar que los usuarios escriban y confirmen la información de autenticación tras un tiempo determinado.
10. Revise el uso y registro de los usuarios al cabo de un tiempo; para ello, consulte los [informes de Azure AD](active-directory-passwords-reporting.md).
11. Cuando esté listo, habilite el restablecimiento de contraseña para todos los usuarios y establezca **Se habilitó el restablecimiento de contraseña del autoservicio** en **Todos**. 

   > [!IMPORTANT]
   > Pruebe SSPR con un usuario que no sea administrador, ya que Microsoft impone estrictos requisitos de autenticación para las cuentas de tipo administrador de Azure. Para más información acerca de la directiva de contraseñas de administrador, consulte el siguiente [artículo sobre la directiva de contraseñas](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>Implementación basada en correo electrónico

Muchos clientes opinan que una campaña de correo electrónico, con instrucciones sencillas, es la manera más fácil de hacer que los usuarios utilicen SSPR. [Hemos creado tres correos electrónicos simples que se puede utilizar como plantilla para facilitar el lanzamiento.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* Plantilla de correo electrónico **Próximamente** para las semanas o los días previos al lanzamiento, informa a los usuarios de que deben hacer algo.
* Plantilla de correo electrónico **Available Now** (Ya disponible) para el día del lanzamiento, dirige a los usuarios al registro y a confirmar los datos de autenticación para que usen SSPR cuando lo necesiten.
* Plantilla de correo electrónico **Recordatorio de registro** para unos días o semanas después de la implementación, recuerda a los usuarios que deben registrarse y confirmar los datos de autenticación.

![Correo electrónico][Email]

## <a name="creating-your-own-password-portal"></a>Creación de su propio portal de contraseñas

Muchos de nuestros mayores clientes eligen hospedar la página web y crear una entrada DNS raíz, como https://passwords.contoso.com. Rellenan esta página con vínculos al restablecimiento de contraseña de Azure AD, al registro para el restablecimiento de contraseña, a portales de cambio de contraseña y otra información específica de la organización. En las comunicaciones por correo electrónico o los folletos que envíe, puede incluir una dirección URL de la marca fácil de recordar que los usuarios puedan visitar cuando necesiten usar los servicios.

* Portal de restablecimiento de contraseña: https://aka.ms/sspr
* Portal de registro para el restablecimiento de contraseña: http://aka.ms/ssprsetup
* Portal para el cambio de contraseña: https://account.activedirectory.windowsazure.com/ChangePassword.aspx

Para ayudarle, hemos creado una página de ejemplo que puede usar y personalizar para las necesidades de su organización; se descargan desde [GitHub](https://github.com/ajamess/password-reset-page).

## <a name="using-enforced-registration"></a>Uso del registro exigido

Si desea que los usuarios se registren para el restablecimiento de la contraseña, puede obligarlos a registrarse cuando inician sesión con Azure AD. Puede habilitar esta opción desde la hoja **Restablecimiento de contraseña** del directorio; para ello, habilite la opción **¿Desea exigir a los usuarios que se registren al iniciar sesión?** de la pestaña **Registro**.

Los administradores pueden solicitar a los usuarios que vuelvan a registrarse después de un tiempo; para ello, deben establecer el **número de días antes de pedir a los usuarios que vuelvan a confirmar la información de autenticación** entre 0 y 730 días.

Después de habilitar esta opción, cuando los usuarios inicien sesión verán un mensaje que les informará de que el administrador les solicita que verifiquen la información de autenticación.

## <a name="populate-authentication-data"></a>Rellenado de los datos de autenticación

Si se [rellenan los datos de autenticación de los usuarios](active-directory-passwords-data.md), estos no deberán registrarse para el restablecimiento de contraseña para poder usar SSPR. Siempre que los datos de autenticación de los usuarios se correspondan con las directivas de restablecimiento de contraseña definida, estos podrán restablecer las contraseñas.

## <a name="disabling-self-service-password-reset"></a>Deshabilitación del autoservicio de restablecimiento de contraseña

Para deshabilitar el autoservicio de restablecimiento de contraseña, tan solo tiene que abrir el inquilino de Azure AD, ir a **Restablecimiento de contraseña**, **Propiedades** y seleccionar **Nadie** en **Se habilitó el restablecimiento de contraseña del autoservicio**.

## <a name="next-steps"></a>Pasos siguientes

* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md).
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).
* [¿Tiene alguna pregunta acerca de las licencias?](active-directory-passwords-licensing.md)
* [¿Qué datos usa SSPR y cuáles se deben rellenar en lugar de los usuarios?](active-directory-passwords-data.md)
* [¿Cuáles son las opciones de directiva con SSPR?](active-directory-passwords-policy.md)
* [¿Qué es la escritura diferida de contraseñas y por qué nos interesa?](active-directory-passwords-writeback.md)
* [¿Cómo se informa sobre la actividad de SSPR?](active-directory-passwords-reporting.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Creo que algo se ha roto. ¿Cómo se solucionan problemas en SSPR?](active-directory-passwords-troubleshoot.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "Personalice estas plantillas de correo electrónico para ajustarlas a sus necesidades organizativas"