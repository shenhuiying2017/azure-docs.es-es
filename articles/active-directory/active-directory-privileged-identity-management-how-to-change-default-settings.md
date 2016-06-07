<properties
   pageTitle="Administración de la configuración de activación del rol | Microsoft Azure"
   description="Obtenga información sobre cómo cambiar la configuración predeterminada de identidades con privilegios con la extensión de Privileged Identity Management de Azure Active Directory."
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

# Administración de la configuración de activación de rol en Privileged Identity Management de Azure AD

Un administrador de roles con seguridad puede personalizar Privileged Identity Management (PIM) de Azure AD en su organización, como cambiar la experiencia de un usuario que va a activar una asignación de rol temporal.

## Administración de la configuración de activación del rol

1. Vaya al [Portal de Azure](https://portal.azure.com) y seleccione la aplicación **Azure AD Privileged Identity Management** en el panel.
2. Seleccione el rol que quiere administrar en la tabla de roles.
3. Haga clic en **Configuración**.
4. Establezca la duración predeterminada de activación en horas ajustando el control deslizante o escribiendo el número de horas en el campo de texto. El valor máximo es de 72 horas.
5. Haga clic en **Habilitar** o **Deshabilitar** para enviar o no notificaciones sobre la activación a los administradores. (Habilitar las notificaciones puede ayudarle a detectar la actividad no autorizada de los administradores).
6. Haga clic en **Habilitar** para permitir que los administradores escriban la información de control de vales en su solicitud de activación. (Esta información puede ser útil cuando se audita posteriormente el acceso al rol).
7. Haga clic en **Habilitar** o **Deshabilitar** en función de que quiera o no exigir autenticación multifactor para una solicitud de activación.
8. Haga clic en **Guardar**.

No se puede deshabilitar MFA para los roles con privilegios elevados para Azure AD y Office 365, incluidos:
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

<!---HONumber=AcomDC_0525_2016-->