<properties
	pageTitle="Servicios de dominio de Azure AD: habilitación de la sincronización de contraseñas | Microsoft Azure"
	description="Introducción a los Servicios de dominio de Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(versión preliminar)*: habilitación de la sincronización de contraseñas con los Servicios de dominio de Azure AD

## Tarea 5: Habilitación de la sincronización de contraseñas con los Servicios de dominio de Azure AD para un directorio de Azure AD solo de nube
Después de habilitar los Servicios de dominio de Azure AD para su inquilino de Azure AD, la siguiente tarea consiste en habilitar las credenciales para sincronizarse con los Servicios de dominio de Azure AD. Así los usuarios pueden iniciar sesión en el dominio administrado con sus credenciales corporativas.

Los pasos que deben seguirse son distintos en función de si su organización tiene un directorio de Azure AD solo de nube o está configurado para sincronizarse con su directorio local mediante Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Directorio de Azure AD solo de nube](active-directory-ds-getting-started-password-sync.md)
- [Directorio de Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Habilitación de la generación de hash de credenciales de NTLM y Kerberos en el directorio de Azure AD solo de nube
Si su organización tiene un directorio de Azure AD solo de nube, los usuarios que tengan que usar los Servicios de dominio de Azure AD deberán cambiar sus contraseñas. Este proceso de cambio de contraseña hace que los valores de hash de credenciales que necesitan los Servicios de dominio de Azure AD para la autenticación Kerberos y NTLM se generen en Azure AD. Puede caducar las contraseñas de todos los usuarios en el inquilino que tiene que usar los Servicios de dominio de Azure AD o indicar a estos usuarios que cambien sus contraseñas.

Estas son las instrucciones que tiene que proporcionar a los usuarios finales para poder cambiar sus contraseñas:

1. Navegue a la página del panel de acceso de Azure AD de su organización. Normalmente se encuentra en [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Seleccione la pestaña **Perfil** en esta página.

3. Haga clic en el icono **Cambiar contraseña** de esta página para iniciar un cambio de contraseña.

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Se abrirá la página **Cambiar contraseña**. Los usuarios pueden escribir su contraseña existente (antigua) y proceder a cambiarla.

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Después de que los usuarios han cambiado su contraseña, la nueva contraseña se podrá usar en breve en los Servicios de dominio de Azure AD. Después de unos minutos, los usuarios pueden iniciar sesión en equipos unidos al dominio administrado con su contraseña recién cambiada.


<br>

## Contenido relacionado

- [Enable password synchronization to AAD Domain Services for a synced Azure AD directory](active-directory-ds-getting-started-password-sync-synced-tenant.md) (Habilitación de la sincronización de contraseñas con los Servicios de dominio de Azure AD para un directorio de Azure AD sincronizado)

- [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)

- [Join a Windows virtual machine to an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-join-windows-vm.md) (Unión de una máquina virtual con Windows a un dominio administrado de Servicios de dominio de Azure AD)

- [Join a Red Hat Enterprise Linux virtual machine to an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-join-rhel-linux-vm.md) (Unión de una máquina virtual con Red Hat Enterprise Linux a un dominio administrado de Servicios de dominio de Azure AD)

<!---HONumber=AcomDC_0427_2016-->