---
title: Configure el entorno de PowerShell del operador de Azure Stack | Microsoft Docs
description: Aprenda a configurar el entorno de PowerShell del operador de Azure Stack.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 7e912dcbfd1c745df2a0fc8717a075c0476e8d60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Configuración del entorno de PowerShell del operador de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Como operador de Azure Stack, puede configurar el entorno de PowerShell en Azure Stack Development Kit. Después de la configuración, puede usar PowerShell para administrar los recursos de Azure Stack, como la creación de ofertas, los planes, las cuotas, la administración de alertas, etcétera. Este tema está pensado para limitarse únicamente a entornos de operadores en la nube, si desea configurar PowerShell para el entorno del usuario, consulte el tema [Configuración del entorno de PowerShell del usuario de Azure Stack](user/azure-stack-powershell-configure-user.md). 

## <a name="prerequisites"></a>Requisitos previos

Implemente los siguientes requisitos previos desde el [kit de desarrollo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o desde un cliente externo basado en Windows, si está [conectado a través de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* Instale los [módulos de Azure PowerShell compatibles con Azure Stack](azure-stack-powershell-install.md).  
* Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configuración del entorno de operador e inicio de sesión en Azure Stack

Según el tipo de implementación (Azure AD o AD FS), ejecute uno de los siguientes scripts para configurar el entorno del operador de Azure Stack con PowerShell (asegúrese de reemplazar los valores tenantName, ArmEndpoint y el punto de conexión GraphAudience de AAD de acuerdo con la configuración de su entorno):

### <a name="azure-active-directory-aad-based-deployments"></a>Implementaciones basadas en Azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Implementaciones basadas en Servicios de federación de Active Directory (AD FS)
         
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

## <a name="test-the-connectivity"></a>Prueba de la conectividad

Ahora que todo está configurado, vamos a usar PowerShell para crear recursos en Azure Stack. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Pasos siguientes
* [Desarrollo de plantillas para Azure Stack](user/azure-stack-develop-templates.md)
* [Implementación de plantillas con PowerShell](user/azure-stack-deploy-template-powershell.md)