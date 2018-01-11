---
title: "Configuración del entorno de PowerShell del usuario de Azure Stack | Microsoft Docs"
description: "Configuración del entorno de PowerShell del usuario de Azure Stack"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: 0bd5b4a98fee7a5d914e53e49a9517f5d3682a88
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Configuración del entorno de PowerShell del usuario de Azure Stack

Como usuario de Azure Stack, puede configurar el entorno de PowerShell en Azure Stack Development Kit. Tras la configuración, puede usar PowerShell para administrar los recursos de Azure Stack como la suscripción a ofertas, la creación de máquinas virtuales, la implementación de plantillas de Azure Resource Manager, etcétera. Este tema está pensado para delimitarse únicamente a entornos de usuario, si desea configurar PowerShell para el entorno de operadores en la nube, consulte el artículo [Configuración del entorno de PowerShell del operador de Azure Stack](../azure-stack-powershell-configure-admin.md). 

## <a name="prerequisites"></a>Requisitos previos 

Implemente los siguientes requisitos previos desde el [kit de desarrollo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o desde un cliente externo basado en Windows, si está [conectado a través de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instale los [módulos de Azure PowerShell compatibles con Azure Stack](azure-stack-powershell-install.md).  
* Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Configuración del entorno de usuario e inicio de sesión en Azure Stack

Según el tipo de implementación (Azure AD o AD FS), ejecute uno de los siguientes scripts para configurar PowerShell para Azure Stack (asegúrese de reemplazar los valores tenantName, ArmEndpoint y el punto de conexión GraphAudience de AAD de acuerdo con la configuración de su entorno):

### <a name="azure-active-directory-aad-based-deployments"></a>Implementaciones basadas en Azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Implementaciones basadas en Servicios de federación de Active Directory (AD FS) 
          
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
  ```

## <a name="register-resource-providers"></a>Registro de proveedores de recursos

Cuando se trabaja en una suscripción de usuario recién creada que no tiene recursos implementados a través del portal, los proveedores de recursos no se registran automáticamente. Se deben registrar de modo explícito mediante el siguiente script:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>Prueba de la conectividad

Ahora que todo está configurado, vamos a usar PowerShell para crear recursos en Azure Stack. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Pasos siguientes
* [Desarrollo de plantillas para Azure Stack](azure-stack-develop-templates.md)
* [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
