<properties
	pageTitle="Creación de una máquina virtual Windows con el Administrador de recursos de Azure y PowerShell."
	description="Utilice el modo de administración de recursos de Azure PowerShell para crear fácilmente una nueva máquina virtual Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="davidmu"/>

# Creación de una máquina virtual Windows con el Administrador de recursos de Azure y PowerShell.

En este tema se describe cómo crear rápidamente una máquina virtual de Azure basada en Windows mediante el Administrador de recursos de Azure y PowerShell.

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Creación de una máquina virtual Windows con PowerShell y Administración de servicios de Azure](virtual-machines-create-windows-powershell-service-manager.md)

## Creación de la máquina virtual de Windows

Si ya ha instalado Azure PowerShell, debe tener la versión 0.9.0 o posterior de Azure PowerShell. Puede comprobar la versión de Azure PowerShell que ha instalado con este comando en el símbolo del sistema de Azure PowerShell.

	Get-Module azure | format-table version

Si aún no lo ha hecho o debe actualizar la versión del Azure PowerShell instalado, utilice las instrucciones de [Instalación y configuración de Azure PowerShell](install-configure-powershell.md) para instalar Azure PowerShell en el equipo local. A continuación, abra un símbolo del sistema de Azure PowerShell.

En primer lugar, debe iniciar sesión en Azure con este comando.

	Add-AzureAccount

Especifique la dirección de correo electrónico de la cuenta de Azure y su contraseña en el cuadro de diálogo de inicio de sesión de Microsoft Azure.

A continuación, si tiene varias suscripciones de Azure, deberá establecer la suscripción de Azure. Para ver una lista de las suscripciones actuales, ejecute este comando.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Ahora, reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por el nombre de suscripción correcto y ejecute estos comandos.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

A continuación, deberá crear una cuenta de almacenamiento. Debe elegir un nombre único que contenga solo letras minúsculas y números. Se puede comprobar que el nombre de cuenta de almacenamiento sea único con este comando.

	Test-AzureName -Storage <Proposed storage account name>

Si este comando devuelve "False", el nombre propuesto es único.

Deberá especificar la ubicación de un centro de datos de Azure. Para obtener una lista de centros de datos de Azure, ejecute este comando.

	Get-AzureLocation | sort Name | Select Name

A continuación, deberá cambiar el modo de Azure PowerShell a Administrador de recursos. Ejecute este comando.

	Switch-AzureMode AzureResourceManager

Ahora, copie el siguiente bloque de comandos de PowerShell en un editor de texto. Rellene la cuenta de almacenamiento y la ubicación elegidas, y reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >.

	$stName="<chosen storage account name>"
	$locName="<chosen Azure location name>"
	$rgName="TestRG"
	New-AzureResourceGroup -Name $rgName -Location $locName
	$storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	$singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	$vnet=New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	$pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$cred = Get-Credential -Message "Type the name and password of the local administrator account."
	$vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	$vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
	$vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Por último, copie el conjunto de comandos anterior en el Portapapeles y, a continuación, haga clic con el botón secundario en el símbolo del sistema del Azure PowerShell abierto. Esto emitirá el conjunto de comandos como una serie de comandos de PowerShell, le solicitará el nombre y la contraseña de la cuenta de administrador local y creará la máquina virtual de Azure.

Este es un ejemplo de lo que puede ver:

	PS C:\> $stName="contosost"
	PS C:\> $locName="West US"
	PS C:\> $rgName="TestRG"
	PS C:\> New-AzureResourceGroup -Name $rgName -Location $locName
	VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'


	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *

	ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG


	PS C:\> $storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	PS C:\> $singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	PS C:\> $vnet=New-AzurevirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	PS C:\> $pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	PS C:\> $nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	PS C:\> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	PS C:\> $vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	PS C:\> $vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	PS C:\> $vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	PS C:\> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
	PS C:\> $vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	PS C:\> New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm


	EndTime             : 4/28/2015 1:00:05 PM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 12:52:52 PM -07:00
	Status              : Succeeded
	TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
	RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
	StatusCode          : OK

## Recursos adicionales

[Proceso, red y proveedores de almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](resource-group-overview.md)

[Creación de una máquina virtual Windows con una plantilla del Administrador de recursos y PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Creación de una máquina virtual Windows con PowerShell y Administración de servicios de Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Instalación y configuración de Azure PowerShell](install-configure-powershell.md)

<!---HONumber=July15_HO4-->