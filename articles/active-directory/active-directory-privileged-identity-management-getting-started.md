<properties
   pageTitle="Introducción a Privileged Identity Management de Azure AD | Microsoft Azure"
   description="Aprenda a administrar identidades con privilegios con la aplicación Privileged Identity Management de Azure Active Directory en el Portal de Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/28/2016"
   ms.author="kgremban"/>

# Introducción a Privileged Identity Management de Azure AD


Privileged Identity Management de Azure Active Directory (AD) le permite administrar, controlar y supervisar las identidades con privilegios y el acceso a los recursos en Azure AD y en otros servicios en línea de Microsoft, como Office 365 o Microsoft Intune.

En este artículo se explica cómo agregar la aplicación PIM (Privileged Identity Management) de Azure AD al panel del Portal de Azure.

## Incorporación de la aplicación Privileged Identity Management

Antes de usar Privileged Identity Management de Azure AD, debe agregar la aplicación al panel del Portal de Azure.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) como administrador global de su directorio.
2. Si su organización tiene más de un directorio, haga clic en su nombre de usuario en la esquina superior derecha del Portal de Azure y seleccione el directorio donde usará PIM.
3. Haga clic en el icono **Nuevo** en el panel de navegación de la izquierda.
4. Seleccione **Seguridad e identidad**.
5. Seleccione **Azure AD Privileged Identity Management**.
6. Active la casilla **Anclar al panel** y luego haga clic en el botón **Crear**. Se abrirá la aplicación Privileged Identity Management.


Si usted es la primera persona que usa Privileged Identity Management de Azure AD en su directorio, el [Asistente para seguridad](active-directory-privileged-identity-management-security-wizard.md) le guiará en la experiencia de asignación inicial. Después de eso, se convertirá automáticamente en el primer **administrador de seguridad** del directorio. Solo un administrador de seguridad puede tener acceso a esta aplicación para administrar el acceso de otros administradores.

Por lo demás, si otro administrador de seguridad le ha asignado a uno o más roles, tendrá la opción de elegir qué rol activar. Si se encuentra en un rol de administrador de seguridad, también tendrá la opción de **administrar identidades**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

En [Información general de Privileged Identity Management de Azure AD](active-directory-privileged-identity-management-configure.md) se incluyen más detalles sobre cómo gestionar el acceso administrativo en su organización.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0504_2016-->