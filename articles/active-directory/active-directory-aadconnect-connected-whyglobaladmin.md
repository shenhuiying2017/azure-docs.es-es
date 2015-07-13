<properties 
	pageTitle="Por qué necesitamos una cuenta de administrador global de Azure AD para configurar Azure AD Connect" 
	description="Descripción de la configuración personalizada de por qué necesitamos una cuenta de administrador global." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Por qué necesitamos una cuenta de administrador global de Azure AD para configurar Azure AD Connect

En la siguiente tabla se indican los motivos por los cuales se necesita una cuenta de administrador global de Azure AD para configurar Azure AD Connect.

En las siguientes condiciones | Descripción 
------------- | ------------- |
Para la configuración rápida y la actualización de la sincronización de directorios | Habilitamos la sincronización (si es necesario) en el directorio de Azure AD y creamos la cuenta de Azure AD que se usará para las operaciones de sincronización continua (la cuenta de Azure AD Connector). 
Para la configuración personalizada | Habilitamos la sincronización en el directorio de Azure AD y creamos la cuenta de Azure AD que se usará para las operaciones de sincronización continua (la cuenta de Azure AD Connector). Para el inicio de sesión único con la opción de AD FS, leemos y configuramos las propiedades de federación en Azure AD.



**Recursos adicionales**


* [Más información sobre permisos y cuentas de Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect en MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=62-->

<!--HONumber=62-->
<!--HONumber=62-->