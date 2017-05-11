---
title: "Tutorial de conjuntos de disponibilidad para máquinas virtuales de Windows en Azure | Microsoft Docs"
description: "Obtenga información sobre los conjuntos de disponibilidad para máquinas virtuales de Windows en Azure."
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 831c55939ad3673aa8e44f165e18e826f64b54cc
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>Cómo usar conjuntos de disponibilidad

En este tutorial, aprenderá a aumentar la disponibilidad de sus máquinas virtuales al colocarlas en un grupo lógico denominado conjunto de disponibilidad. Al crear máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma de Azure las distribuye por la infraestructura subyacente. Si se produce un error de hardware o se realiza un mantenimiento planeado en la plataforma, el uso de conjuntos de disponibilidad garantiza que al menos una máquina virtual siga ejecutándose.

Se pueden completar los pasos de este tutorial con la versión más reciente del módulo [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

## <a name="availability-set-overview"></a>Información general sobre conjuntos de disponibilidad

Las máquinas virtuales se pueden crear en agrupaciones lógicas de hardware en el centro de datos de Azure subyacente. Cuando se crean dos o más máquinas virtuales, los recursos de cálculo y almacenamiento se distribuyen por el hardware, por ejemplo en servidores, conmutadores de red y almacenamiento. Esta distribución mantiene la disponibilidad de la aplicación en el caso de que un componente de hardware deba someterse a mantenimiento. Los conjuntos de disponibilidad permiten definir esta agrupación lógica.

Los conjuntos de disponibilidad proporcionan alta disponibilidad a las máquinas virtuales. Además, debe asegurarse de que las aplicaciones también están diseñadas para tolerar interrupciones o eventos de mantenimiento.

## <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad

Puede crear un conjunto de disponibilidad con [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). En este ejemplo, se establece el número de dominios de actualización y error en *2* para el conjunto de disponibilidad denominado *myAvailabilitySet* en el grupo de recursos *myResourceGroupAvailability*.


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Creación de VM dentro de un conjunto de disponibilidad

Las máquinas virtuales deben crearse en el conjunto de disponibilidad para asegurarse de que se distribuyan correctamente en el hardware. No se puede agregar una máquina virtual existente a un conjunto de disponibilidad después de crearla. 

El hardware de una ubicación está dividido en varios dominios de actualización y de error. Un **dominio de actualización** es un grupo de máquinas virtuales y hardware físico subyacente que se pueden reiniciar al mismo tiempo. Las máquinas virtuales en el mismo **dominio de error** comparten un almacenamiento común, así como una fuente de alimentación y un conmutador de red comunes. 

Cuando se crea una VM mediante [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), se especifica el conjunto de disponibilidad mediante el parámetro `-AvailabilitySetId` para especificar el identificador del conjunto de disponibilidad.

Cree 2 VM con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) en el conjunto de disponibilidad.

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

Se tarda unos minutos en crear y configurar ambas VM. Cuando se acabe, tendrá 2 máquinas virtuales distribuidas en el hardware subyacente. 

## <a name="check-for-available-vm-sizes"></a>Comprobación de los tamaños de VM disponibles 

Se pueden agregar más máquinas virtuales al conjunto de disponibilidad posteriormente, pero debe saber qué tamaños de máquina virtual están disponibles en el hardware. Use [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) para enumerar todos los tamaños disponibles en el clúster de hardware para el conjunto de disponibilidad.

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a crear un conjunto de escalado de máquinas virtuales. Avance al siguiente tutorial para informarse sobre los conjuntos de escalado de máquinas virtuales.

[Creación de un conjunto de escalado de máquinas virtuales](tutorial-create-vmss.md)



