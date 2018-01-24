---
title: "Creación de máquinas virtuales Windows con el cmdlet New-AzureRMVM simplificado en Azure Cloud Shell | Microsoft Docs"
description: "Aprenda rápidamente a crear máquinas virtuales Windows con el cmdlet New-AzureRMVM simplificado en Azure Cloud Shell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 94eb6232cf59d502a9d70545785c3788398f4d27
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Creación de una máquina virtual Windows con el cmdlet New-AzureRMVM simplificado en Cloud Shell 

El cmdlet [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) ha agregado un conjunto simplificado de parámetros para crear una nueva máquina virtual con PowerShell. En este tema se muestra cómo usar PowerShell en Azure Cloud Shell, con la versión más reciente del cmdlet New-AzureVM preinstalada, para crear una nueva máquina virtual. Se usará un conjunto de parámetros simplificado que crea automáticamente todos los recursos necesarios con valores predeterminados inteligentes. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial se requiere la versión 5.1.1 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-the-vm"></a>Creación de la máquina virtual

Puede usar el cmdlet [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) para crear una máquina virtual con los valores predeterminados inteligentes que incluyen el uso de la imagen de Windows Server 2016 Datacenter de Azure Marketplace. Puede usar New-AzureRMVM simplemente con el parámetro **-Name** y se utilizará ese valor para todos los nombres de recursos. En este ejemplo, establecemos el parámetro **-Name** como *myVM*. 

Asegúrese de que **PowerShell** está seleccionado en Cloud Shell y escriba:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

Deberá crear un nombre de usuario y una contraseña para la máquina virtual, que se usarán cuando se conecte a la máquina virtual más adelante en este tema. La contraseña debe tener entre 12 y 123 caracteres y reunir, al menos, tres de los cuatro requisitos de complejidad siguientes: contener al menos una minúscula, una mayúscula, un número y un carácter especial.

Se tarda un minuto en crear la máquina virtual y los recursos asociados. Cuando finalice, podrá ver todos los recursos que se crearon con el cmdlet [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Conexión a la máquina virtual

Una vez finalizada la implementación, cree una conexión del Escritorio remoto con la máquina virtual.

Use el comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de la máquina virtual. Anote esta dirección IP.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

En la máquina local, abra un símbolo del sistema y use el comando **mstsc** para iniciar una sesión de escritorio remoto con la nueva máquina virtual. Reemplace el valor de &lt;publicIPAddress&gt; con la dirección IP de la máquina virtual. Cuando se le solicite, escriba el nombre de usuario y la contraseña que se le asignó a la máquina virtual durante la creación.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Especificación de diferentes nombres de recursos

Puede también proporcionar nombres más descriptivos para los recursos y mantener su creación automática. Este es un ejemplo en el que hemos asignado un nombre a varios recursos para la nueva máquina virtual, incluido un nuevo grupo de recursos.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>pasos siguientes

En este tema, ha implementado una máquina virtual sencilla con New-AzVM y se ha conectado a ella mediante el protocolo de escritorio remoto. Para más información acerca de las máquinas virtuales de Azure, continúe con el tutorial de máquinas virtuales Windows.

> [!div class="nextstepaction"]
> [Tutoriales de máquinas virtuales Windows de Azure](./tutorial-manage-vm.md)
