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
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Privileged Identity Management de Azure AD: Exigencia de MFA

Se recomienda exigir Multi-Factor Authentication para todos los administradores.

## Exigencia de MFA en Privileged Identity Management de Azure AD

Al iniciar sesión como administrador de PIM, recibirá alertas que sugieren que las cuentas con privilegios deben exigir Multi-Factor Authentication (MFA). Haga clic en la alerta de seguridad en el panel PIM y se abrirá una nueva hoja con una lista de las cuentas de administrador que deben exigir MFA. Puede exigir MFA seleccionando varios roles y haciendo clic en el botón **Corregir**, o puede hacer clic en el botón de puntos suspensivos situado junto a cada uno de los roles y luego hacer clic en el botón **Corregir**.

Además, puede cambiar el requisito de MFA por rol haciendo clic en un rol específico en la sección Roles del panel y, luego, habilitar MFA para ese rol haciendo clic en **Configuración** en la hoja del rol y seleccionando **Habilitar** en la autenticación multifactor.

## Cómo PIM de Azure AD valida MFA

> [AZURE.IMPORTANT] Dado que las cuentas Microsoft (por ejemplo, @outlook.com, @live.com o @hotmail.com) no se admiten actualmente para registrarse en Azure MFA, no se permiten como administradores temporales para los roles con privilegios elevados. Si los usuarios necesitan seguir administrando las cargas de trabajo con una cuenta Microsoft, conviértalos en administradores permanentes por el momento.

Hay dos opciones para validar MFA cuando un usuario activa un rol.

La manera más sencilla es depender de Azure MFA (en el caso de los usuarios que activan un rol con privilegios). Para ello, primero compruebe que dichos usuarios cuentan con una licencia, si es necesario, y que se han registrado en Azure MFA. Encontrará más información sobre cómo hacerlo en [Introducción a Multi-Factor Authentication de Azure en la nube](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md#assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users). Tenga en cuenta que, aunque no es necesario, se recomienda configurar Azure AD para que aplique MFA a estos usuarios cuando inicien sesión. Esto se debe a que PIM de Azure AD realizará las comprobaciones de MFA.

Si los usuarios se autentican en el entorno local, también puede hacer que el proveedor de identidades se encargue de MFA. Por ejemplo, si ha configurado Servicios de federación de AD para requerir la autenticación basada en tarjeta inteligente antes de acceder a Azure AD, el artículo [Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) incluye instrucciones para configurar AD FS para enviar notificaciones a Azure AD. Cuando un usuario intenta activar un rol, PIM de Azure AD aceptará que ya se ha validado MFA para el usuario cuando reciba las notificaciones adecuadas.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->