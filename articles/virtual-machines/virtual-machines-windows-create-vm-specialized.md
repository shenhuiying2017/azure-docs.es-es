---
title: "Creación de una máquina virtual a partir de un disco especializado en Azure | Microsoft Docs"
description: "Cree una nueva máquina virtual asociando un disco administrado especializado o un disco no administrado en el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: cbe3d72bbd0d9cc425b1b26ad412e77b33f385b2


---
# <a name="create-a-vm-from-a-specialized-disk"></a>Creación de una máquina virtual a partir de un disco especializado

Cree una nueva máquina virtual conectando un disco duro virtual especializado como disco del sistema operativo mediante PowerShell. Un disco especializado es una copia del VHD de una máquina virtual existente que mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. Puede usar un [disco administrado](../storage/storage-managed-disks-overview.md) o a un disco no administrado especializado para crear una nueva máquina virtual.

## <a name="before-you-begin"></a>Antes de empezar
Si usa PowerShell, asegúrese de que tiene la versión más reciente del módulo de PowerShell AzureRM.Compute. Ejecute el siguiente comando para instalarlo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Si desea obtener más información, consulte [Azure PowerShell Versioning](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning) (Control de versiones de Azure PowerShell).


## <a name="create-the-subnet-and-vnet"></a>Creación de la subred y la red virtual

Cree la red virtual y la subred de la [red virtual](../virtual-network/virtual-networks-overview.md).

1. Cree la subred. En este ejemplo se crea una subred llamada **mySubNet**, en el grupo de recursos **myResourceGroup** y se establece el prefijo de la dirección de subred en **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Cree la red virtual. En este ejemplo se establece el nombre de la red virtual en **myVnetName**, la ubicación en **Oeste de EE. UU.** y el prefijo de dirección de la red virtual en **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>Creación de una dirección IP pública y una NIC
Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

1. Cree la dirección IP pública. En este ejemplo, el nombre de la dirección IP pública se establece en **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Cree la NIC. En este ejemplo, el nombre de la NIC se establece en **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creación del grupo de seguridad de red y las reglas de RDP
Para poder iniciar sesión en la máquina virtual mediante RDP, debe tener una regla de seguridad que permita el acceso RDP en el puerto 3389. Como el disco duro virtual de la nueva máquina virtual se creó a partir de una máquina virtual especializada existente, después de crear la máquina virtual puede usar una cuenta existente de la máquina virtual de origen que tenga permiso para iniciar sesión mediante RDP.

En este ejemplo se establece el nombre de NSG en **myNsg** y el nombre de la regla de RDP en **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para más información sobre puntos de conexión y reglas NSG, consulte [Apertura de puertos para una máquina virtual en Azure mediante PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="set-the-vm-name-and-size"></a>Establecimiento del nombre de máquina virtual y el tamaño

Este ejemplo establece el nombre de máquina virtual en "myVM" y el tamaño de máquina virtual en "Standard_A2".

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>Agregación de la NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>Configuración del disco del sistema operativo

El sistema operativo especializado podría ser un VHD que ha [cargado en Azure](virtual-machines-windows-upload-image.md) o una [copia del VHD de una máquina virtual de Azure existente](virtual-machines-windows-vhd-copy.md). 

Puede elegir una de las dos opciones:
- **Opción 1**: crear un disco administrado especializado a partir de un VHD en una cuenta de almacenamiento existente que se usará como el disco del sistema operativo.

o 

- **Opción 2**: usar un VHD especializado almacenado en su propia cuenta de almacenamiento (un disco no administrado). 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>Opción 1: crear un disco administrado desde un disco especializado no administrado

1. Cree un disco administrado a partir del VHD especializado de su cuenta de almacenamiento. En este ejemplo se utiliza **myOSDisk1** como nombre del disco, se coloca el disco en el almacenamiento **LRS Estándar** y se utiliza **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** como el URI para el VHD de origen.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd) `
    -ResourceGroupName $rgName
    ```

2. Agregue el disco del sistema operativo a la configuración. Este ejemplo establece el tamaño del disco en **128 GB** y conecta el disco administrado como un disco del sistema operativo **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -ManagedDiskStorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

Opcional: conecte discos administrados adicionales como discos de datos. Esta opción da por hecho que ha creado los discos de datos administrados mediante la [creación de discos de datos administrados](virtual-machines-windows-create-managed-disk-ps.md). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Opción 2: conectar un VHD que se encuentra en una cuenta de almacenamiento existente

1. Establezca el URI para el VHD que desea usar. En este ejemplo, el archivo de VHD denominado **myOsDisk.vhd** se mantiene en una cuenta de almacenamiento llamada **myStorageAccount** en un contenedor que se llama **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Agregue el disco del sistema operativo mediante la dirección URL del VHD del sistema operativo copiado. En este ejemplo, cuando se crea el disco del sistema operativo, el término "osDisk" se agrega al nombre de máquina virtual para crear el nombre del disco de sistema operativo. En este ejemplo también se especifica que este VHD basado en Windows debería asociarse a la máquina virtual como el disco del sistema operativo.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: si tiene discos de datos que necesitan estar conectados a la máquina virtual, agregue los discos de datos usando las direcciones URL de VHD de datos y el número de unidad lógica (LUN) correspondiente.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Cuando se use una cuenta de almacenamiento, las URL de los discos de datos y del sistema operativo tienen un formato similar al siguiente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Para encontrarla en el portal, diríjase al contenedor de almacenamiento de destino, haga clic en el VHD de datos o del sistema operativo que se ha copiado y copie el contenido de la dirección URL.


## <a name="create-the-vm"></a>Creación de la máquina virtual

Cree la máquina virtual con las configuraciones que acaba de crear.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Si este comando es correcto, verá un resultado similar al siguiente:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>Comprobación de que se creó la máquina virtual
Debería ver la máquina virtual recién creada en el [portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Pasos siguientes
Para iniciar sesión en la nueva máquina virtual, examine la máquina virtual en el [Portal](https://portal.azure.com), haga clic en **Conectar**y abra el archivo RDP del Escritorio remoto. Utilice las credenciales de cuenta de la máquina virtual original para iniciar sesión en la nueva máquina virtual. Para más información, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Feb17_HO2-->


