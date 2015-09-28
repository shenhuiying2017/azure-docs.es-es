<properties 
	pageTitle="Más información sobre las credenciales y los permisos de Azure AD Connect" 
	description="Descripción de la configuración personalizada de credenciales y permisos de Azure AD Connect." 
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



# Más información sobre las credenciales y los permisos de Azure AD Connect 


El asistente de Azure AD Connect ofrece dos rutas de acceso diferentes con distintos requisitos de permisos:

* En configuración rápida se requieren más privilegios para que podamos realizar su configuración fácilmente, sin necesidad de crear o configurar permisos por separado. 

* En configuración personalizada se ofrecen más opciones, pero existen algunas situaciones en las que necesitará asegurarse de tener los permisos correctos.


## Las credenciales que se recopilan y el uso que se les da en una configuración rápida

Página del asistente | Credenciales recopiladas | Permisos necesarios| Se usa para 
------------- | ------------- |------------- |------------- |
Conectarse a Azure| Credenciales de directorio de Azure AD | Rol de administrador global en Azure AD | <li>Habilitación de la sincronización en el directorio de Azure AD.</li> <li>Creación de la cuenta de Azure AD que se usará para operaciones de sincronización continua en Azure AD.</li>
Conectarse a AD DS | Credenciales de Active Directory local | Miembro del grupo de administradores de empresa (EA) en Active Directory| La cuenta se usa como cuenta local de AD Conector, es decir, es la cuenta que lee y escribe la información del directorio para la sincronización.
N/D|Credenciales de inicio de sesión del usuario que ejecuta el asistente| Administrador del servidor local|El asistente crea la cuenta de AD que se usará como cuenta de inicio de sesión del servicio de sincronización en el equipo local.

<br> <br>


## Las credenciales que se recopilan y el uso que se les da en una configuración personalizada


Página del asistente | Credenciales recopiladas | Permisos necesarios| Se usa para 
------------- | ------------- |------------- |------------- 
N/A|Credenciales de inicio de sesión del usuario que ejecuta el asistente|Administrador del servidor local| <li>De forma predeterminada, el asistente crea la cuenta de AD que se usará como cuenta de inicio de sesión del servicio de sincronización en el equipo local</li><li>Nosotros solo creamos la cuenta de inicio de sesión del servicio de sincronización si el administrador no especifica una cuenta determinada</li> <li>La cuenta es un usuario local a menos que se esté en un DC en cuyo caso la cuenta es un usuario de dominio</li> 
Instalar la página del servicios de sincronización, la opción de cuenta de servicio | Credenciales de cuenta de usuario local o de AD | Usuario local|Si el administrador especifica una cuenta, esta se usa como cuenta de inicio de sesión para el servicio de sincronización.
Conectarse a Azure|Credenciales de directorio de Azure AD| Rol de administrador global en Azure AD|El asistente crea la cuenta de AD que se usará como cuenta de inicio de sesión del servicio de sincronización en el equipo local.
Conectar sus directorios|En Active Directory local, las credenciales de cada bosque que se conectará a Azure AD |<li>El nivel mínimo de permisos requerido por el asistente es usuario de dominio.</li> <li>Sin embargo, la cuenta especificada debe tener los permisos necesarios para el escenario deseado.</li><li>Si desea configurar la sincronización de contraseñas con  
Azure AD, asegúrese de que esta cuenta tenga asignados los siguientes permisos: replicación de cambios de directorio y replicación de todos los cambios de directorio</li> <li>Si su intención es configurar la sincronización para 'reescribir' la información de Azure AD en AD local, asegúrese de que la cuenta tenga permisos de escritura en los objetos de directorio y los atributos que desea reescribir.</li> <li>Si pretende configurar AD FS para el inicio de sesión, asegúrese de que las credenciales de AD que proporcione para el bosque en el que residen los servidores de AD FS tengan privilegios de administrador de dominio.</li><li>Consulte la tabla siguiente para ver una lista de requisitos adicionales para su escenario.</li>|<li>Esta es la cuenta que se utilizará para la cuenta del Agente de administración de AD (MA) local. Se usará para leer y escribir objetos y atributos en AD local para la operación de sincronización continua.</li>
Servidores de AD FS|Para cada servidor de la lista, el asistente recopila credenciales si las credenciales de inicio de sesión del usuario que ejecuta el asistente no son suficientes para conectarse|Administrador de dominio|Instalación y configuración del rol de servidor de AD FS.|
Servidores proxy de aplicación web |Para cada servidor de la lista, el asistente recopila credenciales si las credenciales de inicio de sesión del usuario que ejecuta el asistente no son suficientes para conectarse|Administrador local en el equipo de destino.|Instalación y configuración del rol de servidor de WAP.
Credenciales de confianza del proxy |Credenciales de confianza del servicio de federación (las credenciales que el proxy usará para inscribirse para obtener un certificado de confianza de FS) |Cuenta de dominio que es un administrador local del servidor de AD FS|Inscripción inicial de certificados de confianza de FS WAP
Página de cuenta de servicio de AD FS, "Usar una opción de cuenta de usuario de dominio"|Credenciales de cuenta de usuario de AD|Usuario de dominio|La cuenta de usuario de AD cuyas credenciales se proporcionan se usará como cuenta de inicio de sesión del servicio AD FS.



<br> <br>
## Permisos necesarios para el escenario específico

Escenario |Permiso
------------- | ------------- |
Sincronización de contraseñas| <li>Replicación de cambios de directorio.</li> <li>Replicación de todos los cambios de directorio.</li>
Implementación híbrida de Exchange|Consulte [Permisos y atributos de reescritura de Sincronización de AAD para Office 365 Exchange Hybrid](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange).
Escritura diferida de contraseñas | <li>Cambiar contraseña</li><li>Restablecer contraseña</li>
Reescritura de usuario, grupo y dispositivo|Permisos de escritura en los objetos y atributos de directorio que desea 'reescribir'
Inicio de sesión único y AD FS| Permisos de administrador de dominio en el dominio en el que están ubicados los servidores federados.
Permisos de SQL Server|La cuenta de servicio de Azure AD Connect Sync requiere permisos de dbo en SQL. Estos permisos se establecen durante la instalación de Azure AD Connect y no se admite la realización de cambios.

<br> <br>
## Resumen de las cuentas que se crean mediante Azure AD Connect



Cuenta creada |Permisos asignados | Usado para
------------- | ------------- |------------- |
Cuenta de Azure AD para sincronizar|Rol dedicado de sincronización de directorios|Operaciones de sincronización continua (cuenta de Azure AD MA)
Configuración rápida: cuenta de AD usada para sincronizar|Permisos de lectura y escritura en el directorio según sea necesario para la sincronización + sincronización de contraseñas|Operaciones de sincronización continua (cuenta de Azure AD MA)
Configuración rápida: cuenta de inicio de sesión del servicio de sincronización | Credenciales de inicio de sesión del usuario que ejecuta el asistente|Cuenta de inicio de sesión del servicio de sincronización
Configuración rápida: cuenta de inicio de sesión del servicio de sincronización |N/A|Cuenta de inicio de sesión del servicio de sincronización
Cuenta de AD FS:GMSA (aadcsvc$)|Usuario de dominio|Cuenta de inicio de sesión del servicio de FS


**Recursos adicionales**



* [Permisos para la sincronización de contraseñas](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Permisos para la implementación híbrida de Exchange](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Permisos de escritura diferida de contraseñas](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect en MSDN](active-directory-aadconnect.md)
 

<!---HONumber=Sept15_HO3-->