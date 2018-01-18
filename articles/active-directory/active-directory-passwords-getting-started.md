---
title: "Guía de inicio rápido de restablecimiento de contraseña de autoservicio: Azure Active Directory"
description: "Implementación rápida del autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 88123cead40968ebf7327b81f94233529f97aa2f
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Implementación rápida del autoservicio de restablecimiento de contraseña de Azure AD

> [!IMPORTANT]
> **¿Está aquí porque tiene problemas para iniciar sesión?** En este caso, consulte la [ayuda para restablecer la contraseña de Azure AD](active-directory-passwords-update-your-own-password.md).

El autoservicio de restablecimiento de contraseña (SSPR) ofrece un medio sencillo con el que los administradores de TI pueden permitir que los usuarios restablezcan o desbloqueen sus cuentas o contraseñas. El sistema incluye informes detallados del seguimiento de acceso de los usuarios al sistema, además de notificaciones de alerta de posibles abusos o usos indebidos.

En esta guía se da por hecho que ya dispone de un inquilino de Azure Active Directory (Azure AD) con licencia o una prueba operativa. Si necesita ayuda para configurar Azure AD, consulte el artículo de [introducción a Azure AD](get-started-azure-ad.md).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Habilitación de SSPR para el inquilino de Azure AD

1. En su inquilino de Azure AD existente, seleccione **Restablecimiento de contraseña**.

2. En la página **Propiedades**, en **Se habilitó el restablecimiento de contraseña del autoservicio**, elija una de las siguientes opciones:
   * **Ninguno**: nadie puede usar la funcionalidad SSPR.
   * **Seleccionado**: solo los miembros de un grupo de Azure AD determinado que elija podrán usar la funcionalidad SSPR. Se recomienda definir un grupo de usuarios y usarlo al implementar la funcionalidad para una prueba de concepto.
   * **Todos**: todos los usuarios con cuentas en su inquilino de Azure AD podrán usar la funcionalidad SSPR. Se recomienda establecer esta opción cuando esté listo para implementar la funcionalidad en todo su inquilino una vez haya completado una prueba de concepto.

   > [!IMPORTANT]
   > Las cuentas de administrador de Azure siempre tendrán la capacidad de restablecer sus contraseñas con independencia del ajuste de esta opción. 

3. En la página **Métodos de autenticación**, elija lo siguiente:
   * **Número de métodos requeridos para el restablecimiento**: se admite uno como mínimo o dos como máximo.
   * **Métodos disponibles para los usuarios**: se necesita al menos uno, pero es recomendable tener una opción adicional disponible.
      * **Correo electrónico**: se envía un correo electrónico con un código a la dirección de correo electrónico de autenticación configurada del usuario.
      * **Teléfono móvil**: proporciona al usuario la opción de recibir una llamada o un mensaje de texto con un código en su número de teléfono móvil configurado.
      * **Teléfono de la oficina**: realiza una llamada al número de teléfono de la oficina configurado del usuario para transmitirle un código.
      * **Preguntas de seguridad**: es necesario elegir:
         * **Número de preguntas necesarias para registrarse**: el valor mínimo para el registro correcto. Un usuario puede elegir responder más preguntas para crear un grupo de preguntas del que escoger. Esta opción permite definir de tres a cinco preguntas y debe ser mayor o igual que el número de preguntas necesarias para el restablecimiento de la contraseña. El usuario puede agregar preguntas personalizadas al seleccionar el botón **Personalizado** al seleccionar las preguntas de seguridad.
         * **Número de preguntas necesarias para el restablecimiento**: pueden establecerse de tres a cinco preguntas que responder correctamente para permitir restablecer o desbloquear la contraseña de los usuarios.
            
    ![Autenticación][Authentication]

4. Recomendado: en **Personalización** puede cambiar el vínculo **Póngase en contacto con el administrador** para la dirección a otra página o dirección de correo electrónico que defina. Se recomienda establecer este vínculo en algo como una dirección de correo electrónico o un sitio web que los usuarios ya utilicen para las preguntas de soporte técnico.

5. Opcional: la página **Registro** proporciona a los administradores la opción de:
   * Exigir a los usuarios que se registren al iniciar sesión.
   * Establecer el número de días que pasan hasta que se pide a los usuarios que vuelvan a confirmar su información de autenticación.

6. Opcional: la página **Notificaciones** proporciona a los administradores la opción de:
   * Notificar a los usuarios el restablecimiento de contraseña.
   * Notificar a todos los administradores cuando otros administradores restablezcan su contraseña.

Llegados a este punto, ya ha configurado SSPR para su inquilino de Azure AD. Los usuarios ahora pueden usar las instrucciones de los artículos [Registro para restablecer la contraseña de autoservicio](active-directory-passwords-reset-register.md) y [Restablecimiento o cambio de la contraseña](active-directory-passwords-update-your-own-password.md) para actualizar su contraseña sin intervención del administrador. Puede detenerse aquí si está solo en la nube. O bien, continuar con la sección siguiente para configurar la sincronización de contraseñas para un dominio de Active Directory local.

> [!TIP]
> Pruebe SSPR con un usuario en lugar de con un administrador, ya que Microsoft impone requisitos de autenticación estrictos para las cuentas de administrador de Azure. Para más información acerca de la directiva de contraseñas de administrador, consulte nuestro artículo sobre la [directiva de contraseñas](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-an-existing-identity-source"></a>Configuración de la sincronización con un origen de identidades existente

Para habilitar la sincronización de identidades local con Azure AD, debe instalar y configurar [Azure AD Connect](./connect/active-directory-aadconnect.md) en un servidor de su organización. Esta aplicación administra la sincronización de usuarios y grupos de su origen de identidades existente con su inquilino de Azure AD. Para obtener más información, consulte 

* [Actualización desde DirSync o Azure AD Sync a Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Introducción a Azure AD Connect mediante la configuración rápida](./connect/active-directory-aadconnect-get-started-express.md)
* [Configuración de la escritura diferida de contraseñas](active-directory-passwords-writeback.md#configure-password-writeback) para escribir contraseñas de Azure AD en su directorio local

### <a name="on-premises-policy-change"></a>Cambio de la directiva local

Si se sincronizan los usuarios de un dominio de Active Directory local y desea permitirles restablecer sus contraseñas inmediatamente, realice el siguiente cambio en la directiva de contraseñas local:

1. Vaya a **Configuración del equipo** > **Directivas** > **Configuración de Windows** > **Configuración de seguridad**  >  **Directivas de cuenta** > **Directiva de contraseñas**.

2. Establezca la **vigencia mínima de la contraseña** en **0 días**.

Esta configuración de seguridad determina el período de tiempo (en días) que se debe usar una contraseña antes de que el usuario pueda cambiarla. Si establece la vigencia mínima en **0 días**, los usuarios pueden usar SSPR si los equipos de soporte técnico cambian sus contraseñas.

![Directiva][Policy]

## <a name="disable-self-service-password-reset"></a>Deshabilitación del autoservicio de restablecimiento de contraseña

Deshabilitar el autoservicio de restablecimiento de contraseña es fácil. Abra el inquilino de Azure AD y vaya a **Restablecimiento de contraseña** > **Propiedades** y seleccione **Ninguno** en **Se habilitó el restablecimiento de contraseña del autoservicio**.

### <a name="learn-more"></a>Más información
En los siguientes artículos se proporciona información adicional sobre el restablecimiento de contraseña con Azure AD:

* [¿Cómo se realiza un lanzamiento correcto de SSPR?](active-directory-passwords-best-practices.md)
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md)
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md)
* [¿Tiene alguna pregunta acerca de las licencias?](active-directory-passwords-licensing.md)
* [¿Qué datos usa SSPR y cuáles se deben rellenar en lugar de los usuarios?](active-directory-passwords-data.md)
* [¿Qué métodos de autenticación están disponibles para los usuarios?](active-directory-passwords-how-it-works.md#authentication-methods)
* [¿Cuáles son las opciones de directiva con SSPR?](active-directory-passwords-policy.md)
* [¿Qué es la escritura diferida de contraseñas y por qué nos interesa?](active-directory-passwords-writeback.md)
* [¿Cómo se informa sobre la actividad de SSPR?](active-directory-passwords-reporting.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Creo que algo se ha roto. ¿Cómo se solucionan problemas en SSPR?](active-directory-passwords-troubleshoot.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)

## <a name="next-steps"></a>pasos siguientes

En este inicio rápido, ha aprendido a configurar el autoservicio de restablecimiento de contraseña para los usuarios. Para completar estos pasos, proceda a Azure Portal:

> [!div class="nextstepaction"]
> [Habilitar el autoservicio de restablecimiento de contraseña](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Métodos de autenticación de Azure AD disponibles y cantidad requerida"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "Directiva de grupo de contraseña local establecida en 0 días"

