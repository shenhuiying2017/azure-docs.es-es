<properties 
	pageTitle="Conexión de directorios con Azure AD Connect" 
	description="Descripción de la configuración personalizada de directorios conectados de Azure AD Connect." 
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



# Conexión de directorios con Azure AD Connect

Para la configuración personalizada, no se requiere una cuenta de administrador de empresa para conectarse a los bosques de Active Directory. El asistente aceptará una cuenta de dominio o de usuario local. Sin embargo, la cuenta se usa como cuenta local de AD Conector, es decir, es la cuenta que lee y escribe la información del directorio para la sincronización.

Esto significa que debe asignar permisos adicionales para habilitar los escenarios siguientes:

Escenario |Permiso
------------- | ------------- |
Sincronización de contraseñas| <li>Replicación de cambios de directorio.</li> <li>Replicación de todos los cambios de directorio.</li>
Implementación híbrida de Exchange|Consulte [Permisos y atributos de reescritura de Sincronización de AAD para Office 365 Exchange Hybrid](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange).
Escritura diferida de contraseñas | <li>Cambiar contraseña</li><li>Restablecer contraseña</li>
Reescritura de usuario, grupo y dispositivo|Permisos de escritura en los objetos y atributos de directorio que desea 'reescribir'
Inicio de sesión único y AD FS| Permisos de administrador de dominio en el dominio en el que están ubicados los servidores federados.





**Recursos adicionales**

* [Más información sobre permisos y cuentas de Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Permisos para la sincronización de contraseñas](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Permisos para la implementación híbrida de Exchange](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Permisos de escritura diferida de contraseñas](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect en MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=July15_HO4-->