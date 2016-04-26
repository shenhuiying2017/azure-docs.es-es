<properties
	pageTitle="Creación de una máquina virtual de Azure con PowerShell | Microsoft Azure"
	description="Use Azure PowerShell y Azure Resource Manager para crear fácilmente una nueva máquina virtual con Windows Server."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/12/2016"
	ms.author="davidmu"/>

# Creación de una máquina virtual de Windows con Resource Manager y PowerShell

En este artículo se muestra cómo crear rápidamente una máquina virtual de Azure con Windows Server y sus recursos asociados mediante Resource Manager y PowerShell.

Tardará unos 30 minutos en realizar los pasos de este artículo.

## Paso 1: Instalación de Azure PowerShell

Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta de Azure.
        
## Paso 2: Creación de un grupo de recursos

Todos los recursos se deben implementar en un grupo de recursos. Para más información, consulte [Información general de Azure Resource Manager](../resource-group-overview.md).

1. Obtenga una lista de las ubicaciones donde se pueden crear los recursos.

	    Get-AzureLocation | sort Name | Select Name

2. Reemplace el valor de **$locName** por una ubicación de la lista, por ejemplo **centro de EE. UU**. Cree la variable.

        $locName = "location name"
        
3. Reemplace el valor de **$rgName** por el nombre del nuevo grupo de recursos. Cree la variable y el grupo de recursos.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Paso 3: Creación de una cuenta de almacenamiento

Se necesita una cuenta de almacenamiento para almacenar el disco duro virtual que está asociado a la máquina virtual que cree.

1. Reemplace el valor de **$stName** (letras minúsculas y números) por el nombre de la cuenta de almacenamiento. Compruebe que el nombre sea único.

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    Si este comando devuelve **False**, el nombre propuesto es único.
    
2. Ahora, ejecute el comando para crear la cuenta de almacenamiento.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## Paso 4: Creación de una red virtual

Todas las máquinas virtuales debe estar asociadas a una red virtual.

1. Reemplace el valor de **$subnetName** por el nombre de la subred. Cree la variable y la subred.
    	
        $subnetName = "subnet name"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Reemplace el valor de **$vnetName** por el nombre de la red virtual. Cree la variable y la red virtual con la subred.

        $vnetName = "virtual network name"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
## Paso 5: Creación de una dirección IP y una interfaz de red

Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una dirección IP pública y una interfaz de red.

1. Reemplace el valor de **$ipName** por el nombre de la dirección IP pública. Cree la variable y la dirección IP pública.

        $ipName = "public IP address name"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Reemplace el valor de **$nicName** por el nombre de la interfaz de red. Cree la variable y la interfaz de red.

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Paso 6: Creación de una máquina virtual

Ahora que tiene todas las piezas en su sitio, es el momento de crear la máquina virtual.

1. Ejecute el comando para establecer el nombre de la cuenta de administrador y la contraseña de la máquina virtual.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
2. Reemplace el valor de **$vmName** `por el nombre de la máquina virtual. Cree la variable y la configuración de la máquina virtual.

        $vmName = "virtual machine name"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    Consulte [Tamaños de las máquinas virtuales Windows en Azure](virtual-machines-windows-sizes.md) para ver una lista de los tamaños disponibles para una máquina virtual.
    
3. Reemplace el valor de **$compName** por el nombre de equipo de la máquina virtual. Cree la variable y agregue la información del sistema operativo a la configuración.

        $compName = "computer name"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Defina la imagen que se usará para aprovisionar la máquina virtual.

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Consulte [Navegación y selección de las imágenes de máquina virtual Windows en Azure con PowerShell o CLI](virtual-machines-windows-cli-ps-findimage.md) para más información sobre cómo seleccionar las imágenes que se utilizarán.
        
5. Agregue la interfaz de red que creó a la configuración.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Reemplace el valor de **$blobPath** por la ruta de acceso y el nombre de archivo del almacenamiento del disco duro virtual. Normalmente, el archivo vhd se almacena en un contenedor, por ejemplo "vhds/WindowsVMosDisk.vhd". Cree las variables.

        $blobPath = "vhd path and file name"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Reemplace el valor de **$diskName** por el nombre del disco del sistema operativo. Cree la variable y agregue la información del disco a la configuración.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Finalmente, cree la máquina virtual.

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    Debería ver el grupo de recursos y todos sus recursos en el Portal de Azure y un estado correcto en la ventana de PowerShell:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## Pasos siguientes

- Si hay problemas con la implementación, el paso siguiente sería mirar en [Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Aprenda a administrar la máquina virtual que acaba de crear. Para ello, consulte [Administración de máquinas virtuales con Azure Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).
- Aproveche las ventajas de usar una plantilla para crear una máquina virtual con la información que se muestra en [Creación de una máquina virtual Windows con una plantilla de Resource Manager](virtual-machines-windows-ps-template.md).

<!---HONumber=AcomDC_0420_2016-->