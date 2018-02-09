---
title: Ayuda para acceder al portal Mis aplicaciones de Azure Active Directory y usarlo | Microsoft Docs
description: "Obtenga ayuda para iniciar sesión en el panel de acceso y realizar tareas comunes."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 285ee0c4e7f963c372c4f95face73864ba2a4f68
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>Solución de problemas de acceso y uso en el portal Mis aplicaciones

Si tiene problemas de inicio de sesión o de uso en el portal Mis aplicaciones, pruebe estas sugerencias de solución de problemas antes de pedir ayuda al soporte técnico o al administrador.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Tengo problemas para iniciar sesión en el portal Mis aplicaciones

Pruebe estas sugerencias generales:

- Primero, asegúrese de que la dirección URL es la correcta: [https://myapps.microsoft.com](https://myapps.microsoft.com).
- Intente agregar la dirección URL a los sitios de confianza de su explorador.
- Asegúrese de que la contraseña es correcta y de que no ha expirado. Para más información, consulte [Restablecimiento de la contraseña profesional o educativa](active-directory-passwords-update-your-own-password.md).
- Compruebe si la información de contacto de autenticación está actualizada y no impide el acceso. Para más información, consulte [¿Qué relevancia tiene Azure Multi-Factor Authentication para mí?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user)
- Intente borrar las cookies del explorador y volver a iniciar sesión.

Si se siguen produciendo problemas al iniciar sesión, póngase en contacto con el administrador.


## <a name="i-seem-to-be-having-password-issues"></a>Parece que tengo problemas con la contraseña

Si ha olvidado la contraseña, no ha recibido una del equipo de TI, desea cambiarla o no puede acceder a su cuenta, consulte el artículo de [ayuda con la contraseña de Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Necesito registrarme para restablecer la contraseña

Gracias al autoservicio de restablecimiento de contraseña (SSPR), puede restablecer la contraseña o desbloquear la cuenta sin necesidad de hablar con una persona. Para usar esta funcionalidad, debe registrar los métodos de autenticación o confirmar los métodos predefinidos que requiera el administrador. Para más información, consulte [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Tengo problemas para instalar la Extensión de inicio de sesión seguro de mis aplicaciones

El portal Mis aplicaciones requiere un explorador compatible con JavaScript y con CSS habilitado. Si usa aplicaciones de inicio de sesión único basado en contraseña, también debe instalarse la extensión que lo acompaña. Esta extensión se descarga automáticamente al iniciar cualquier aplicación configurada para el inicio de sesión único basado en contraseña.

Asegúrese de que cumple los siguientes requisitos de explorador:
- **Edge**: en Windows 10 Anniversary Edition, o cualquier versión posterior.
- **Chrome**: en Windows 7 o posterior y en Mac OS X o posterior.
- **Firefox 26.0 o posterior**: en Windows XP SP2 o posterior y en Mac OS X 10.6 o posterior.
- **Internet Explorer 8, 9, 10, 11**: en Windows 7, o cualquier versión posterior (compatibilidad limitada).

También puede descargar la extensión para Chrome y Edge directamente desde los siguientes sitios:

- [Extensión para Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Extensión para Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Si ha instalado la extensión y sigue teniendo problemas, pruebe lo siguiente:

- Compruebe la configuración de la extensión del explorador para asegurarse de que está habilitada.
- Reinicie el explorador e inicie sesión en el portal Mis aplicaciones.
- Borre las cookies del explorador e inicie sesión en el portal Mis aplicaciones.
- Para acceder a una herramienta de diagnóstico y a instrucciones detalladas sobre la configuración de la extensión para Internet Explorer, consulte [Solución de problemas de la extensión del Panel de acceso para Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Uso de la extensión de inicio de sesión seguro de Mis aplicaciones
* Si va utilizar una dirección URL de Mis aplicaciones que no sea `https://myapps.microsoft.com`, configure la dirección URL predeterminada siguiendo estos pasos:
   1. *Sin* iniciar sesión en la extensión, haga clic con el botón derecho en el icono de la extensión.
   2. En el menú, seleccione **My Apps URL** (URL de Mis aplicaciones).
   3. Seleccione la dirección URL predeterminada.
   4. Seleccione el icono de la extensión.
   5. Para iniciar sesión en la extensión, seleccione **Iniciar sesión para comenzar**.

* Para iniciar sesión directamente en una aplicación desde el explorador, haga lo siguiente:
   1. Después de instalar la extensión, seleccione **Iniciar sesión para comenzar** para iniciar sesión.
   2. Inicie sesión en la aplicación con la dirección URL de inicio de sesión.  
       La dirección URL de inicio de sesión suele ser la dirección URL de la aplicación que muestra el formulario de inicio de sesión.
      La extensión debe cambiar el estado e indicar que hay una contraseña disponible.
   3. Para iniciar sesión, seleccione el icono de la extensión.

* Para iniciar una aplicación desde la extensión, haga lo siguiente:
   1. Después de instalar la extensión, seleccione **Iniciar sesión para comenzar** para iniciar sesión.
   2. Seleccione el icono de extensión para abrir su menú.
   3. Busque una aplicación que esté disponible en el portal Mis aplicaciones.
   4. En la lista de resultados de la búsqueda, seleccione la aplicación.  
       Las tres últimas aplicaciones que ha utilizado se muestran en la lista de accesos directos **Usados recientemente**.

> [!NOTE]
> Estas opciones están disponibles solo para Edge, Chrome y Firefox.

## <a name="how-do-i-add-a-new-app"></a>¿Cómo puedo agregar una nueva aplicación?

1.  En la página **Aplicaciones**, seleccione en **Agregar aplicación**.
2.  Busque la aplicación que desea agregar y seleccione **Agregar**.

   > [!NOTE]
   > * Solo puede acceder a esta opción si el administrador la ha habilitado para su cuenta.
   > * Si la aplicación requiere permiso, debe esperar la aprobación del administrador.
   > 

## <a name="how-do-i-manage-my-group-memberships"></a>¿Cómo puedo administrar la pertenencia a grupos?

Seleccione el icono **Grupos** y realice una de las siguientes acciones: 
* Para crear un grupo, en **Grupos de mi propiedad**, seleccione **Crear grupo** y siga las instrucciones.
* Para unirse a un grupo, en **Grupos en los que estoy**, seleccione **Unirse al grupo** y siga las instrucciones.

   > [!NOTE]
   > * Solo puede acceder a esta opción si el administrador la ha habilitado para su cuenta.
   > * Si es miembro de un grupo, podrá ver los detalles y abandonar el grupo.
   > * Si es propietario de un grupo, podrá ver los detalles, agregar o quitar miembros y abandonar el grupo.
   >


## <a name="next-steps"></a>pasos siguientes

Para más información sobre la solución de problemas, consulte [Problemas con el sitio web del panel de acceso de la aplicación o con aplicaciones móviles](active-directory-application-access-panel-content-map.md).

