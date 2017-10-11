---
title: "¿Necesita ayuda con el portal Mis aplicaciones en Azure Active Directory? | Microsoft Docs"
description: Obtenga instrucciones para realizar tareas comunes al trabajar con el panel de acceso.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 613c68d5c13793a3b696b6afbfc0e1a31595e201
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>¿Necesita ayuda con el portal Mis aplicaciones?

Es probable que haya llegado a esta página porque lamentablemente se encontró con un problema cuando usaba el portal My Apps. Aunque hay casos que requieren ponerse en contacto con el departamento de soporte técnico o con el administrador para resolver un problema, aquí se tratan algunos temas que podrían servirle de ayuda primero.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Tengo problemas para iniciar sesión en el portal Mis aplicaciones

Algunas cuestiones generales que se deben comprobar primero son:

- Asegúrese de que el usuario inicia sesión en la dirección URL correcta: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Intente agregar la dirección URL a los sitios de confianza de su explorador.

- Asegúrese de que el usuario no haya olvidado la contraseña o esta haya expirado. Consulte [aquí](active-directory-passwords-update-your-own-password.md) más detalles sobre cómo actualizar la contraseña.

- Compruebe si la información de contacto de autenticación está actualizada y no le impide el acceso. Consulte [este artículo](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) para más información sobre cómo configurar la información de autenticación.

- Intente borrar las cookies del explorador y volver a iniciar sesión.

Si se siguen produciendo problemas, póngase en contacto con el administrador para obtener ayuda.


## <a name="how-do-i-update-my-password"></a>¿Cómo puedo actualizar mi contraseña?

Si ha olvidado la contraseña, nunca ha recibido ninguna de su equipo de TI, está bloqueado fuera de su cuenta o si desea cambiarla, consulte [Ayuda, he olvidado mi contraseña de Azure AD](active-directory-passwords-update-your-own-password.md) para más información.

## <a name="how-do-i-register-for-password-reset"></a>¿Cómo puedo registrarme para restablecer la contraseña?

Como usuario final, puede restablecer su contraseña o desbloquear su cuenta sin necesidad de hablar con una persona que use el autoservicio de restablecimiento de contraseña (SSPR). Para poder usar esta funcionalidad, debe registrar los métodos de autenticación o confirmar los métodos de autenticación predefinidos que ha rellenado el administrador. Para más información, consulte [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-portal-browser-extension"></a>Tengo problemas para instalar la extensión de explorador del portal My Apps

Compruebe si se cumplen los requisitos del explorador:

- El portal requiere un explorador compatible con JavaScript y que tenga habilitado CSS. Si usa aplicaciones de inicio de sesión único basado en contraseña, también debe instalarse la extensión que lo acompaña. Esta extensión se descarga automáticamente cuando inicia una aplicación que está configurada para usar inicio de sesión único basado en contraseña.

- Los requisitos del explorador para la extensión son los siguientes:
    - Internet Explorer 8, 9, 10, 11 en Windows 7 o posterior
    - Edge en Windows 10 Anniversary Edition o posterior
    - Chrome en Windows 7 o posterior, y en Mac OS X o posterior
    - Firefox 26.0 o posterior en Windows XP SP2 o posterior, y en Mac OS X 10.6 o posterior

También puede descargar la extensión para Chrome y Edge desde los siguientes vínculos directos:

- [Extensión para Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Extensión para Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Después de la instalación, intente los pasos siguientes, si se están produciendo problemas:

- Compruebe en la configuración de la extensión del explorador que la extensión está habilitada.

- Reinicie el explorador e inicie sesión en el portal Mis aplicaciones.

- Borre las cookies del explorador e inicie sesión en el portal Mis aplicaciones.

## <a name="how-do-i-add-a-new-app"></a>¿Cómo puedo agregar una nueva aplicación?

1.  En la página **Aplicaciones**, haga clic en **Agregar aplicación**.

2.  Busque la aplicación que desee agregar y, a continuación, haga clic en **Agregar**.

**Comentarios:**

- Solo tiene acceso a esta opción si el administrador la ha habilitado para su cuenta.

- Si la aplicación requiere permiso, debe esperar la aprobación del administrador.


## <a name="how-do-i-manage-my-group-memberships"></a>¿Cómo puedo administrar la pertenencia a grupos?

1. Haga clic en el icono de aplicación Grupos. 
2. Para crear un grupo, en Grupos de mi propiedad, haga clic en Crear grupo y, a continuación, siga las instrucciones.
3. Para unirse a un grupo, en Grupos de mi propiedad, haga clic en Unirse al grupo y, a continuación, siga las instrucciones.

**Comentarios:**

- Solo tiene acceso a esta opción si el administrador la ha habilitado para su cuenta.

- En los grupos de los que es miembro, se le permite ver los detalles y abandonarlos.

- En los grupos de los que es propietario, se le permite ver los detalles, agregar o quitar miembros, y abandonarlos.


## <a name="next-steps"></a>Pasos siguientes

Para más información relacionada con la solución de problemas, consulte [Problemas con el sitio web del panel de acceso de la aplicación o con aplicaciones móviles](active-directory-application-access-panel-content-map.md).

