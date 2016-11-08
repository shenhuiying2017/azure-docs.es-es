---
title: Introducción a Privileged Identity Management de Azure AD | Microsoft Docs
description: Aprenda a administrar identidades con privilegios con la aplicación Privileged Identity Management de Azure Active Directory en el Portal de Azure.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2016
ms.author: kgremban

---
# Introducción a Privileged Identity Management de Azure AD
Con Privileged Identity Management de Azure Active Directory (AD), puede administrar, controlar y supervisar el acceso dentro de su organización. Esto incluye el acceso a los recursos de Azure AD y de otros servicios en línea de Microsoft, como Office 365 o Microsoft Intune.

En este artículo se explica cómo agregar la aplicación PIM (Privileged Identity Management) de Azure AD al panel del Portal de Azure.

## Incorporación de la aplicación Privileged Identity Management
Antes de usar Privileged Identity Management de Azure AD, debe agregar la aplicación al panel del Portal de Azure.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) como administrador global de su directorio.
2. Si su organización tiene más de un directorio, seleccione su nombre de usuario en la esquina superior derecha del Portal de Azure. Seleccione el directorio en el que va a usar PIM.
3. Seleccione **Más servicios** y utilice el cuadro de texto Filtro para buscar **Azure AD Privileged Identity Management**.
4. Active **Anclar al panel** y haga clic en **Crear**. Se abre la aplicación Privileged Identity Management.

Si usted es la primera persona que usa Azure AD Privileged Identity Management en su directorio, el [Asistente para seguridad](active-directory-privileged-identity-management-security-wizard.md) le guía en la experiencia de asignación inicial. Después, se convertirá automáticamente en el primer **administrador de seguridad** y **administrador de rol con privilegios** del directorio. Solo un administrador de roles con privilegios puede acceder a esta aplicación para administrar el acceso de otros administradores.

## Navegación a sus tareas
Después de configurar Azure AD Privileged Identity Management, verá la hoja de navegación cada vez que abra la aplicación. Utilice esta hoja para realizar las tareas de administración de identidades.

![Tareas de nivel superior para PIM (captura de pantalla)](./media/active-directory-privileged-identity-management-getting-started/pim_tasks.png)

* **Activar mis roles** le lleva a la lista de roles que tiene asignados. Aquí es donde activará los roles para los que es apto.
* **Administrar roles con privilegios** es el panel para que los administradores de roles con privilegios administren las asignaciones de roles, cambien la configuración de activación de roles e inicien revisiones del acceso, entre otras operaciones. Las opciones de este panel están deshabilitadas para todos aquellos que no sean administradores de roles con privilegios.
* **Revisar acceso con privilegios** le lleva a todas las revisiones de acceso pendientes que necesita completar, tanto si revisa su acceso como el de otro usuario.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
En la información general de [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md), se incluye más información sobre cómo administrar el acceso administrativo en una organización.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

<!----HONumber=AcomDC_0921_2016-->