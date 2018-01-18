---
title: "Problemas al instalar la extensión de explorador del Panel de acceso | Microsoft Docs"
description: "Cómo corregir los errores comunes encontrados al instalar la extensión de explorador del Panel de acceso"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 26dc5d5ffce84206450123132c0633c2aa323e9f
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="problem-installing-the-application-access-panel-browser-extension"></a>Problemas al instalar la extensión de explorador del Panel de acceso

El Panel de acceso es un portal web que permite que un usuario con una cuenta profesional o educativa de Azure Active Directory (Azure AD) vea e inicie aplicaciones basadas en la nube a las que el administrador de Azure AD le ha concedido acceso. Un usuario que posee ediciones de Azure AD también puede usar las funcionalidades de administración de grupos de autoservicio y aplicaciones a través del Panel de acceso. El Panel de acceso es independiente de Azure Portal y no requiere que los usuarios tengan una suscripción de Azure.

Para usar el inicio de sesión único (SSO) basado en contraseña en el Panel de acceso, se debe instalar la extensión del Panel de acceso en el explorador del usuario. Esta extensión se descarga automáticamente cuando un usuario selecciona una aplicación que está configurada para SSO basado en contraseña.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Cumplimiento de los requisitos del explorador para el Panel de acceso

El Panel de acceso requiere un explorador compatible con JavaScript y que tenga habilitado CSS. Para usar el inicio de sesión único (SSO) basado en contraseña en el Panel de acceso, se debe instalar la extensión del Panel de acceso en el explorador del usuario. Esta extensión se descarga automáticamente cuando un usuario selecciona una aplicación que está configurada para SSO basado en contraseña.

Para el SSO basado en contraseña, los exploradores del usuario final pueden ser:

-   Edge en Windows 10 Anniversary Edition o posterior 

-   Chrome (en Windows 7 o posterior y en Mac OS X o posterior)

-   Firefox 26.0 o posterior (en Windows XP SP2 o posterior y en Mac OS X 10.6 o posterior)

-   Internet Explorer 8, 9, 10, 11 en Windows 7 o posterior (compatibilidad limitada)
## <a name="how-to-install-the-access-panel-browser-extension"></a>Cómo instalar la extensión de explorador del Panel de acceso

Para instalar la extensión de explorador del Panel de acceso, siga estos pasos:

1.  Abra el [Panel de acceso](https://myapps.microsoft.com) en uno de los exploradores admitidos e inicie sesión como **usuario** en su instancia de Azure AD.

2.  Haga clic en una **aplicación de SSO con contraseña** en el Panel de acceso.

3.  En el mensaje que le pregunta si desea instalar el software, seleccione **Instalar ahora**.

4.  Se le dirigirá al vínculo de descarga en función del explorador. **Agregue** la extensión al explorador.

5.  Si el explorador lo solicita, seleccione **Habilitar** o **Permitir** la extensión.

6.  Una vez instalada, **reinicie** la sesión del explorador.

7.  Inicie sesión en el Panel de acceso y vea si puede **iniciar** las aplicaciones de SSO con contraseña

También puede descargar la extensión para Chrome y Edge desde los siguientes vínculos directos:

-   [Extensión del Panel de acceso para Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensión del Panel de acceso para Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>¿Cómo uso Extensión de inicio de sesión seguro de mis aplicaciones?
Cambie la dirección URL predeterminada de Mis aplicaciones para la extensión

Si está utilizando una dirección URL de Mis aplicaciones diferente de https://myapps.microsoft.com, debe seguir estos pasos para configurar la dirección URL predeterminada:
1. Sin iniciar sesión en la extensión, **haga clic con el botón derecho** en el icono de la extensión.
2. Haga clic en **Select My Apps URL**  (Seleccionar dirección URL de Mis aplicaciones) en el menú.
3. **Seleccione** la dirección URL predeterminada.
4. Haga clic en el icono de la extensión.
5. Inicie sesión en la extensión seleccionando **Iniciar sesión para comenzar**.

Iniciar sesión directamente en una aplicación desde el explorador
1. Después de instalar la extensión, inicie sesión en ella seleccionando **Iniciar sesión para empezar**.
2. Vaya a la **dirección URL de inicio de sesión** de la aplicación en la que desea iniciar sesión; suele ser la dirección URL de la aplicación que muestra el formulario de inicio de sesión.
3. La extensión debe cambiar el estado y le permite saber que hay una contraseña disponible; haga clic en el **icono de extensión** para iniciar sesión.

Iniciar una aplicación desde la extensión
1. Después de instalar la extensión, inicie sesión en ella seleccionando **Iniciar sesión para empezar**.
2. Haga clic en el icono de extensión para abrir su **menú**.
3. **Busque** una aplicación disponible en el portal Mis aplicaciones.
4. Haga clic en la aplicación desde los **resultados de la búsqueda** para iniciarla.
5. Las tres últimas aplicaciones iniciadas también se mostrarán en la lista de accesos directos **Usados recientemente**.

> [!NOTE]
> Estas opciones solo están disponibles para Edge, Chrome y Firefox.

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Configuración de una directiva de grupo para Internet Explorer

Puede configurar una directiva de grupo que le permita instalar de forma remota la extensión del Panel de acceso para Internet Explorer en las máquinas de los usuarios.

Los requisitos previos son:

-   Configuró [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)y unió los equipos de los usuarios a su dominio.

-   Debe tener el permiso "Editar configuración" para editar el objeto de directiva de grupo (GPO). De forma predeterminada, los miembros de los siguientes grupos de seguridad poseen este permiso: Administradores de dominio, Administradores de organización y Propietarios del creador de directivas de grupo. [Más información](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Siga el tutorial [Implementación de la extensión de panel de acceso para Internet Explorer mediante la directiva de grupo](active-directory-saas-ie-group-policy.md) para obtener instrucciones paso a paso sobre cómo configurar la directiva de grupo e implementarla en los usuarios.

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Solución de problemas de la extensión Panel de acceso en Internet Explorer

Siga la guía de [Solución de problemas de la extensión del Panel de acceso para Internet Explorer](active-directory-saas-ie-troubleshooting.md) para acceder a la herramienta de diagnóstico, junto con instrucciones paso a paso sobre cómo configurar la extensión para IE.

> [!NOTE]
> Internet Explorer se encuentra en un periodo de compatibilidad limitada y ya no recibe las nuevas actualizaciones de software. Edge es el explorador recomendado.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Si después de seguir estos pasos el problema no se ha resuelto,

abra una incidencia de soporte técnico con la información siguiente si está disponible:

-   Id. de error de correlación

-   UPN (dirección de correo electrónico del usuario)

-   TenantID

-   Tipo de explorador

-   Zona horaria y hora/período de tiempo durante el que se produce el error

-   Seguimientos de Fiddler

## <a name="next-steps"></a>pasos siguientes
[¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
