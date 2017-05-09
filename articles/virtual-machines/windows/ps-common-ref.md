---
title: "Comandos comunes de PowerShell para máquinas virtuales de Azure | Microsoft Docs"
description: "Comandos comunes de PowerShell para comenzar a crear y administrar máquinas virtuales Windows en Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d0a92475258176ad20cbf8e9cf34f35038d2133d
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandos comunes de PowerShell para crear y administrar máquinas virtuales de Azure

En este artículo se tratan algunos de los comandos de Azure PowerShell que puede usar para crear y administrar máquinas virtuales de su suscripción de Azure.  Para obtener más ayuda con las opciones y los modificadores de línea de comandos específicos, puede utilizar el **comando** *Get-Help*.

Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview) para obtener más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta.

Estas variables podrían serle útiles si ejecuta más de uno de los comandos de este artículo:

- $location: la ubicación de la máquina virtual. Puede usar [Get-AzureRmLocation](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/get-azurermlocation) para buscar una [región geográfica](https://azure.microsoft.com/regions/) que se adapte a sus necesidades.
- Valor predeterminado personalizado: Nombre del grupo de recursos que contiene las máquinas virtuales.
- $myVM: el nombre de la máquina virtual.

## <a name="create-a-vm"></a>Creación de una VM

| Tarea | Get-Help |
| ---- | ------- |
| Creación de una configuración de máquina virtual |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/New-AzureRmVMConfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>La configuración de máquina virtual se utiliza para definir o actualizar la configuración de la máquina virtual. La configuración se inicializa con el nombre de la VM y su [tamaño](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adición de valores de configuración |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Set-AzureRmVMOperatingSystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>La configuración del sistema operativo que incluye [credenciales](https://technet.microsoft.com/library/hh849815.aspx) se agrega al objeto de configuración que creó anteriormente mediante New-AzureRmVMConfig. |
| Adición de una interfaz de red |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Una VM debe tener un [interfaz de red](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para comunicarse en una red virtual. También puede usar [Get AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) para recuperar un objeto de interfaz de red existente. |
| Especificación de una imagen de plataforma |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Set-AzureRmVMSourceImage) -VM $vm -PublisherName "nombre_publicador" -Offer "oferta_publicador" -Skus "sku_producto" -Version "más_reciente"<BR></BR><BR></BR>La [información de la imagen](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) se agrega al objeto de configuración que creó anteriormente mediante New-AzureRmVMConfig. El objeto que se devuelve con este comando solo se utiliza cuando se establece el disco del sistema operativo que utilizará una imagen de plataforma. |
| Establecimiento del disco del sistema operativo que utilizará una imagen de plataforma |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Set-AzureRmVMOSDisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>El nombre del disco del sistema operativo y su ubicación en el [almacenamiento](../../storage/storage-powershell-guide-full.md) se agregan al objeto de configuración que creó anteriormente. |
| Establecimiento del disco del sistema operativo que utilizará una imagen generalizada |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>El nombre del disco del sistema operativo, la ubicación de la imagen de origen y la ubicación del disco en el [almacenamiento](../../storage/storage-powershell-guide-full.md) se agregan al objeto de configuración. |
| Establecimiento del disco del sistema operativo que utilizará una imagen especializada |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Creación de una VM |[New-AzureRmVM]() -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Todos los recursos se crean en un [grupo de recursos](../../azure-resource-manager/powershell-azure-resource-manager.md). Antes de utilizar este comando, ejecute New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface y Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Obtención de información sobre VM

| Tarea | Comando |
| ---- | ------- |
| Enumeración de las máquinas virtuales de una suscripción |[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) |
| Enumeración de las máquinas virtuales de un grupo de recursos |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Para obtener una lista de grupos de recursos de la suscripción, utilice [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Obtención información acerca de una máquina virtual |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Administración de máquinas virtuales
| Tarea | Comando |
| --- | --- |
| Inicio de una máquina virtual |[Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Detención de una máquina virtual |[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Reinicio de una máquina virtual en ejecución |[Restart-AzureRmVM](/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Eliminación de una máquina virtual |[Remove-AzureRmVM](/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Generalización de una máquina virtual |[Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>Ejecute este comando antes de ejecutar Save-AzureRmVMImage. |
| Captura de una máquina virtual |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Save-AzureRmVMImage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>Una máquina virtual debe estar [cerrada y generalizada](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para usarse para crear una imagen. Antes de utilizar este comando, ejecute Set-AzureRmVm. |
| Actualización de una máquina virtual |[Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Obtenga la configuración de máquina virtual actual mediante Get-AzureRmVM, cambie los valores de configuración del objeto de máquina virtual y, luego, ejecute este comando. |
| Incorporación de un disco de datos a una máquina virtual |[Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMDataDisk) -VM $vm -Name "myDataDisk" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Utilice Get-AzureRmVM para obtener el objeto de máquina virtual. Especifique el número LUN y el tamaño del disco. Ejecute Update-AzureRmVM para aplicar los cambios de configuración en la máquina virtual. No se inicializó el disco que agregó. Para información sobre la inicialización de discos a medida que se agregan, consulte [Administración de máquinas virtuales de Azure con Resource Manager y PowerShell](ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Eliminación de un disco de datos de una máquina virtual |[Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Utilice Get-AzureRmVM para obtener el objeto de máquina virtual. Ejecute Update-AzureRmVM para aplicar los cambios de configuración en la máquina virtual. |
| Adición de una extensión a una máquina virtual |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Set-AzureRmVMExtension) -ResourceGroupName $myResourceGroup -Location $location -VMName $myVM -Name "extensionName" -Publisher "publisherName" -Type "extensionType" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Ejecute este comando con la [información de configuración](extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) apropiada para la extensión que desea instalar. |
| Eliminación de una extensión de máquina virtual |[Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>Pasos siguientes
* Consulte los pasos básicos para crear una máquina virtual en [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


