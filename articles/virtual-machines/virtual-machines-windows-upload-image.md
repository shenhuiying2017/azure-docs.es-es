<properties
	pageTitle="Carga de un VHD de Windows para el Administrador de recursos | Microsoft Azure"
	description="Aprenda a cargar una imagen de máquina virtual de Windows para usar con el modelo de implementación de Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="cynthn"/>

# Carga de una imagen de máquina virtual de Windows en Azure para implementaciones de Resource Manager


En este artículo se muestra cómo crear y cargar una imagen de disco duro virtual (VHD) de Windows para que pueda crear rápidamente máquinas virtuales. Para más detalles sobre discos y VHD en Azure, consulte [Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md).


## Requisitos previos

En este artículo se supone que ha:

- **Una suscripción a Azure**: si todavía no la tiene, [abra una cuenta de Azure gratis](/pricing/free-trial/?WT.mc_id=A261C142F) o [active los beneficios de suscripción a MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

- **Azure PowerShell versión 1.4 o superior**: si todavía no la ha instalado, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

- **Una máquina virtual de Windows**: hay muchas herramientas para crear máquinas virtuales locales. Por ejemplo, consulte [Instalar Hyper-V y crear una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx). Para más información sobre qué sistemas operativos Windows se admiten en Azure, consulte [Soporte de software de servidor de Microsoft para las máquinas virtuales de Microsoft Azure](https://support.microsoft.com/kb/2721672).

- Asegúrese de que los roles de servidor que se ejecutan en la máquina virtual sean compatibles con sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Compatibilidad de Sysprep con roles de servidor). Cree una copia de seguridad de la VM antes de ejecutar Sysprep.


## Comprobación de que la máquina virtual se encuentra en el formato de archivo correcto

En Azure, solo puede usar [máquinas virtuales de generación 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) que están en el formato de archivo VHD. El VHD debe ser de tamaño fijo y ser un número entero de megabytes, es decir, un número divisible por 8. El tamaño máximo permitido para los discos duros virtuales es de 1023 GB.

- Si tiene una imagen de máquina virtual de Windows en formato VHDX, conviértalo a un disco duro virtual mediante cualquiera de las siguientes opciones:

	- Hyper-V: abra Hyper-V y seleccione el equipo local a la izquierda. En el menú superior, haga clic en **Acción** > **Editar disco...** Desplácese por las pantallas haciendo clic en **Siguiente** y especificando estas opciones: *Ruta de acceso para el archivo VHDX* > **Convertir** > **VHD** > **Tamaño fijo** > *Ruta de acceso para el nuevo archivo de VHD*. Haga clic en **Finalizar** para cerrar.

	- [Cmdlet Convert-VHD PowerShell](http://technet.microsoft.com/library/hh848454.aspx): consulte la entrada del blog [Converting Hyper-V .vhdx to .vhd file formats](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) (Conversión de formatos de archivo Hyper-V .de vhdx a .vhd) para más información.

- Si tiene una imagen de máquina virtual de Windows en [formato de archivo VMDK](https://en.wikipedia.org/wiki/VMDK), puede convertirla a un formato VHD mediante [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Consulte el blog [How to Convert a VMWare VMDK to Hyper-V VHD (Cómo convertir un VMDK de VMWare a VHD de Hyper-V)](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) para más información.


## Preparación del VHD para carga

En esta sección se muestra cómo generalizar la máquina virtual de Windows. Entre otras cosas, Sysprep elimina toda la información personal de la cuenta. Para obtener más información sobre Sysprep, vea [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

1. Inicie sesión en la máquina virtual de Windows.

2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a **%windir%\\system32\\sysprep** y, a continuación, ejecute `sysprep.exe`.

3. En el cuadro de diálogo **Herramienta de preparación del sistema**, haga lo siguiente:

	1. En **Acción de limpieza del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la casilla **Generalizar** está activada.

	2. En **Opciones de apagado**, seleccione **Apagar**.

	3. Haga clic en **Aceptar**.

	![Iniciar Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br>


## Inicie sesión en Azure.

1. Abra Azure PowerShell e inicie sesión en la cuenta de Azure.

		Login-AzureRmAccount

	Se abrirá una ventana emergente para que escriba sus credenciales de cuenta de Azure.

2. Obtenga los identificadores de suscripción para las suscripciones disponibles.

		Get-AzureRmSubscription

3. Establezca la suscripción correcta con el identificador de suscripción.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"

	
## Obtención de la cuenta de almacenamiento

Necesita una cuenta de almacenamiento de Azure para hospedar la imagen de máquina virtual cargada. Puede usar una cuenta de almacenamiento existente o crear una nueva.

Muestre las cuentas de almacenamiento disponibles.

		Get-AzureRmStorageAccount

Si desea utilizar una cuenta de almacenamiento existente, puede continuar con la sección [Carga de la imagen de VM en la cuenta de almacenamiento](#upload-the-vm-image-to-your-storage-account).

Si quiere crear una nueva cuenta de almacenamiento, siga estos pasos:

1. Asegúrese de que tiene un grupo de recursos para esta cuenta de almacenamiento. Averigüe todos los grupos de recursos de la suscripción mediante:

		Get-AzureRmResourceGroup

2. Para crear un grupo de recursos, use este comando:

		New-AzureRmResourceGroup -Name <resourceGroupName> -Location <location>

3. Cree una cuenta de almacenamiento en este grupo de recursos con el cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx):

		New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Location "<location>" -SkuName "<skuName>" -Kind "Storage"
			
Los valores válidos para - SkuName son los siguientes:

- **Standard\_LRS**: almacenamiento con redundancia local.
- **Standard\_ZRS**: almacenamiento con redundancia de zona.
- **Standard\_GRS**: almacenamiento con redundancia geográfica.
- **Standard\_RAGRS**: almacenamiento con redundancia geográfica con acceso de lectura.
- **Premium\_LRS**: almacenamiento con redundancia local Premium.



## Carga de la imagen de VM en la cuenta de almacenamiento

Utilice el cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) para cargar la imagen en un contenedor de su cuenta de almacenamiento:

		$rgName = "<resourceGroupName>"
		$urlOfUploadedImageVhd = "<storageAccount>/<blobContainer>/<targetVHDName>.vhd"
		Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath <localPathOfVHDFile>

Donde:

- **storageAccount** es el nombre de la cuenta de almacenamiento para la imagen.

- **blobContainer** es el contenedor de blobs donde desea almacenar la imagen. Si no se encuentra un contenedor de blobs existente con este nombre, se crea uno automáticamente.

- **targetVHDName** es el nombre que quiere utilizar para el archivo VHD cargado.

- **LocalPathOfVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd en la máquina local.


Si se realiza correctamente, obtendrá una respuesta similar a la siguiente:

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

Este comando tardará algún tiempo en completarse, dependiendo de la conexión de red y del tamaño del archivo VHD.




## Crear una red virtual

Cree la red virtual y la subred de la [red virtual](../virtual-network/virtual-networks-overview.md).

1. Reemplace el valor de las variables por su propia información. Proporcione el prefijo de dirección de la subred en formato CIDR. Cree las variables y la subred.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. Reemplace el valor de **$vnetName** por el nombre de la red virtual. Proporcione el prefijo de dirección de la red virtual en formato CIDR. Cree la variable y la red virtual con la subred.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## Creación de una dirección IP y una interfaz de red

Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

1. Reemplace el valor de **$ipName** por el nombre de la dirección IP pública. Cree la variable y la dirección IP pública.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
        
2. Reemplace el valor de **$nicName** por el nombre de la interfaz de red. Cree la variable y la interfaz de red.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

		

## Creación de la máquina virtual

En el siguiente script de PowerShell se muestra cómo establecer las configuraciones de máquina virtual y usar la imagen de máquina virtual cargada como origen para la nueva instalación.

>[AZURE.NOTE] La máquina virtual debe estar en la misma cuenta de almacenamiento que el archivo VHD cargado.

</br>

	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account where the VHD file is and where the OS disk will be created
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You set this variable when you uploaded the VHD
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



Cuando finalice, debería ver la máquina virtual recién creada en el [Portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Pasos siguientes

Para administrar la nueva máquina virtual de Azure PowerShell, consulte [Administración de máquinas virtuales con Azure Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0831_2016-->