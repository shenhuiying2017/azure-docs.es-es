<properties 
	pageTitle="Por qué se requiere una cuenta de administrador de empresa"
	description="Descripción de la configuración personalizada."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Por qué se requiere una cuenta de administrador de empresa para conectarse a AD DS al configurar Azure AD Connect

La siguiente tabla muestra los motivos por los cuales se requiere una cuenta de administrador de empresa para configurar Azure AD Connect.

En las siguientes condiciones | Descripción 
------------- | ------------- |
Para la configuración rápida y la actualización de la sincronización de directorios | <li>En el caso de la configuración rápida, se crea la cuenta de Active Directory local que se utiliza para la sincronización (también conocida como la cuenta de AD Connector) y se asignan los permisos correctos para la sincronización y la sincronización de contraseñas.</li> <li>En el caso de la actualización de la sincronización de directorios, se restablece la contraseña de la cuenta de AD Connector configurada actualmente y se configura el nuevo servicio de sincronización de Azure AD Connect para utilizar esta cuenta. </li>



**Recursos adicionales**


* [Más información sobre permisos y cuentas de Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect en MSDN](active-directory-aadconnect.md) 

<!---HONumber=August15_HO9-->