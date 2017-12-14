---
title: "Active Directory Domain Services: habilitación de la sincronización de contraseñas | Microsoft Docs"
description: "Introducción a Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 0f6204e8f0f779809cd9c657acbcbcf39d57d481
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Habilitación de la sincronización de contraseñas con Azure Active Directory Domain Services
En las tareas anteriores, habilitó Azure Active Directory Domain Services para su inquilino de Azure Active Directory (Azure AD). La siguiente tarea consiste en habilitar la sincronización de los hashes de credenciales necesarios para que la autenticación NT LAN Manager (NTLM) y Kerberos se sincronice con Azure AD Domain Services. Una vez configurada la sincronización de credenciales, los usuarios pueden iniciar sesión en el dominio administrado mediante sus credenciales corporativas.

Los pasos necesarios son diferentes según se trate de cuentas de usuario solo de nube o de las cuentas de usuarios que se sincronizan desde el directorio local mediante Azure AD Connect.

<br>
| **Tipo de cuenta de usuario.** | **Pasos que seguir** |
| --- | --- |
| **Cuentas de usuario sincronizadas desde un directorio local** |**&#x2713;** [Siga las instrucciones de este artículo](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) | 
| **Cuentas de usuario en la nube creadas en Azure AD** |**&#x2713;**: [Sincronización de contraseñas de cuentas de usuario solo de nube con el dominio administrado](active-directory-ds-getting-started-password-sync.md) |
<br>

> [!TIP]
> **Puede que tenga que seguir ambos conjuntos de pasos.**
> Si el inquilino de Azure AD tiene una combinación entre usuarios solo de nube y usuarios de la instalación local de AD, debe realizar ambos procedimientos.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>Tarea 5: habilitar la sincronización de contraseñas con el dominio administrado para las cuentas de usuario sincronizadas con la instancia local de AD
Se establece un inquilino de Azure AD para sincronizar con el directorio local de su organización con Azure AD Connect. De forma predeterminada, Azure AD Connect no sincroniza los hashes de credenciales de NTLM y Kerberos con Azure AD. Para usar Azure AD Domain Services, debe configurar Azure AD Connect para sincronizar los hashes de credenciales necesarios para la autenticación NTLM y Kerberos. Los pasos siguientes permiten la sincronización de los hashes de credenciales necesarios del directorio local con el inquilino de Azure AD.

> [!NOTE]
> **Si la organización tiene cuentas de usuario que están sincronizadas desde el directorio local, debe habilitar la sincronización de los hashes de NTLM y Kerberos para usar el dominio administrado.** Una cuenta de usuario sincronizada es una cuenta que se creó en el directorio local y se ha sincronizado con el inquilino de Azure AD mediante Azure AD Connect.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Instalación o actualización de Azure AD Connect
Instale la versión recomendada más reciente de Azure AD Connect en un equipo unido a un dominio. Si tiene una instancia existente del programa de instalación de Azure AD Connect, debe actualizarla para usar la versión más reciente de Azure AD Connect. Con el fin de evitar errores y problemas conocidos que puedan estar ya corregidos, use siempre la versión más reciente de Azure AD Connect.

**[Descarga de Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versión recomendada: **1.1.614.0**, publicada el 5 de septiembre de 2017.

> [!WARNING]
> Para permitir que las credenciales de contraseñas heredadas (necesarias para la autenticación de NTLM y Kerberos) se sincronicen con el inquilino de Azure AD, DEBE instalar la versión recomendada más reciente de Azure AD Connect. Esta funcionalidad no está disponible en versiones anteriores de Azure AD Connect o con la herramienta DirSync heredada.
>
>

Puede encontrar las instrucciones de instalación de Azure AD Connect en el siguiente artículo: [Introducción a Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Habilitación de la sincronización de valores hash de credenciales de NTLM y Kerberos con Azure AD
Ejecute el siguiente script de PowerShell en cada bosque de AD. El script permite que todos los hashes de contraseña de los usuarios locales NTLM y Kerberos se sincronicen con el inquilino de Azure AD. El script también inicia una sincronización completa en Azure AD Connect.

```powershell
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
