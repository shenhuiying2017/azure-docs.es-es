---
title: Comandos comunes de PowerShell para VM | Microsoft Docs
description: "Comandos comunes de PowerShell para comenzar a crear y administrar máquinas virtuales en Azure (Windows)"
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
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: d86db0f0487c8c3627fa91b656db6a4c1576348c


---
# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Comandos comunes de PowerShell para crear y administrar máquinas virtuales
En este artículo se tratan algunos de los comandos de Azure PowerShell que puede usar para crear y administrar máquinas virtuales de su suscripción de Azure.  Para obtener más ayuda con las opciones y los modificadores de línea de comandos específicos, puede utilizar el **comando** *Get-Help*.

Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que desea usar e iniciar sesión en su cuenta.

## <a name="create-a-vm"></a>Creación de una VM
| Tarea | Get-Help |
| --- | --- |
| Creación de una configuración de máquina virtual |$vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "nombre_vm" -VMSize "tamaño_vm"<BR></BR><BR></BR>La configuración de máquina virtual se utiliza para definir o actualizar la configuración de la máquina virtual. La configuración se inicializa con el nombre de la VM y su [tamaño](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adición de valores de configuración |$vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "nombre_equipo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>La configuración del sistema operativo que incluye [credenciales](https://technet.microsoft.com/library/hh849815.aspx) se agrega al objeto de configuración que creó anteriormente mediante New-AzureRmVMConfig. |
| Adición de una interfaz de red |$vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Una VM debe tener un [interfaz de red](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para comunicarse en una red virtual. También puede usar [Get AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) para recuperar un objeto de interfaz de red existente. |
| Especificación de una imagen de plataforma |$vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "nombre_publicador" -Offer "oferta_publicador" -Skus "sku_producto" -Version "más_reciente"<BR></BR><BR></BR>La [información de la imagen](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) se agrega al objeto de configuración que creó anteriormente mediante New-AzureRmVMConfig. El objeto que se devuelve con este comando solo se utiliza cuando se establece el disco del sistema operativo que utilizará una imagen de plataforma. |
| Establecimiento del disco del sistema operativo que utilizará una imagen de plataforma |$vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "nombre_disco" -VhdUri "http://mystore1.blob.core.windows.net/vhds/nombre_disco.vhd" -CreateOption FromImage<BR></BR><BR></BR>El nombre del disco del sistema operativo y su ubicación en el [almacenamiento](../storage/storage-powershell-guide-full.md) se agregan al objeto de configuración que creó anteriormente. |
| Establecimiento del disco del sistema operativo que utilizará una imagen generalizada |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "nombre_disco" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/nombre_disco.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>El nombre del disco del sistema operativo, la ubicación de la imagen de origen y la ubicación del disco en el [almacenamiento](../storage/storage-powershell-guide-full.md) se agregan al objeto de configuración. |
| Establecimiento del disco del sistema operativo que utilizará una imagen especializada |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "nombre_de_disco" -VhdUri "http://mialmacén1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Creación de una VM |[New-AzureRmVM]() -ResourceGroupName "nombre_grupo_recursos" -Location "nombre_ubicación" -VM $vm<BR></BR><BR></BR>Todos los recursos se crean en un [grupo de recursos](../powershell-azure-resource-manager.md). La máquina virtual debe crearse en la misma [ubicación](https://msdn.microsoft.com/library/azure/dn495177.aspx) que el grupo de recursos. Antes de utilizar este comando, ejecute New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface y Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Obtención de información sobre VM
| Tarea | Comando |
| --- | --- |
| Enumeración de las máquinas virtuales de una suscripción |[Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx) |
| Enumeración de las máquinas virtuales de un grupo de recursos |Get-AzureRmVM -ResourceGroupName "nombre_grupo_recursos"<BR></BR><BR></BR>Para obtener una lista de grupos de recursos de la suscripción, utilice [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx). |
| Obtención información acerca de una máquina virtual |Get-AzureRmVM -ResourceGroupName "nombre_grupo_recursos" -Name "nombre_vm" |

## <a name="manage-vms"></a>Administración de máquinas virtuales
| Tarea | Comando |
| --- | --- |
| Inicio de una máquina virtual |[Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "nombre_grupo_recursos" -Name "nombre_vm" |
| Detención de una máquina virtual |[Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "nombre_grupo_recursos" -Name "nombre_vm" |
| Reinicio de una máquina virtual en ejecución |[Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "nombre_grupo_recursos" -Name "nombre_vm" |
| Eliminación de una máquina virtual |[Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "nombre_grupo_recursos" -Name "nombre_vm" |
| Generalización de una máquina virtual |[Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "nombre_vm" -Generalized<BR></BR><BR></BR>Ejecute este comando antes de ejecutar Save-AzureRmVMImage. |
| Captura de una máquina virtual |[Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "nombre_grupo_recursos" -VMName "nombre_vm" -DestinationContainerName "contenedor_imágenes" -VHDNamePrefix "prefijo_nombre_imagen" -Path "C:\rutanombrearchivo\nombrearchivo.json"<BR></BR><BR></BR>Una máquina virtual debe estar [cerrada y generalizada](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para usarse para crear una imagen. Antes de utilizar este comando, ejecute Set-AzureRmVm. |
| Actualización de una máquina virtual |[Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "nombre_grupo_recursos" -VM $vm<BR></BR><BR></BR>Obtenga la configuración de máquina virtual actual mediante Get-AzureRmVM, cambie los valores de configuración del objeto de máquina virtual y, luego, ejecute este comando. |
| Incorporación de un disco de datos a una máquina virtual |[Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "nombre_disco" -VhdUri "https://mystore1.blob.core.windows.net/vhds/nombre_disco.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Utilice Get-AzureRmVM para obtener el objeto de máquina virtual. Especifique el número LUN y el tamaño del disco. Ejecute Update-AzureRmVM para aplicar los cambios de configuración en la máquina virtual. No se inicializó el disco que agregó. Para información sobre la inicialización de discos a medida que se agregan, consulte [Administración de máquinas virtuales de Azure con Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Eliminación de un disco de datos de una máquina virtual |[Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "nombre_disco"<BR></BR><BR></BR>Utilice Get-AzureRmVM para obtener el objeto de máquina virtual. Ejecute Update-AzureRmVM para aplicar los cambios de configuración en la máquina virtual. |
| Adición de una extensión a una máquina virtual |[Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "nombre_grupo_recursos" -Location "ubicación_azure" -VMName "nombre_vm" -Name "nombre_extensión" -Publisher "nombre_publicador" -Type "tipo_extensión" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Ejecute este comando con la [información de configuración](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) apropiada para la extensión que desea instalar. |
| Eliminación de una extensión de máquina virtual |[Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "nombre_grupo_recursos" -Name "nombre_extensión" -VMName "nombre_vm" |

## <a name="next-steps"></a>Pasos siguientes
* Consulte los pasos básicos para crear una máquina virtual en [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Dec16_HO2-->


