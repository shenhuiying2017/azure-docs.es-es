---
title: Habilitar los servicios multiinquilino en Azure Stack | Microsoft Docs
description: "Aprenda cómo admitir varios directorios de Azure Active Directory en Azure Stack"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 3a90057b43e3f2074e72f3d0f896b35b4884368b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Habilitar los servicios multiinquilino en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Puede configurar Azure Stack para admitir usuarios de varios inquilinos de Azure Active Directory (Azure AD) para usar los servicios en Azure Stack. Por ejemplo, tenga en cuenta el siguiente caso:

 - Usted es el administrador de servicios de contoso.onmicrosoft.com, donde está instalado Azure Stack.
 - Mary es la administradora de directorios de fabrikam.onmicrosoft.com, donde se encuentran los usuarios invitados. 
 - La empresa de Mary recibe servicios IaaS y PaaS de su empresa y debe permitir que los usuarios del directorio de invitados (fabrikam.onmicrosoft.com) inicien sesión y usen los recursos de Azure Stack en contoso.onmicrosoft.com.

En esta guía encontrará los pasos necesarios, en el contexto de este escenario, para configurar los servicios multiinquilino en Azure Stack.  En este escenario, usted y Mary deben completar los pasos para permitir que los usuarios de Fabrikam inicien sesión y consuman servicios de la implementación de Azure Stack en Contoso.  

## <a name="before-you-begin"></a>Antes de empezar
Hay algunos requisitos previos que debe tener en cuenta antes de configurar los servicios multiinquilino en Azure Stack:
  
 - Usted y Mary deben coordinar los procedimientos administrativos tanto en el directorio donde está instalado Azure Stack (Contoso) como en el directorio de invitados (Fabrikam).  
 - Asegúrese de que ha [instalado](azure-stack-powershell-install.md) y [configurado](azure-stack-powershell-configure-admin.md) PowerShell para Azure Stack.
 - [Descargue Azure Stack Tools](azure-stack-powershell-download.md) e importe los módulos Connect e Identity:

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Mary necesitará acceso por [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) a Azure Stack. 

## <a name="configure-azure-stack-directory"></a>Configurar el directorio de Azure Stack
En esta sección, configurará Azure Stack para permitir inicios de sesión de inquilinos del directorio de Azure AD de Fabrikam.

### <a name="onboard-guest-directory-tenant"></a>Incorporar el inquilino del directorio de invitados
A continuación, incorpore el inquilino del directorio de invitados (Fabrikam) en Azure Stack.  Este paso configura Azure Resource Manager para que acepte usuarios y entidades de servicio desde el inquilino del directorio de invitados.

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local"
````



## <a name="configure-guest-directory"></a>Configurar el directorio de invitados
Después de completar los pasos en el directorio de Azure Stack, Mary debe dar su consentimiento para que Azure Stack tenga acceso al directorio de invitados y para registrar Azure Stack en el directorio de invitados. 

### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrar Azure Stack en el directorio de invitados
Una vez que el administrador del directorio de invitados ha dado su consentimiento para que Azure Stack tenga acceso al directorio de Fabrikam, debe registrar Azure Stack en el inquilino del directorio de Fabrikam.

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName ` 
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>Instruir a los usuarios para iniciar sesión
Ahora que usted y Mary han completado los pasos para incorporar el directorio de Mary, Mary puede instruir a los usuarios de Fabrikam para que inicien sesión.  Para iniciar sesión, los usuarios de Fabrikam (es decir, los usuarios con el sufijo fabrikam.onmicrosoft.com) van a https://portal.local.azurestack.external.  

Mary instruirá a todas las [entidades de seguridad externas](../active-directory/active-directory-understanding-resource-access.md) en el directorio de Fabrikam (es decir, los usuarios en el directorio de Fabrikam sin el sufijo fabrikam.onmicrosoft.com) para que inicien sesión con https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Si no utilizan esta dirección URL, se dirigen a su directorio predeterminado (Fabrikam) y reciben un error que indica que su administrador no ha dado su consentimiento.

## <a name="next-steps"></a>Pasos siguientes

- [Administrar proveedores delegados](azure-stack-delegated-provider.md)
- [Conceptos clave de Azure Stack](azure-stack-key-features.md)
