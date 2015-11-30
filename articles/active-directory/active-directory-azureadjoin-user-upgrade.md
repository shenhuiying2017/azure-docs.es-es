<properties 
	pageTitle="Configuración de un dispositivo de Windows 10 con Azure AD desde Configuración | Microsoft Azure" 
	description="Se explica cómo pueden los usuarios unirse a Azure AD a través del menú Configuración de Windows 10." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="femila"/>

# Configuración de un dispositivo de Windows 10 con Azure AD desde Configuración
Si ya usa Windows 7 o Windows 8 y actualiza su máquina a Windows 10, puede unirse a Azure AD a través del menú Configuración.

Para unirse a Azure AD desde el menú Configuración
-----------------------------------------------------------------------------------------------

1. En el menú Inicio, haga clic en el acceso a Configuración.
2. En Configuración->**Sistema**->**Acerca de**->**Unirse a Azure AD**
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Haga clic en **Continuar** en la ventana de mensaje de Azure AD Join.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Proporcione sus credenciales de inicio de sesión. Esta experiencia de inicio de sesión incluirá todos los pasos necesarios para completar la autenticación. Si forma parte de un inquilino federado, el administrador le brindará una experiencia de federación que hospeda su organización.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Si la organización tiene configurada la autenticación multifactor para unirse a Azure AD, deberá proporcionar el segundo factor antes de poder continuar.
6. Haga clic en **Aceptar** en la ventana **Permitir la administración de este dispositivo**.
7. Verá el mensaje "El dispositivo está unido a su organización en Azure AD".


## Información adicional
* [Windows 10 para empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->