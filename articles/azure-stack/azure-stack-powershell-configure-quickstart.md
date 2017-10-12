---
title: "Guía de inicio rápido de instalación y configuración de PowerShell para Azure Stack | Microsoft Docs"
description: Aprenda a instalar y configurar PowerShell para Azure Stack.
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
ms.openlocfilehash: 6e5c420ed80127213e38849ac1999bba199e36c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Póngase a trabajar con PowerShell en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Este artículo es una guía de inicio rápido para instalar y configurar el entorno de Azure Stack con PowerShell. El script proporcionado en este artículo solo viene delimitado por el **operador de Azure Stack**.

Este artículo es una versión comprimida de los pasos descritos en los artículos [Instalación de PowerShell]( azure-stack-powershell-install.md), [Descarga de herramientas]( azure-stack-powershell-download.md) y [Configuración del entorno de PowerShell del operador de Azure Stack]( azure-stack-powershell-configure-admin.md). Mediante el uso de scripts en este tema, puede configurar PowerShell para entornos de Azure Stack que se implementan con Azure Active Directory o con los Servicios de federación de Active Directory (AD FS).  


## <a name="set-up-powershell-for-aad-based-deployments"></a>Configuración de PowerShell para las implementaciones basadas en AAD

Inicie sesión en Azure Stack Development Kit o en un cliente externo basado en Windows, si está conectado a través de VPN. Abra una sesión de PowerShell ISE con privilegios elevados y ejecute el script siguiente (asegúrese de actualizar las variables TenantName, ArmEndpoint y GraphAudience según la configuración del entorno):

```powershell
# Specify Azure Active Directory tenant name
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackAdmin" `
  -ArmEndpoint $ArmEndpoint

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience $GraphAudience

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $TenantName `
  -EnvironmentName AzureStackAdmin

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configuración de PowerShell para las implementaciones basadas en AD FS 

Inicie sesión en Azure Stack Development Kit o en un cliente externo basado en Windows, si está conectado a través de VPN. Abra una sesión de PowerShell ISE con privilegios elevados y ejecute el script siguiente (asegúrese de actualizar las variables ArmEndpoint y GraphAudience según la configuración del entorno):

```powershell

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the cloud administrator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackAdmin" `
  -ArmEndpoint $ArmEndpoint

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience $GraphAudience `
  -EnableAdfsAuthentication:$true

$TenantID = Get-AzsDirectoryTenantId `
  -ADFS `
  -EnvironmentName "AzureStackAdmin"

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="test-the-connectivity"></a>Prueba de la conectividad

Ahora que ha configurado PowerShell, puede probar la configuración creando un grupo de recursos:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Cuando se crea el grupo de recursos, la salida del cmdlet tiene la propiedad de estado de aprovisionamiento establecida en "Correcto".

## <a name="next-steps"></a>Pasos siguientes

* [Instalación y configuración de la CLI](azure-stack-connect-cli.md)

* [Desarrollo de plantillas](user/azure-stack-develop-templates.md)







