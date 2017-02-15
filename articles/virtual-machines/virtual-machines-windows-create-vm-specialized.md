---
title: "Creación de una copia de la máquina virtual Windows | Microsoft Docs"
description: "Aprenda a crear una copia de una máquina virtual de Azure especializada que ejecuta Windows con el modelo de implementación de Resource Manager."
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
ms.date: 09/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: a779f084e0ad6de71ad3e2de86a2fb85738b8fe6


---
# <a name="create-a-vm-from-a-specialized-vhd"></a>Creación de máquinas virtuales a partir de un disco duro virtual especializado
Cree una nueva máquina virtual conectando un disco duro virtual especializado como disco del sistema operativo mediante Powershell. Un disco duro virtual especializado mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. 

Si desea crear una máquina virtual a partir de un VHD especializado, consulte [Create a VM from a generalized VHD image](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Creación de una máquina virtual a partir de una imagen de VHD generalizada).

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
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
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

## <a name="create-the-vm-configuration"></a>Creación de la configuración de máquina virtual
Realice la configuración de la máquina virtual para conectar el disco duro virtual copiado como disco duro virtual del sistema operativo.

```powershell
# Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept 
# in a storage account named "myStorageAccount" in a container named "myContainer".
$osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"

# Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

# Add the NIC
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the 
# term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this 
# Windows-based VHD should be attached to the VM as the OS disk.
$osDiskName = $vmName + "osDisk"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


Si tiene discos de datos que necesitan estar conectados a la máquina virtual, también debe agregar lo siguiente: 

```powershell
# Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit 
# Number (Lun).
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

Las direcciones URL del disco de datos y sistema operativo deben tener un aspecto similar al siguiente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Para encontrarla en el portal, diríjase al contenedor de almacenamiento de destino, haga clic en el VHD de datos o del sistema operativo que se ha copiado y copie el contenido de la dirección URL.

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




<!--HONumber=Nov16_HO3-->


