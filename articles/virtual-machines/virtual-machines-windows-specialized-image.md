<properties
	pageTitle="Creación de una copia de la máquina virtual de Windows | Microsoft Azure"
	description="Conozca cómo crear una copia de la máquina virtual de Azure con Windows, en el modelo de implementación de Resource Manager, creando una *imagen especializada*."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# Creación de una copia de una máquina virtual de Windows en el modelo de implementación de Resource Manager


En este artículo se muestra cómo crear una copia de la máquina virtual (VM) de Azure con Windows, en el modelo de implementación de Resource Manager mediante Azure PowerShell y el Portal de Azure. Muestra cómo crear una imagen **_especializada_** de la máquina virtual de Azure, que mantiene las cuentas de usuario y otros datos de estado de la máquina virtual original. Una imagen especializada es útil en escenarios como el traslado de la máquina virtual de Windows del modelo de implementación clásica al modelo de implementación de Resource Manager, o la creación de una copia de seguridad de la máquina virtual de Windows creada en el modelo de implementación de Resource Manager. Puede copiar a través de los discos de datos y sistema operativo de esta forma y, a continuación, configurar los recursos de red para crear la nueva máquina virtual.

Si necesita crear implementaciones masivas de máquinas virtuales de Windows similares, necesita una imagen *generalizada*; para ello, lea [Captura de una máquina virtual de Windows](virtual-machines-windows-capture-image.md).



## Comprobaciones antes de comenzar

En este artículo se supone que ya dispones de:

1. Una **máquina virtual de Azure que ejecuta Windows**, en el modelo de implementación clásica o en el de Resource Manager, con el sistema operativo configurado, los discos de datos adjuntos y las aplicaciones necesarias instaladas. Si necesita ayuda para crear esta máquina virtual, consulte [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](virtual-machines-windows-ps-create.md). 

1. **Azure PowerShell 1.0 o superior** instalado en su equipo y ha iniciado sesión en la suscripción de Azure. Para más información, consulte [Instalación y configuración de PowerShell](../powershell-install-configure.md).

1. La **herramienta AzCopy** instalada en el equipo. Para más información, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](../storage/storage-use-azcopy.md).

1. Un **grupo de recursos** con una **cuenta de almacenamiento** y un **contenedor de blobs** creado en ella para copiar los VHD. Consulte la sección [Creación o búsqueda de una cuenta de almacenamiento de Azure](virtual-machines-windows-upload-image.md#createstorage) para conocer los pasos para usar una cuenta de almacenamiento existente o crear una nueva.



> [AZURE.NOTE] Para las máquinas virtuales creadas mediante cualquiera de los dos modelos de implementación se aplican los mismos pasos que a la imagen de origen. Se indicarán las diferencias menores en su caso.


## Copia de los VHD para la cuenta de almacenamiento de Resource Manager


1. Primero libere los VHD utilizados por la máquina virtual de origen mediante una de las dos opciones siguientes:

	- Si desea **_copiar_** la máquina virtual de origen, **deténgala** y **desasígnela**.
	
		- Para una máquina virtual creada mediante el modelo de implementación clásica, puede utilizar el [portal](https://portal.azure.com) y hacer clic en **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual* > **Detener** o el comando de PowerShell `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`. 
		
		- Para una máquina virtual en el modelo de implementación de Resource Manager, puede iniciar sesión en el portal y hacer clic en **Examinar** > **Máquinas virtuales** > *su máquina virtual* > **Detener** o usar el comando de PowerShell `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`. Tenga en cuenta que el *estado* de la máquina virtual en el portal cambia de **En ejecución** a **Detenido (desasignado)**.

	
	- O bien, si desea **_migrar_** la máquina virtual de origen, **elimine** la máquina virtual y use el VHD anterior. **Desplácese** a la máquina virtual en el [portal](https://portal.azure.com) y haga clic en **Eliminar**.
	
1. Busque las teclas de acceso para la cuenta de almacenamiento que contiene el VHD de origen, así como la cuenta de almacenamiento donde se va a copiar el VHD para crear la nueva máquina virtual. La clave de la cuenta desde donde estamos copiando el VHD se denomina *clave de origen* y la de la cuenta a la que se copiará se denomina *clave de destino*. Para más información sobre las claves de acceso, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

	- Si la máquina virtual de origen se creó mediante el modelo de implementación clásica, haga clic en **Examinar** > **Cuentas de almacenamiento (clásico)** > *su cuenta de almacenamiento* > **Todas las configuraciones** > **Claves** y copie la clave con la etiqueta **CLAVE DE ACCESO PRINCIPAL**. 
	
	- Para una máquina virtual creada con el modelo de implementación de Resource Manager o la cuenta de almacenamiento que se va a utilizar para la nueva máquina virtual, haga clic en **Examinar** > **Cuentas de almacenamiento** > *la cuenta de almacenamiento* > **Todas las configuraciones** > **Claves de acceso** y copie la clave etiquetada como **key1**.

1. Obtenga las direcciones URL para obtener acceso a las cuentas de almacenamiento de origen y de destino. En el portal, utilice la opción **Examinar** en su cuenta de almacenamiento y haga clic en **Blobs**. A continuación, haga clic en el contenedor que hospeda el VHD de origen (por ejemplo, *vhds* para el modelo de implementación clásica) o el contenedor en el que desee que se copie el VHD. Haga clic en **Propiedades** para el contenedor y copie el texto etiquetado como **Dirección URL**. Necesitaremos las direcciones URL de los contenedores de origen y de destino. Las direcciones URL tendrán un aspecto similar a `https://myaccount.blob.core.windows.net/mycontainer`.

1. En el equipo local, abra una ventana de comandos y navegue hasta la carpeta donde está instalado AzCopy. Sería similar a *C:\\Program Files (x86) \\Microsoft SDKs\\Azure\\AzCopy*. Desde aquí, ejecute el siguiente comando: </br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] Tendrá que copiar los discos de datos y sistema operativo de forma independiente mediante AzCopy como se ha descrito anteriormente.


## Creación de una máquina virtual mediante el VHD copiado

Estos pasos muestran cómo utilizar Azure PowerShell para crear una máquina virtual de Windows basada en Resource Manager en una nueva red virtual con el VHD copiado en los pasos anteriores. El VHD debe estar presente en la misma cuenta de almacenamiento que la nueva máquina virtual que se va a crear.


Primero configure una red virtual y una NIC para la nueva máquina virtual parecida al siguiente script. Use los valores para las variables (representadas por el signo **$**) según resulte apropiado para su aplicación.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


Ahora establezca las configuraciones de máquina virtual y utilice los VHD copiados para crear una nueva máquina virtual como se muestra a continuación. </br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
	
	#Add data disks using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
	
Las direcciones URL de los discos de datos y del sistema operativo deben ser similares a la siguiente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Puede ver esto en el portal examinando el contenedor de almacenamiento de destino, haciendo clic en el VHD de datos o sistema operativo que se ha copiado y, a continuación, copiando el contenido de la **dirección URL**.
	
	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
	
Si este comando es correcto, verá un resultado similar al siguiente:

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


Debería ver la máquina virtual recién creada en el [Portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales**, O mediante los comandos de PowerShell siguientes:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

Para iniciar sesión en la nueva máquina virtual, utilice la opción **Examinar** en la máquina virtual en el [portal](https://portal.azure.com), haga clic en **Conectar** y abra el archivo RDP del *Escritorio remoto*. Utilice las credenciales de cuenta de la máquina virtual original para iniciar sesión en la nueva máquina virtual.


## Pasos siguientes

Para administrar la nueva máquina virtual de Azure PowerShell, consulte [Administración de máquinas virtuales con Azure Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0511_2016-->