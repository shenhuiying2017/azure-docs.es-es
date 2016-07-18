<properties
	pageTitle="Privileged Identity Management de Azure AD | Microsoft Azure"
	description="Un tema que explica qué es Privileged Identity Management de Azure AD y cómo usar PIM para mejorar la seguridad de la nube."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="kgremban"/>

# Administración de identidades con privilegios de Azure AD

Con Privileged Identity Management de Azure Active Directory (AD), puede administrar, controlar y supervisar el acceso a los recursos en Azure AD, así como a otros servicios de Microsoft Online Services, como Office 365 o Microsoft Intune.

Las organizaciones buscan reducir el número de personas que tienen acceso a información segura o a recursos, ya que de esta manera se reduce la posibilidad de que usuarios malintencionados obtengan acceso a ellos. Sin embargo, los usuarios siguen teniendo la necesidad de llevar a cabo operaciones con privilegios en Azure, Office 365 o SaaS. Al final, el trabajo se tiene que hacer, así que las organizaciones proporcionan a los usuarios acceso con privilegios en Azure AD sin supervisar lo que hacen con ellos. Administración de identidades con privilegios de Azure AD le ayuda a resolver este riesgo.

Privileged Identity Management de Azure AD le ayuda a:

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

Históricamente, se podía asignar a un usuario un rol de administrador a través del Portal de administración de Azure o Windows PowerShell. Por lo tanto, ese usuario se convierte en **administrador permanente**, siempre activo en su rol asignado. Privileged Identity Management de Azure AD presenta el concepto de **administrador apto**. Los administradores aptos deben ser usuarios que necesiten acceso con privilegios de vez en cuando, pero no todos los días. El rol está inactivo hasta que el usuario necesita acceso, luego realiza un proceso de activación y se convierte en un administrador activo durante una cantidad de tiempo predeterminada.

## Habilitación de Privileged Identity Management para el directorio

Para empezar a usar Privileged Identity Management de Azure AD, acceda al [Portal de Azure](https://portal.azure.com/).

>[AZURE.NOTE] Debe ser un administrador global con una cuenta profesional (por ejemplo, @yourdomain.com), no una cuenta de Microsoft (por ejemplo, @outlook.com), para habilitar Privileged Identity Management de Azure AD en un directorio.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) como administrador global de su directorio.
2. Si su organización tiene más de un directorio, haga clic en su nombre de usuario en la esquina superior derecha del Portal de Azure y seleccione el directorio donde usará Privileged Identity Management de Azure AD.
3. Seleccione **Nuevo** > **Seguridad e identidad** > **Privileged Identity Management de Azure AD**.

	![Habilitación de PIM en el portal][1]

4. Active la opción **Anclar al panel** y luego haga clic en el botón **Crear**. Se abrirá el panel Privileged Identity Management.

Si usted es la primera persona que usa Privileged Identity Management de Azure AD en su directorio, el [Asistente para seguridad](active-directory-privileged-identity-management-security-wizard.md) le guiará en la experiencia de asignación inicial. Después de eso, se convertirá automáticamente en el primer **administrador de seguridad** y **administrador de rol con privilegios** del directorio.

El administrador de rol con privilegios es el único que puede administrar el acceso de otros administradores. También puede [conceder a otros usuarios la capacidad de administrar en PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Panel de Administración de identidades con privilegios

Privileged Identity Management de Azure AD proporciona un panel que ofrece información importante, por ejemplo:

- Alertas que destacan oportunidades para mejorar la seguridad
- El número de usuarios que están asignados a cada rol con privilegios
- El número de administradores aptos y permanentes
- Revisiones de servicios continuas

![Panel de PIM - captura de pantalla][2]

## Administración de roles con privilegios

Con Privileged Identity Management de Azure AD, puede administrar los administradores agregando o quitando administradores permanentes o aptos en cada rol.

![Adición o eliminación de administradores de PIM - captura de pantalla][3]

## Configuración de las opciones de activación de rol

Mediante la configuración de activación de roles, puede configurar las propiedades de activación de roles aptos como:

- La duración del período de activación del rol
- La notificación de activación del rol
- La información que un usuario debe proporcionar durante el proceso de activación del rol

![Configuración de PIM - activación de administrador - captura de pantalla][4]

Tenga en cuenta que en la imagen, los botones de **Multi-Factor Authentication** están deshabilitados. Para algunos roles con privilegios elevados, se requiere MFA para aumentar la protección.

## Activación de rol  

Para activar un rol, un administrador apto debe solicitar una "activación" controlada por tiempo para el rol. Se puede solicitar la activación mediante la opción **Activar mi rol** en Administración de identidades con privilegios de Azure AD.

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

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0706_2016-->