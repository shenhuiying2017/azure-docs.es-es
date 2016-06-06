<properties
	pageTitle="Privileged Identity Management de Azure AD | Microsoft Azure"
	description="Un tema que explica qué es Privileged Identity Management de Azure AD y cómo usar PIM para mejorar la seguridad de la nube."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="kgremban"/>

# Administración de identidades con privilegios de Azure AD

Privileged Identity Management de Azure Active Directory (AD) le permite administrar, controlar y supervisar las identidades con privilegios y el acceso a los recursos en Azure AD y en otros servicios en línea de Microsoft, como Office 365 o Microsoft Intune.

A veces, los usuarios necesitan llevar a cabo operaciones con privilegios en recursos de Azure u Office 365 o en otras aplicaciones SaaS. Esto significa a menudo que las organizaciones tienen que concederles acceso con privilegios permanentes en Azure AD. Esto representa un riesgo de seguridad creciente para los recursos hospedados en la nube porque las organizaciones no pueden supervisar suficientemente qué hacen esos usuarios con sus privilegios administrativos. Además, si se pone en peligro una cuenta de usuario que tiene acceso con privilegios, esta situación podría afectar a su seguridad en la nube global. Administración de identidades con privilegios de Azure AD le ayuda a resolver este riesgo.

Azure AD Privileged Identity Management le permite:

- Ver qué usuarios son administradores de Azure AD
- Habilitar el acceso administrativo a petición y "justo a tiempo" en servicios de Microsoft Online Services, como Office 365 e Intune
- Obtener informes sobre el historial de acceso de administrador y sobre los cambios en las asignaciones de administrador
- Obtener alertas sobre el acceso a un rol con privilegios

Azure AD Privileged Identity Management puede administrar los roles de organización integrados de Azure AD, que incluyen los siguientes:

- Administrador global
- Administrador de facturación
- Administrador de servicios  
- Administrador de usuarios
- Administrador de contraseñas

## Acceso de administrador justo a tiempo

Históricamente, se podía asignar a un usuario un rol de administrador a través del Portal de administración de Azure anterior o Windows PowerShell. Como resultado, ese usuario se convertía en **administrador permanente** para ese rol, siempre activo en su rol asignado. Privileged Identity Management de Azure AD introduce el concepto de **administrador temporal** para un rol, que es un usuario que necesita completar un proceso de activación para ese rol asignado. El proceso de activación cambia la asignación del usuario a un rol en Azure AD de inactiva a activa durante un período de tiempo especificado, como 8 horas.

## Habilitación de Privileged Identity Management para el directorio

Para empezar a usar Privileged Identity Management de Azure AD, acceda al [Portal de Azure](https://portal.azure.com/). Privileged Identity Management de Azure AD no aparece en el portal de clásico anterior.

>[AZURE.NOTE] Debe ser un administrador global con una cuenta profesional, no una cuenta Microsoft, para habilitar Privileged Identity Management de Azure AD en un directorio.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) como administrador global de su directorio.
2. Si su organización tiene más de un directorio, haga clic en su nombre de usuario en la esquina superior derecha del Portal de Azure y seleccione el directorio donde usará Privileged Identity Management de Azure AD.
3. Haga clic en el icono **Nuevo** en el panel de navegación de la izquierda.
4. Seleccione **Seguridad e identidad**.
5. Seleccione **Azure AD Privileged Identity Management**.
6. Active la opción **Anclar al panel** y luego haga clic en el botón **Crear**. Se abrirá el panel Privileged Identity Management.

Si usted es la primera persona que usa Privileged Identity Management de Azure AD en su directorio, el [Asistente para seguridad](active-directory-privileged-identity-management-security-wizard.md) le guiará en la experiencia de asignación inicial. Después de eso, se convertirá automáticamente en el primer **administrador de seguridad** y **administrador de roles con privilegios** del directorio.

El administrador de roles con privilegios es el único que puede utilizar la aplicación PIM para administrar el acceso de otros administradores. También puede [conceder a otros usuarios la capacidad de administrar en PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Panel de Administración de identidades con privilegios

Privileged Identity Management de Azure AD proporciona un panel que ofrece información importante, por ejemplo:

- El número de usuarios que están asignados a cada rol con privilegios  
- El número de administradores temporales y permanentes
- El historial de acceso de cada administrador

![Panel de PIM - captura de pantalla][2]

## Administración de roles con privilegios

Con Administración de identidades con privilegios de Azure AD, puede administrar los administradores agregando o quitando administradores permanentes o temporales en cada rol.

![Adición o eliminación de administradores de PIM - captura de pantalla][3]

## Configuración de las opciones de activación de rol

Mediante la opción de configuración de activación de rol, puede configurar propiedades de activación de rol temporal como:

- La duración del período de activación del rol
- La notificación de activación del rol
- La información que un usuario debe proporcionar durante el proceso de activación del rol  

![Configuración de PIM - activación de administrador - captura de pantalla][4]

## Activación de rol  

Para activar un rol, un administrador temporal debe solicitar una "activación" controlada por tiempo para el rol. Se puede solicitar la activación mediante la opción **Activar mi rol** en Administración de identidades con privilegios de Azure AD.

Un administrador que quiera activar un rol necesita inicializar Privileged Identity Management de Azure AD en el Portal de Azure.

Cualquier tipo de administrador puede usar Administración de identidades con privilegios de Azure AD para activar su propio rol.

La activación del rol está controlada por tiempo. En la configuración Activación de rol, puede configurar la longitud de la activación, así como la información necesaria que el administrador debe proporcionar para activar el rol.

![Activación de rol de solicitud de administrador de PIM - captura de pantalla][5]

## Historial de activaciones de rol

Mediante Privileged Identity Management de Azure AD, también puede realizar un seguimiento de los cambios en las asignaciones de rol con privilegios y el historial de activaciones de rol. Esto puede realizarse mediante las opciones de registro de auditoría:

![Historial de activación de PIM - captura de pantalla][6]

## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0525_2016-->