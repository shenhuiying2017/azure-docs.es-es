---
title: "Personalizar una máquina virtual de Windows en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo usar la extensión de script personalizado y Key Vault para personalizar las máquinas virtuales de Windows en Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/19/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: f8ce553dc528fa98fcffd42750da6bec72266129
ms.lasthandoff: 04/21/2017

---

# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Cómo personalizar una máquina virtual de Windows en Azure
Para configurar las máquinas virtuales de una manera rápida y coherente, normalmente se desea alguna forma de automatización. Un enfoque común para personalizar una máquina virtual de Windows consiste en usar la [Extensión de la secuencia de comandos personalizada para Windows](extensions-customscript.md). Este tutorial describe cómo usar la extensión de script personalizado para instalar y configurar IIS para ejecutar un sitio web básico.

Se pueden completar los pasos de este tutorial con la versión más reciente del módulo [Azure PowerShell](/powershell/azureps-cmdlets-docs/).


## <a name="custom-script-extension-overview"></a>Información general de la extensión de script personalizado
La extensión de script personalizado descarga y ejecuta scripts en máquinas virtuales de Azure. Esta extensión es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión.

La extensión de script personalizado se integra con las plantillas de Azure Resource Manager y también se puede ejecutar mediante la CLI de Azure, PowerShell, Azure Portal o la API de REST de máquina virtual de Azure.

Se puede usar la extensión de script personalizado con máquinas virtuales de Linux y Windows.


## <a name="create-virtual-machine"></a>Create virtual machine
Antes de poder crear una máquina virtual, cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroupAutomate` en la ubicación `westus`:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupAutomate -Location westus
```

Establezca un nombre de usuario de administrador y una contraseña para las máquinas virtuales con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Ahora puede crear la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crean los componentes de red virtual necesarios, la configuración del sistema operativo y, después, se crea una máquina virtual denominada `myVM`:

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAutomate -Location westus `
-Name myVnet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupAutomate -Location westus `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroupAutomate -Location westus `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroupAutomate -Location westus `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName myResourceGroupAutomate -Location westus -VM $vmConfig
```

Los recursos y la máquina virtual tardan unos minutos en crearse.


## <a name="automate-iis-install"></a>Automatizar la instalación de IIS
Use [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para instalar la extensión de script personalizado. La extensión ejecuta `powershell Add-WindowsFeature Web-Server` para instalar el servidor web IIS y después actualiza la página `Default.htm` para mostrar el nombre de host de la máquina virtual:

```powershell
Set-AzureRmVMExtension -ResourceGroupName myResourceGroupAutomate `
    -ExtensionName IIS `
    -VMName myVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location westus
```


## <a name="test-web-site"></a>Sitio web de prueba
Obtenga la dirección IP pública del equilibrador de carga con [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). En el ejemplo siguiente se obtiene la dirección IP de `myPublicIP` que se ha creado anteriormente:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAutomate -Name myPublicIP | select IpAddress
```

Puede escribir la dirección IP pública en un explorador web. Se muestra el sitio web, incluido el nombre de host de la máquina virtual a la que el equilibrador de carga distribuye el tráfico como en el ejemplo siguiente:

![Ejecución del sitio web de IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, aprendió a personalizar una máquina virtual con la extensión de script personalizado. Avance al siguiente tutorial para aprender a crear un sitio web de IIS con equilibrio de carga.

[Load balance virtual machines](./tutorial-load-balancer.md) (Equilibrar la carga de máquinas virtuales)
