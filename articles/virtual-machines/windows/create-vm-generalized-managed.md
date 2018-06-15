---
title: Creación de una VM a partir de una imagen administrada en Azure | Microsoft Docs
description: Cree una máquina virtual con Windows a partir de una imagen administrada generalizada con Azure PowerShell o Azure Portal en el modelo de implementación de Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239062"
---
# <a name="create-a-vm-from-a-managed-image"></a>Creación de una máquina virtual a partir de una imagen administrada

Puede crear varias VM a partir de una imagen de VM administrada con PowerShell o Azure Portal. Una imagen de VM administrada contiene la información necesaria para crear una VM, incluidos los discos del SO y de datos. Los VHD que componen la imagen, incluidos los discos del SO y los discos de datos, se almacenan como discos administrados. 

Debe tener ya [creada una imagen de VM administrada](capture-image-resource.md) para usarla al crear la nueva VM. 

## <a name="use-the-portal"></a>Uso del portal

1. Abra el [Azure Portal](https://portal.azure.com).
2. En el menú izquierdo, seleccione **Todos los recursos**. Puede ordenar los recursos por **Tipo** para localizar fácilmente sus imágenes.
3. Seleccione la imagen que desea usar en la lista. Se abre la página **Información general** de la imagen.
4. Haga clic en **+ Crear VM** en el menú.
5. Escriba la información de la máquina virtual. El nombre de usuario y la contraseña que especifique aquí se usarán para iniciar sesión en la máquina virtual. Cuando haya terminado, haga clic en **Aceptar**. Puede crear la nueva máquina virtual en un grupo de recursos existente, o bien elegir **Crear nuevo** para crear un nuevo grupo de recursos para almacenar la máquina virtual.
6. Seleccione un tamaño para la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). 
7. En **Configuración**, realice cambios según sea necesario y haga clic en **Aceptar**. 
8. En la página de resumen, debería ver el nombre de la imagen incluido en **Imagen privada**. Haga clic en **Aceptar** para iniciar la implementación de la máquina virtual.


## <a name="use-powershell"></a>Uso de PowerShell

Puede usar PowerShell para crear una máquina virtual a partir de una imagen mediante el parámetro simplificado establecido para el cmdlet [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). La imagen debe estar en el mismo grupo de recursos donde desea crear la máquina virtual.

Este ejemplo requiere la versión 5.6.0 del módulo de AzureRM o una versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

El parámetro simplificado establecido para New-AzureRmVm solo requiere que proporcione un nombre, un grupo de recursos y un nombre de imagen para crear una máquina virtual a partir de una imagen, pero usará el valor del parámetro **-Name** como nombre de todos los recursos que crea automáticamente. En este ejemplo, se proporcionan nombres más detallados para cada uno de los recursos, pero se permite que el cmdlet los cree automáticamente. También puede crear recursos, como la red virtual, de antemano y pasar el nombre al cmdlet. Usará los recursos existentes si los encuentra por su nombre.

En el ejemplo siguiente, se crea una máquina virtual denominada *myVMfromImage* en el grupo de recursos *myResourceGroup*, a partir de la imagen llamada *myImage*. 


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



## <a name="next-steps"></a>Pasos siguientes
Para administrar máquinas virtuales con Azure PowerShell, consulte [Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

