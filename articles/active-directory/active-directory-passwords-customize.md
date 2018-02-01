---
title: "Personalización del autoservicio de restablecimiento de contraseña: Azure Active Directory"
description: "Opciones de personalización para el autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 526286c7f6b62d165af43487ca63fe9055623d0c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar la funcionalidad del autoservicio de restablecimiento de contraseña de Azure AD

Los profesionales de TI que quieran implementar el autoservicio de restablecimiento de contraseña (SSPR) en Azure Active Directory (Azure AD) pueden personalizar la experiencia para adaptarla a las necesidades de sus usuarios.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar el vínculo para ponerse en contacto con el administrador

Aunque SSPR no esté habilitado, los usuarios pueden usar un vínculo para ponerse en contacto con el administrador en el portal de restablecimiento de contraseñas. Si el usuario selecciona este vínculo, sucederá una de estas dos cosas:
   * Se envía un correo electrónico a los administradores en el que se solicita asistencia para cambiar la contraseña del usuario. 
   * Se proporciona a los usuarios a una dirección URL de su elección para que obtengan ayuda. 

Le recomendamos que establezca como forma de contacto algo como una dirección de correo electrónico o un sitio web que los usuarios ya utilicen para realizar preguntas de soporte técnico.

![Contacto][Contact]

Este correo electrónico se envía a los siguientes destinatarios en el orden siguiente:

1. Si el rol de **administrador de contraseñas** está asignado, se notificará a todos aquellos que lo tengan.
2. Si no hay asignado ningún administrador de contraseñas, entonces se notificará a quienes tengan el rol de **administrador de usuarios**.
3. Si no se ha asignado ninguno de los roles anteriores, se notificará a los **administradores globales**.

En todos los casos, se notificará a un máximo de 100 destinatarios en total.

Para obtener más información sobre los diferentes roles de administrador y cómo asignarlos, consulte [Asignación de roles de administrador en Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>Deshabilitar los correos electrónicos para contactar con los administradores

Si la organización no quiere que se notifique a los administradores de las solicitudes de restablecimiento de contraseñas, se puede habilitar la configuración siguiente:

* Habilite el autoservicio de restablecimiento de contraseñas para todos los usuarios finales. Esta opción está disponible en **Restablecimiento de contraseña** > **Propiedades**.
  
  Si no quiere que los usuarios restablezcan sus propias contraseñas, puede aumentar el ámbito del acceso a un grupo vacío. *(Esta opción no se recomienda).*
* Personalice el vínculo del departamento de soporte técnico para proporcionar una dirección URL web o una dirección mailto: que los usuarios puedan usar para obtener asistencia. Esta opción se encuentra en **Restablecimiento de contraseña** > **Personalización** > **Dirección URL o correo electrónico personalizados del departamento de soporte técnico**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalizar la página de inicio de sesión de AD FS para SSPR

Los administradores de Servicios de federación de Active Directory (AD FS) pueden agregar un vínculo a su página de inicio de sesión mediante las directrices recogidas en el artículo [Add sign-in page description](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) (Agregar la descripción de la página de inicio de sesión).

Para agregar un vínculo a la página de inicio de sesión de AD FS, use el siguiente comando en el servidor de AD FS. Los usuarios pueden usar esta página para especificar el flujo de trabajo de SSPR.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalizar la apariencia del panel de acceso y la página de inicio de sesión

Puede personalizar la página de inicio de sesión. Puede agregar un logotipo que aparezca junto a la imagen que se adapte a la marca de la empresa.

Los gráficos que elija se muestran en los siguientes casos:

* Después de que un usuario escriba su nombre de usuario.
* Si el usuario accede a la dirección URL personalizada:
    * Al pasar el parámetro *whr* a la página de restablecimiento de contraseñas, como "https://login.microsoftonline.com/?whr=contoso.com".
    * Al pasar el parámetro *username* a la página de restablecimiento de contraseñas, como "https://login.microsoftonline.com/?username=admin@contoso.com".

Obtenga información detallada sobre cómo configurar la personalización de marca de empresa en el artículo [Incorporación de personalización de marca de empresa a su página de inicio de sesión de Azure AD](customize-branding.md).

### <a name="directory-name"></a>Nombre de directorio

Puede cambiar el atributo del nombre de directorio en **Azure Active Directory** > **Propiedades**. Asimismo, puede mostrar un nombre descriptivo de la organización para que se vea en el portal y en las comunicaciones automatizadas. Esta opción es la más visible en correos electrónicos automatizados, ya que usa los elementos que se indican a continuación:

* Nombre descriptivo del correo electrónico "Demostración de Microsoft en nombre de CONTOSO".
* Línea de asunto del correo electrónico "Código de verificación del correo electrónico de la cuenta de demostración CONTOSO".

## <a name="next-steps"></a>pasos siguientes

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

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "Póngase en contacto con el administrador para obtener ayuda al restablecer el ejemplo de correo electrónico de contraseña"
