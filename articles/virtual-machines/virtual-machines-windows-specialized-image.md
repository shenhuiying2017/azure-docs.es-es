<properties
	pageTitle="Creación de una copia de la máquina virtual de Windows | Microsoft Azure"
	description="Conozca cómo crear una copia de la máquina virtual de Azure con Windows, en el modelo de implementación de Resource Manager, creando una *imagen especializada*."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
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
	ms.author="cynthn"/>

# Creación de una copia de una máquina virtual con Windows en el modelo de implementación de Azure Resource Manager


En este artículo se muestra cómo crear una copia de su máquina virtual de Azure con Windows. Concretamente, se describe cómo llevar a cabo este procedimiento en el modelo de implementación de Azure Resource Manager mediante Azure PowerShell y el Portal de Azure. Se explica cómo crear una imagen *especializada* de la máquina virtual de Azure, que mantiene las cuentas de usuario y otros datos de estado de la máquina virtual original. Las imágenes especializadas son útiles para la portabilidad de una máquina virtual con Windows del modelo de implementación clásica al modelo de implementación de Resource Manager, o para la creación de una copia de seguridad de la máquina virtual con Windows creada en el modelo de implementación de Resource Manager. Puede copiar a través de los discos de datos y sistema operativo de esta forma y, a continuación, configurar los recursos de red para crear la nueva máquina virtual.

Si necesita crear implementaciones masivas de máquinas virtuales con Windows similares, debe usar una imagen *generalizada*. Para ello, consulte [Captura de una máquina virtual de Windows](virtual-machines-windows-capture-image.md).



## Antes de empezar

Asegúrese de que se cumplen los siguientes requisitos previos antes de comenzar los pasos:

- **Tiene una máquina virtual de Azure con Windows** creada mediante el modelo de implementación clásica o de Resource Manager. Ha configurado el sistema operativo y conectado los discos de datos. Además, ha realizado otras personalizaciones como la instalación de las aplicaciones requeridas. Va a utilizar esta máquina virtual para crear la copia. Si necesita ayuda para crear la máquina virtual de origen, consulte [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](virtual-machines-windows-ps-create.md).

- Tiene** Azure PowerShell 1.0 o posterior** instalado en su equipo y ha iniciado sesión en la suscripción de Azure. Para más información, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

- Ha descargado e instalado la **herramienta AzCopy**. Para más información sobre esta herramienta, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](../storage/storage-use-azcopy.md).

- Tiene un **grupo de recursos**, una **cuenta de almacenamiento** y un **contenedor de blobs** creados en el grupo de recursos en el que va a copiar los VHD. Consulte la sección [Para crear o buscar una cuenta de Almacenamiento de Azure mediante el Portal de Azure](virtual-machines-windows-upload-image.md#createstorage) para conocer los pasos para usar una cuenta de almacenamiento existente o crear una nueva.

> [AZURE.NOTE] Para las máquinas virtuales creadas mediante cualquiera de los dos modelos de implementación se aplican los mismos pasos que a la imagen de origen. En su caso, este artículo indica las diferencias menores.


## Copia de los VHD para la cuenta de almacenamiento de Resource Manager


1. Libere los VHD utilizados por la máquina virtual de origen mediante alguno de los siguientes procedimientos:

	- Si desea copiar la máquina virtual de origen, deténgala y desasígnela.

		- Para una máquina virtual creada mediante el modelo de implementación clásica, puede utilizar el [Portal](https://portal.azure.com) y hacer clic en **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual* > **Detener**, o el comando de PowerShell `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`.

		- Para una máquina virtual creada mediante el modelo de implementación de Resource Manager, puede iniciar sesión en el Portal y hacer clic en **Examinar** > **Máquinas virtuales** > *su máquina virtual* > **Detener**, o usar el comando de PowerShell `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`. Tenga en cuenta que el estado de la máquina virtual en el portal cambia de **En ejecución** a **Detenido (desasignado)**.

	- Si desea migrar la máquina virtual de origen, elimine la máquina virtual y use el VHD anterior. Desplácese a la máquina virtual en el [Portal](https://portal.azure.com) y haga clic en **Eliminar**.

1. Busque las claves de acceso para la cuenta de almacenamiento que contiene el VHD de origen, así como la cuenta de almacenamiento donde se va a copiar el VHD para crear la nueva máquina virtual. La clave de la cuenta desde donde estamos copiando el VHD se denomina *clave de origen*, y la de la cuenta a la que se copiará se denomina *clave de destino*. Para más información acerca de las claves de acceso, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

	- Si la máquina virtual de origen se creó mediante el modelo de implementación clásica, haga clic en **Examinar** > **Cuentas de almacenamiento (clásico)** > *su cuenta de almacenamiento* > **Todas las configuraciones** > **Claves**. Copie la clave con la etiqueta **CLAVE DE ACCESO PRINCIPAL**.

	- Si su máquina virtual se creó con el modelo de implementación de Resource Manager o la cuenta de almacenamiento que se va a utilizar para la nueva máquina virtual, haga clic en **Examinar** > **Cuentas de almacenamiento** > *su cuenta de almacenamiento* > **Todas las configuraciones** > **Claves de acceso**. Copie la clave etiquetada como **clave1**.

1. Obtenga las direcciones URL para obtener acceso a las cuentas de almacenamiento de origen y de destino. En el Portal, examine su cuenta de almacenamiento y haga clic en **Blobs**. A continuación, haga clic en el contenedor que hospeda el VHD de origen (por ejemplo, *vhds* para el modelo de implementación clásica) o el contenedor en el que desee que se copie el VHD. Haga clic en **Propiedades** para el contenedor y copie el texto etiquetado como **Dirección URL**. Necesitará las direcciones URL de los contenedores de origen y de destino. Las direcciones URL tendrán un aspecto similar a `https://myaccount.blob.core.windows.net/mycontainer`.

1. En el equipo local, abra una ventana de comandos y vaya hasta la carpeta donde está instalado AzCopy. (Será similar a *C:\\Program Files (x86) \\Microsoft SDKs\\Azure\\AzCopy*). Desde aquí, ejecute el siguiente comando: </br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] Debe copiar los discos de datos y del sistema operativo por separado, usando AzCopy como se describió en el paso anterior.


## Creación de una máquina virtual mediante el VHD copiado

Con el VHD copiado en los pasos anteriores, ahora puede utilizar Azure PowerShell para crear una máquina virtual con Windows basada en Resource Manager en una nueva red virtual. El VHD debe estar presente en la misma cuenta de almacenamiento que la nueva máquina virtual que se va a crear.


Primero configure una red virtual y una NIC para la nueva máquina virtual parecida al siguiente script. Use los valores para las variables (representadas por el signo **$**) según resulte apropiado para su aplicación.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


Ahora establezca las configuraciones de máquina virtual y utilice los VHD copiados para crear una nueva máquina virtual. </br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk by using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows

	#Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach

Las direcciones URL del disco de datos y sistema operativo deben tener un aspecto similar al siguiente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Para encontrarla en el portal, diríjase al contenedor de almacenamiento de destino, haga clic en el VHD de datos o del sistema operativo que se ha copiado y copie el contenido de la dirección URL.

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Si este comando es correcto, verá un resultado similar al siguiente:

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


Debería ver la máquina virtual recién creada en el [Portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

Para iniciar sesión en la nueva máquina virtual, examine la máquina virtual en el [Portal](https://portal.azure.com), haga clic en **Conectar** y abra el archivo RDP del Escritorio remoto. Utilice las credenciales de cuenta de la máquina virtual original para iniciar sesión en la nueva máquina virtual.


## Pasos siguientes

Para administrar la nueva máquina virtual de Azure PowerShell, consulte [Administración de máquinas virtuales de Azure con Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0525_2016-->