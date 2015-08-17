<properties 
	pageTitle="Azure AD Connect: sugerencias para la Administración remota de Windows" 
	description="Sugerencias para la Administración remota de Windows de Azure AD Connect para usar con AD FS." 
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

# Azure AD Connect: sugerencias para la Administración remota de Windows


Al utilizar Azure AD Connect para implementar los Servicios de federación de Active Directory (AD FS) o el Proxy de aplicación web, compruebe los requisitos y las sugerencias siguientes para garantizar la conectividad y la configuración se completará correctamente.

- Si el servidor de destino está unido al dominio, compruebe que está habilitada la opción Administración remota de Windows. 
	* En una ventana de comandos PSH con privilegios elevados, use el comando "Enable-PSRemoting –force". 

- Si el servidor de destino es un equipo WAP no unido al dominio, hay un par de requisitos adicionales.
	- En el equipo de destino (equipo WAP): 

- Asegúrese de que winrm (Administración remota de Windows / WS-Management) se está ejecutando mediante el complemento Servicios.

- En una ventana de comandos PSH con privilegios elevados, use el comando "Enable-PSRemoting –force".
	- En el equipo en el que se está ejecutando el asistente (si el equipo de destino no está unido al dominio o el dominio no es de confianza): 

- En una ventana de comandos PSH con privilegios elevados, use el comando "Set-Item WSMan:\\localhost\\Client\\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate"
	- En el Administrador de servidores:
		- Agregue el host WAP de DMZ al grupo de máquinas (pestaña Administrador de servidores -> Administrar -> Agregar servidores... usar DNS) 
		- Pestaña Todos los servidores del Administrador de servidores: haga clic con el botón derecho en el servidor WAP y elija Administrar como..., escriba credenciales locales (no de dominio) para la máquina WAP. 
		- Para validar la conectividad remota de PSH, en la pestaña Todos los servidores del Administrador de servidores, haga clic con el botón derecho en el servidor WAP y elija Windows PowerShell. Debe abrirse una sesión remota de PSH para asegurarse de que se pueden establecer sesiones remotas de PowerShell. 

**Recursos adicionales**


* [Más información sobre permisos y cuentas de Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect en MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=August15_HO6-->