---
title: "Creación de una VM a partir de una imagen de VM administrada en Azure | Microsoft Docs"
description: "Cree una máquina virtual con Windows a partir de una imagen de VM administrada generalizada con Azure PowerShell en el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
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
ms.date: 02/7/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: f6fdbfcccd0bb44643d79ce866a3fa96410b298e


---
# <a name="create-a-vm-from-a-generalized-managed-vm-image"></a>Creación de una VM a partir de una imagen de VM administrada generalizada

Puede crear varias VM a partir de una imagen de VM administrada en Azure. Una imagen de VM administrada contiene la información necesaria para crear una VM, incluidos los discos del SO y de datos. Los VHD que componen la imagen, incluidos los discos del SO y los discos de datos, se almacenan como discos administrados. 

La información de cuenta personal se ha eliminado de una VM generalizada mediante [Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Puede crear una VM generalizada mediante la ejecución de Sysprep en una VM local, la [carga del VHD en Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o la ejecución de Sysprep en una VM de Azure existente y, luego, la [captura de una imagen de la VM](virtual-machines-windows-capture-image-resource.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="prerequisites"></a>Requisitos previos

Debe tener ya [creada una imagen de VM administrada](virtual-machines-windows-capture-image-resource.md) para usarla al crear la nueva VM. 

Asegúrese de que tiene la última versión del módulo AzureRM.Compute de PowerShell. Ejecute el siguiente comando para realizar la instalación.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning) (Control de versiones de Azure PowerShell).



## <a name="collect-information-about-the-image"></a>Recopilación de información sobre la imagen

Primero se debe recopilar información básica sobre la imagen y crear una variable para la imagen. En este ejemplo se usa una imagen de VM administrada denominada **myImage** que se encuentra en el grupo de recursos **myResourceGroup** en la ubicación **Centro occidental de EE. UU.** 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>Crear una red virtual
Cree la red virtual y la subred de la [red virtual](../virtual-network/virtual-networks-overview.md).

1. Cree la subred. Este ejemplo crea una subred denominada **mySubnet** con el prefijo de dirección **10.0.0.0/24**.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Creación de la red virtual. Este ejemplo crea una red virtual denominada **myVnet** con el prefijo de dirección **10.0.0.0/16**.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Creación de una dirección IP y una interfaz de red

Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

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

Para poder iniciar sesión en la VM mediante RDP, debe tener una regla de seguridad de red (NSG) que permita el acceso RDP en el puerto 3389. 

Este ejemplo crea un NSG denominado"**myNsg**" que contiene una regla llamada "**myRdpRule**" que permita el tráfico RDP en el puerto 3389. Para obtener más información sobre NSG, consulte [Apertura de puertos para una máquina virtual en Azure mediante PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
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

## <a name="get-the-credentials-for-the-vm"></a>Obtención de las credenciales para la VM

El siguiente cmdlet abrirá una ventana en la que escribirá un nuevo nombre de usuario y una contraseña que se usará como la cuenta de administrador local para tener acceso remoto a la VM. 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Definición de las variables para el nombre de VM, el nombre del equipo y el tamaño de la VM

1. Cree variables para el nombre de VM y el nombre del equipo. Este ejemplo establece el nombre de VM como **myVM** y el nombre del equipo como **myComputer**.

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. Establezca el tamaño de la máquina virtual. Este ejemplo crea la VM con el tamaño **Standard_DS1_v2**. Consulte la documentación sobre los [tamaños de VM](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) para más información.

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. Agregue el nombre y el tamaño de la VM a la configuración de la VM.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Establecimiento de la imagen de VM como imagen de origen para la nueva VM

Establezca la imagen de origen con el identificador de la imagen de VM administrada.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Establezca la configuración del SO y agregue la NIC.

Escriba el tipo de almacenamiento (PremiumLRS o StandardLRS) y el tamaño del disco del SO. Este ejemplo establece el tipo de cuenta en **PremiumLRS**, el tamaño del disco en **128 GB** y el almacenamiento en caché en disco en **ReadWrite**.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -ManagedDiskStorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Creación de la máquina virtual

Cree la nueva VM utilizando la configuración creada y almacenada en la variable **$vm**.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Comprobación de que se creó la máquina virtual
Cuando finalice, debería ver la máquina virtual recién creada en el [Azure Portal](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Pasos siguientes
Para administrar la nueva máquina virtual de Azure PowerShell, consulte [Administración de máquinas virtuales de Azure con Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Feb17_HO2-->


