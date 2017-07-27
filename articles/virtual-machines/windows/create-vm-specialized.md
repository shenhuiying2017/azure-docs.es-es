---
title: "Creación de una máquina virtual Windows desde un VHD especializado en Azure | Microsoft Docs"
description: "Cree una nueva máquina virtual Windows asociando un disco administrado especializado como el disco del SO en el modelo de implementación de Resource Manager."
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
ms.date: 06/29/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b315a37f72f34d45bb55c2bbe6be20cca8c42424
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017


---
# <a name="create-a-windows-vm-from-a-specialized-disk"></a>Creación de una máquina virtual Windows a partir de un disco especializado

Cree una máquina virtual mediante la asociación de un disco administrado especializado como el disco del SO mediante Powershell. Un disco especializado es una copia del disco duro virtual (VHD) de una máquina virtual existente que mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. 

Cuando se usa un VHD especializado para crear una máquina virtual, la nueva máquina virtual conserva el nombre de equipo de la máquina virtual original. También se conserva otra información específica del equipo y, en algunos casos, esta información duplicada podría ocasionar problemas. Tenga en cuenta en qué tipo de información específica del equipo se basan las aplicaciones al copiar una máquina virtual.

Tiene dos opciones:
* [Carga de un disco duro virtual](#option-1-upload-a-specialized-vhd)
* [Copia de una máquina virtual de Azure existente](#option-2-copy-an-existing-azure-vm)

En este tema se muestra cómo usar Managed Disks. Si tiene una implementación heredada que requiere el uso de una cuenta de Storage, vea [Create a VM from a specialized VHD in a storage account](sa-create-vm-specialized.md) (Creación de una máquina virtual a partir de un VHD especializado en una cuenta de Storage).

## <a name="before-you-begin"></a>Antes de empezar
Si usa PowerShell, asegúrese de que tiene la versión más reciente del módulo de PowerShell AzureRM.Compute. 

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](/powershell/azure/overview) (Control de versiones de Azure PowerShell).


## <a name="option-1-upload-a-specialized-vhd"></a>Opción 1: Cargar un VHD especializado

Puede cargar el VHD de una máquina virtual especializada creada con una herramienta de virtualización local, como Hyper-V, o de una máquina virtual exportada desde otra nube.

### <a name="prepare-the-vm"></a>Preparación de la VM
Si tiene previsto usar el VHD como está para crear una nueva VM, asegúrese de completar los pasos siguientes. 
  
  * [Preparar de un VHD de Windows para cargar en Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **No** generalice la VM mediante Sysprep.
  * Quite todas las herramientas de virtualización de invitado y los agentes instalados en la VM (es decir, herramientas de VMware).
  * Asegúrese de que la VM se configura para extraer su dirección IP y la configuración de DNS a través de DHCP. Esto garantiza que el servidor obtiene una dirección IP dentro de la red virtual cuando se inicia. 


### <a name="get-the-storage-account"></a>Obtención de la cuenta de almacenamiento
Necesita una cuenta de almacenamiento de Azure para almacenar el VHD cargado. Puede usar una cuenta de almacenamiento existente o crear una nueva. 

Para mostrar las cuentas de almacenamiento disponibles, escriba:

```powershell
Get-AzureRmStorageAccount
```

Si desea utilizar una cuenta de Storage existente, continúe con la sección [Carga del VHD](#upload-the-vhd-to-your-storage-account).

Si necesita crear una nueva cuenta de almacenamiento, siga estos pasos:

1. Necesita el nombre del grupo de recursos donde se debe crear la cuenta de almacenamiento. Para averiguar todos los grupos de recursos que están en la suscripción, escriba:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Para crear un grupo de recursos denominado *myResourceGroup* en la región *Oeste de EE. UU.*, escriba:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Cree una cuenta de almacenamiento denominada *mystorageaccount* en este grupo de recursos con el cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount):
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Carga del VHD en la cuenta de almacenamiento 
Use el cmdlet [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) para cargar el VHD en un contenedor de su cuenta de almacenamiento. En este ejemplo se carga el archivo *myVHD.vhd* de `"C:\Users\Public\Documents\Virtual hard disks\"` para una cuenta de almacenamiento denominada *mystorageaccount* en el grupo de recursos *myResourceGroup*. El archivo se almacena en el contenedor llamado *mycontainer* y el nuevo nombre de archivo será *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName -Destination $urlOfUploadedVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Si se realiza correctamente, obtendrá una respuesta similar a la siguiente:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependiendo de la conexión de red y del tamaño del archivo VHD, este comando tardará algún tiempo en completarse.

### <a name="create-a-managed-disk-from-the-vhd"></a>Creación de un disco administrado a partir del VHD

Cree un disco administrado a partir del VHD especializado en la cuenta de Storage mediante [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). En este ejemplo se usa **myOSDisk1** como nombre del disco, se coloca el disco en el almacenamiento *StandardLRS* y se usa *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* como el URI para el VHD de origen.

Cree un grupo de recursos para la máquina virtual.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location -Name $destinationResourceGroup
```

Cree el disco del SO a partir del VHD cargado. 

```powershell
$sourceUri = https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType StandardLRS  -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-2-copy-an-existing-azure-vm"></a>Opción 2: Copiar una máquina virtual de Azure existente

Puede crear una copia de una máquina virtual que usa Managed Disks mediante la realización de una instantánea de la máquina virtual, para usarla después para crear un disco administrado y una máquina virtual.


### <a name="take-a-snapshot-of-the-os-disk"></a>Realización de la instantánea del disco del SO

Puede realizar una instantánea de una máquina virtual completa (incluidos todos los discos) o solo de un disco. En los pasos siguientes se describe cómo realizar una instantánea solo del disco del SO de la máquina virtual mediante el cmdlet [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Configure algunos parámetros. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Obtenga el objeto de VM.

```powershell
$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
```
Obtenga el nombre del disco del SO.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $vm.StorageProfile.OsDisk.Name
```

Cree la configuración de la instantánea. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -OsType Windows -CreateOption Copy -Location $location 
```

Tome la instantánea.

```powershell
$snapShot = New-AzureRmSnapshot -Snapshot $snapshotConfig -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName
```


Si tiene previsto utilizar la instantánea para crear una máquina virtual que precisa de un alto rendimiento, use el parámetro `-AccountType Premium_LRS` con el comando New-AzureRmSnapshot. El parámetro crea la instantánea para que se almacene como disco administrado Premium. Managed Disks Premium son más costosos que los Estándar. Por lo tanto, asegúrese de que realmente necesita discos Premium antes de usar el parámetro.

### <a name="create-a-new-disk-from-the-snapshot"></a>Creación de un disco a partir de la instantánea

Cree un disco administrado a partir de la instantánea mediante [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Este ejemplo usa *myOSDisk* para el nombre del disco.

Cree un grupo de recursos para la máquina virtual.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location -Name $destinationResourceGroup
```

Defina el nombre del disco del SO. 

```powershell
$osDiskName = 'myOsDisk'
```

Cree el disco administrado.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Creación de la máquina virtual 

Cree recursos de red y otros recursos de máquina virtual para que los use la nueva máquina virtual.

### <a name="create-the-subnet-and-vnet"></a>Creación de la subred y la red virtual

Cree la red virtual y la subred de la [red virtual](../../virtual-network/virtual-networks-overview.md).

Cree la subred. En este ejemplo se crea una subred llamada **mySubNet**, en el grupo de recursos **myDestinationResourceGroup** y se establece el prefijo de la dirección de subred en **10.0.0.0/24**.
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
```

Cree la red virtual. En este ejemplo se establece el nombre de la red virtual en **myVnetName**, la ubicación en **Oeste de EE. UU.** y el prefijo de dirección de la red virtual en **10.0.0.0/16**. 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $destinationResourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creación del grupo de seguridad de red y las reglas de RDP
Para poder iniciar sesión en la máquina virtual mediante RDP, debe tener una regla de seguridad que permita el acceso RDP en el puerto 3389. Como el disco duro virtual de la nueva máquina virtual se creó a partir de una máquina virtual especializada existente, puede usar una cuenta de la máquina virtual de origen para RDP.

En este ejemplo se establece el nombre de NSG en **myNsg** y el nombre de la regla de RDP en **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $destinationResourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para más información sobre puntos de conexión y reglas NSG, consulte [Apertura de puertos para una máquina virtual en Azure mediante PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Creación de una dirección IP pública y una NIC
Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

Cree la dirección IP pública. En este ejemplo, el nombre de la dirección IP pública se establece en **myIP**.
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $destinationResourceGroup -Location $location `
   -AllocationMethod Dynamic
```       

Cree la NIC. En este ejemplo, el nombre de la NIC se establece en **myNicName**.
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $destinationResourceGroup `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>Establecimiento del nombre de VM y el tamaño

En este ejemplo se establece el nombre de la máquina virtual como *myVM* y el tamaño de la máquina virtual en *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Incorporación de la NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Adición del disco del SO 

Agregue el disco del SO a la configuración mediante [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk). Este ejemplo establece el tamaño del disco en *128 GB* y adjunta el disco administrado como un disco del SO *Windows*.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Finalización de la máquina virtual 

Cree la máquina virtual mediante [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm), con las configuraciones que se acaban de crear.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Si este comando es correcto, verá un resultado similar al siguiente:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Comprobación de que se creó la máquina virtual
Debería ver la máquina virtual recién creada en el [portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Pasos siguientes
Para iniciar sesión en la nueva máquina virtual, examine la máquina virtual en el [Portal](https://portal.azure.com), haga clic en **Conectar**y abra el archivo RDP del Escritorio remoto. Utilice las credenciales de cuenta de la máquina virtual original para iniciar sesión en la nueva máquina virtual. Para más información, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](connect-logon.md).


