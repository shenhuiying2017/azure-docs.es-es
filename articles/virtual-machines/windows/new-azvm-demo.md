---
title: "Creación de máquinas virtuales Windows con el cmdlet New-AzVM en Azure Cloud Shell | Microsoft Docs"
description: "Aprenda rápidamente a crear máquinas virtuales Windows con el cmdlet New-AzVM en Azure Cloud Shell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3be46c8c02ad136edb1936fbb39560d479b27277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Creación de una máquina virtual Windows con New-AzVM (versión preliminar) Cloud Shell 

El cmdlet New-AzVM (versión preliminar) es una manera simplificada de crear una máquina virtual con PowerShell. Esta guía detalla cómo usar PowerShell en Azure Cloud Shell con el cmdlet New-AzVM preinstalado para crear una máquina virtual de Azure que ejecute Windows Server 2016. Una vez completada la implementación, nos conectaremos al servidor mediante el protocolo de escritorio remoto.  

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>Creación de la máquina virtual

Puede usar el cmdlet **New-AzVM** para crear una máquina virtual con los valores predeterminados inteligentes que incluyen el uso de la imagen de Windows Server 2016 Datacenter de Azure Marketplace. Puede usar New-AzVM por sí solo y se usarán valores predeterminados para los nombres de los recursos. En este ejemplo, establecemos el parámetro **-Name** como *myVM*. El cmdlet crea todos los recursos necesarios con *myVM* como prefijo en el nombre del recurso. 

Asegúrese de que **PowerShell** está seleccionado en Cloud Shell y escriba:

```azurepowershell-interactive
New-AzVm -Name myVM
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

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tema, ha implementado una máquina virtual sencilla con New-AzVM y se ha conectado a ella mediante el protocolo de escritorio remoto. Para más información acerca de las máquinas virtuales de Azure, continúe con el tutorial de máquinas virtuales Windows.

> [!div class="nextstepaction"]
> [Tutoriales de máquinas virtuales Windows de Azure](./tutorial-manage-vm.md)
