<properties
	pageTitle="Vista previa de los Servicios de dominio de Azure Active Directory: introducción | Microsoft Azure"
	description="Introducción a los Servicios de dominio de Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(vista previa)*: introducción

## Paso 5: Habilitación de la sincronización de contraseñas
Después de habilitar los Servicios de dominio de Azure AD para su inquilino de Azure AD, el siguiente paso es habilitar la sincronización de contraseñas. Así los usuarios pueden iniciar sesión en el dominio con sus credenciales corporativas.

Los pasos que se deben seguir son distintos en función de si su organización es un inquilino de Azure AD solo de nube o está configurado para sincronizarse con su directorio local mediante Azure AD Connect.

### Habilitación de la sincronización de contraseñas para inquilinos solo de nube
Si su organización es un inquilino de Azure AD solo de nube, los usuarios que tengan que usar los Servicios de dominio de Azure AD deberán cambiar sus contraseñas. Con este paso se generan en Azure AD los valores de hash de credenciales que necesitan los Servicios de dominio de Azure AD para la autenticación Kerberos y NTLM. O bien puede caducar las contraseñas de todos los usuarios en el inquilino que tiene que cursar los Servicios de dominio de Azure AD, o indicar a estos usuarios que cambien sus contraseñas.

Estas son las instrucciones que necesarias que debe proporcionar a los usuarios finales para poder cambiar sus contraseñas:

1. Navegue a la página del panel de acceso de Azure AD de su organización. Normalmente se encuentra en [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Seleccione la pestaña **Perfil** en esta página.
3. Haga clic en el icono **Cambiar contraseña** de esta página para iniciar un cambio de contraseña.

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Se abrirá la página **Cambiar contraseña**. Aquí el usuario puede escribir su contraseña (antigua) y proceder a cambiarla.

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Después de que los usuarios han cambiado su contraseña, la nueva contraseña se sincronizará en breve con los Servicios de dominio de Azure AD. Cuando finalice la sincronización de contraseñas, los usuarios podrán iniciar sesión en el dominio con su contraseña recién cambiada.


### Habilitación de la sincronización de contraseñas para los inquilinos sincronizados
Si el inquilino de Azure AD de la organización está configurado para sincronizarse con su directorio local mediante Azure AD Connect, deberá configurar Azure AD Connect para sincronizar los valores de hash de credenciales que son necesarios para la autenticación NTLM y Kerberos. Estos valores de hash no están sincronizados con Azure AD de forma predeterminada pero los pasos siguientes le permitirán sincronizar los valores de hash con el inquilino de Azure AD.

#### Instalación de Azure AD Connect

Deberá instalar la versión de disponibilidad general de Azure AD Connect en un equipo unido a un dominio. Si tiene una instancia existente del programa de instalación de Azure AD Connect, deberá actualizarla para usar la compilación de disponibilidad general de Azure AD Connect.

  [Descargar Azure AD Connect: versión de disponibilidad general](http://download.microsoft.com/download/B/0/0/B00291D0-5A83-4DE7-86F5-980BC00DE05A/AzureADConnect.msi)

> [AZURE.WARNING]Para permitir que las credenciales de contraseñas heredadas (necesarias para la autenticación NTML y Kerberos) se sincronicen con el inquilino de Azure AD, DEBE instalar la versión de disponibilidad general de Azure AD Connect. Esta función no está disponible en versiones anteriores de Azure AD Connect.

Puede encontrar las instrucciones de instalación de Azure AD Connect en el siguiente artículo: [Introducción a Azure AD Connect](../active-directory/active-directory-aadconnect.md).


#### Habilitación de la sincronización de credenciales heredadas con Azure AD

Habilite la sincronización de credenciales heredadas necesarias para la autenticación NTLM en los Servicios de dominio de Azure AD. Para ello, cree la siguiente clave del Registro en el equipo donde se instaló Azure AD Connect.

Cree la siguiente clave del Registro DWORD y establézcala en 1.

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\MSOLCoExistence\PasswordSync\EnableWindowsLegacyCredentialsSync

Set its value to 1.
```

#### Forzar la sincronización completa de contraseñas con Azure AD

Para forzar la sincronización completa de contraseñas y permitir que los valores de hash de contraseña (incluidos los valores de hash de credenciales necesarios para la autenticación NTLM o Kerberos) de los usuarios locales se sincronicen con el inquilino de Azure AD, ejecute el siguiente script de PowerShell en cada bosque de AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null  
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

En función del tamaño de su directorio (número de usuarios, grupos etc.), la sincronización de credenciales con Azure AD y luego con los Servicios de dominio de Azure AD llevará tiempo.

<!---HONumber=Oct15_HO4-->