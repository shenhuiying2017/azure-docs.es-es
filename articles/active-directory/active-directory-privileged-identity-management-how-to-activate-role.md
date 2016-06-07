<properties
   pageTitle="Activación o desactivación de un rol | Microsoft Azure"
   description="Aprenda a activar roles para identidades con privilegios con la aplicación Privileged Identity Management de Azure."
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

# Activación y desactivación de roles en Privileged Identity Management de Azure AD

Privileged Identity Management de Azure Active Directory (AD) simplifica el modo en que las empresas administran las identidades con privilegios y el acceso a los recursos en Azure AD y en otros servicios en línea de Microsoft como Office 365 o Microsoft Intune.

Este artículo va dirigido a los administradores que necesitan activar su rol en Privileged Identity Management (PIM) de Azure AD. Le levará por los pasos para activar un rol cuando necesite los permisos y desactivar el rol cuando haya terminado.

Si se le ha asignado a un rol administrativo, puede activar ese rol cuando necesite realizar una tarea que lo exija. Por ejemplo, si solo necesita administrar Office 365 en algunas ocasiones, los administradores de roles con privilegios de su organización no le harán administrador permanente. Por el contrario, le harán candidato a los roles de Administrador global o Administrador de Exchange Online en Azure AD. Esto significa que puede solicitar una asignación de rol temporal cuando necesite esos privilegios y tendrá el control de administración de Office 365 durante un período de tiempo predeterminado.


## Incorporación de la aplicación Privileged Identity Management

Use la aplicación Privileged Identity Management de Azure AD en el [Portal de Azure](https://portal.azure.com/) para solicitar una "activación" del rol aunque vaya a trabajar en otro portal o mediante PowerShell. Si no tiene la aplicación en el Portal de Azure, siga estos pasos para comenzar:

1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Si su organización tiene más de un directorio, haga clic en su nombre de usuario en la esquina superior derecha del Portal de Azure y seleccione el directorio donde va a trabajar.
3. Haga clic en el icono **Nuevo** en el panel de navegación de la izquierda.
4. Seleccione **Seguridad e identidad** en el menú Crear.
5. Seleccione **Azure AD Privileged Identity Management**.
6. Active la casilla **Anclar al panel** y luego haga clic en el botón **Crear**. Se abrirá la aplicación Privileged Identity Management.

## Activación de un rol

Cuando necesite asumir un rol, puede solicitar la activación mediante el botón **Activar mi rol** de la aplicación Privileged Identity Management de Azure AD.


1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y seleccione el icono de Privileged Identity Management de Azure AD.
2. Seleccione **Activar mi rol**. Aparecerá una lista de los roles que se le asignaron.
3. Seleccione el rol que quiera activar.
4. Seleccione **Activar**. Aparecerá la hoja **Solicitar activación del role**.
5. En el caso de algunos roles, como Administrador global, es necesario Multi-Factor Authentication (MFA) para activar el rol. Si no ha ejecutado MFA al iniciar sesión, tendrá que hacerlo para poder activar el rol.
6. En el campo de texto, escriba el motivo de la solicitud de activación. El administrador de roles con privilegios podría también pedirle que suministre un vale de problema.
7. Seleccione **Aceptar**. El rol se activará y el cambio de rol se hará visible en Microsoft Online Services.

## Desactivación de un rol

Cuando se ha activado un rol, si alcanza su límite de tiempo, se desactiva automáticamente.

Si termina pronto, también puede desactivar un rol de forma manual en la aplicación Privileged Identity Management de Azure AD. Seleccione **Activar mi rol**, busque el rol que ya no necesita y seleccione **Desactivar**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Si le interesa aprender más sobre Privileged Identity Management de Azure AD, puede encontrar más información en los siguientes vínculos.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->