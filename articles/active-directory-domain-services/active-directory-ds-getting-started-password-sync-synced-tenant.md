---
title: "Active Directory Domain Services: habilitación de la sincronización de contraseñas | Microsoft Docs"
description: "Introducción a los Servicios de dominio de Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/20/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9c43a831768684c6458d5f62557c8a06b2c5ca6b


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Servicios de dominio de Azure AD (versión preliminar): habilitación de la sincronización de contraseñas con Azure Active Directory Domain Services
En las tareas anteriores, ha habilitado Azure AD Domain Services para su inquilino de Azure AD. La siguiente tarea consiste en habilitar la sincronización de contraseñas con Azure Active Directory Domain Services. Una vez configurada la sincronización de credenciales, los usuarios pueden iniciar sesión en el dominio administrado mediante sus credenciales corporativas.

Los pasos que se deben seguir son distintos en función de si su organización tiene un inquilino de Azure AD solo de nube o está configurado para sincronizarse con su directorio local mediante Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Inquilino de Azure AD solo de nube](active-directory-ds-getting-started-password-sync.md)
> * [Inquilino de Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
> 
> 

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Tarea 5: Habilitación de la sincronización de contraseñas con los Servicios de dominio de Azure AD para un inquilino de Azure AD sincronizado
Se establece un inquilino de Azure AD para sincronizar con el directorio local de su organización con Azure AD Connect. De forma predeterminada, Azure AD Connect no sincroniza los hashes de credenciales de NTLM y Kerberos con Azure AD. Para usar Azure AD Domain Services, debe configurar Azure AD Connect para sincronizar los hashes de credenciales necesarios para la autenticación NTLM y Kerberos. Los pasos siguientes permiten la sincronización de los hashes de credenciales necesarios con el inquilino de Azure AD.

### <a name="install-or-update-azure-ad-connect"></a>Instalación o actualización de Azure AD Connect
Instale la versión recomendada más reciente de Azure AD Connect en un equipo unido a un dominio. Si tiene una instancia existente del programa de instalación de Azure AD Connect, debe actualizarla para usar la versión más reciente de Azure AD Connect. Con el fin de evitar errores y problemas conocidos que puedan estar ya corregidos, asegúrese de usar la versión más reciente de Azure AD Connect.

**[Descarga de Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versión recomendada: **1.1.281.0** , publicada el 7 de septiembre de 2016.

> [!WARNING]
> Para permitir que las credenciales de contraseñas heredadas (necesarias para la autenticación de NTLM y Kerberos) se sincronicen con el inquilino de Azure AD, DEBE instalar la versión recomendada más reciente de Azure AD Connect. Esta funcionalidad no está disponible en versiones anteriores de Azure AD Connect o con la herramienta DirSync heredada.
> 
> 

Puede encontrar las instrucciones de instalación de Azure AD Connect en el siguiente artículo: [Introducción a Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Habilitación de la sincronización de valores hash de credenciales de NTLM y Kerberos con Azure AD
Ejecute el siguiente script de PowerShell en cada bosque de AD para forzar la sincronización completa de contraseñas y permitir que los hashes de credenciales de los usuarios locales se sincronicen con el inquilino de Azure AD. Este script permite que los hashes de credenciales necesarios para que la autenticación NTLM o Kerberos se sincronice con el inquilino de Azure AD.

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

En función del tamaño de su directorio (número de usuarios o grupos, por ejemplo), la sincronización de los hashes de credenciales con Azure AD llevará tiempo. Las contraseñas se podrán usar en el dominio administrado de los Servicios de dominio de Azure AD poco después de que los valores hash se hayan sincronizado con Azure AD.

<br>

## <a name="related-content"></a>Contenido relacionado
* [Habilitación de la sincronización de contraseñas con los Servicios de dominio de Azure AD para un directorio de Azure AD solo de nube](active-directory-ds-getting-started-password-sync.md)
* [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)
* [Join a Windows virtual machine to an Azure AD Domain Services managed domain (Unión de una máquina virtual con Windows a un dominio administrado de Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-join-windows-vm.md)
* [Join a Red Hat Enterprise Linux virtual machine to an Azure AD Domain Services managed domain (Unión de una máquina virtual con Red Hat Enterprise Linux a un dominio administrado de Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-join-rhel-linux-vm.md)




<!--HONumber=Dec16_HO2-->


