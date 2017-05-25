---
title: "Creación de una máquina virtual a partir de un VHD generalizado | Microsoft Docs"
description: "Aprenda a crear y configurar una máquina virtual Windows a partir de una imagen de VHD generalizado en una cuenta de almacenamiento con Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: b4808871-9ef1-49ea-a617-9154d417abb0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 537f9878a22a90a55f30b0b4cee766017e753f37
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017


---
# <a name="create-a-vm-from-a-generalized-vhd-image-in-a-storage-account"></a>Creación una máquina virtual a partir de una imagen VHD generalizado en una cuenta de almacenamiento 

Este tema trata la creación de una máquina virtual desde un disco no administrado generalizado que se encuentra en una cuenta de almacenamiento. Una imagen de VHD generalizado tiene toda la información de cuenta personal elimina con [Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Puede crear un VHD generalizado ejecutando Sysprep en una máquina virtual local, [cargando el VHD en Azure](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o ejecutando Sysprep en una máquina virtual de Azure existente, y [copiando el VHD](vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Si desea crear una máquina virtual a partir de un VHD especializado en una cuenta de almacenamiento, consulte [Crear una VM a partir de un VHD especializado](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obtener información sobre el uso de discos administrados en lugar de discos en una cuenta de almacenamiento, consulte los artículos sobre cómo [crear una imagen de máquina virtual administrada](capture-image-resource.md) y [una máquina virtual a partir de una imagen administrada](create-vm-generalized-managed.md).

## <a name="prerequisites"></a>Requisitos previos
Si va a usar un VHD cargado desde una máquina virtual local, como la que creó con Hyper-V, debe asegurarse de que siguió las instrucciones de [Preparación de un disco duro virtual de Windows para cargar en Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Los VHD cargados y los de máquina virtual de Azure existentes deben generalizarse para poder crear una máquina virtual con este método. Para obtener más información, consulte el artículos obre cómo [generalizar una máquina virtual Windows con Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="set-the-uri-of-the-vhd"></a>Establecimiento del URI del VHD

El URI para el VHD que se usará está en el formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. En este ejemplo, el VHD denominado "**myVHD**" es la cuenta de almacenamiento **mystorageaccount** del contenedor **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Crear una red virtual
Cree la red virtual y la subred de la [red virtual](../../virtual-network/virtual-networks-overview.md).

1. Cree la subred. El ejemplo siguiente crea una subred denominada "**mySubnet**" en el grupo de recursos "**myResourceGroup**" con el prefijo de dirección **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Creación de la red virtual. El ejemplo siguiente crea una red virtual denominada "**myVnet**" en la ubicación **oeste de EE. UU.** con el prefijo de dirección **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Creación de una dirección IP y una interfaz de red
Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

1. Cree una dirección IP pública. Este ejemplo crea una dirección IP pública denominada "**myPip**". 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Cree la NIC. Este ejemplo crea una NIC denominada "**myNic**". 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creación del grupo de seguridad de red y las reglas de RDP
Para poder iniciar sesión en la máquina virtual mediante RDP, debe tener una regla de seguridad que permita el acceso RDP en el puerto 3389. 

Este ejemplo crea un NSG denominado"**myNsg**" que contiene una regla llamada "**myRdpRule**" que permita el tráfico RDP en el puerto 3389. Para obtener más información sobre NSG, consulte [Apertura de puertos para una máquina virtual en Azure mediante PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Creación de una variable para la red virtual
Cree una variable para la red virtual completada. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Creación de la máquina virtual
En el siguiente script de PowerShell se muestra cómo establecer las configuraciones de máquina virtual y usar la imagen de máquina virtual cargada como origen para la nueva instalación.

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Comprobación de que se creó la máquina virtual
Cuando finalice, debería ver la máquina virtual recién creada en el [Azure Portal](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Pasos siguientes
Para administrar máquinas virtuales con Azure PowerShell, consulte [Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


