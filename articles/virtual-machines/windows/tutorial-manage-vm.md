---
title: "Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell | Microsoft Docs"
description: "Tutorial: Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4cf406dfbab40631c99da70085e99ba90f563411
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell

Las máquinas virtuales de Azure proporcionan un entorno informático completamente configurable y flexible. En este tutorial se tratan elementos básicos de la implementación de máquinas virtuales de Azure, como la selección de su tamaño, la selección de una imagen de máquina virtual y la implementación de una máquina virtual. Aprenderá a:

> [!div class="checklist"]
> * Crear y conectar elementos a una máquina virtual
> * Seleccionar y usar imágenes de máquinas virtuales
> * Ver y usar tamaños de una máquina virtual específicos
> * Cambiar el tamaño de una máquina virtual
> * Ver y entender el estado de las máquinas virtuales


[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial se requiere la versión 5.3 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Se debe crear un grupo de recursos antes de una máquina virtual. En el siguiente ejemplo, se crea un grupo de recursos denominado *myResourceGroupVM* en la región *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

Se especifica el grupo de recursos al crear o modificar una máquina virtual, como se ve a lo largo de este tutorial.

## <a name="create-virtual-machine"></a>Create virtual machine

Al crear una máquina virtual, hay varias opciones disponibles, como la imagen de sistema operativo,la configuración de red y las credenciales administrativas. En este ejemplo se crea una máquina virtual con el nombre *myVM* que ejecuta la versión predeterminada más reciente de Windows Server 2016 Datacenter.

Establezca el nombre de usuario y la contraseña que se necesitan para la cuenta de administrador en la máquina virtual con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Cree la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Conexión a una máquina virtual

Una vez finalizada la implementación, cree una conexión del Escritorio remoto con la máquina virtual.

Ejecute los comandos siguientes para devolver la dirección IP pública de la máquina virtual. Tome nota de esta dirección IP para poder conectarse a ella con un explorador para probar la conectividad web en un paso posterior.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Ejecute el comando siguiente en el equipo local para crear una sesión de escritorio remoto con la máquina virtual. Reemplace la dirección IP con el valor de *publicIPAddress* de la máquina virtual. Cuando se le solicite, escriba las credenciales usadas al crear la máquina virtual.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>Descripción de las imágenes de máquina virtual

Azure Marketplace incluye muchas imágenes de máquina virtual que se pueden usar para crear una nueva máquina virtual. En los pasos anteriores, se creó una máquina virtual mediante la imagen de Windows Server 2016 Datacenter. En este paso, el módulo de PowerShell se usa para buscar en Marketplace otras imágenes de Windows, que también se pueden usar como base para nuevas máquinas virtuales. Este proceso consiste en buscar el publicador, la oferta y el nombre de imagen (SKU). 

Use el comando [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) para devolver una lista de publicadores de imágenes:

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Use el comando [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) para devolver una lista de ofertas de imágenes. Con este comando, la lista devuelta se filtra por el publicador especificado:

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

El comando [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) filtra entonces por el publicador y el nombre de la oferta para devolver una lista de nombres de imágenes.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Esta información puede usarse para implementar una máquina virtual con una imagen específica. En este ejemplo se implementa una máquina virtual mediante Windows Server 2016 con una imagen de contenedores.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

El parámetro `-AsJob` crea la máquina virtual como tarea en segundo plano, por lo que PowerShell solicita la vuelta. Puede ver detalles de trabajos en segundo plano con el cmdlet `Job`.


## <a name="understand-vm-sizes"></a>Comprensión de los tamaños de máquina virtual

El tamaño de la máquina virtual determina la cantidad de recursos de proceso, como memoria, CPU y GPU, que están disponibles para la máquina virtual. Las máquinas virtuales deben crearse con un tamaño adecuado para la carga de trabajo esperada. Si aumenta la carga de trabajo, se puede cambiar el tamaño de una máquina virtual existente.

### <a name="vm-sizes"></a>Tamaños de máquina virtual

En la tabla siguiente se clasifican los tamaños en casos de uso.  
| type                     | Tamaños comunes           |    DESCRIPCIÓN       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Uso general](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Uso equilibrado de CPU y memoria. Ideal para desarrollo/pruebas, así como soluciones de datos y aplicaciones de tamaño pequeño a mediano.  |
| [Proceso optimizado](sizes-compute.md)   | Fs, F             | Uso elevado de la CPU respecto a la memoria. Adecuado para aplicaciones, dispositivos de red y procesos por lotes con tráfico mediano.        |
| [Memoria optimizada](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Uso elevado de memoria respecto al núcleo. Excelente para bases de datos relacionales, memorias caché de capacidad de mediana a grande y análisis en memoria.                 |
| [Almacenamiento optimizado](sizes-storage.md)      | LS                | Alto rendimiento de disco y E/S. Perfecto para bases de datos SQL y NoSQL y macrodatos.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | Máquinas virtuales especializadas específicas para actividades intensas de representación de gráficos y edición de vídeo.       |
| [Alto rendimiento](sizes-hpc.md) | H, A8-11          | Nuestras máquinas virtuales con CPU más eficaces e interfaces de red de alto rendimiento (RDMA) opcionales. 


### <a name="find-available-vm-sizes"></a>Búsqueda de los tamaños de máquina virtual disponibles

Para ver una lista de tamaños de máquina virtual disponibles en una región determinada, use el comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Cambiar el tamaño de una máquina virtual

Una vez implementada una máquina virtual, se puede cambiar su tamaño para aumentar o disminuir la asignación de recursos.

Antes de cambiar el tamaño de una máquina virtual, compruebe si el tamaño deseado está disponible en el clúster actual de máquinas virtuales. El comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) devuelve una lista de tamaños. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Si el tamaño deseado está disponible, puede cambiarlo con la máquina virtual encendida, aunque se reiniciará durante la operación.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

Si el tamaño deseado no está en el clúster actual, se debe desasignar la máquina virtual para que se pueda llevar a cabo la operación de cambio de tamaño. Tenga en cuenta que cuando la máquina virtual se enciende de nuevo, se quitan todos los datos del disco temporal y la dirección IP pública cambia a menos que se use una dirección IP estática. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>Estados de energía de la máquina virtual

Una máquina virtual de Azure puede tener uno de muchos estados de energía. Este estado representa el estado actual de la máquina virtual desde el punto de vista del hipervisor. 

### <a name="power-states"></a>Estados de energía

| Estado de energía | DESCRIPCIÓN
|----|----|
| Iniciando | Indica que se está iniciando la máquina virtual. |
| Ejecución | Indica que la máquina virtual se está ejecutando. |
| Deteniéndose | Indica que se está deteniendo la máquina virtual. | 
| Stopped | Indica que la máquina virtual se ha detenido. Tenga en cuenta que las máquinas virtuales en estado detenido siguen acumulando cargos de proceso.  |
| Desasignando | Indica que se está desasignando la máquina virtual. |
| Desasignado | Indica que la máquina virtual se quitó completamente del hipervisor pero sigue estando disponible en el plano de control. Las máquinas virtuales en el estado Desasignado no incurren en gastos de proceso. |
| - | Indica que se desconoce el estado de la máquina virtual. |

### <a name="find-power-state"></a>Búsqueda del estado de energía

Para recuperar el estado de una máquina virtual concreta, use el comando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Asegúrese de especificar un nombre válido para la máquina virtual y el grupo de recursos. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Salida:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Tareas de administración

Durante el ciclo de vida de una máquina virtual, puede ejecutar tareas de administración como iniciar, detener o eliminar una máquina virtual. Además, puede crear scripts para automatizar tareas repetitivas o complejas. Con Azure PowerShell, se pueden ejecutar muchas tareas comunes de administración desde la línea de comandos o en scripts.

### <a name="stop-virtual-machine"></a>Detención de la máquina virtual

Detenga y desasigne una máquina virtual con [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

Si desea mantener la máquina virtual en un estado aprovisionado, use el parámetro -StayProvisioned.

### <a name="start-virtual-machine"></a>Inicio de la máquina virtual

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>Eliminación de un grupo de recursos

Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha aprendido conceptos básicos sobre la creación y administración de máquinas virtuales. Por ejemplo:

> [!div class="checklist"]
> * Crear y conectar elementos a una máquina virtual
> * Seleccionar y usar imágenes de máquinas virtuales
> * Ver y usar tamaños de una máquina virtual específicos
> * Cambiar el tamaño de una máquina virtual
> * Ver y entender el estado de las máquinas virtuales

Prosiga con el siguiente tutorial para aprender sobre los discos en máquinas virtuales de Azure.  

> [!div class="nextstepaction"]
> [Creación y administración de discos de máquinas virtuales](./tutorial-manage-data-disk.md)
