<properties
	pageTitle="Servicios de dominio de Azure AD: habilitación de la sincronización de contraseñas | Microsoft Azure"
	description="Introducción a los Servicios de dominio de Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(versión preliminar)*: habilitación de la sincronización de contraseñas con los Servicios de dominio de Azure AD

## Tarea 5: Habilitación de la sincronización de contraseñas con los Servicios de dominio de Azure AD para un inquilino de Azure AD sincronizado
Después de habilitar los Servicios de dominio de Azure AD para su directorio de Azure AD, la siguiente tarea consiste en habilitar la sincronización de contraseñas con los Servicios de dominio de Azure AD. Así los usuarios pueden iniciar sesión en el dominio con sus credenciales corporativas.

Los pasos que deben seguirse son distintos en función de si su organización tiene un directorio de Azure AD solo de nube o está configurado para sincronizarse con su directorio local mediante Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Directorio de Azure AD solo de nube](active-directory-ds-getting-started-password-sync.md)
- [Directorio de Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Inquilinos sincronizados: habilitación de la sincronización de valores hash de credenciales de NTLM y Kerberos con Azure AD
Si el inquilino de Azure AD de la organización está configurado para sincronizarse con su directorio local mediante Azure AD Connect, deberá configurar Azure AD Connect para sincronizar los valores de hash de credenciales que son necesarios para la autenticación NTLM y Kerberos. Estos valores de hash no están sincronizados con Azure AD de forma predeterminada pero los pasos siguientes le permitirán sincronizar los valores de hash con el inquilino de Azure AD.

#### Instalación o actualización de Azure AD Connect

Deberá instalar la versión recomendada más reciente de Azure AD Connect en un equipo unido a un dominio. Si tiene una instancia existente del programa de instalación de Azure AD Connect, deberá actualizarla para usar la compilación de disponibilidad general de Azure AD Connect. Asegúrese de usar la versión más reciente de Azure AD Connect, con el fin de evitar errores y problemas conocidos que puedan estar ya corregidos.

**[Descargar Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versión recomendada: **1.1.189.0**, publicada el 3 de junio de 2016.

  > [AZURE.WARNING] Para permitir que las credenciales de contraseñas heredadas (necesarias para la autenticación de NTLM y Kerberos) se sincronicen con el inquilino de Azure AD, DEBE instalar la versión recomendada más reciente de Azure AD Connect. Esta funcionalidad no está disponible en versiones anteriores de Azure AD Connect o con la herramienta DirSync heredada.

Puede encontrar las instrucciones de instalación de Azure AD Connect en el siguiente artículo: [Introducción a Azure AD Connect](../active-directory/active-directory-aadconnect.md).


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

En función del tamaño de su directorio (número de usuarios, grupos etc.), la sincronización de credenciales con Azure AD llevará tiempo. Las contraseñas se podrán usar en el dominio administrado de los Servicios de dominio de Azure AD poco después de que los valores hash se hayan sincronizado con Azure AD.


<br>

## Contenido relacionado

- [Habilitación de la sincronización de contraseñas con los Servicios de dominio de Azure AD para un directorio de Azure AD solo de nube](active-directory-ds-getting-started-password-sync.md)

- [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)

- [Join a Windows virtual machine to an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-join-windows-vm.md) (Unión de una máquina virtual con Windows a un dominio administrado de Servicios de dominio de Azure AD)

- [Join a Red Hat Enterprise Linux virtual machine to an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-join-rhel-linux-vm.md) (Unión de una máquina virtual con Red Hat Enterprise Linux a un dominio administrado de Servicios de dominio de Azure AD)

<!---HONumber=AcomDC_0706_2016-->