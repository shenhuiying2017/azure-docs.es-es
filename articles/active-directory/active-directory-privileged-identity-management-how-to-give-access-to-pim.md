<properties
   pageTitle="Concesión de acceso a PIM | Microsoft Azure"
   description="Aprenda a agregar roles a los usuarios con la extensión Privileged Identity Management de Azure Active Directory para que así puedan administrar PIM."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Concesión de acceso para administrar Privileged Identity Management de Azure AD

El primer usuario que habilite Privileged Identity Management (PIM) de Azure AD en una organización debe ser un administrador global. Sin embargo, otros administradores globales no tendrán acceso de forma predeterminada a PIM, así que no podrán administrar asignaciones temporales. Para dar acceso a PIM, el primer usuario puede asignar a los demás al rol Administrador de seguridad. Esta asignación se debe realizar desde dentro de PIM y no se puede cambiar mediante PowerShell u otros portales.

> [AZURE.NOTE] La administración de PIM de Azure AD requiere Azure MFA. Dado que las cuentas de Microsoft no se pueden registrar en Azure MFA, un usuario que inicia sesión con una cuenta de Microsoft no puede tener acceso a PIM de Azure AD.

Asegúrese de que siempre haya al menos dos usuarios en un rol de administrador de seguridad, por si se diera el caso de que a un usuario se le impida el acceso o su cuenta se haya eliminado.

## Concesión de acceso a otro usuario para administrar PIM

1. Vaya al [Portal de Azure](https://portal.azure.com/) y seleccione la aplicación **Azure AD Privileged Identity Management** en el panel.
2. Seleccione el rol **Administrador de seguridad**. Se mostrará la lista de usuarios que tienen actualmente ese rol.
3. Haga clic en **Agregar**, aparecerá una hoja del asistente. El rol ya estará seleccionado.
4. Haga clic en **Seleccionar usuarios**, se abrirá la hoja de lista de usuarios.
5. Escriba el nombre del usuario en el campo de búsqueda. Si el usuario está en el directorio, su cuenta aparecerá mientras escribe.
6. Seleccione el usuario en los resultados de búsqueda y haga clic en **Listo**.
7. Haga clic en **Aceptar** para guardar la selección. El usuario que seleccionó aparecerá en la lista y el rol será temporal de forma predeterminada.

  - Si quiere que el rol sea permanente, haga clic en el usuario en la lista. La información del usuario aparecerá en una nueva hoja. Seleccione **convertir en permanente** en el menú de información de usuario.

8. Envíe al usuario un vínculo a la documentación de Azure en [Introducción a Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).


## Eliminación de los derechos de acceso de otro usuario para administrar PIM

Antes de eliminar a alguien del rol Administrador de seguridad, asegúrese siempre de que haya dos usuarios asignados a él.

1. En el panel de PIM, haga clic en el rol **Administrador de seguridad**. Se mostrará la lista de usuarios que tienen actualmente ese rol.
2. Haga clic en el usuario en la lista de usuarios.
3. Haga clic en **Quitar**. Aparecerá un mensaje de confirmación.
4. Haga clic en **Sí** para quitar el rol del usuario.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->