---
title: "Inicio rápido: SSPR de Azure AD | Microsoft Docs"
description: "Implementación rápida del autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 8497c6c6d7cfc6c4457073783d20f48a722ea18e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-azure-ad-self-service-password-reset"></a>Inicio rápido: Autoservicio de restablecimiento de contraseña de Azure AD

> [!IMPORTANT]
> **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md).

## <a name="rapidly-deploy-self-service-password-reset"></a>Implementación rápida del autoservicio de restablecimiento de contraseña

El autoservicio de restablecimiento de contraseña (SSPR) ofrece un medio sencillo con el que los administradores de TI pueden permitir que los usuarios restablezcan o desbloqueen sus cuentas o contraseñas. El sistema incluye informes detallados para realizar un seguimiento de cuándo usan los usuarios el sistema, además de notificaciones para alertar de posibles abusos o usos indebidos.

En esta guía se da por hecho que ya dispone de un inquilino de Azure AD con licencia o una prueba operativa. Si necesita ayuda para configurar Azure AD, consulte el artículo [Introducción a Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

1. En su inquilino de Azure AD existente, seleccione **"Restablecimiento de contraseña"**.

2. En la pantalla **"Propiedades"**, en "Se habilitó el restablecimiento de contraseña del autoservicio", seleccione una de las siguientes opciones:
    * Nadie: nadie podrá usar la funcionalidad de SSPR.
    * Un grupo: solo los miembros de un grupo de Azure AD determinado que elija podrán usar la funcionalidad de SSPR.
    * Todo el mundo: todos los usuarios con cuentas en su inquilino de Azure AD podrán usar la funcionalidad de SSPR.

3. En la pantalla **"Métodos de autenticación"**, seleccione:
    * Número de métodos requeridos para el restablecimiento: se admite uno como mínimo o dos como máximo.
    * Métodos disponibles para los usuarios: se necesita al menos uno, pero es recomendable tener una opción adicional disponible.
        * **Correo electrónico** envía un correo electrónico con un código a la dirección de correo electrónico de autenticación configurada del usuario.
        * **Teléfono móvil** proporciona al usuario la opción de recibir una llamada o un mensaje de texto con un código en su número de teléfono móvil configurado.
        * **Teléfono de la oficina** realiza una llamada al número de teléfono de la oficina configurado del usuario para transmitirle un código.
        * **Preguntas de seguridad** requiere que se definan las siguientes opciones:
            * Número de preguntas necesarias para registrarse: es cantidad mínima de preguntas para registrarse correctamente, de forma que un usuario puede responder aún más para crear una serie de preguntas entre las que escoger. Esta opción puede definirse entre 3 y 5 y debe ser mayor o igual que el número de preguntas necesarias para el restablecimiento.
                * Se pueden agregar preguntas personalizadas haciendo clic en el botón "Personalizar" al seleccionar preguntas de seguridad.
            * Número de preguntas necesarias para el restablecimiento: puede establecerse entre 3 y 5 preguntas, que deben responderse correctamente para permitir restablecer o desbloquear la contraseña de los usuarios.

4. RECOMENDADO: **"Personalización"** permite cambiar el vínculo "Póngase en contacto con el administrador" para dirigir a otra página o dirección de correo electrónico que defina.

5. OPCIONAL: La pantalla **"Registro"** proporciona a los administradores las opciones siguientes:
    * Exigir a los usuarios que se registren al iniciar sesión
    * Número de días que pasan hasta que se pide a los usuarios que vuelvan a confirmar su información de autenticación

6. OPCIONAL: la pantalla **"Notificación"** proporciona a los administradores las opciones siguientes:
    * ¿Quiere notificar a los usuarios los restablecimientos de contraseña?
    * ¿Quiere notificar a todos los administradores cuando otros administradores restablezcan su contraseña?

**Ya ha configurado SSPR para su inquilino de Azure AD**. Puede detenerse aquí o continuar para configurar la sincronización de contraseñas con un dominio de Active Directory local.

> [!NOTE]
> Pruebe SSPR con un usuario que no sea administrador, ya que Microsoft impone estrictos requisitos de autenticación para las cuentas de tipo administrador de Azure. Para más información acerca de la directiva de contraseñas de administrador, consulte el siguiente [artículo sobre la directiva de contraseñas](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Configuración de la sincronización con un origen de identidades existente

Para habilitar la sincronización de identidades local con Azure AD, debe instalar y configurar [Azure AD Connect](./connect/active-directory-aadconnect.md) en un servidor de su organización. Esta aplicación administra la sincronización de usuarios y grupos de su origen de identidades existente con su inquilino de Azure AD.

* [Actualización desde DirSync o Azure AD Sync a Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Introducción a Azure AD Connect mediante la configuración rápida](./connect/active-directory-aadconnect-get-started-express.md)
* [Configuración de la escritura diferida de contraseñas](active-directory-passwords-writeback.md#configuring-password-writeback) para escribir contraseñas de Azure AD en su directorio local.

## <a name="disabling-self-service-password-reset"></a>Deshabilitación del autoservicio de restablecimiento de contraseña

Para deshabilitar el autoservicio de restablecimiento de contraseña, tan solo tiene que abrir el inquilino de Azure AD, ir a **Restablecimiento de contraseña, Propiedades** y seleccionar **Nadie** en **Se habilitó el restablecimiento de contraseña del autoservicio**.

### <a name="learn-more"></a>Más información
Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planee e implemente SSPR en sus usuarios mediante las instrucciones que se encuentran aquí.
* [**Personalización**](active-directory-passwords-customize.md): personalice el aspecto de la experiencia SSPR para su empresa.
* [**Directiva**](active-directory-passwords-policy.md): información sobre las directivas de contraseñas de Azure AD y cómo establecerlas
* [**Informes**](active-directory-passwords-reporting.md): informes para detectar si los usuarios acceden a la funcionalidad de SSPR que especifican el momento y el lugar del acceso
* [**Profundización técnica**](active-directory-passwords-how-it-works.md): conozca lo que hay detrás para comprender cómo funciona.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? : respuestas a las preguntas que siempre se ha hecho.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a configurar el autoservicio de restablecimiento de contraseña para los usuarios. Para continuar en Azure Portal para completar estos pasos siga el vínculo siguiente al portal.

> [!div class="nextstepaction"]
> [Habilitar el autoservicio de restablecimiento de contraseña](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

