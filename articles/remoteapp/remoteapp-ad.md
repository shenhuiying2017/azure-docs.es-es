
<properties 
    pageTitle="Configurar Active Directory para RemoteApp de Azure" 
    description="Aprenda a configurar Active Directory para trabajar con RemoteApp de Azure." 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/03/2015" 
    ms.author="elizapo" />



# Configuración de Active Directory para RemoteApp de Azure


Para una colección híbrida de Azure RemoteApp, tendrá que configurar una infraestructura de dominio de Active Directory local y un inquilino de Azure Active Directory con integración de directorios (e inicio de sesión único opcional). Además, deberá crear algunos objetos de Active Directory en el directorio local. Utilice la información siguiente para configurar Active Directory local y Azure AD para, a continuación, integrar estos dos elementos.

## Configurar su Active Directory local
Empiece por configurar Active Directory local. Deberá identificar el sufijo del dominio UPN que desea utilizar y, a continuación, crear objetos de Active Directory para RemoteApp.

¿Todavía no ha agregado los servicios de dominio de Active Directory a su entorno de Windows Server 2012 R2? Consulte [estas instrucciones](https://technet.microsoft.com/library/cc731053.aspx) para obtener información sobre cómo realizar esta operación.
### Examinar y configurar el sufijo de dominio UPN
Si el bosque no está configurado con un sufijo UPN que coincida con el dominio de Azure AD que va a utilizar para RemoteApp, deberá agregarlo. Determine si está configurado el sufijo necesario:


1. Inicie los equipos y usuarios de Active Directory.
2.	Expanda el nombre de dominio y haga clic en **Usuarios**.
3.	Haga clic con el botón secundario en **Administrador** y luego haga clic en **Propiedades**.
4.	En la pestaña **Cuenta**, mire los nombres de UPN configurados para este dominio en el campo **Nombre de inicio de sesión de usuario**.
5.	Si no ve ningún sufijo que coincida con el nombre de dominio que desea utilizar para la recopilación de RemoteApp, haga lo siguiente:
	1.	Inicie las relaciones de confianza y dominios de Active Directory.
	2.	Haga clic con el botón secundario en **Dominios y confianzas de Active Directory** y haga clic en **Propiedades**.
	3.	Revise la lista de sufijos.
	4.	Escriba el FQDN del nombre de dominio en el cuadro y haga clic en **Agregar** y en **Aceptar**. Ejemplo: contoso.com. 

		NO incluya el símbolo "@" en el sufijo aquí.

De ahora en adelante, cuando cree nuevos usuarios, podrá seleccionar el nuevo sufijo del nombre de inicio de sesión de usuario. Además, podrá cambiar el sufijo de los usuarios existentes en la pestaña Cuenta en las propiedades de los usuarios.

Para obtener más información, consulte [Incorporación de sufijos de nombre principal de usuario](http://technet.microsoft.com/library/cc772007.aspx).

### Crear objetos para RemoteApp en Active Directory
RemoteApp necesita dos objetos en Active Directory local:


- Una cuenta de servicio para proporcionar acceso a los recursos del dominio para los programas de RemoteApp mediante la combinación de extremos RDSH al dominio local.
- Una unidad organizativa (OU) que contenga objetos de equipo de RemoteApp. Se recomienda usar la unidad organizativa (aunque no es necesario) para aislar las cuentas y las directivas que se van a utilizar con RemoteApp.

Utilice la siguiente información para crear cada uno de estos objetos.

#### Cree la cuenta de servicio:


1. Inicie los equipos y usuarios de Active Directory.
2.	Haga clic con el botón secundario en **Usuarios** y haga clic en **Nuevo > Usuario**.
3.	Escriba un nombre de usuario y una contraseña para la cuenta de servicio de RemoteApp.

	**Nota:** necesitará esta información de cuenta al crear la colección de RemoteApp.

#### Crear una nueva unidad organizativa (OU)


1. En Usuarios y equipos de Active Directory, haga clic con el botón secundario en el dominio. Haga clic en **Nueva > Unidad organizativa**.
2. Agregue la cuenta de servicio que creó para RemoteApp a esta nueva unidad organizativa.

	Para ello, busque la cuenta de servicio que creó. Haga clic con el botón secundario en la cuenta y haga clic en **Mover**. Seleccione la nueva unidad organizativa y haga clic en **Aceptar**.


1. Conceda a la cuenta de servicio RemoteApp la autoridad para agregar y eliminar equipos en esta unidad organizativa:
	1. Desde el complemento Usuarios y equipos de Active Directory, haga clic en **Vista > Características avanzadas**. De este modo, agregará la pestaña **Seguridad** a la información de propiedades.
	2. Haga clic con el botón secundario en la unidad organizativa del servicio RemoteApp y haga clic en **Propiedades**.
	3. En la pestaña **Seguridad**, haga clic en **Agregar**. Seleccione el objeto de usuario de la cuenta de servicio RemoteApp y haga clic en **Aceptar**.
	4. Haga clic en **Avanzadas**.
	5. En la pestaña **Permisos**, seleccione la cuenta de servicio RemoteApp y haga clic en **Editar**.
	6. Seleccione **Este objeto y todos los descendientes** en el campo **Se aplica a**.
	7. En el campo **Permisos**, seleccione la opción **Permitir** situada junto a Crear objetos de equipo y Eliminar objetos de equipo, y haga clic en **Aceptar**. 
	8. Haga clic en **Aceptar** en las otras dos ventanas.


## Configurar Azure Active Directory
Ahora que está configurado Active Directory local, vaya a Azure AD. Deberá crear un dominio personalizado que coincida con el sufijo UPN del dominio del dominio local para, a continuación, configurar la integración de directorio. La recopilación híbrida solo admite cuentas de Azure Active Directory que se hayan sincronizado (con una herramienta como DirSync) desde una implementación de Windows Server Active Directory; en concreto, sincronizada con la opción Sincronización de contraseña o con bien se sincronizan con la opción de sincronización de contraseña o con los Servicios de federación de Active Directory (AD FS) configurados.

Utilice la siguiente información para configurar Azure Active Directory


- [Incorporación del dominio](http://technet.microsoft.com/library/hh969247.aspx): use esta información para agregar un dominio que coincida con el sufijo de dominio de UPN de su dominio de Active Directory local.
- [Integración de directorios](http://technet.microsoft.com/library/jj573653.aspx): use esta información para elegir una opción de integración de directorios como, por ejemplo, [DirSync con sincronización de contraseña](http://technet.microsoft.com/library/dn441214.aspx) o [DirSync con federación](http://technet.microsoft.com/library/dn441213.aspx).

También puede configurar la [Autenticación multifactor (MFA)](http://technet.microsoft.com/library/dn249466.aspx).

## ¿Tiene problemas para configurar la sincronización de directorios?

Si tiene dificultades para configurar la sincronización de directorios, compruebe lo siguiente:

- Está usando la versión más reciente de la herramienta de sincronización de directorios de Azure 
-	En el portal de administración, en **Active Directory->Directorio predeterminado->Dominios**, ya agregó su dominio personalizado (por ejemplo, mydomain.com) y lo convirtió en el principal.
-	En **Active Directory->Directorio predeterminado->Usuarios**, agregue un nuevo usuario en ese dominio (por ejemplo, myAzureSyncUser@mydomain.com).
-	En su dominio de Active Directory, agregó un nuevo usuario de dominio y lo convirtió en miembro de Administradores de organización (por ejemplo, myDomainSyncUser@mydomain.com)).

Inicie ahora la herramienta de sincronización de directorios de Azure y use las credenciales de ****myAzureSyncUser@mydomain.com** para la primera petición de datos (credenciales de administrador de Microsoft Azure Active Directory Administrator) y ****myDomainSyncUser@mydomain.com** para la segunda petición de datos.
 

<!---HONumber=August15_HO6-->