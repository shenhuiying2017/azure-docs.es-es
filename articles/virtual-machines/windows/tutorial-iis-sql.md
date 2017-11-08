---
title: "Creación de máquinas virtuales que ejecuten una pila SQL&#92;IIS&#92;.NET en Azure | Microsoft Docs"
description: "Tutorial: Instalación de una pila de Azure SQL, IIS y .NET en una máquina virtual Windows."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6f7ef46d9c40138c211427845423783fefde5dc3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Instalación de una pila SQL&#92;IIS&#92;.NET en Azure

En este tutorial, vamos a instalar una pila SQL&#92;IIS&#92;.NET con Azure PowerShell. Esta pila está formada por dos máquinas virtuales que ejecutan Windows Server 2016, una con IIS y .NET y la otra con SQL Server.

> [!div class="checklist"]
> * Creación de una máquina virtual con New-AzVM
> * Instalación de IIS y el SDK de .NET Core en la máquina virtual
> * Creación de una máquina virtual que ejecute SQL Server
> * Instalación de la extensión de SQL Server



## <a name="create-a-iis-vm"></a>Creación de una máquina virtual con IIS 

En este ejemplo, usamos el cmdlet [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) en PowerShell Cloud Shell para crear rápidamente una máquina virtual Windows Server 2016 y, a continuación, instalar IIS y .NET Framework. Las máquinas virtuales con IIS y SQL comparten un grupo de recursos y una red virtual, por lo que creamos variables para esos nombres.

Haga clic en el botón **Pruébelo** en la esquina superior derecha del bloque de código para iniciar Cloud Shell en esta ventana. Se le pedirá que proporcione las credenciales de la máquina virtual en el símbolo del sistema.

```azurepowershell-interactive
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm -Name myIISVM -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

Instale IIS y .NET framework utilizando la extensión de script personalizado.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName myIISVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>Máquina virtual de Azure SQL

Se utiliza una imagen de Marketplace de Azure configurada previamente de un servidor SQL server para crear la máquina virtual con SQL. Primero creamos la máquina virtual y, a continuación, vamos a instalar la extensión de SQL Server en la máquina virtual. 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

Use [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) para agregar la [extensión de SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) a la máquina virtual con SQL.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha instalado una pila SQL&#92;IIS&#92;.NET con Azure PowerShell. Ha aprendido a:

> [!div class="checklist"]
> * Creación de una máquina virtual con New-AzVM
> * Instalación de IIS y el SDK de .NET Core en la máquina virtual
> * Creación de una máquina virtual que ejecute SQL Server
> * Instalación de la extensión de SQL Server

Pase al siguiente tutorial para aprender a proteger un servidor web IIS con certificados SSL.

> [!div class="nextstepaction"]
> [Protección de un servidor web IIS con certificados SSL](tutorial-secure-web-server.md)

