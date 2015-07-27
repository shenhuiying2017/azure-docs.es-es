<properties 
	pageTitle="Administración de identidades con privilegios de Azure AD" 
	description="Un tema que explica qué es y cómo se configura Administración de identidades con privilegios de Azure AD" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="Justinha"/>

# Administración de identidades con privilegios de Azure AD

Administración de identidades con privilegios de AD Azure le permite administrar, controlar y supervisar las identidades con privilegios y su acceso a recursos en Azure AD y en otros servicios en línea de Microsoft como Office 365 o Microsoft Intune.

Para que los usuarios puedan realizar operaciones con privilegios, a menudo las organizaciones han tenido que proporcionar a muchos de su usuarios acceso con privilegios permanente en Azure AD o para recursos de Azure u Office 365, así como para otras aplicaciones de SaaS. Para muchos clientes, esto es un riesgo de seguridad creciente para los recursos hospedados en la nube porque no pueden supervisar suficientemente qué hacen esos usuarios con sus privilegios administrativos. Además, una cuenta de usuario en peligro que tiene acceso con privilegios puede afectar su seguridad global en la nube. Administración de identidades con privilegios de Azure AD le ayuda a resolver este riesgo.

Administración de identidades con privilegios de Azure AD en esta vista previa le permite:

- Detectar qué usuarios son los administradores de Azure AD
- Habilitar, a petición, el acceso administrativo "justo a tiempo" a los recursos del directorio
- Obtener informes sobre el historial de acceso de administrador y sobre los cambios en las asignaciones de administrador 
- Obtener alertas sobre el acceso a un rol con privilegios 

En esta vista previa, Administración de identidades con privilegios de Azure AD puede administrar los roles organizativos integrados de Azure Active Directory:

- Administrador global 
- Administrador de facturación 
- Administrador de servicios  
- Administrador de usuarios 
- Administrador de contraseñas 

## Acceso de administrador justo a tiempo 

Históricamente, se podía asignar a un usuario un rol de administrador a través del Portal de administración de Azure o Windows PowerShell. Por lo tanto, ese usuario se convertía en **administrador permanente**, siempre activo en su rol asignado. Esta vista previa agrega compatibilidad con un **administrador temporal**, que es un usuario que necesita completar un proceso de activación para el rol asignado. El proceso de activación cambia la asignación del usuario a un rol en Azure AD de inactiva a activa.

## Habilitación de Administración de identidades con privilegios para el directorio

Puede empezar a usar Administración de identidades con privilegios de Azure AD a través del [Portal de Microsoft Azure](https://portal.azure.com/). De momento, Administración de identidades con privilegios de Azure AD solo aparece en el Portal de Microsoft Azure. Debe ser administrador global para habilitar Administración de identidades con privilegios de Azure AD para un directorio.

![][1]

Después de inicializar esta extensión, se convertirá automáticamente en el primer **administrador de seguridad** del directorio. Solo un administrador de seguridad puede tener acceso a esta extensión para administrar el acceso de otros administradores. Durante la inicialización, se agregará un icono de Administración de identidades con privilegios de Azure AD al panel de inicio del Portal de vista previa de Azure.

## Panel de Administración de identidades con privilegios 

Administrador de identidades con privilegios de Azure AD proporciona un panel que ofrece información importante como:

- El número de usuarios que están asignados a cada rol con privilegios  
- El número de administradores temporales y permanentes 
- El historial de acceso de administrador 

![][2]

## Administración de roles con privilegios 

Con Administración de identidades con privilegios de Azure AD, puede administrar los administradores agregando o quitando administradores permanentes o temporales en cada rol.

![][3]

## Configuración de las opciones de activación de rol 

Mediante la opción de configuración de activación de rol, puede configurar propiedades de activación de rol temporal como:

- La duración del período de activación del rol
- La notificación de activación del rol 
- La información que un usuario debe proporcionar durante el proceso de activación del rol  

![][4]

## Activación de rol  

Para activar un rol, un administrador temporal debe solicitar una "activación" controlada por tiempo para el rol. Se puede solicitar la activación mediante la opción **Activar mi rol** en Administración de identidades con privilegios de Azure AD.

Un administrador que quiera activar un rol necesita inicializar Administración de identidades con privilegios de Azure AD en el Portal de vista previa de Azure.

Cualquier tipo de administrador puede usar Administración de identidades con privilegios de Azure AD para activar su propio rol.
 
La activación del rol está controlada por tiempo. En la configuración Activación de rol, puede configurar la longitud de la activación, así como la información necesaria que el administrador debe proporcionar para activar el rol.

![][5]

## Historial de activaciones de rol

Mediante Administración de identidades con privilegios de Azure AD, también puede realizar un seguimiento de los cambios en las asignaciones de rol con privilegios y el historial de activaciones de rol. Esto puede realizarse mediante las opciones de registro de auditoría:

![][6]

## Pasos siguientes

[Blog de Microsoft Azure](http://azure.microsoft.com/blog/) [Control de acceso basado en roles](../role-based-access-control-configure.md)

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

 

<!---HONumber=July15_HO3-->