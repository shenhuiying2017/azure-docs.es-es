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
ms.date: 05/08/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 022396a8bf0478414be179b9f7341a459ed2bc60
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-use-availability-sets"></a>Cómo usar conjuntos de disponibilidad

En este tutorial, obtendrá información sobre cómo aumentar la disponibilidad y confiabilidad de las soluciones de máquina virtual en Azure mediante una funcionalidad denominada "conjuntos de disponibilidad". Los conjuntos de disponibilidad garantizan que las máquinas virtuales implementadas en Azure se distribuyan entre varios clústeres de hardware aislados. De este modo, se asegura de que, si se produce un error de hardware o software en Azure, solo un subconjunto de las máquinas virtuales se verá afectado, y que la solución estará disponible y en funcionamiento para los clientes. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un conjunto de disponibilidad
> * Crear una máquina virtual en un conjunto de disponibilidad
> * Comprobar los tamaños de máquina virtual disponibles

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="availability-set-overview"></a>Información general sobre conjuntos de disponibilidad

Un conjunto de disponibilidad es una funcionalidad de agrupación lógica que puede usar en Azure para asegurarse de que los recursos de máquina virtual que coloque en dicho conjunto de disponibilidad estén aislados entre sí cuando se implementen en un centro de datos de Azure. Azure garantiza que las máquinas virtuales colocados en un conjunto de disponibilidad se ejecuten en varios servidores físicos, grupos de proceso, unidades de almacenamiento y conmutadores de red. De este modo, se garantiza que si se produce un error de hardware o software de Azure, solo un subconjunto de las máquinas virtuales se verá afectado, y que la aplicación se mantendrá actualizada y seguirá estando disponible para los clientes. Usar conjuntos de disponibilidad es una funcionalidad fundamental que debe tener en cuenta para crear soluciones en la nube confiables.

Veamos una solución basada en máquina virtual típica en la podría haber 4 servidores web front-end y usar 2 máquinas virtuales de back-end que hospedan una base de datos. Con Azure, desea definir 2 conjuntos de disponibilidad antes de implementar las máquinas virtuales: un conjunto de disponibilidad para el nivel de "web" y otro para el nivel de "base de datos". Al crear una máquina virtual, podrá especificar el conjunto de disponibilidad como un parámetro en el comando az vm create, y Azure garantizará automáticamente que las máquinas virtuales que cree en el conjunto de disponibilidad estén aisladas en varios recursos de hardware físico. Esto significa que si el hardware físico que está ejecutando una de sus máquinas virtuales del servidor web o del servidor de base de datos tiene un problema, sabrá que las demás instancias de las máquinas virtuales del servidor de base de datos y del servidor web seguirán ejecutándose correctamente porque están en un hardware diferente.

Siempre debe utilizar los conjuntos de disponibilidad cuando quiera implementar soluciones basadas en máquinas virtuales confiables en Azure.

## <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad

Puede crear un conjunto de disponibilidad con [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). En este ejemplo, se establece el número de dominios de actualización y error en *2* para el conjunto de disponibilidad denominado *myAvailabilitySet* en el grupo de recursos *myResourceGroupAvailability*.

Cree un grupo de recursos.

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```


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

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear un conjunto de disponibilidad
> * Crear una máquina virtual en un conjunto de disponibilidad
> * Comprobar los tamaños de máquina virtual disponibles

Avance al siguiente tutorial para informarse sobre los conjuntos de escalado de máquinas virtuales.

> [!div class="nextstepaction"]
> [Creación de un conjunto de escalado de máquinas virtuales](tutorial-create-vmss.md)



