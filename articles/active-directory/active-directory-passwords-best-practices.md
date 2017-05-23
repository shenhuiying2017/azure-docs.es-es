---
title: 'Lanzamiento: SSPR de Azure AD | Microsoft Docs'
description: "Sugerencia para el lanzamiento del autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 6cc1589f2ebad9267334f70926be369b32c8e075
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---
# <a name="roll-out-password-reset-for-users"></a>Lanzamiento del restablecimiento de contraseña para los usuarios

La mayoría de los clientes siguen los pasos a continuación para garantizar que la funcionalidad SSPR se lanza sin problemas.

1. [Habilitación del restablecimiento de contraseña en el directorio](active-directory-passwords-getting-started.md)
2. [Configuración de los permisos de AD local para la escritura diferida de contraseñas](active-directory-passwords-how-it-works.md#active-directory-permissions)
3. [Configuración de la escritura diferida de contraseñas](active-directory-passwords-writeback.md#configuring-password-writeback) para escribir contraseñas de Azure AD en su directorio local
4. [Asignación y comprobación de las licencias necesarias](active-directory-passwords-licensing.md)
5. Si desea el lanzamiento gradual, también puede limitar el restablecimiento de contraseña a un grupo de usuarios para que el lanzamiento de la característica se lance poco a poco. Para ello, cambie **Se habilitó el restablecimiento de contraseña del autoservicio** de **Todo el mundo** a **Un grupo** y seleccione un grupo de seguridad para habilitar el restablecimiento de contraseña. Todos los miembros de este grupo deben tener licencias asignadas; es una excelente manera de habilitar [licencias por grupos](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing).
6. Rellene el conjunto mínimo de [datos de autenticación](active-directory-passwords-data.md), según las directivas.
7. Enseñe a los usuarios usar SSPR; envíeles instrucciones para mostrarles el registro y el restablecimiento.
    > [!NOTE]
    > Pruebe SSPR con un usuario que no sea administrador, ya que Microsoft impone estrictos requisitos de autenticación para las cuentas de tipo administrador de Azure. Para más información sobre la directiva de contraseñas de administrador, consulte el [artículo de profundización](active-directory-passwords-how-it-works.md).

8. Puede elegir aplicar el registro en cualquier momento y solicitar que los usuarios escriban y confirmen la información de autenticación tras un tiempo determinado. Si no desea que los usuarios tengan que registrarse, puede elegir la [implementación del restablecimiento de contraseña sin necesidad de registro del usuario final](active-directory-passwords-data.md).
9. Revise el uso y registro de los usuarios al cabo de un tiempo; para ello, consulte los [informes de Azure AD](active-directory-passwords-reporting.md).

## <a name="email-based-rollout"></a>Implementación basada en correo electrónico

Muchos clientes opinan que una campaña de correo electrónico, con instrucciones sencillas, es la manera más fácil de hacer que los usuarios utilicen SSPR. Hemos creado tres correos electrónicos sencillos que puede utilizar como plantilla para facilitar el lanzamiento.

* Plantilla de correo electrónico **Próximamente** para las semanas o los días previos al lanzamiento, informa a los usuarios de que deben hacer algo.
* Plantilla de correo electrónico **Available Now** (Ya disponible) para el día del lanzamiento, dirige a los usuarios al registro y a confirmar los datos de autenticación para que usen SSPR cuando lo necesiten.
* Plantilla de correo electrónico **Recordatorio de registro** para unos días o semanas después de la implementación, recuerda a los usuarios que deben registrarse y confirmar los datos de autenticación.

## <a name="creating-your-own-password-portal"></a>Creación de su propio portal de contraseñas

Muchos de nuestros mayores clientes eligen hospedar la página web y crear una entrada DNS raíz, como https://passwords.contoso.com. Rellenan esta página con vínculos al restablecimiento de contraseña de Azure AD, al registro para el restablecimiento de contraseña, a portales de cambio de contraseña y otra información específica de la organización. En las comunicaciones por correo electrónico o los folletos que envíe, puede incluir una dirección URL de la marca fácil de recordar que los usuarios puedan visitar cuando necesiten usar los servicios.

* Portal de restablecimiento de contraseña: https://passwordreset.microsoftonline.com/
* Portal de registro para el restablecimiento de contraseña: http://aka.ms/ssprsetup
* Portal para el cambio de contraseña: https://account.activedirectory.windowsazure.com/ChangePassword.aspx

## <a name="using-enforced-registration"></a>Uso del registro exigido

Si desea que los usuarios se registren para el restablecimiento de la contraseña, puede obligarlos a registrarse cuando inician sesión con Azure AD. Puede habilitar esta opción desde la hoja **Restablecimiento de contraseña** del directorio; para ello, habilite la opción **¿Desea exigir a los usuarios que se registren al iniciar sesión?** de la pestaña **Registro**.

Los administradores pueden solicitar a los usuarios que vuelvan a registrarse después de un tiempo; para ello, deben establecer el **número de días antes de pedir a los usuarios que vuelvan a confirmar la información de autenticación** entre 0 y 730 días.

Después de habilitar esta opción, cuando los usuarios inicien sesión verán un mensaje que les informará de que el administrador les solicita que verifiquen la información de autenticación.

## <a name="populate-authentication-data"></a>Rellenado de los datos de autenticación

Si se [rellenan los datos de autenticación de los usuarios](active-directory-passwords-data.md), estos no deberán registrarse para el restablecimiento de contraseña para poder usar SSPR. Siempre que los datos de autenticación de los usuarios se correspondan con las directivas de restablecimiento de contraseña definida, estos podrán restablecer las contraseñas.

## <a name="disabling-self-service-password-reset"></a>Deshabilitación del autoservicio de restablecimiento de contraseña

Para deshabilitar el autoservicio de restablecimiento de contraseña, tan solo tiene que abrir el inquilino de Azure AD, ir a **Restablecimiento de contraseña**, **Propiedades** y seleccionar **Nadie** en **Se habilitó el restablecimiento de contraseña del autoservicio**.

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Personalización**](active-directory-passwords-customize.md): personalización de la experiencia de SSPR para la empresa
* [**Directiva**](active-directory-passwords-policy.md): conozca y establezca directivas de contraseñas de Azure AD.
* [**Escritura diferida de contraseñas** ](active-directory-passwords-writeback.md): cómo funciona la escritura diferida de contraseñas con su directorio local.
* [**Informes**](active-directory-passwords-reporting.md): descubra si, cuándo y dónde sus usuarios acceden a la funcionalidad SSPT.
* [**Profundización técnica**](active-directory-passwords-how-it-works.md): conozca lo que hay detrás para comprender cómo funciona.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? : respuestas a las preguntas que siempre se ha hecho.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR
