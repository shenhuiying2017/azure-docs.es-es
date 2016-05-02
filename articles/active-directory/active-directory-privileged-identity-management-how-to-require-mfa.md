<properties
   pageTitle="Exigencia de autenticación multifactor | Microsoft Azure"
   description="Obtenga información sobre cómo requerir Multi-Factor Authentication (MFA) para identidades con privilegios con la extensión de Privileged Identity Management de Azure Active Directory."
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

# Exigencia de MFA en Privileged Identity Management de Azure AD

Se recomienda exigir la autenticación multifactor (MFA) a todos los administradores y que todos los administradores actuales y candidatos se registren para MFA. De esta forma, se reduce el riesgo de ataques debidos a que una contraseña se ha puesto en peligro.

Puede exigir que los usuarios completen un desafío de MFA cuando inician sesión. La entrada de blog [MFA for Office 365 and MFA for Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) (MFA para Office 365 y MFA para Azure) compara lo que se incluye en las suscripciones de Office y Azure con las características de la oferta de Multi-Factor Authentication de Microsoft Azure.

También puede exigir que los usuarios realicen un desafío de MFA cuando activan un rol en PIM de Azure AD. De este modo, si el usuario no realizó un desafío de MFA cuando inició sesión, se le pedirá que lo haga en PIM.

## Exigencia de MFA en Privileged Identity Management de Azure AD

Al administrar identidades en PIM como administrador de seguridad, puede ver las alertas que recomienda MFA para las cuentas con privilegios. Haga clic en la alerta de seguridad en el panel de PIM y se abrirá una nueva hoja con una lista de las cuentas de administrador que deben exigir MFA. Para exigir MFA seleccione varios roles y haga clic en el botón **Corregir**; también puede hacer clic en el botón de puntos suspensivos situado junto a cada uno de los roles y luego hacer clic en el botón **Corregir**.

> [AZURE.IMPORTANT] Dado que las cuentas Microsoft (por ejemplo, @outlook.com, @live.com o @hotmail.com) no se admiten actualmente para registrarse en Azure MFA, no se permiten como administradores temporales para los roles con privilegios elevados. Si los usuarios necesitan seguir administrando las cargas de trabajo con una cuenta Microsoft, conviértalos en administradores permanentes por el momento.

Además, puede cambiar el requisito de MFA para un rol específico haciendo clic en él en la sección Roles del panel de PIM. Luego, haga clic en **Configuración** en la hoja de roles y seleccione **Habilitar** en Multi-Factor Authentication.

## Cómo PIM de Azure AD valida MFA

Hay dos opciones para validar MFA cuando un usuario activa un rol.

La opción más sencilla es depender de Azure MFA en el caso de los usuarios que activan un rol con privilegios. Para ello, primero compruebe que dichos usuarios cuentan con una licencia, si es necesario, y que se han registrado en Azure MFA. Encontrará más información sobre cómo hacerlo en [Introducción a Azure Multi-Factor Authentication en la nube](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md#assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users). Se recomienda, aunque no es necesario, configurar Azure AD para imponer MFA en estos usuarios cuando inicien sesión. Esto se debe a que PIM de Azure AD realizará las comprobaciones de MFA.

Si los usuarios se autentican en el entorno local, también puede hacer que el proveedor de identidades se encargue de MFA. Por ejemplo, si ha configurado Servicios de federación de AD para exigir la autenticación basada en tarjeta inteligente antes de acceder a Azure AD, el artículo [Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) incluye instrucciones para configurar AD FS para enviar notificaciones a Azure AD. Cuando un usuario intenta activar un rol, PIM de Azure AD aceptará que ya se ha validado MFA para el usuario cuando reciba las notificaciones adecuadas.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->