<properties 
   pageTitle="Comandos comunes de PowerShell para máquinas virtuales | Microsoft Azure"
   description="Comandos comunes de PowerShell para comenzar a crear y administrar máquinas virtuales en Azure (Windows)"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="davidmu" />

# Comandos comunes de PowerShell para crear y administrar máquinas virtuales

En este artículo se tratan algunos de los comandos de Azure PowerShell que puede usar para crear y administrar máquinas virtuales de su suscripción de Azure. Para obtener más ayuda con las opciones y los modificadores de línea de comandos específicos, puede utilizar el *comando* **Get-Help**.

## Creación de recursos con Azure PowerShell

Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta de Azure.

Tarea | Comando
-------------- | -------------------------
Creación de una configuración de máquina virtual | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "nombre\_vm" -VMSize "tamaño\_vm"<BR></BR><BR></BR>La configuración de máquina virtual se utiliza para definir o actualizar la configuración de la máquina virtual. La configuración se inicializa con el nombre de la máquina virtual y su [tamaño](virtual-machines-windows-sizes.md).
Adición de valores de configuración | $vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "nombre\_equipo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>La configuración del sistema operativo, incluidas las [credenciales](https://technet.microsoft.com/library/hh849815.aspx), se agrega al objeto de configuración que creó anteriormente mediante New-AzureRmVMConfig.
Adición de una interfaz de red | $vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Una máquina virtual debe tener una [interfaz de red](virtual-machines-windows-ps-create.md) para comunicarse en una red virtual. También puede usar [Get AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) para recuperar un objeto de interfaz de red existente.
Especificación de una imagen de plataforma | $vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "nombre\_editor" -Offer "oferta\_editor" -Skus "sku\_producto" -Version "la más reciente"<BR></BR><BR></BR>[La información de la imagen](virtual-machines-windows-cli-ps-findimage.md) se agrega al objeto de configuración que creó anteriormente mediante New-AzureRmVMConfig. El objeto que se devuelve con este comando solo se utiliza cuando se establece el disco del sistema operativo que utilizará una imagen de plataforma.
Establecimiento del disco del sistema operativo que utilizará una imagen de plataforma | $vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "nombre\_de\_disco" -VhdUri "http://mystore1.blob.core.windows.net/vhds/nombre\_de\_disco.vhd" -CreateOption FromImage<BR></BR><BR></BR>El nombre del disco del sistema operativo y la ubicación del [almacenamiento](../storage/storage-powershell-guide-full.md) donde se colocará se agregan al objeto de configuración que creó anteriormente.
Establecimiento del disco del sistema operativo que utilizará una imagen generalizada | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "nombre\_de\_disco" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/nombre\_de\_disco.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>El nombre del disco del sistema operativo, la ubicación de la imagen de origen y el lugar donde se ubicará el disco en el [almacenamiento](../storage/storage-powershell-guide-full.md) se agregan al objeto de configuración que creó anteriormente.
Establecimiento del disco del sistema operativo que utilizará una imagen especializada | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "nombre\_de\_disco" -VhdUri "http://mialmacén1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows
Crear una VM | [New-AzureRmVM]() -ResourceGroupName "nombre\_grupo\_recursos" -Location "nombre\_ubicación" -VM $vm<BR></BR><BR></BR>Todos los recursos se crean en un [grupo de recursos](../powershell-azure-resource-manager.md). La máquina virtual debe crearse en la misma [ubicación](https://msdn.microsoft.com/library/azure/dn495177.aspx) que el grupo de recursos. Antes de utilizar este comando, ejecute New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface y Set-AzureRmVMOSDisk.
Enumeración de las máquinas virtuales de una suscripción| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Enumeración de las máquinas virtuales de un grupo de recursos | Get-AzureRmVM -ResourceGroupName "nombre\_grupo\_recursos"<BR></BR><BR></BR>Para obtener una lista de grupos de recursos de la suscripción, utilice [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Obtención información acerca de una máquina virtual | Get-AzureRmVM -ResourceGroupName "nombre\_grupo\_recursos" -Name "nombre\_vm"
Inicio de una máquina virtual | [Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "nombre\_grupo\_recursos" -Name "nombre\_vm"
Detención de una máquina virtual | [Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "nombre\_grupo\_recursos" -Name "nombre\_vm"
Reinicio de una máquina virtual en ejecución | [Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "nombre\_grupo\_recursos" -Name "nombre\_vm"
Eliminación de una máquina virtual | [Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "nombre\_grupo\_recursos" -Name "nombre\_vm"
Generalización de una máquina virtual | [Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "nombre\_vm" -Generalized<BR></BR><BR></BR>Debe utilizar este comando antes de ejecutar Save-AzureRmVMImage.
Captura de una máquina virtual | [Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "nombre\_grupo\_recursos" -VMName "nombre\_vm" -DestinationContainerName "contenedor\_imagen" -VHDNamePrefix "prefijo\_nombre\_imagen" -Path "C:\\rutadeaccesodearchivo\\nombredearchivo.json"<BR></BR><BR></BR>Una máquina virtual debe [apagarse y desconectarse](virtual-machines-windows-capture-image.md) con el fin de utilizarse para crear una imagen. Antes de utilizar este comando, ejecute Set-AzureRmVm.
Actualización de una máquina virtual | [Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "nombre\_grupo\_recursos" -VM $vm<BR></BR><BR></BR>Obtenga la configuración de máquina virtual actual mediante Get-AzureRmVM, cambie los valores de configuración del objeto de máquina virtual y, luego, ejecute este comando.
Incorporación de un disco de datos a una máquina virtual | [Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "nombre\_disco" -VhdUri "https://mialmacén1.blob.core.windows.net/vhds/nombre\_disco.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Utilice Get-AzureRmVM para obtener el objeto de máquina virtual. Especifique el número LUN y el tamaño del disco. Ejecute Update-AzureRmVM para aplicar los cambios de configuración en la máquina virtual. El disco que ha agregado no se ha inicializado. Para ello, consulte [Administración de máquinas virtuales de Azure con Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).
Eliminación de un disco de datos de una máquina virtual | [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "nombre\_disco"<BR></BR><BR></BR>Utilice Get-AzureRmVM para obtener el objeto de máquina virtual. Ejecute Update-AzureRmVM para aplicar los cambios de configuración en la máquina virtual.
Adición de una extensión a una máquina virtual | [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "nombre\_grupo\_recursos" -Location "ubicación\_azure" -VMName "nombre\_vm" -Name "nombre\_extensión" -Publisher "nombre\_editor" -Type "tipo\_extensión" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Ejecute este comando con la [información de configuración](virtual-machines-windows-extensions-configuration-samples.md) adecuada para la extensión que quiere instalar.
Eliminación de una extensión de máquina virtual | [Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "nombre\_grupo\_recursos" -Name "nombre\_extensión" -VMName "nombre\_vm"

## Pasos siguientes

- Consulte los pasos básicos para crear una máquina virtual en [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](virtual-machines-windows-ps-create.md).

<!---HONumber=AcomDC_0629_2016-->