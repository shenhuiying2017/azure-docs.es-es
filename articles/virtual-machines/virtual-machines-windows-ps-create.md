---
title: "Creación de una máquina virtual de Azure con PowerShell | Microsoft Docs"
description: "Use Azure PowerShell y Azure Resource Manager para crear fácilmente una máquina virtual con Windows Server."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.topic: get-started-article
ms.date: 02/14/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 8d8dfb9b165d82e8567f6b5577d46d562f9f8db3
ms.openlocfilehash: 89e306d3e3312531878da088575c7429a941d34f
ms.lasthandoff: 02/23/2017

---

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Creación de una máquina virtual de Windows con Resource Manager y PowerShell

En este artículo se muestra cómo crear rápidamente una máquina virtual de Azure que ejecuta Windows Server y los recursos que necesita mediante [Resource Manager](../azure-resource-manager/resource-group-overview.md) y Azure PowerShell.  

Todos los pasos de este artículo son necesarios para crear una máquina virtual; se tardará unos 30 minutos en realizarlos. Reemplace los valores de parámetros de ejemplo en los comandos por nombres que tengan sentido para su entorno.

## <a name="step-1-install-azure-powershell"></a>Paso 1: Instalación de Azure PowerShell

Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para obtener más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta.

> [!NOTE]
> Puede que deba volver a instalar Azure PowerShell para usar la funcionalidad de este artículo. Las funcionalidades de Managed Disks están en la versión 3.5 y superior.
> 
> 

## <a name="step-2-create-a-resource-group"></a>Paso 2: Creación de un grupo de recursos

Todos los recursos deben encontrarse en un grupo de recursos, así que es lo primero que hay que crear.  

1. Obtenga una lista de las ubicaciones donde se pueden crear los recursos.
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Establezca la ubicación de los recursos. Este comando establece la ubicación en **centralus**.
   
    ```powershell
    $location = "centralus"
    ```

3. Cree un grupo de recursos. Este comando crea el grupo de recursos denominado **myResourceGroup** en la ubicación que estableció.
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-optional-create-a-storage-account"></a>Paso 3: (Opcional) Creación de una cuenta de almacenamiento

Actualmente, al crear una máquina virtual, puede elegir usar [Azure Managed Disks](../storage/storage-managed-disks-overview.md) o discos no administrados. Si elige un disco no administrado, tiene que crear una [cuenta de almacenamiento](../storage/storage-introduction.md) para almacenar el disco duro virtual que usará la máquina virtual que cree. Si decide usar un disco administrado, no es necesaria la cuenta de almacenamiento. Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.

1. Pruebe si el nombre de la cuenta de almacenamiento es único. Este comando comprueba el nombre **myStorageAccount**.
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    Si este comando devuelve **True**, el nombre propuesto es único en Azure. 

2. Ahora, cree la cuenta de almacenamiento.
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>Paso 4: Creación de una red virtual

Todas las máquinas virtuales forman parte de una [red virtual](../virtual-network/virtual-networks-overview.md).

1. Cree una subred para la red virtual. Este comando crea una subred denominada **mySubnet** con un prefijo de dirección 10.0.0.0/24.
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```

2. Ahora cree la red virtual. Este comando crea una red virtual denominada **myVnet** con la subred que creó y un prefijo de dirección **10.0.0.0/16**.
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Paso 5: Creación de una dirección IP y una interfaz de red

Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

1. Cree la dirección IP pública. Este comando crea una dirección IP pública denominada **myPublicIp** con un método de asignación **dinámico**.
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. Cree la interfaz de red. Este comando crea una interfaz de red denominada **myNIC**.
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>Paso 6: Creación de una máquina virtual

Ahora que tiene todas las piezas en su sitio, es el momento de crear la máquina virtual. Puede crear una máquina virtual con una [imagen de Marketplace](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), una [imagen generalizada personalizada (preparada con Sysprep)](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), o una [imagen especializada personalizada (no preparada con Sysprep)](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este ejemplo utiliza una imagen de Windows Server de Marketplace. 

1. Ejecute este comando para establecer el nombre de la cuenta de administrador y la contraseña de la máquina virtual.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    La contraseña tiene que tener de 12 a 123 caracteres y contener al menos una minúscula, una mayúscula, un número y un carácter especial.

2. Cree el objeto de configuración de la máquina virtual. Este comando crea un objeto de configuración denominado **myVmConfig** que define el nombre y el tamaño de la máquina virtual.
   
    ```powershell
    $myVM = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    Consulte [Tamaños de las máquinas virtuales Linux en Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ver una lista de los tamaños disponibles para una máquina virtual.

3. Configure el sistema operativo para la máquina virtual. Este comando establece el nombre del equipo, el tipo de sistema operativo y las credenciales de cuenta para la máquina virtual.
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Defina la imagen que se va a usar para aprovisionar la máquina virtual. Este comando define la imagen de Windows Server que se usará para la máquina virtual. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```

5. Agregue la interfaz de red que creó a la configuración.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```

6. Si usa un disco no administrado, ejecute este comando para definir el nombre y la ubicación del disco duro de la máquina virtual; en caso contrario, omita este paso. El archivo de disco duro virtual para los discos no administrados se almacena en un contenedor. Este comando crea el disco en un contenedor denominado **vhds/myOsDisk1.vhd** en la cuenta de almacenamiento que ha creado.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```

7. Agregue la información del disco de sistema operativo a la configuración de máquina virtual. Este comando crea un disco llamado **myOsDisk1**.
   
    Si usa un disco administrado, ejecute este comando para establecer el disco del sistema operativo en la configuración:

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
    ```

    Si usa un disco no administrado, ejecute este comando para establecer el disco del sistema operativo en la configuración:

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```

8. Finalmente, cree la máquina virtual.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Pasos siguientes

* Si se produjeron problemas durante la implementación, el paso siguiente sería consultar [Solución de errores comunes de implementación de Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
* Aprenda a administrar la máquina virtual que ha creado. Para ello, consulte [Administración de máquinas virtuales con Azure Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aproveche las ventajas de usar una plantilla para crear una máquina virtual con la información que se muestra en [Creación de una máquina virtual Windows con una plantilla de Resource Manager](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


