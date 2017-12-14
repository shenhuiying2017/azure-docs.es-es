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
ms.date: 07/11/2017
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 66e002b092e02f92a33c6e498b176331ebdc190f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="problem-installing-the-application-access-panel-browser-extension"></a>Problemas al instalar la extensión de explorador del Panel de acceso

El Panel de acceso es un portal web que permite que un usuario con una cuenta profesional o educativa de Azure Active Directory (Azure AD) vea e inicie aplicaciones basadas en la nube a las que el administrador de Azure AD le ha concedido acceso. Un usuario que posee ediciones de Azure AD también puede usar las funcionalidades de administración de grupos de autoservicio y aplicaciones a través del Panel de acceso. El Panel de acceso es independiente de Azure Portal y no requiere que los usuarios tengan una suscripción de Azure.

Para usar el inicio de sesión único (SSO) basado en contraseña en el Panel de acceso, se debe instalar la extensión del Panel de acceso en el explorador del usuario. Esta extensión se descarga automáticamente cuando un usuario selecciona una aplicación que está configurada para SSO basado en contraseña.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Cumplimiento de los requisitos del explorador para el Panel de acceso

El Panel de acceso requiere un explorador compatible con JavaScript y que tenga habilitado CSS. Para usar el inicio de sesión único (SSO) basado en contraseña en el Panel de acceso, se debe instalar la extensión del Panel de acceso en el explorador del usuario. Esta extensión se descarga automáticamente cuando un usuario selecciona una aplicación que está configurada para SSO basado en contraseña.

Para el SSO basado en contraseña, los exploradores del usuario final pueden ser:

-   Internet Explorer 8, 9, 10, 11 (en Windows 7 o posterior)

-   Edge en Windows 10 Anniversary Edition o posterior 

-   Chrome (en Windows 7 o posterior y en Mac OS X o posterior)

-   Firefox 26.0 o posterior (en Windows XP SP2 o posterior y en Mac OS X 10.6 o posterior)

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

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Configuración de una directiva de grupo para Internet Explorer

Puede configurar una directiva de grupo que le permita instalar de forma remota la extensión del Panel de acceso para Internet Explorer en las máquinas de los usuarios.

Los requisitos previos son:

-   Configuró [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)y unió los equipos de los usuarios a su dominio.

-   Debe tener el permiso "Editar configuración" para editar el objeto de directiva de grupo (GPO). De forma predeterminada, los miembros de los siguientes grupos de seguridad poseen este permiso: Administradores de dominio, Administradores de organización y Propietarios del creador de directivas de grupo. [Más información](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Siga el tutorial [Implementación de la extensión de panel de acceso para Internet Explorer mediante la directiva de grupo](active-directory-saas-ie-group-policy.md) para obtener instrucciones paso a paso sobre cómo configurar la directiva de grupo e implementarla en los usuarios.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Solución de problemas del Panel de acceso en Internet Explorer

Siga la guía de [Solución de problemas de la extensión del Panel de acceso para Internet Explorer](active-directory-saas-ie-troubleshooting.md) para acceder a la herramienta de diagnóstico, junto con instrucciones paso a paso sobre cómo configurar la extensión para IE.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Si después de seguir estos pasos el problema no se ha resuelto,

abra una incidencia de soporte técnico con la información siguiente si está disponible:

-   Id. de error de correlación

-   UPN (dirección de correo electrónico del usuario)

-   TenantID

-   Tipo de explorador

-   Zona horaria y hora/período de tiempo durante el que se produce el error

-   Seguimientos de Fiddler

## <a name="next-steps"></a>Pasos siguientes
[¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
