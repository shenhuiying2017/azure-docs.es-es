<properties
   pageTitle="Incorporación o eliminación de un rol de usuario | Microsoft Azure"
   description="Aprenda a agregar roles a identidades con privilegios con la aplicación Privileged Identity Management de Azure Active Directory."
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Privileged Identity Management de Azure AD: Incorporación o eliminación de un rol de usuario

Con Azure Active Directory (AD), un administrador global (o un administrador de empresa) puede actualizar a los usuarios que están asignados **permanentemente** a roles en Azure AD. Para ello, se usan cmdlets de PowerShell, como `Add-MsolRoleMember` y `Remove-MsolRoleMember`. O bien, se puede utilizar el Portal de Azure clásico, como se describe en [Asignación de roles de administrador en Azure Active Directory (Azure AD)](active-directory-assign-admin-roles.md).

La aplicación Privileged Identity Management de Azure AD permite también a los administradores de roles con privilegios realizar asignaciones de roles permanentes. Asimismo, les permite agregar o quitar candidatos de la asignación **temporal** a estos roles. Un candidato puede activar el rol cuando lo necesita y, cuando termina, sus permisos caducan.

## Administración de roles con PIM en el Portal de Azure

En su organización, puede asignar a los usuarios a roles administrativos diferentes en Azure AD. Estas asignaciones de roles controlan qué tareas, como agregar y quitar usuarios o cambiar la configuración de los servicios, pueden realizar los usuarios en Azure AD, Office 365 y otros servicios y aplicaciones de Microsoft. Encontrará más detalles sobre los roles disponibles en [Privileged Identity Management de Azure AD: Roles](active-directory-privileged-identity-management-roles.md).

Para agregar a un usuario a un rol o quitar un usuario de un rol con Privileged Identity Management, abra el panel de PIM y haga clic en el botón **Usuarios en roles de administrador**, o seleccione un rol específico (como Administrador global) de la tabla de roles.

> [AZURE.NOTE] Si aún no ha habilitado PIM en el Portal de Azure, vaya a [Introducción a Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) para más información.

Si desea dar a otro usuario acceso a PIM, los roles que debe tener el usuario para PIM se describen con más detalle en [How to give access to manage Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-give-access-to-pim.md) (Cómo proporcionar acceso para administrar Privileged Identity Management de Azure AD).

## Adición de un usuario a un rol
Cuando haya llegado a la hoja de roles, bien mediante la selección de un rol en el panel de PIM de Azure AD o con un clic en el botón **Usuarios en roles de administrador**,

1. Haga clic en **Agregar**.
  - Si para llegar aquí hizo clic en un rol de usuario en la hoja de roles, el rol ya estará seleccionado.  
  - Si no, haga clic en **Seleccionar un rol** y elija un rol de la lista de roles. Por ejemplo, **Administrador de contraseñas**.
2. Busque al usuario en la hoja **Seleccionar usuarios**. Si el usuario está en el directorio, su cuenta aparecerá mientras escribe.
3. Seleccione el usuario en la lista de resultados de búsqueda y haga clic en **Listo**.
4. Haga clic en **Aceptar** para guardar la selección. El usuario que seleccionó aparecerá en la lista y el rol será temporal de forma predeterminada.

  >[AZURE.NOTE] Si quiere que el rol sea permanente, haga clic en el usuario en la lista. La información del usuario aparecerá en una nueva hoja. Seleccione **convertir en permanente** en el menú de información de usuario. Deberá hacer esto si el usuario no se puede registrar en Azure Multi-Factor Authentication (MFA) o si usa una cuenta de Microsoft. Los administradores temporales deben registrarse en MFA durante la activación.

Ahora que se ha asignado al usuario a un rol temporal, hágale saber que puede activarlo de acuerdo con las instrucciones que se describen en [Privileged Identity Management de Azure AD: Activación y desactivación de un rol](active-directory-privileged-identity-management-how-to-activate-role.md).

## Eliminación de un usuario de un rol

Puede quitar a los usuarios de las asignaciones de rol temporal, pero asegúrese de que siempre haya al menos un usuario que sea un administrador global permanente.

Siga estos pasos para quitar a un usuario específico de un rol:

1. Vaya al rol en la lista de roles, para ello, seleccione un rol en el panel de PIM de Azure AD o haga clic en el botón **Usuarios en roles de administrador**.
2. Haga clic en el usuario en la lista de usuarios.
3. Haga clic en **Quitar**. Un mensaje le pedirá que confirme la operación.
4. Haga clic en **Sí** para quitar el rol del usuario.

Si no está seguro de si los usuarios necesitan aún sus asignaciones de roles, puede [iniciar una revisión de seguridad del rol](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->