---
title: "Inicio rápido de Azure: creación de máquinas virtuales Windows con PowerShell | Microsoft Docs"
description: "Aprenda rápidamente a crear una máquina virtual Windows con PowerShell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ba0a0c336fbdab91121f5c4bff00b24c1088bd8a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-windows-virtual-machine-with-powershell"></a>Creación de una máquina virtual Windows con PowerShell

El módulo de Azure PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos de PowerShell o en scripts. En esta guía de inicio rápido se detalla cómo usar PowerShell para crear y ejecutar máquinas virtuales de Azure con Windows Server 2016. Una vez completada la implementación, nos conectaremos al servidor e instalaremos IIS.
  

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial se requiere la versión 5.1.1 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.



## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```


## <a name="create-virtual-machine"></a>Create virtual machine

Cree la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Solo tiene que proporcionar los nombres de cada uno de los recursos y el cmdlet de New-AzureRMVM los creará si aún no existen.

Cuando se realiza este paso, se le solicitará las credenciales. Los valores que especifique se configuran como el nombre de usuario y la contraseña de la máquina virtual.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM$(Get-Random)" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389  
```

## <a name="connect-to-virtual-machine"></a>Conexión a la máquina virtual

Una vez finalizada la implementación, cree una conexión del Escritorio remoto con la máquina virtual.

Use el comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de la máquina virtual. Tome nota de esta dirección IP para poder conectarse a ella con un explorador para probar la conectividad web en un paso posterior.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Ejecute el comando siguiente en el equipo local para crear una sesión de escritorio remoto con la máquina virtual. Reemplace la dirección IP con el valor de *publicIPAddress* de la máquina virtual. Cuando se le solicite, escriba las credenciales usadas al crear la máquina virtual.

```
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalación de IIS mediante PowerShell

Ahora que ha iniciado sesión en la máquina virtual de Azure, puede usar una sola línea de PowerShell para instalar IIS y habilitar la regla de firewall local para permitir el tráfico web. Abra un símbolo del sistema de PowerShell en la máquina virtual y ejecute el siguiente comando:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Página principal de IIS

Con IIS instalado y el puerto 80 abierto en la máquina virtual desde Internet, puede usar el explorador web que elija para ver la página principal de IIS. Asegúrese de utilizar el valor de *publicIpAddress* que ha anotado antes para visitar la página predeterminada. 

![Sitio predeterminado de IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, ha implementado una máquina virtual simple y una regla de grupo de seguridad de red, y ha instalado un servidor web. Para más información acerca de las máquinas virtuales de Azure, continúe con el tutorial de máquinas virtuales Windows.

> [!div class="nextstepaction"]
> [Tutoriales de máquinas virtuales Windows de Azure](./tutorial-manage-vm.md)
