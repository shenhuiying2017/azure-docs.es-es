---
title: "Guía de inicio rápido de instalación y configuración de PowerShell para Azure Stack | Microsoft Docs"
description: Aprenda a instalar y configurar PowerShell para Azure Stack.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: mabrigg
ms.openlocfilehash: 0a85fc78d31c58ee83721d7dccbcdf46f98b3ddd
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Póngase a trabajar con PowerShell en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Este inicio rápido le ayuda a instalar y configurar un entorno de Azure Stack con PowerShell. El script proporcionado en este artículo solo viene delimitado por el **operador de Azure Stack**.

Este artículo es una versión comprimida de los pasos descritos en los artículos [Instalación de PowerShell]( azure-stack-powershell-install.md), [Descarga de herramientas]( azure-stack-powershell-download.md) y [Configuración del entorno de PowerShell del operador de Azure Stack]( azure-stack-powershell-configure-admin.md). Mediante el uso de scripts en este tema, puede configurar PowerShell para entornos de Azure Stack que se implementan con Azure Active Directory o con los Servicios de federación de Active Directory (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Configuración de PowerShell para las implementaciones basadas en Azure Active Directory

Inicie sesión en Azure Stack Development Kit o en un cliente externo basado en Windows, si está conectado a través de VPN. Abra una sesión de PowerShell ISE con privilegios elevados y, luego, ejecute el siguiente script. Asegúrese de actualizar las variables **TenantName**, **ArmEndpoint** y **GraphAudience** según sea necesario para la configuración del entorno:

> [!IMPORTANT]
> La versión del módulo de PowerShell AzureRM 1.2.11 incluye una lista de los últimos cambios. Para actualizar desde la versión 1.2.10, vea la [guía de migración](https://aka.ms/azspowershellmigration).

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configuración de PowerShell para las implementaciones basadas en AD FS

Si al conectarse a Internet está trabajando con Azure Stack, puede utilizar el siguiente script. Sin embargo, si trabaja con Azure Stack sin conectividad a Internet, use la [forma desconectada de instalar PowerShell](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) y los cmdlets para configurar PowerShell permanecerán igual que se muestran en este script. Inicie sesión en Azure Stack Development Kit o en un cliente externo basado en Windows, si está conectado a través de VPN. Abra una sesión de PowerShell ISE con privilegios elevados y, luego, ejecute el siguiente script. Asegúrese de actualizar las variables **ArmEndpoint** y **GraphAudience** según sea necesario para la configuración del entorno:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

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

Ahora que ha configurado PowerShell, puede probar la configuración creando un grupo de recursos:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Cuando se crea el grupo de recursos, la propiedad de **estado de aprovisionamiento** se establece en **Correcto**.

## <a name="next-steps"></a>Pasos siguientes

* [Instalación y configuración de la CLI](azure-stack-connect-cli.md)

* [Desarrollo de plantillas](user/azure-stack-develop-templates.md)







