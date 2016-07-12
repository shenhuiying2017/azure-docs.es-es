<properties
	pageTitle="Azure AD Connect Sync: administración de la cuenta de servicio de Azure AD | Microsoft Azure"
	description="En este tema se describe cómo restaurar la cuenta de servicio de Azure AD."
	services="active-directory"
    keywords="AADSTS70002, AADSTS50054, Procedimiento para restablecer la contraseña de la cuenta de servicio del conector de Azure AD Connect Sync"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="andkjell"/>

# Sincronización de Azure AD Connect: administración de la cuenta de servicio de Azure AD
Se supone que la cuenta de servicio utilizada por el conector de Azure AD está libre de servicio. Pero si necesita restablecer sus credenciales, este tema le interesa. Este caso puede darse si un administrador global ha restablecido la contraseña por error en la cuenta de servicio mediante PowerShell.

## Restablecimiento de las credenciales
Si la cuenta de servicio definida en el conector de Azure AD no puede ponerse en contacto con Azure AD debido a problemas de autenticación, se puede restablecer la contraseña.

1. Inicie sesión en el servidor de Azure AD Connect Sync e inicie PowerShell.
2. Ejecute `Add-ADSyncAADServiceAccount`. ![Cmdlet addadsyncaadserviceaccount de PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Proporcione las credenciales de administrador global de Azure AD.

Este cmdlet restablecerá la contraseña de la cuenta de servicio y la actualizará en Azure AD y en el motor de sincronización.

## Problemas conocidos que pueden solucionarse siguiendo estos pasos
Se trata de una lista de los errores que notifican los clientes que se corrigieron siguiendo estos pasos.

-----------
Evento 6900: el servidor encontró un error inesperado al procesar una notificación de cambio de contraseña: AADSTS70002: error al validar las credenciales. AADSTS50054: se utilizó una contraseña antigua para realizar la autenticación.

----------
Evento 659: error al recuperar la configuración de sincronización de directivas de contraseña. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: error al validar las credenciales. AADSTS50054: se utilizó una contraseña antigua para realizar la autenticación.

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->