---
title: "Administración de discos de Azure con Azure PowerShell | Microsoft Docs"
description: "Tutorial: Administración de discos de Azure con Azure PowerShell"
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
ms.openlocfilehash: ea38fe599960db42c518603b59a60a920d1f1daf
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="manage-azure-disks-with-powershell"></a>Administración de discos de Azure con PowerShell

Las máquinas virtuales de Azure usan discos para almacenar el sistema operativo, las aplicaciones y los datos de máquinas virtuales. Al crear una máquina virtual es importante elegir un tamaño de disco y la configuración adecuada para la carga de trabajo esperada. Este tutorial trata la implementación y administración de discos de máquina virtual. Aprenderá sobre los siguientes temas:

> [!div class="checklist"]
> * Discos del SO y temporales
> * Discos de datos
> * Discos Estándar y Premium
> * Rendimiento de disco
> * Conectar y preparar los discos de datos

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial se requiere la versión 5.3 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 

## <a name="default-azure-disks"></a>Discos de Azure predeterminados

Cuando se crea una máquina virtual de Azure, se conectan dos discos automáticamente a la máquina virtual. 

**Disco del sistema operativo**: hospeda el sistema operativo de las máquinas virtuales. Se puede cambiar su tamaño hasta 4 terabyte.  Al disco del sistema operativo se le asigna una letra de unidad *c:* de forma predeterminada. La configuración de almacenamiento en caché del disco del sistema operativo está optimizada para el rendimiento del sistema operativo. El disco del sistema operativo **no debería** hospedar aplicaciones o datos. Para aplicaciones y datos, use un disco de datos. Explicamos esto más adelante en este artículo.

**Disco temporal**: los discos temporales utilizan una unidad de estado sólida que se encuentra en el mismo host de Azure que la máquina virtual. Los discos temporales son muy eficiente y se pueden usar para operaciones tales como el procesamiento temporal de los datos. Sin embargo, si la máquina virtual se mueve a un nuevo host, los datos almacenados en un disco temporal se eliminarán. El tamaño del disco temporal se determina por el tamaño de la máquina virtual. A los discos temporales se les asigna una letra de unidad de *d:* de forma predeterminada.

### <a name="temporary-disk-sizes"></a>Tamaños de disco temporal

| type | Tamaños comunes | Tamaño máximo de disco temporal (GiB) |
|----|----|----|
| [Uso general](sizes-general.md) | Series A, B y D | 1600 |
| [Proceso optimizado](sizes-compute.md) | Serie F | 576 |
| [Memoria optimizada](sizes-memory.md) | Series D, E, G y M | 6144 |
| [Almacenamiento optimizado](sizes-storage.md) | Serie L | 5630 |
| [GPU](sizes-gpu.md) | Serie N | 1440 |
| [Alto rendimiento](sizes-hpc.md) | Series A y H | 2000 |

## <a name="azure-data-disks"></a>Discos de datos de Azure

Se pueden agregar discos de datos adicionales para instalar aplicaciones y almacenar datos. Los discos de datos deben usarse en cualquier situación donde desee un almacenamiento de datos duradero y con capacidad de respuesta. Cada disco de datos tiene una capacidad máxima de 4 terabytes. El tamaño de la máquina virtual determina cuántos discos de datos se pueden conectar a una máquina virtual. Para cada vCPU de la máquina virtual, se pueden asociar dos discos de datos. 

### <a name="max-data-disks-per-vm"></a>Discos de datos máximos por máquina virtual

| type | Tamaños comunes | Discos de datos máximos por máquina virtual |
|----|----|----|
| [Uso general](sizes-general.md) | Series A, B y D | 64 |
| [Proceso optimizado](sizes-compute.md) | Serie F | 64 |
| [Memoria optimizada](sizes-memory.md) | Series D, E, G y M | 64 |
| [Almacenamiento optimizado](sizes-storage.md) | Serie L | 64 |
| [GPU](sizes-gpu.md) | Serie N | 64 |
| [Alto rendimiento](sizes-hpc.md) | Series A y H | 64 |

## <a name="vm-disk-types"></a>Tipos de disco de máquina virtual

Azure proporciona dos tipos de disco.

### <a name="standard-disk"></a>Disco estándar

Standard Storage está respaldado por unidades de disco duro y ofrece un almacenamiento rentable al mismo tiempo que tiene un rendimiento superior. Los discos estándar son ideales para cargas de trabajo de desarrollo y prueba rentables.

### <a name="premium-disk"></a>Disco Premium

Los discos Premium están respaldados por un disco de latencia reducida y alto rendimiento basado en SSD. Es perfecto para máquinas virtuales que ejecutan cargas de trabajo de producción. Premium Storage es compatible con las máquinas virtuales de las series DS, DSv2, GS y FS. Los discos Premium incluyen cinco tipos (P10, P20, P30, P40 y P50); el tamaño del disco determina el tipo de disco. Al seleccionar el tamaño de un disco, el valor se redondea al siguiente tipo. Por ejemplo, si el tamaño es inferior a 128 GB, el tipo de disco es P10; si es de 129 a 512 GB, el disco es P20.

### <a name="premium-disk-performance"></a>Rendimiento del disco Premium

|Tipo de disco de Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Tamaño del disco (redondeo hacia arriba) | 32 GB | 64 GB | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) |
| Máximo de IOPS por disco | 120 | 240 | 500 | 2,300 | 5.000 | 7500 | 7500 |
Rendimiento de disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Aunque la tabla anterior identifica las IOPS máximas por disco, se puede obtener un mayor nivel de rendimiento dividiendo varios discos de datos. Por ejemplo, 64 discos de datos pueden conectarse a la máquina virtual Standard_GS5. Si cada uno de estos discos tienen un tamaño de P30, se puede lograr un máximo de 80 000 IOPS. Para más información sobre el número máximo de IOPS por máquina virtual, vea los [tamaños y topos de máquinas virtuales](./sizes.md).

## <a name="create-and-attach-disks"></a>Creación y conexión de discos

Para completar el ejemplo de este tutorial, debe tener una máquina virtual. Si es necesario, cree una máquina virtual con los siguientes comandos.

Establezca el nombre de usuario y la contraseña que se necesitan para la cuenta de administrador en la máquina virtual con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Cree la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -AsJob
```

El parámetro `-AsJob` crea la máquina virtual como tarea en segundo plano, por lo que PowerShell solicita la vuelta. Puede ver detalles de trabajos en segundo plano con el cmdlet `Job`.

Cree la configuración inicial con [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). En el ejemplo siguiente se crea un disco denominado con un tamaño de 128 gigabytes.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Cree el disco de datos con el comando [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk).

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Obtenga la máquina virtual que desea agregar al disco de datos con el comando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm).

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Agregue el disco de datos a la configuración de máquina virtual con el comando [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Actualice la máquina virtual con el comando [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Preparación de los discos de datos

Cuando se haya conectado un disco a la máquina virtual, el sistema operativo debe configurarse para utilizar el disco. En el ejemplo siguiente se muestra cómo configurar manualmente el primer disco que se agrega a la máquina virtual. Este proceso también se puede automatizar utilizando la [extensión de script personalizado](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuración manual

Crear una conexión RDP con la máquina virtual. Abra PowerShell y ejecute este script.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha aprendido sobre temas relacionados con los discos de máquina virtual; por ejemplo:

> [!div class="checklist"]
> * Discos del SO y temporales
> * Discos de datos
> * Discos Estándar y Premium
> * Rendimiento de disco
> * Conectar y preparar los discos de datos

Siga con el siguiente tutorial para aprender sobre la automatización de la configuración de la máquina virtual.

> [!div class="nextstepaction"]
> [Automatización de la configuración de máquinas virtuales](./tutorial-automate-vm-deployment.md)
