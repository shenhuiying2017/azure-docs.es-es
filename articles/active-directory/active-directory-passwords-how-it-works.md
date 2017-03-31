---
title: "Funcionamiento de la administración de contraseñas de Azure AD | Microsoft Docs"
description: "Obtenga información sobre los distintos componentes de la administración de contraseñas de Azure AD, entre ellos, dónde los usuarios registran, restablecen y cambian sus contraseñas y dónde los administradores configuran y habilitan la administración de contraseñas de Active Directory locales, además de generar informes sobre ellas."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 24c94f80afdb3d3330a835fae2f77ecb213a9ae5
ms.lasthandoff: 03/28/2017


---
# <a name="how-password-management-works-in-azure-active-directory"></a>Funcionamiento de la administración de contraseñas en Azure Active Directory
> [!IMPORTANT]
> **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md#reset-your-password).

La administración de contraseñas en Azure Active Directory (Azure AD) consta de los siguientes componentes lógicos:

* **Portal de configuración de administración de contraseñas**: puede controlar distintas facetas de cómo se administran las contraseñas en sus inquilinos. Para ello, vaya a la pestaña **Configurar** de su directorio en [Azure Portal](https://manage.windowsazure.com).
* **Portal de registro de usuario**: los usuarios se pueden registrar automáticamente para restablecer la contraseña a través de este portal web.
* **Portal de restablecimiento de contraseñas de usuario**: los usuarios pueden restablecer sus propias contraseñas mediante varios desafíos de acuerdo con la directiva de restablecimiento de contraseña controlada por el administrador.
* **Portal de cambio de contraseñas de usuario**: los usuarios pueden cambiar sus propias contraseñas en cualquier momento si escriben su contraseña anterior y seleccionan una contraseña nueva en este portal web.
* **Informes de administración de contraseñas**: puede ver y analizar la actividad de registro y restablecimiento de contraseña en su inquilino. Para ello, vaya a la sección **Informes de actividad** de la pestaña **Informes** de su directorio en [Azure Portal](https://manage.windowsazure.com).
* **Componente de escritura diferida de contraseñas de Azure AD Connect**: tiene la opción de habilitar la característica de escritura diferida cuando instale Azure AD Connect para permitir la administración de contraseñas de usuarios federados o con contraseña sincronizada desde la nube.

## <a name="password-management-configuration-portal"></a>Portal de configuración de administración de contraseñas
Puede configurar directivas de administración de contraseñas para un directorio específico en [Azure Portal](https://manage.windowsazure.com), en la sección **Directiva de restablecimiento de contraseña de usuario** de la pestaña **Configurar** del directorio. Desde esta página de configuración, puede controlar muchos aspectos de la administración de contraseñas en su organización, entre ellos:

* Habilitar y deshabilitar el restablecimiento de contraseña para todos los usuarios de un directorio.
* Establecer la cantidad de desafíos (uno o dos) que debe completar un usuario para restablecer la contraseña.
* Establecer los tipos específicos de desafíos que desea habilitar para los usuarios de su organización a partir de estas opciones:
  * Teléfono móvil (código de comprobación mediante texto o llamada de voz)
  * Teléfono de la oficina (llamada de voz)
  * Correo electrónico alternativo (código de comprobación mediante correo electrónico)
  * Preguntas de seguridad (autenticación basada en conocimiento)
* Establecer la cantidad de preguntas que un usuario debe registrar para usar el método de autenticación de preguntas de seguridad (solo visible si están habilitadas las preguntas de seguridad).
* Establecer la cantidad de preguntas que un usuario debe proporcionar durante el restablecimiento para usar el método de autenticación de preguntas de seguridad (solo visible si están habilitadas las preguntas de seguridad).
* Usar preguntas de seguridad predefinidas y adaptadas que un usuario pueda elegir usar al registrarse para el restablecimiento de contraseña (solo es visible si están habilitadas las preguntas de seguridad).
* Definir preguntas de seguridad personalizadas que un usuario pueda elegir usar al registrarse para el restablecimiento de contraseña (solo es visible si están habilitadas las preguntas de seguridad).
* Pedir a los usuarios que se registren para el restablecimiento de contraseña cuando van al [Panel de acceso](http://myapps.microsoft.com) de la aplicación.
* Pedir a los usuarios que vuelvan a confirmar los datos registrados anteriormente después de un número de días configurable (solo visible si está habilitado el registro obligatorio).
* Proporcionar una dirección URL o un correo electrónico del departamento de soporte técnico personalizados que se mostrarán a los usuarios en caso de que experimenten algún problema al restablecer sus contraseñas.
* Habilitar o deshabilitar la función de escritura diferida de contraseñas (cuando se ha implementado la escritura diferida de contraseñas con Azure AD Connect).
* Ver el estado del agente de escritura diferida de contraseñas (cuando se ha implementado la escritura diferida de contraseñas con Azure AD Connect).
* Habilitar las notificaciones por correo electrónico a los usuarios cuando se ha restablecido su contraseña (se encuentra en la sección **Notificaciones** de [Azure Portal](https://manage.windowsazure.com)).
* Habilitar las notificaciones por correo electrónico a los administradores cuando otros administradores han restablecido sus propias contraseñas (se encuentra en la sección **Notificaciones** de [Azure Portal](https://manage.windowsazure.com)).
* Personalizar el portal y los correos electrónicos de restablecimiento de contraseña de usuario con el logotipo y el nombre de su organización mediante la característica de personalización de la marca del inquilino (se encuentra en la sección **Propiedades del directorio** de [Azure Portal](https://manage.windowsazure.com)).

Para más información sobre cómo configurar la administración de contraseñas en la organización, consulte [Introducción a la administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md).

## <a name="user-registration-portal"></a>Portal de registro de usuario
Para que los usuarios puedan usar el restablecimiento de contraseña, debe actualizar las cuentas de usuario en la nube con los datos de autenticación correctos a fin de garantizar que puedan superar la cantidad de desafíos de restablecimiento de contraseña adecuados que defina. También puede definir esta información de autenticación en nombre del usuario en los portales web de Azure y Office, con DirSync/Azure AD Connect o Windows PowerShell.

Sin embargo, si prefiere que sean los usuarios los que registren sus propios datos, también proporcionamos una página web a la que los usuarios pueden ir para agregar esta información. Esta página permite que los usuarios especifiquen la información de autenticación de acuerdo con las directivas de restablecimiento de contraseña que se hayan habilitado en su organización. Después de comprobar estos datos, se almacenan en la cuenta de usuario en la nube para poder recuperar la cuenta más adelante.

El portal de registro tiene el siguiente aspecto:

  ![][001]

Para más información, consulte [Introducción a la administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md) y [Procedimientos recomendados: administración de contraseñas de Azure AD](active-directory-passwords-best-practices.md).

## <a name="user-password-reset-portal"></a>Portal de restablecimiento de contraseña de usuario
Una vez que habilite el restablecimiento de contraseña de autoservicio, configure la directiva de restablecimiento de contraseña de autoservicio de la organización y se asegure de que los usuarios tienen los datos de contacto apropiados en el directorio, los usuarios de la organización podrán restablecer sus propias contraseñas de manera automática desde cualquier página web en la que se use una cuenta profesional o educativa para iniciar sesión (como [portal.microsoftonline.com](https://portal.microsoftonline.com)). En las páginas de este tipo, los usuarios verán el vínculo **¿No puede acceder a su cuenta?**

  ![][002]

Si hace clic en este vínculo, se abrirá el portal de restablecimiento de contraseña de autoservicio.

  ![][003]

Para más información sobre cómo los usuarios pueden restablecer sus propias contraseñas, consulte [Introducción a la administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md).

## <a name="user-password-change-portal"></a>Portal de cambio de contraseñas de usuario
Si los usuarios desean cambiar sus contraseñas, pueden hacerlo en el portal de cambio de contraseñas en cualquier momento. Los usuarios pueden tener acceso al portal de cambio de contraseñas a través de la página de perfil del Panel de acceso o mediante un clic en el vínculo **Cambiar contraseña** en las aplicaciones de Office 365. En el caso de que las contraseñas expiren, también se pedirá a los usuarios que las cambien automáticamente al iniciar sesión.

  ![][004]

En ambos casos, si se habilitó la escritura diferida de contraseñas y el usuario es federado o tiene la contraseña sincronizada, las contraseñas cambiadas se escriben en diferido en su instancia local de Active Directory.

El portal de cambio de contraseña tiene el siguiente aspecto:

  ![][005]

Para más información sobre cómo los usuarios pueden cambiar sus propias contraseñas de Active Directory local, consulte [Introducción a la administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md).

## <a name="password-management-reports"></a>Informes de administración de contraseñas
Cuando va a la pestaña **Informes** y mira la sección **Registros de actividad**, puede ver dos informes de administración de contraseñas: **Actividad de restablecimiento de contraseña** y **Actividad de registro de restablecimiento de contraseña**. Puede usar estos dos informes para obtener una vista de los usuarios que se registran y usan el restablecimiento de contraseña en la organización.

Este es el aspecto de estos informes en [Azure Portal](https://manage.windowsazure.com):

  ![][006]

Para más información, consulte [Visión operativa con los informes de administración de contraseñas de Azure AD](active-directory-passwords-get-insights.md).

## <a name="password-writeback-component-of-azure-ad-connect"></a>Componente de escritura diferida de contraseñas de Azure AD Connect
Si las contraseñas de los usuarios de su organización proceden de su entorno local (mediante federación o sincronización de contraseña), puede instalar la versión más reciente de Azure AD Connect para habilitar la actualización de esas contraseñas directamente desde la nube. Esto significa que, cuando los usuarios olvidan su contraseña de Azure AD o desean cambiarla, pueden hacerlo directamente desde la web. Aquí es donde puede encontrar la opción de escritura diferida de contraseñas en el asistente para la instalación de AD Connect:

  ![][007]

Para más información sobre Azure AD Connect, consulte [Introducción a Azure AD Connect](active-directory-aadconnect.md). Para más información sobre la escritura diferida de contraseñas, consulte [Introducción a la administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md).


## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte las siguientes páginas sobre restablecimiento de contraseña en Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, sepa cómo [cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir que los usuarios restablezcan y cambien sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md): obtenga información sobre cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización.
* [**Procedimientos recomendados**](active-directory-passwords-best-practices.md): obtenga información sobre cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización.
* [**Información detallada**](active-directory-passwords-get-insights.md): obtenga información acerca de nuestras funcionalidades integradas de creación de informes.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.

[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

