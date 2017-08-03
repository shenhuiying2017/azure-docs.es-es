---
title: "Personalización: SSPR de Azure AD | Microsoft Docs"
description: "Opciones de personalización para el restablecimiento de contraseñas de autoservicio de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 5de3e36a97ead2f5ddfe57591e7c80a6df64c064
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>Personalice la funcionalidad de autoservicio de restablecimiento de contraseña de Azure AD

Los profesionales de TI que desean implementar el restablecimiento de contraseñas de autoservicio pueden personalizar la experiencia para adaptarla a sus usuarios.

## <a name="customize-the-contact-your-administrator-link"></a>Personalización del vínculo para ponerse en contacto con el administrador

Aunque SSPR no esté habilitado, los usuarios pueden incluir un vínculo "Póngase en contacto con el administrador" en el portal de restablecimiento de contraseñas.  Al hacer clic en el vínculo, se envía un correo electrónico a los administradores en el que se solicita asistencia para cambiar la contraseña del usuario. Este correo electrónico se envía a los siguientes destinatarios en el orden siguiente:

1. Si el rol de **administrador de contraseñas** está asignado, se notificará a todos aquellos que lo tengan
2. Si no hay asignado ningún administrador de contraseñas, entonces se notificará a quienes tengan el rol de **administrador de usuarios**
3. Si no se han asignado ninguno de los roles anteriores, se notificará a los **administradores globales**.

En todos los casos, se notificará a un máximo de 100 destinatarios en total.

### <a name="disable-contact-your-administrator-emails"></a>Deshabilitar los correos electrónicos de contacto con los administradores

Si la organización no desea que se notifique a los administradores de las solicitudes de restablecimiento de contraseñas, se puede habilitar la configuración siguiente:

* Habilite el autoservicio de restablecimiento de contraseñas para todos los usuarios finales. Esta opción está disponible en **Restablecimiento de contraseña > Propiedades**.
    * Si no desea que los usuarios restablezcan sus propias contraseñas, puede aumentar el ámbito del acceso a un grupo vacío **(no se recomienda esta opción)**.
* Personalice el vínculo del departamento de soporte técnico para proporcionar una dirección URL web o una dirección mailto: que los usuarios puedan usar para obtener asistencia. Esta opción se encuentra en **Restablecimiento de contraseña > Personalización > Dirección URL o correo electrónico del departamento de soporte técnico personalizados**.

## <a name="customize-adfs-sign-in-page-for-sspr"></a>Personalización de la página de inicio de sesión de ADFS para SSPR

Los administradores de ADFS pueden agregar un vínculo a su página de inicio de sesión con las directrices recogidas en el artículo [Adición de la descripción a la página de inicio de sesión](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Con el comando siguiente en el servidor ADFS, se agrega un vínculo a la página de inicio de sesión ADFS que permite a los usuarios escribir el flujo de trabajo de restablecimiento de contraseñas de autoservicio directamente.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?<A/></p>" ```

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>Personalización de la apariencia del panel de acceso e inicio de sesión

Cuando los usuarios acceden a la página de registro, puede personalizar el logotipo que aparece junto a la imagen de la página de inicio de sesión para adaptarla a la personalización de marca de empresa.

Estos gráficos se muestran en los siguientes casos:

* Después de que un usuario escribe su nombre de usuario.
* Cuando el usuario accede a la dirección URL personalizada.
    * Al pasar el parámetro "whr" a la página de restablecimiento de contraseñas, como "https://login.microsoftonline.com/?whr=contoso.com".
    * Al pasar el parámetro "username" a la página de restablecimiento de contraseñas, como "https://login.microsoftonline.com/?username=admin@contoso.com".

### <a name="graphics-details"></a>Detalles de gráficos

La configuración siguiente permite cambiar las características visuales de la página de inicio de sesión y se encuentra disponible en **Azure Active Directory**, **Personalización de marca de empresa**, **Editar personalización de marca de empresa**.

* La imagen de la página de inicio de sesión debe ser un archivo PNG o JPG de 1420x1200 píxeles, con un tamaño máximo de 500 KB. Se recomienda un tamaño aproximado de 200 KB para obtener resultados óptimos.
* El color de fondo de la página de inicio de sesión se usa en conexiones de alta latencia y debe tener el formato hexadecimal RGB.
* La imagen del banner debe ser un archivo PNG o JPG de 60x280 píxeles, con un tamaño máximo de 10 KB.
* El logotipo debe ser cuadrado (tema normal u oscuro) y debe ser un archivo PNG o JPG de 240x240 píxeles (redimensionable), con un tamaño máximo de 10 KB.

### <a name="sign-in-text-options"></a>Opciones de texto de inicio de sesión

La siguiente configuración permite agregar texto a la página de inicio de sesión pertinente para su organización. Esta configuración se puede encontrar en **Azure Active Directory**, **Personalización de marca de empresa**, **Editar personalización de marca de empresa**.

* La **sugerencia de nombre de usuario** sustituye al texto de ejemplo de someone@example.com con algo más adecuado para los usuarios; se recomienda dejar el valor predeterminado si se admiten usuarios internos y externos.
* El **texto de la página de inicio de sesión** admite una longitud máxima de 256 caracteres. Este texto aparece en cualquier lugar en que los usuarios se registran en línea, así como en la experiencia de Azure AD Join en Windows 10. Utilice este texto para los términos de uso, las instrucciones y las sugerencias para los usuarios. **Cualquier usuario puede ver la página de inicio de sesión, por lo que no debe proporcionar ninguna información confidencial aquí.**

### <a name="keep-me-signed-in-disabled"></a>Se deshabilitó la opción Mantener la sesión iniciada

La opción "Se deshabilitó la opción Mantener la sesión iniciada" permite a los usuarios permanecer con la sesión iniciada cuando se cierra y vuelve a abrir la ventana del explorador. Esta opción no afecta a la duración de las sesiones. Esta configuración se encuentra en **Azure Active Directory > Personalización de marca de empresa > Editar personalización de marca de empresa**.

Algunas características de SharePoint Online y Office 2010 dependen de que los usuarios puedan activar esta casilla. Si oculta esta opción, los usuarios pueden obtener solicitudes de inicio de sesión adicionales e inesperadas.

### <a name="directory-name"></a>Nombre de directorio

Puede cambiar el atributo de nombre en **Azure Active Directory > Propiedades** para mostrar un nombre de organización descriptivo en el portal y en las comunicaciones automatizadas. Esta opción es más visible en correos electrónicos automatizados de las formas siguientes:

* Nombre descriptivo del correo electrónico "Demostración de Microsoft en nombre de CONTOSO"
* Línea de asunto del correo electrónico "Código de verificación del correo electrónico de la cuenta de demostración CONTOSO"

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planeamiento e implementación de SSPR para los usuarios con las directrices que aquí se proporcionan
* [**Directiva**](active-directory-passwords-policy.md): información sobre las directivas de contraseñas de Azure AD y cómo establecerlas
* [**Escritura diferida de contraseñas** ](active-directory-passwords-writeback.md): cómo funciona la escritura diferida de contraseñas con su directorio local.
* [**Informes**](active-directory-passwords-reporting.md): descubra si, cuándo y dónde sus usuarios acceden a la funcionalidad SSPT.
* [**Profundización técnica**](active-directory-passwords-how-it-works.md): conozca lo que hay detrás para comprender cómo funciona.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? : respuestas a las preguntas que siempre se ha hecho.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR


