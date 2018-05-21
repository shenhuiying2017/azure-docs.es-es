---
title: 'Tutorial: Creación de imágenes de máquina virtual personalizadas con Azure PowerShell | Microsoft Docs'
description: En este tutorial, aprenderá a usar Azure PowerShell para crear una imagen de máquina virtual personalizada en Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a449c1f9781ffc86de4786eaab3cb83999b86a72
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Tutorial: Creación de una imagen personalizada de una máquina virtual de Azure con Azure PowerShell

Las imágenes personalizadas son como las imágenes de Marketplace, pero las puede crear usted mismo. Las imágenes personalizadas pueden usarse para configuraciones de arranque como la carga previa de aplicaciones, configuraciones de aplicaciones y otras configuraciones del sistema operativo. En este tutorial, creará su propia imagen personalizada de una máquina virtual de Azure. Aprenderá a:

> [!div class="checklist"]
> * Ejecutar Sysprep y generalizar máquinas virtuales
> * Crear una imagen personalizada
> * Crear una imagen personalizada a partir de una máquina virtual
> * Enumerar todas las imágenes en su suscripción
> * Eliminar una imagen

## <a name="before-you-begin"></a>Antes de empezar

Los siguientes pasos explican cómo tomar una máquina virtual existente y convertirla en una imagen personalizada reutilizable que puede usar para crear nuevas instancias de máquinas virtuales.

Para completar el ejemplo de este tutorial, debe tener una máquina virtual. Si es necesario, este [script de ejemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) puede crear una. Al trabajar en el tutorial, reemplace los nombres de grupo de recursos y máquina virtual cuando proceda.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, en este tutorial se requiere la versión 5.7.0 del módulo AzureRM o posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

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

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Establezca el estado de la máquina virtual en `-Generalized` mediante [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Crear la imagen

Ahora puede crear una imagen de la VM mediante [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) y [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). En el ejemplo siguiente se crea una imagen denominada *myImage* a partir de la VM llamada *myVM*.

Obtenga la máquina virtual. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Cree la configuración de la imagen.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Cree la imagen.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Creación de VM a partir de la imagen

Ahora que tiene una imagen, puede crear una o varias VM desde la imagen. Crear una máquina virtual a partir de una imagen personalizada es muy similar a crear una máquina virtual mediante una imagen de Marketplace. Cuando use una imagen de Marketplace, tendrá que proporcionar información sobre la imagen, el proveedor de esa imagen, la oferta, la SKU y la versión. Mediante el parámetro simplificado establecido para el cmdlet [New-AzureRMVM](), basta con que proporcione el nombre de la imagen personalizada, siempre que se encuentre en el mismo grupo de recursos. 

En este ejemplo se crea una máquina virtual denominada *myVMfromImage* a partir de *myImage*, en *myResourceGroup*.


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>Administración de imágenes 

Estos son algunos ejemplos de tareas comunes de administración de imágenes y cómo realizarlas mediante PowerShell.

Enumere todas las imágenes por nombre.

```azurepowershell-interactive
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Elimine una imagen. Este ejemplo elimina la imagen con el nombre *myOldImage* de *myResourceGroup*.

```azurepowershell-interactive
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



