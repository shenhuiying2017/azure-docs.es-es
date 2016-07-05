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
	ms.date="05/06/2016"
	ms.author="cynthn"/>

# Carga de una imagen de máquina virtual de Windows en Azure para implementaciones de Resource Manager


En este artículo se muestra cómo puede cargar un disco duro virtual (VHD) con un sistema operativo Windows, que podrá utilizar para crear nuevas máquinas virtuales de Windows utilizando el modelo de implementación de Azure Resource Manager. Para más detalles sobre discos y VHD en Azure, consulte [Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md).



## Requisitos previos

En este artículo se supone que ha:

- **Una suscripción a Azure**: si todavía no la tiene, [abra una cuenta de Azure gratis](/pricing/free-trial/?WT.mc_id=A261C142F) y [active los beneficios de suscripción a MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

- **Azure PowerShell versión 1.4 o superior**: si todavía no la ha instalado, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

- **Una máquina virtual de Windows**: hay muchas herramientas para crear máquinas virtuales locales. Por ejemplo, consulte [Instalar Hyper-V y crear una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx). Para saber qué sistemas operativos Windows admiten Azure, consulte [Soporte de software de servidor de Microsoft para las máquinas virtuales de Microsoft Azure](https://support.microsoft.com/kb/2721672).


## Comprobación de que la máquina virtual tiene el formato de archivo correcto

Azure solo puede aceptar imágenes para [máquinas virtuales de generación 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx), que se guardan en el formato de archivo VHD. El tamaño del VHD debe ser fijo y ser un número entero de megabytes, es decir, un número divisible por 8. El tamaño máximo permitido para los discos duros virtuales es de 1023 GB.

- Si tiene una imagen de máquina virtual de Windows en formato VHDX, conviértalo a un disco duro virtual mediante cualquiera de las siguientes opciones:

	- Hyper-V: abra Hyper-V y seleccione el equipo local a la izquierda. En el menú superior, haga clic en **Acción** > **Editar disco...** Desplácese por las pantallas haciendo clic en **Siguiente** y especificando estas opciones: *Ruta de acceso para el archivo VHDX* > **Convertir** > **VHD** > **Tamaño fijo** > *Ruta de acceso para el nuevo archivo de VHD*. Haga clic en **Finalizar** para cerrar.

	- [Cmdlet Convert-VHD PowerShell](http://technet.microsoft.com/library/hh848454.aspx): consulte la entrada del blog [Converting Hyper-V .vhdx to .vhd file formats](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) (Conversión de formatos de archivo Hyper-V .de vhdx a .vhd) para más información.

- Si tiene una imagen de máquina virtual de Windows en [formato de archivo VMDK](https://en.wikipedia.org/wiki/VMDK), puede convertirla a un formato VHD mediante [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Consulte el blog [How to Convert a VMWare VMDK to Hyper-V VHD (Cómo convertir un VMDK de VMWare a VHD de Hyper-V)](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) para más información.


## Preparación del VHD para carga

En esta sección se muestra cómo generalizar la máquina virtual de Windows. Entre otras cosas, se elimina toda la información personal de la cuenta. Normalmente, deseará hacer esto si quiere usar esta imagen de máquina virtual para implementar rápidamente máquinas virtuales similares. Para obtener más información sobre Sysprep, vea [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

1. Inicie sesión en la máquina virtual de Windows.

2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a **%windir%\\system32\\sysprep** y, a continuación, ejecute `sysprep.exe`.

3. En el cuadro de diálogo **Herramienta de preparación del sistema**, haga lo siguiente:

	1. En **Acción de limpieza del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la casilla **Generalizar** está activada.

	2. En **Opciones de apagado**, seleccione **Apagar**.

	3. Haga clic en **Aceptar**.

	![Iniciar Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br> <a id="createstorage"></a>
## Creación o búsqueda de una cuenta de almacenamiento de Azure

Necesitará una cuenta de almacenamiento de Azure para cargar la imagen de VM. Puede usar una cuenta de almacenamiento existente o crear una nueva. Puede usar el Portal de Azure o PowerShell para hacer esto.

### Para crear o buscar una cuenta de Almacenamiento de Azure mediante el Portal de Azure

1. Inicie sesión en el [portal](https://portal.azure.com).

2. Haga clic en **Examinar** > **Cuentas de almacenamiento**.

3. Compruebe si existe una cuenta de almacenamiento que desee usar para cargar esta imagen. Anote el nombre de esta cuenta de almacenamiento. Puede continuar con la sección [Carga de la imagen de VM en la cuenta de almacenamiento](#uploadvm) si utiliza una cuenta de almacenamiento existente.

4. Si desea crear una nueva cuenta de almacenamiento, haga clic en **Agregar** y escriba la información siguiente:

	1. En **Nombre**, escriba un nombre para la cuenta de almacenamiento. Debe contener entre 3 y 24 letras minúsculas y números únicamente. Este nombre se convierte en parte de la dirección URL que se va a utilizar para acceder a los blobs, archivos y otros recursos de la cuenta de almacenamiento.
	
	2. Seleccione *Resource Manager* como **modelo de implementación**.

	3. Seleccione los valores apropiados de la **variante de cuenta**, **rendimiento** y **replicación**. Puede pasar el cursor por los iconos de información para saber más de estos valores.

	4. Seleccione *+Nuevo* en **Grupo de recursos** o uno existente. Escriba el nombre del nuevo grupo de recursos si desea crear uno nuevo.

	5. En **Ubicación**, elija la ubicación de almacenamiento y haga clic en **Crear**. La cuenta aparece ahora en el panel **Cuentas de almacenamiento**.

		![Escribir los detalles de la cuenta de almacenamiento](./media/virtual-machines-windows-upload-image/portal_create_storage_account.png)

	6. Este paso y los pasos siguientes muestran cómo crear un contenedor de blobs en esta cuenta de almacenamiento. Esto es opcional porque también puede usar el comando de PowerShell que carga la imagen para crear un nuevo contenedor de blobs para la imagen. Si no desea crearlo usted mismo, continúe con la sección [Carga de la imagen de VM en la cuenta de almacenamiento](#uploadvm). En caso contrario, haga clic en **Blobs** en el icono **Servicios**.

		![Servicio BLOB](./media/virtual-machines-windows-upload-image/portal_create_blob.png)

	7. Cuando aparezca el panel de blobs, haga clic en **+ Contenedor** para crear un nuevo contenedor de almacenamiento de blobs. Escriba el nombre del contenedor y el tipo de acceso.

		![Crear un blob nuevo](./media/virtual-machines-windows-upload-image/portal_create_container.png)

  		> [AZURE.NOTE] De manera predeterminada, el contenedor es privado y solo puede acceder a él el propietario de la cuenta. Para conceder permiso de lectura público a los blobs del contenedor, pero no a las propiedades y metadatos de dicho contenedor, use la opción **Blob**. Para conceder permiso de acceso de lectura público completo para el contenedor y los blobs, use la opción **Contenedor**.

	8. El panel **Servicio BLOB** mostrará el nuevo contenedor de blobs. Anote la dirección URL de este contenedor, ya que la necesitará para que el comando de PowerShell cargue la imagen. Dependiendo de la longitud de la dirección URL y la resolución de pantalla, la dirección URL podría ocultarse parcialmente. Si esto sucede, maximice el panel haciendo clic en el icono **Maximizar** situado en la esquina superior derecha.


### Para crear o buscar una cuenta de Almacenamiento de Azure mediante PowerShell

1. Abra Azure PowerShell e inicie sesión en la cuenta de Azure.

		Login-AzureRmAccount

	Este comando abrirá una ventana emergente para escribir sus credenciales de Azure.

2. Si el identificador de suscripción que se selecciona de forma predeterminada es diferente del identificador con que desea trabajar, use cualquiera de los comandos siguientes para establecer la suscripción adecuada.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	o

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Puede encontrar las suscripciones que tiene la cuenta de Azure mediante el comando `Get-AzureRmSubscription`.

3. Busque las cuentas de almacenamiento disponibles bajo esta suscripción.

		Get-AzureRmStorageAccount

	Si desea utilizar una cuenta de almacenamiento existente, puede continuar con la sección [Carga de la imagen de VM en la cuenta de almacenamiento](#uploadvm).

4. Si desea crear una nueva cuenta de almacenamiento para hospedar esta imagen, siga estos pasos:

	1. Asegúrese de que tiene un grupo de recursos para esta cuenta de almacenamiento. Averigüe todos los grupos de recursos de la suscripción mediante:

			Get-AzureRmResourceGroup

	2. Si desea crear un nuevo grupo de recursos, use este comando:

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. Cree una nueva cuenta de almacenamiento en este grupo de recursos mediante:

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -SkuName "Standard_GRS" -Kind "Storage"


</br> <a id="uploadvm"></a>

## Carga de la imagen de VM en la cuenta de almacenamiento

Use estos pasos en Azure PowerShell para cargar la imagen de máquina virtual en la cuenta de almacenamiento. La imagen se cargará en un contenedor de Almacenamiento de blobs en esta cuenta. Puede usar un contenedor existente o crear uno nuevo.

1. Inicie sesión en Azure PowerShell 1.0 mediante `Login-AzureRmAccount`. Asegúrese de que está utilizando la suscripción correcta mediante el uso de `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"`, tal como se mencionó en la sección anterior.

2. Agregue el disco duro virtual de Azure generalizado a la cuenta de almacenamiento mediante el cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx):

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	Donde:
	- **StorageAccountURL** es la dirección URL de la cuenta de almacenamiento. Normalmente tendrá este formato: `https://YourStorageAccountName.blob.core.windows.net`. Tenga en cuenta que debe usar el nombre de la cuenta de almacenamiento nueva o existente en lugar de *YourStorageAccountName*.
	- **BlobContainer** es el contenedor de blobs en el que desea almacenar las imágenes. Si el cmdlet no encuentra ningún contenedor de blobs existente con este nombre, creará uno nuevo.
	- **TargetVHDName** es el nombre con el que desea guardar la imagen.
	- **LocalPathOfVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd en la máquina local.

	Una ejecución `Add-AzureRmVhd` correcta tendrá un aspecto similar al siguiente:

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

	Este comando tardará algún tiempo en completarse, dependiendo de la conexión de red y el tamaño del archivo VHD.

</br>
## Implementación de una nueva VM a partir de la imagen capturada

Ahora puede usar la imagen cargada para crear una nueva VM de Windows. Estos pasos explican cómo usar Azure PowerShell y la imagen de máquina virtual que ha cargado en los pasos anteriores para crear la máquina virtual en una nueva red virtual.

>[AZURE.NOTE] La imagen de máquina virtual debe estar presente en la misma cuenta de almacenamiento que la máquina virtual real que va a crear.

### Crear recursos de red

Use el siguiente script de PowerShell de ejemplo para configurar una red virtual y la NIC para la nueva máquina virtual. Use los valores para las variables que están representadas por el signo **$**, según resulte apropiado para su aplicación.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Creación de una máquina virtual nueva

En el siguiente script de PowerShell se muestra cómo establecer las configuraciones de máquina virtual y usar la imagen de máquina virtual cargada como origen para la nueva instalación. </br>

	#Enter a new user name and password in the pop-up window for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Por ejemplo, el flujo de trabajo podría ser algo parecido a esto:

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

Debería ver la máquina virtual recién creada en el [Portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Pasos siguientes

Para administrar la nueva máquina virtual de Azure PowerShell, consulte [Administración de máquinas virtuales de Azure con Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0622_2016-->