<properties
	pageTitle="Conjunto de escalas de máquina virtual| Microsoft Azure"
	description="Creación de un conjunto de escalado de máquinas virtuales mediante Powershell"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="davidmu"/>

# Creación de un conjunto de escalado de máquinas virtuales de Windows mediante Azure PowerShell

En estos pasos se sigue un enfoque consistente en atar cabos para crear un conjunto de escalado de máquinas virtuales de Azure. Para obtener más información sobre los conjuntos de escala, consulte [Información general de conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-overview.md).

Tardará unos 30 minutos en realizar los pasos de este artículo.

## Paso 1: Instalación de Azure PowerShell

Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta de Azure.

## Paso 2: Creación de recursos

Cree los recursos necesarios para su nuevo conjunto de escalado de máquinas virtuales.

### Grupos de recursos

Un conjunto de escalado de máquinas virtuales debe estar contenido en un grupo de recursos.

1.  Obtenga una lista de ubicaciones y servicios disponibles admitidos:

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    Puede ver algo así:

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

2. Elija una ubicación que mejor le convenga, reemplace el valor de **$locName** por ese nombre de ubicación y, a continuación, cree la variable:

        $locName = "location name from the list, such as Central US"

3. Reemplace el valor de **$rgName** por el nombre que desee utilizar para el nuevo grupo de recursos y, a continuación, cree la variable:

        $rgName = "resource group name"
        
4. Creación del grupo de recursos:
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Puede ver algo así:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### Cuenta de almacenamiento

Las máquinas virtuales utilizan las cuentas de almacenamiento para almacenar el disco del sistema operativo y los datos de diagnóstico que se emplean en las operaciones de escalado. Cuando sea posible, se recomienda tener una cuenta de almacenamiento para cada una de las máquinas virtuales creadas en un conjunto de escalas. Si procede, no planee disponer de más de 20 máquinas virtuales por cuenta de almacenamiento. En el ejemplo de este artículo se muestran 3 cuentas de almacenamiento que se crean para 3 máquinas virtuales de un conjunto de escalas.

1. Reemplace el valor de **$rgName** por el nombre que desee utilizar para la cuenta de almacenamiento y, a continuación, cree la variable: 

        $saName = "storage account name"
        
2. Comprobación de si el nombre que seleccionó es único:
    
        Test-AzureName -Storage $saName

    Si la respuesta es **False**, el nombre propuesto es único.

3. Reemplace el valor de **$rgName** por el tipo de cuenta de almacenamiento y, a continuación, cree la variable:

        $saType = "storage account type"
        
    Los valores posibles son: Standard\_LRS, Standard\_GRS, Standard\_RAGRS o Premium\_LRS.
        
4. Creación de la cuenta:
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Puede ver algo así:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
	                    	.Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

5. Repita los pasos del 1 al 4 para crear 3 cuentas de almacenamiento; por ejemplo, myst1, myst2 y myst3.

### Red virtual

Se requiere una red virtual para las máquinas virtuales del conjunto de escalado.

1. Reemplace el valor de **$subName** por el nombre que desee utilizar para la subred de la red virtual y, después, cree la variable: 

        $subName = "subnet name"
        
2. Creación de la configuración de subred:
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24
        
    El prefijo de dirección puede ser diferente en la red virtual.

3. Reemplace el valor de **$netName** por el nombre que desee utilizar para la red virtual y, después, cree la variable:

        $netName = "virtual network name"
        
4. Creación de la red virtual.
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### Dirección IP pública

Antes de poder crear una interfaz de red, necesita crear una dirección IP pública.

1. Reemplace el valor de **$domName** por la etiqueta de nombre de dominio que desea utilizar para la dirección IP pública y, después, cree la variable:  

        $domName = "domain name label"
        
    La etiqueta puede contener solo letras, números y guiones, y el último carácter debe ser una letra o un número:
    
2. Compruebe si el nombre es único:
    
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    Si la respuesta es **True**, significa que el nombre propuesto es único.

3. Reemplace el valor de **$pipName** por el nombre que desee utilizar para la dirección IP pública y, después, cree la variable.

        $pipName = "public ip address name"
        
4. Creación de la dirección IP pública:
    
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### Interfaz de red

Ahora que tiene la dirección IP pública, puede crear la interfaz de red.

1. Reemplace el valor de **$nicName** por el nombre que desee utilizar para la interfaz de red y, después, cree la variable: 

        $nicName = "network interface name"
        
2. Creación de la interfaz de red:
    
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Configuración del conjunto de escalado

Tiene todos los recursos que necesita para la configuración del conjunto de escalado, así que creémosla.

1. Reemplace el valor de **$ipName** por el nombre que desee utilizar para la configuración de IP y, después, cree la variable: 

        $ipName = "IP configuration name"
        
2. Creación de la configuración de IP:

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Reemplace el valor de **$vmssConfig** por el nombre que desee utilizar para la configuración del conjunto de escalado y, después, cree la variable:

        $vmssConfig = "Scale set configuration name"
        
3. Cree la configuración para el conjunto de escalado:

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    En este ejemplo se muestra un conjunto de escalado que se crea con 3 máquinas virtuales. Para obtener más información sobre la capacidad de los conjuntos de escala, consulte [Información general de conjuntos de escala de máquinas virtuales](virtual-machine-scale-sets-overview.md). Este paso también incluye el establecimiento del tamaño (al que se hace referencia como SkuName) de las máquinas virtuales del conjunto. Consulte [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md) para encontrar un tamaño que satisfaga sus necesidades.
    
4. Adición de la configuración de la interfaz de red a la configuración del conjunto de escalado:
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Puede ver algo así:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### Perfil de sistema operativo

1. Reemplace el valor de **$computerName** por el prefijo de nombre de equipo que quiera usar y, después, cree la variable: 

        $computerName = "computer name prefix"
        
2. Reemplace el valor de **$adminName** por el nombre de la cuenta de administrador en las máquinas virtuales y, después, cree la variable:

        $adminName = "administrator account name"
        
3. Reemplace el valor de **$adminPassword** por la contraseña de cuenta y, después, cree la variable:

        $adminPassword = "password for administrator accounts"
        
4. Creación del perfil del sistema operativo:

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### Perfil de almacenamiento

1. Reemplace el valor de **$storageProfile** por el nombre que desee utilizar para el perfil de almacenamiento y, después, cree la variable:  

        $storeProfile = "storage profile name"
        
2. Creación de las variables que definen la imagen que usar:
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    Para encontrar información sobre otras imágenes que usar, consulte [Navegación y selección de las imágenes de máquina virtual Linux en Azure con CLI o PowerShell](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
        
3. Reemplace el valor de **$vhdContainers** por una lista que contenga las rutas de acceso donde se almacenan los discos duros virtuales, como "https://mystorage.blob.core.windows.net/vhds" y, después, cree la variable:
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. Creación de un perfil de almacenamiento:

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### Conjunto de escalado de máquina virtual

Por último, puede crear el conjunto de escalado.

1. Reemplace el texto de **$vmssName** por el nombre del conjunto de escalado de máquina virtual y cree la variable:

        $vmssName = "scale set name"
        
2. Creación del conjunto de escalado:

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Debería ver algo similar a lo siguiente que muestra que la implementación se realizó correctamente:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                               ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## Paso 3: Exploración de recursos

Use estos recursos para explorar el conjunto de escalado de máquinas virtuales que acaba de crear:

- Portal de Azure: Hay disponible una cantidad limitada de información mediante el portal.
- [Explorador de recursos de Azure](https://resources.azure.com/): se trata de es la mejor herramienta para explorar el estado actual del conjunto de escalas.
- Azure PowerShell: Use este comando para obtener información:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## Pasos siguientes

- Administre el conjunto de escalado que acaba de crear con la información de [Administración de máquinas de un conjunto de escalado de máquinas virtuales](virtual-machine-scale-sets-windows-manage.md).
- Plantéese configurar el escalado automático del conjunto de escalas mediante la información de [Escalado automático y conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-autoscale-overview.md).
- Puede obtener más información sobre el escalado si consulta [Autoescala vertical con conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-vertical-scale-reprovision.md).

<!---HONumber=AcomDC_0615_2016-->