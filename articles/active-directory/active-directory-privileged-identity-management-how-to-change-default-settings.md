<properties
   pageTitle="Administración de la configuración de activación del rol | Microsoft Azure"
   description="Obtenga información sobre cómo cambiar la configuración predeterminada de identidades con privilegios con la extensión de Privileged Identity Management de Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# Administración de la configuración de activación de rol en Privileged Identity Management de Azure AD

Un administrador de roles con privilegios puede personalizar Privileged Identity Management (PIM) de Azure AD en su organización, como cambiar la experiencia de un usuario que va a activar una asignación de rol apto.

## Administración de la configuración de activación del rol

1. Vaya al [Portal de Azure](https://portal.azure.com) y seleccione la aplicación **Azure AD Privileged Identity Management** en el panel.
2. Seleccione el rol que quiere administrar en la tabla de roles.
3. Haga clic en **Configuración**.

En la página de configuración de cada rol, hay una serie de valores que puede configurar. Esta configuración solo afecta a los usuarios que son administradores aptos, no a los administradores permanentes.

**Activaciones**: el tiempo, en horas, que un rol permanece activo antes de que caduque. Este valor puede estar comprendido entre 1 y 72 horas.

**Notificaciones**: puede elegir si el sistema enviará o no correos electrónicos a los administradores para confirmar que han activado un rol. Puede ser útil para detectar activaciones no autorizadas o ilegales.

**Vale de incidente o solicitud**: puede elegir que los administradores incluyan o no un número de vale cuando activen su rol. Puede ser útil al realizar auditorías de acceso a rol.

**Multi-Factor Authentication**: puede elegir que los usuarios comprueben o no su identidad con MFA para poder activar sus roles. Solo tiene que realizar esta acción una vez por sesión, no cada vez que activen un rol. Al habilitar MFA, es conveniente tener en cuenta dos sugerencias:

- Los usuarios que tienen cuentas de Microsoft para sus direcciones de correo electrónico (normalmente @outlook.com, pero no siempre) no se pueden registrar para Azure MFA. Si quiere asignar roles a los usuarios con cuentas de Microsoft, debe establecerlos en administradores permanentes o deshabilitar MFA para ese rol.

- No se puede deshabilitar MFA para roles con privilegios elevados en Azure AD y Office 365. Esta es una característica de seguridad porque estos roles deben protegerse cuidadosamente:

    - Administrador global
    - Administrador de roles con privilegios
    - Administrador de cuenta de usuario
    - Escritor de directorio
    - Soporte para asociados de nivel 1
    - Soporte para asociados de nivel 2
    - Administrador de facturación
    - Administrador de seguridad
    - Administrador de Exchange
    - Administrador de buzón de correo
    - Administrador de Skype Empresarial
    - Administrador de SharePoint
    - Administrador de cumplimiento

Para obtener más información sobre cómo usar MFA con PIM, vea [Exigencia de MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0706_2016-->