---
title: "Creación de imágenes de máquina virtual personalizadas con Azure PowerShell | Microsoft Docs"
description: "Tutorial: creación de una imagen de máquina virtual personalizada mediante Azure PowerShell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/07/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7001e5df235d65c531b9102f879bde9693c4f853
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Creación de una imagen personalizada de una máquina virtual de Azure mediante PowerShell

Las imágenes personalizadas son como las imágenes de Marketplace, pero las puede crear usted mismo. Las imágenes personalizadas pueden usarse para configuraciones de arranque como la carga previa de aplicaciones, configuraciones de aplicaciones y otras configuraciones del sistema operativo. En este tutorial, creará su propia imagen personalizada de una máquina virtual de Azure. Aprenderá a:

> [!div class="checklist"]
> * Ejecutar Sysprep y generalizar máquinas virtuales
> * Crear una imagen personalizada
> * Crear una imagen personalizada a partir de una máquina virtual
> * Enumerar todas las imágenes en su suscripción
> * Eliminar una imagen

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Antes de empezar

Los siguientes pasos explican cómo tomar una máquina virtual existente y convertirla en una imagen personalizada reutilizable que puede usar para crear nuevas instancias de máquinas virtuales.

Para completar el ejemplo de este tutorial, debe tener una máquina virtual. Si es necesario, este [script de ejemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) puede crear una. Al trabajar en el tutorial, reemplace los nombres de grupo de recursos y máquina virtual cuando proceda.

## <a name="prepare-vm"></a>Preparación de la VM

Para crear una imagen de una máquina virtual, debe preparar la VM generalizando la VM y desasignando y marcando después la VM de origen como generalizada en Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalización de VM con Windows mediante Sysprep

Entre otras características, Sysprep elimina toda la información personal de la cuenta y prepara, entre otras cosas, la máquina para usarse como imagen. Para obtener más información sobre Sysprep, vea [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).


1. Conexión a una máquina virtual.
2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a *%windir%\system32\sysprep* y, después, ejecute *sysprep.exe*.
3. En **Herramienta de preparación del sistema**, seleccione *Iniciar la Configuración rápida (OOBE)* y asegúrese de que la casilla *Generalizar* está activada.
4. En **Opciones de apagado**, seleccione *Apagar* y, a continuación, haga clic en **Aceptar**.
5. Cuando Sysprep finaliza, apaga la máquina virtual. **No reinicie la VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desasignación y marcado de la VM como generalizada

Para crear una imagen, la VM debe desasignarse y estar marcada como generalizada en Azure.

Cancele la asignación de la VM con [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Establezca el estado de la máquina virtual en `-Generalized` mediante [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Crear la imagen

Ahora puede crear una imagen de la VM mediante [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) y [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). En el ejemplo siguiente se crea una imagen denominada *myImage* a partir de la VM llamada *myVM*.

Obtenga la máquina virtual. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Cree la configuración de la imagen.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Cree la imagen.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Creación de VM a partir de la imagen

Ahora que tiene una imagen, puede crear una o varias VM desde la imagen. La creación de una VM a partir de una imagen personalizada es muy similar a la creación de una VM mediante una imagen de Marketplace. Cuando use una imagen de Marketplace, tendrá que proporcionar información sobre la imagen, el proveedor de esa imagen, la oferta, la SKU y la versión. Con una imagen personalizada, solo debe proporcionar el identificador del recurso de imagen personalizada. 

En el siguiente script, se creará una variable *$image* para almacenar información sobre la imagen personalizada mediante [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) y, a continuación, usaremos [conjunto AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) y especificaremos el identificador con la variable *$image* que acabamos de crear. 

El script crea una VM denominada *myVMfromImage* a partir de nuestra imagen personalizada en un nuevo grupo de recursos denominado *myResourceGroupFromImage* en la ubicación del *oeste de EE. UU.*


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="image-management"></a>Administración de imágenes 

Estos son algunos ejemplos de tareas comunes de administración de imágenes y cómo realizarlas mediante PowerShell.

Enumere todas las imágenes por nombre.

```powershell
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Elimine una imagen. Este ejemplo elimina la imagen con el nombre *myOldImage* de *myResourceGroup*.

```powershell
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado una imagen de máquina virtual personalizada. Ha aprendido a:

> [!div class="checklist"]
> * Ejecutar Sysprep y generalizar máquinas virtuales
> * Crear una imagen personalizada
> * Crear una imagen personalizada a partir de una máquina virtual
> * Enumerar todas las imágenes en su suscripción
> * Eliminar una imagen

Avance al siguiente tutorial para obtener información sobre máquinas virtuales de alta disponibilidad.

> [!div class="nextstepaction"]
> [Creación de máquinas virtuales de alta disponibilidad](tutorial-availability-sets.md)



