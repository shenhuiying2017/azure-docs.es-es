<properties
	pageTitle="Crear una imagen de máquina virtual desde una máquina virtual de Azure | Microsoft Azure"
	description="Obtenga información sobre cómo crear una imagen de máquina virtual generalizada a partir de una máquina virtual de Azure existente creada en el modelo de implementación de Resource Manager"
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
	ms.date="08/04/2016"
	ms.author="cynthn"/>

# Cómo crear una imagen de máquina virtual desde una máquina virtual de Azure existente


En este artículo se muestra cómo usar Azure PowerShell para crear una imagen generalizada de una máquina virtual de Azure existente. Después, puede usar la imagen para crear otra máquina virtual. Esta imagen incluye el disco del sistema operativo y los discos de datos conectados a la máquina virtual. La imagen no incluye los recursos de red virtual, por lo que tiene que configurar dichos recursos cuando crea una máquina virtual mediante la imagen. Este proceso crea una [imagen de Windows generalizada](https://technet.microsoft.com/library/hh824938.aspx).


## Requisitos previos

- En estos pasos se supone que ya tiene una máquina virtual de Azure en el modelo de implementación de Resource Manager que quiere usar para crear la imagen. Necesita el nombre de la máquina virtual y el nombre del grupo de recursos. Puede obtener una lista de los grupos de recursos de su suscripción si escribe el cmdlet de PowerShell `Get-AzureRmResourceGroup`. Puede obtener una lista de las máquinas virtuales de su suscripción si escribe `Get-AzureRMVM`.

- Debe tener instalada Azure PowerShell versión 1.0.x. Si aún no ha instalado PowerShell, lea [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para ver los pasos de instalación.

- Asegúrese de que los roles de servidor que se ejecutan en la máquina sean compatibles con Sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Compatibilidad de Sysprep con roles de servidor).

## Preparar la máquina virtual de origen 

En esta sección se muestra cómo generalizar la máquina virtual de Windows para que se pueda usar como una imagen.

> [AZURE.WARNING] No puede iniciar sesión en la máquina virtual a través de RDP una vez que se ha generalizado, dado que el proceso elimina todas las cuentas de usuario. Los cambios son irreversibles.

1. Inicie sesión en una máquina virtual de Windows. En el [Portal de Azure](https://portal.azure.com), haga clic en **Examinar** > **Máquinas virtuales** > Su máquina virtual Windows > **Conectar**.

2. Abra una ventana de símbolo del sistema como administrador.

3. Cambie el directorio a `%windir%\system32\sysprep` y después ejecute sysprep.exe.

4. En el cuadro de diálogo **Herramienta de preparación del sistema**, haga lo siguiente:

	- En **Acción de limpieza del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la opción **Generalizar** está marcada. Para obtener más información acerca del uso de Sysprep, consulte [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

	- En **Opciones de apagado**, seleccione **Apagar**.

	- Haga clic en **Aceptar**.

	![Ejecute Sysprep](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   Sysprep apaga la máquina virtual. Su estado cambia a **Detenido** en el Portal de Azure.


## Iniciar sesión en Azure PowerShell

1. Abra Azure PowerShell e inicie sesión en la cuenta de Azure.

		Login-AzureRmAccount

	Se abrirá una ventana emergente para que escriba sus credenciales de cuenta de Azure.

2. Obtenga los identificadores de suscripción para las suscripciones disponibles.

		Get-AzureRmSubscription

3. Establezca la suscripción correcta con el identificador de suscripción.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"


## Desasignar la máquina virtual y establecer el estado en generalizado		

1. Desasigne los recursos de máquina virtual.

		Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>

	El *Estado* de la máquina virtual en el Portal de Azure cambia de **Detenido** a **Detenido (desasignado)**.

2. Establezca el estado de la máquina virtual en **Generalizado**.

		Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized

3. Compruebe el estado de la máquina virtual. En la sección **OSState/generalized** de la máquina virtual, **DisplayStatus** debe estar establecido en **Máquina virtual generalizada**.
		
		$vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -status
		$vm.Statuses

		
## Crear la imagen 

1. Copie la imagen de máquina virtual en el contenedor de almacenamiento de destino con este comando. La imagen se crea en la misma cuenta de almacenamiento que la de la máquina virtual original. La variable `-Path` guarda una copia local de la plantilla JSON. La variable `-DestinationContainerName` es el nombre del contenedor en el que quiere almacenar las imágenes. Si el contenedor no existe, se creará.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -Name YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	Puede obtener la dirección URL de la imagen de la plantilla del archivo JSON. Vaya a la sección **recursos** > **storageProfile** > **osDisk** > **image** > **uri** para ver la ruta de acceso completa de la imagen. La dirección URL de la imagen es similar a esta: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
	
	También puede comprobar el URI en el portal. La imagen se copia en un contenedor denominado **system** de su cuenta de almacenamiento.

2. Cree una variable para la ruta de acceso a la imagen.

		$imageURI = "<https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd>"


## Crear una red virtual

Cree la red virtual y la subred de la [red virtual](../virtual-network/virtual-networks-overview.md).
		

1. Reemplace el valor de las variables por su propia información. Proporcione el prefijo de dirección de la subred en formato CIDR. Cree las variables y la subred.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. Reemplace el valor de **$vnetName** por el nombre de la red virtual. Proporcione el prefijo de dirección de la red virtual en formato CIDR. Cree la variable y la red virtual con la subred.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## Creación de una dirección IP y una interfaz de red

Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

1. Reemplace el valor de **$ipName** por el nombre de la dirección IP pública. Cree la variable y la dirección IP pública.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Reemplace el valor de **$nicName** por el nombre de la interfaz de red. Cree la variable y la interfaz de red.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


## Creación de la máquina virtual

En el siguiente script de PowerShell se muestra cómo establecer las configuraciones de máquina virtual y usar la imagen de máquina virtual cargada como origen para la nueva instalación.

>[AZURE.NOTE] La máquina virtual debe estar en la misma cuenta de almacenamiento que el VHD original.

</br>

	
	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account 
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"
	
	# Assign a SKU name
	# Valid values for -SkuName are: **Standard_LRS** - locally redundant storage, **Standard_ZRS** - zone redundant storage, **Standard_GRS** - geo redundant storage, **Standard_RAGRS** - read access geo redundant storage, **Premium_LRS** - premium locally redundant storage. 
	$skuName = "<skuName>"
	
	# Create a new storage account for the VM
	New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $storageAccName -Location $location -SkuName $skuName -Kind "Storage"

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
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $imageURI -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



Cuando finalice, debería ver la máquina virtual recién creada en el [Portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Pasos siguientes

Para administrar la nueva máquina virtual con Azure PowerShell, consulte [Administración de máquinas virtuales de Azure con Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0824_2016-->