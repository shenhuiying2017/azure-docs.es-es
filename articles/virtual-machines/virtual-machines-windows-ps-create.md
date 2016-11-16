---
title: "Creación de una máquina virtual de Azure con PowerShell | Microsoft Docs"
description: "Use Azure PowerShell y Azure Resource Manager para crear fácilmente una nueva máquina virtual con Windows Server."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6a78e83d84df9bdd4fedd9c90aa02dc26e9d94c9


---
# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Creación de una máquina virtual de Windows con Resource Manager y PowerShell
En este artículo se muestra cómo crear rápidamente una máquina virtual de Azure con Windows Server y los recursos que necesita mediante [Resource Manager](../azure-resource-manager/resource-group-overview.md) y PowerShell. 

Todos los pasos de este artículo son necesarios para crear una máquina virtual; se tardará unos 30 minutos en realizarlos. Reemplace los valores de parámetros de ejemplo en los comandos por nombres que tengan sentido para su entorno.

## <a name="step-1-install-azure-powershell"></a>Paso 1: Instalación de Azure PowerShell
Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta.

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

## <a name="step-3-create-a-storage-account"></a>Paso 3: Creación de una cuenta de almacenamiento
Se necesita una [cuenta de almacenamiento](../storage/storage-introduction.md) para almacenar el disco duro virtual que se usa con la máquina virtual que cree. Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.

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
Ahora que tiene todas las piezas en su sitio, es el momento de crear la máquina virtual.

1. Ejecute este comando para establecer el nombre de la cuenta de administrador y la contraseña de la máquina virtual.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    La contraseña tiene que tener de 12 a 123 caracteres y contener al menos una minúscula, una mayúscula, un número y un carácter especial. 
2. Cree el objeto de configuración de la máquina virtual. Este comando crea un objeto de configuración denominado **myVmConfig** que define el nombre y el tamaño de la máquina virtual.
   
    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    Consulte [Tamaños de las máquinas virtuales Linux en Azure](virtual-machines-windows-sizes.md) para ver una lista de los tamaños disponibles para una máquina virtual.
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
   
    Para más información sobre cómo seleccionar las imágenes que se van a usar, consulte [Navegación y selección de las imágenes de máquina virtual Windows en Azure con PowerShell o CLI](virtual-machines-windows-cli-ps-findimage.md).
5. Agregue la interfaz de red que creó a la configuración.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
6. Defina el nombre y la ubicación del disco duro de la máquina virtual. El archivo de disco duro virtual se almacena en un contenedor. Este comando crea el disco en un contenedor denominado **vhds/WindowsVMosDisk.vhd** en la cuenta de almacenamiento que ha creado.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
7. Agregue la información del disco de sistema operativo a la configuración de máquina virtual. Reemplace el valor de **$diskName** por el nombre del disco del sistema operativo. Cree la variable y agregue la información del disco a la configuración.
   
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
8. Finalmente, cree la máquina virtual.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Pasos siguientes
* Si se han producido problemas durante la implementación, el paso siguiente será consultar [Solución de problemas de implementaciones de grupo de recursos con Azure Portal](../resource-manager-troubleshoot-deployments-portal.md)
* Aprenda a administrar la máquina virtual que ha creado. Para ello, consulte [Administración de máquinas virtuales con Azure Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).
* Aproveche las ventajas de usar una plantilla para crear una máquina virtual con la información que se muestra en [Creación de una máquina virtual Windows con una plantilla de Resource Manager](virtual-machines-windows-ps-template.md)




<!--HONumber=Nov16_HO2-->


