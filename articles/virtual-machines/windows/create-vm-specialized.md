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
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 78f993ce9bab6266479cdd121eeea4965724d9bd
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-vm-from-a-specialized-disk"></a>Creación de una máquina virtual a partir de un disco especializado

Cree una nueva máquina virtual conectando un disco duro virtual especializado como disco del sistema operativo mediante PowerShell. Un disco especializado es una copia del VHD de una máquina virtual existente que mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. Puede usar un [disco administrado](../../storage/storage-managed-disks-overview.md) o a un disco no administrado especializado para crear una nueva máquina virtual.

## <a name="before-you-begin"></a>Antes de empezar
Si usa PowerShell, asegúrese de que tiene la versión más reciente del módulo de PowerShell AzureRM.Compute. Ejecute el siguiente comando para instalarla.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Si desea obtener más información, consulte [Azure PowerShell Versioning](/powershell/azure/overview) (Control de versiones de Azure PowerShell).


## <a name="create-the-subnet-and-vnet"></a>Creación de la subred y la red virtual

Cree la red virtual y la subred de la [red virtual](../../virtual-network/virtual-networks-overview.md).

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
Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

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

Para más información sobre puntos de conexión y reglas NSG, consulte [Apertura de puertos para una máquina virtual en Azure mediante PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="set-the-vm-name-and-size"></a>Establecimiento del nombre de VM y el tamaño

Este ejemplo establece el nombre de VM en "myVM" y el tamaño de VM en "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>Incorporación de la NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>Configuración del disco del SO

El SO especializado podría ser un VHD que ha [cargado en Azure](upload-image.md) o una [copia del VHD de una VM de Azure existente](vhd-copy.md). 

Puede elegir una de las dos opciones:
- **Opción 1**: crear un disco administrado especializado a partir de un VHD en una cuenta de almacenamiento existente que se usará como el disco del SO.

o 

- **Opción 2**: usar un VHD especializado almacenado en su propia cuenta de almacenamiento (un disco no administrado). 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>Opción 1: crear un disco administrado desde un disco especializado no administrado

1. Cree un disco administrado a partir del VHD especializado de su cuenta de almacenamiento. En este ejemplo se usa **myOSDisk1** como nombre del disco, se coloca el disco en el almacenamiento **StandardLRS** y se usa **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** como el URI para el VHD de origen.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreateOption Import `
    -SourceUri https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd) `
    -ResourceGroupName $rgName
    ```

2. Agregue el disco del SO a la configuración. Este ejemplo establece el tamaño del disco en **128 GB** y adjunta el disco administrado como un disco del SO **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

Opcional: conecte discos administrados adicionales como discos de datos. Esta opción da por hecho que ha creado los discos de datos administrados mediante la [creación de discos de datos administrados](create-managed-disk-ps.md). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Opción 2: conectar un VHD que se encuentra en una cuenta de almacenamiento existente

1. Establezca el URI para el VHD que desea usar. En este ejemplo, el archivo de VHD denominado **myOsDisk.vhd** se mantiene en una cuenta de almacenamiento llamada **myStorageAccount** en un contenedor denominado **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Agregue el disco del SO mediante la dirección URL del VHD del SO copiado. En este ejemplo, cuando se crea el disco del SO, el término "osDisk" se agrega al nombre de VM para crear el nombre del disco de SO. En este ejemplo también se especifica que este VHD basado en Windows debería asociarse a la VM como el disco del SO.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: si tiene discos de datos que necesitan estar conectados a la VM, agregue los discos de datos mediante el uso de las direcciones URL de VHD de datos y el número de unidad lógica (LUN) correspondiente.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Cuando se use una cuenta de almacenamiento, las direcciones URL de los discos de datos y del sistema operativo tienen un formato similar al siguiente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Para encontrarla en el portal, diríjase al contenedor de almacenamiento de destino, haga clic en el VHD de datos o del sistema operativo que se ha copiado y copie el contenido de la dirección URL.


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
Para iniciar sesión en la nueva máquina virtual, examine la máquina virtual en el [Portal](https://portal.azure.com), haga clic en **Conectar**y abra el archivo RDP del Escritorio remoto. Utilice las credenciales de cuenta de la máquina virtual original para iniciar sesión en la nueva máquina virtual. Para más información, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](connect-logon.md).


