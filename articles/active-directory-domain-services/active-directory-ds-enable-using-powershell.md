---
title: "Habilitación de Azure Active Directory Domain Services mediante PowerShell | Microsoft Docs"
description: "Habilitación de Azure Active Directory Domain Services mediante PowerShell | Microsoft Docs"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: maheshu
ms.openlocfilehash: a456a6265400abe1d3a3620df74e41d8b4399b97
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Habilitación de Azure Active Directory Domain Services mediante PowerShell
Este artículo muestra cómo habilitar Azure Active Directory (AD) Domain Services mediante PowerShell.

## <a name="task-1-install-the-required-powershell-modules"></a>Tarea 1: Instalación de los módulos de PowerShell necesarios

### <a name="install-and-configure-azure-ad-powershell"></a>Instalación y configuración de Azure AD PowerShell
Siga las instrucciones que aparecen en el artículo para [instalar el módulo de Azure AD PowerShell y conectarse a Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instale y configure Azure PowerShell.
Siga las instrucciones que aparecen en el artículo para [instalar el módulo de Azure PowerShell y conectarse a la suscripción de Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Tarea 2: Creación de la entidad de servicio necesaria en el directorio de Azure AD
Escriba el siguiente comando de PowerShell para crear la entidad de servicio necesaria para Azure AD Domain Services en su directorio de Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Paso 3: Creación y configuración del grupo "Administradores de controladores de dominio de AAD"
La siguiente tarea consiste en crear el grupo de administrador que se utilizará para delegar tareas de administración en el dominio administrado.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Ahora que ha creado el grupo, agregue un par de usuarios al grupo.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Tarea 4: Registro del proveedor de recursos de Azure AD Domain Services
Escriba el siguiente comando de PowerShell para registrar el proveedor de recursos para Azure AD Domain Services:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Tarea 5: Creación de un grupo de recursos
Escriba el siguiente comando de PowerShell para crear un grupo de recursos:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Puede crear la red virtual y el dominio administrado de Azure AD Domain Services en este grupo de recursos.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Tarea 6: Creación y configuración de la red virtual
Ahora, cree la red virtual en la que habilita Azure AD Domain Services. Asegúrese de que crear una subred dedicada para Azure AD Domain Services. No implemente máquinas virtuales de carga de trabajo en esta subred dedicada.

Escriba los siguientes comandos de PowerShell para crear una red virtual con una subred dedicada para Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Tarea 7: Aprovisionamiento del dominio administrado de Azure AD Domain Services
Escriba el siguiente comando de PowerShell para habilitar Azure AD Domain Services para su directorio:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **No olvide los pasos de configuración adicionales después de aprovisionar el dominio administrado.**
> Después de aprovisiona el dominio administrado, deberá completar las tareas siguientes:
> * **[Actualice la configuración de DNS](active-directory-ds-getting-started-dns.md)**  para la red virtual de manera que las máquinas virtuales puedan encontrar el dominio administrado para la unión o autenticación de dominios.
* **[Habilite la sincronización de contraseñas en Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md)** de manera que los usuarios puedan iniciar sesión en el dominio administrado mediante sus credenciales corporativas.
>


## <a name="powershell-script"></a>Script de PowerShell
A continuación aparece el script de PowerShell que se usa para realizar todas las tareas enumeradas en este artículo. Copie el script y guárdelo en un archivo con la extensión '. ps1'. Ejecute el script de PowerShell o mediante el Entorno de scripting integrado (ISE) de Windows PowerShell.

> [!NOTE]
> **Permisos necesarios para ejecutar este script** Para habilitar Azure AD Domain Services, debe ser el administrador global para el directorio de Azure AD. Además, necesita al menos privilegios de "Colaborador" en la red virtual en la que habilitar Azure AD Domain Services.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Login-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **No olvide los pasos de configuración adicionales después de aprovisionar el dominio administrado.**
> Después de aprovisiona el dominio administrado, deberá completar las tareas siguientes:
> * Actualice la configuración de DNS para la red virtual de manera que las máquinas virtuales puedan encontrar el dominio administrado para la unión o autenticación de dominios.
* Habilite la sincronización de contraseñas en Azure AD Domain Services de manera que los usuarios puedan iniciar sesión en el dominio administrado mediante sus credenciales corporativas.
>

## <a name="next-steps"></a>Pasos siguientes
Una vez creado el dominio administrado, realice las siguientes tareas de configuración para poder usar el dominio administrado:

* [Actualización de la configuración del servidor DNS para la red virtual para señalar al dominio administrado](active-directory-ds-getting-started-dns.md)
* [Habilitación de la sincronización de contraseñas en el dominio administrado](active-directory-ds-getting-started-password-sync.md)
