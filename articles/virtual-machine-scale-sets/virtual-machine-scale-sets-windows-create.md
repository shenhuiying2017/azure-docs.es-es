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
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Creación de un conjunto de escalado de máquinas virtuales de Windows mediante Azure PowerShell

En estos pasos se sigue un enfoque consistente en atar cabos para crear un conjunto de escalado de máquinas virtuales de Azure. En todo el artículo hay variables para las que el usuario tiene que proporcionar valores. Reemplace todo el contenido dentro de las comillas por valores que tengan sentido para su suscripción y aplicación.

## Paso 1: Instalación de Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Paso 2: Establecimiento de la suscripción

1. Inicie un símbolo del sistema de PowerShell.

2. Inicie sesión en su cuenta:

        Login-AzureRmAccount

3. Obtenga su suscripción:

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

4. Establezca la suscripción que desea usar como actual.

        $subscr = "subscription name"
        Select-AzureSubscription -SubscriptionName $subscr –Current


## Paso 2: Creación de recursos

Cree los recursos necesarios para su nuevo conjunto de escalado de máquinas virtuales.

### Grupos de recursos

Un conjunto de escalado de máquinas virtuales debe estar contenido en un grupo de recursos.

1.  Ejecute este comando para obtener una lista de ubicaciones disponibles y los servicios que se admiten:

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

    Elija la ubicación que más le convenga y luego reemplace el texto entre comillas por ese nombre de ubicación:

        $locName = "location name from the list, such as Central US"

4. Reemplace el texto entre comillas por el nombre que desea utilizar para el nuevo grupo de recursos y luego créelo en la ubicación que estableció anteriormente:

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Puede ver algo así:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### Cuenta de almacenamiento

Las máquinas virtuales creadas en un conjunto de escalado requieren una cuenta de almacenamiento para almacenar los discos asociados.

1. Reemplace el texto entre comillas por el nombre que desea utilizar para la cuenta de almacenamiento y luego pruebe si es único:

        $saName = "storage account name"
        Test-AzureName -Storage $saName

    Si la respuesta es **False**, el nombre propuesto es único.

2. Reemplace el texto entre comillas por el tipo de la cuenta de almacenamiento y luego cree la cuenta con el nombre y la ubicación establecidos anteriormente. Los valores posibles son: Standard\_LRS, Standard\_GRS, Standard\_RAGRS o Premium\_LRS:

        $saType = "storage account type"
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

### Red virtual

Se requiere una red virtual para las máquinas virtuales del conjunto de escalado.

1. Reemplace el texto entre comillas por el nombre que desea utilizar para la subred de la red virtual y luego cree la configuración:

        $subName = "subnet name"
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24

2. Reemplace el texto entre comillas por el nombre que desea utilizar para la red virtual y luego créela con la información y los recursos que definió anteriormente:

        $netName="virtual network name"
        $vnet=New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Dirección IP pública

Antes de poder crear una interfaz de red, necesita crear una dirección IP pública.

1. Reemplace el texto entre comillas por la etiqueta de nombre de dominio que desea utilizar con la dirección IP pública y luego pruebe si el nombre es único. La etiqueta puede contener solo letras, números y guiones, y el último carácter debe ser una letra o un número:

        $domName = "domain name label"
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    Si la respuesta es **False**, el nombre propuesto es único.

2. Reemplace el texto entre comillas por el nombre que desea utilizar para la dirección IP pública y, a continuación, vuelva a crearlo:

        $pipName = "public ip address name"
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### Interfaz de red

Ahora que tiene la dirección IP pública, puede crear la interfaz de red.

1. Reemplace el texto entre comillas por el nombre que desea utilizar para la interfaz de red y luego créela con los recursos que creó anteriormente:

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


### Creación de un conjunto de escalado de máquinas virtuales

Tiene todos los recursos que necesita; ahora es el momento de crear el conjunto de escalado.

1. Reemplace el texto entre comillas por el nombre que desea utilizar para la configuración IP y luego créela:

        $ipName = "IP configuration name"
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Reemplace el texto entre comillas por el nombre que desea utilizar para la configuración del conjunto de escalado y luego créelo. Este paso incluye establecer el tamaño (al que se hace referencia como SkuName) de las máquinas virtuales del conjunto. Vea [Sizes for virtual machines (Tamaños de máquinas virtuales)](..\virtual-machines\virtual-machines-windows-sizes.md) para encontrar un tamaño que satisfaga sus necesidades. En este ejemplo, se recomienda utilizar Standard\_A0:

        $vmssName = "Scale set configuration name"
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0"
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssName -Primary $true -IPConfiguration $ipConfig

    Puede ver algo así:

        Sku                   : {
                                  "name": "Standard_A0",
                                  "tier": null,
                                  "capacity": 3
        						}
        UpgradePolicy         : {
                                  "mode": "automatic"
                                }
        VirtualMachineProfile : {
                                  "osProfile": null,
                                  "storageProfile": null,
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myniccfg1",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "myipconfig1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [],
                                            "properties.loadBalancerInboundNatPools": [],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": null
                                  }
                                }
        ProvisioningState     :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags.Count            : 0
        Tags                  :

3. Reemplace el texto entre comillas del prefijo del nombre de equipo que desea usar para el nombre de la cuenta de administrador de las máquinas virtuales, para la contraseña de la cuenta, y luego cree el perfil de sistema operativo:

        $computerName = "computer name prefix"
        $adminName = "administrator account name"
        $adminPassword = "password for administrator accounts"
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

    Debería ver algo similar a lo siguiente en la sección osProfile:

        "osProfile": {
          "computerNamePrefix": "myvmss1",
          "adminUsername": "########",
          "adminPassword": "########",
          "customData": null,
          "windowsConfiguration": null,
          "linuxConfiguration": null,
          "secrets": null
        },

4. Reemplace el texto entre comillas por el nombre que desea usar para el perfil de almacenamiento, la información de la imagen y la ruta de acceso de almacenamiento en la que se almacenan los discos para las máquinas virtuales y luego cree el perfil. Para encontrar la información que necesita, vea [Navigate and select Azure virtual machine images with Windows PowerShell and the Azure CLI (Navegación por imágenes de máquina virtual de Azure con Windows PowerShell y la CLI de Azure y selección de las mismas)](..\virtual-machines\virtual-machines-windows-cli-ps-findimage.md).

        $storeProfile = "storage profile name"
        $imagePublisher = "image publisher name, such as MicrosoftWindowsServer"
        $imageOffer = "offer from publisher, such as WindowsServer"
        $imageSku = "sku of image, such as 2012-R2-Datacenter"
        $vhdContainer = "URI of storage container"
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainer -OsDiskCreateOption "FromImage" -OsDiskCaching "None"

    Debería ver algo similar a lo siguiente en la sección storageProfile:

        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "mystore1",
            "caching": "None",
            "createOption": "FromImage",
            "osType": null,
            "image": null,
            "vhdContainers": {
              "http://myst1.blob.core.windows.net/vhds"
            }
          }
        },

5. Reemplace el texto entre comillas por el nombre del conjunto de escalado de máquinas virtuales y luego créelo:

        $vmssName = "scale set name"
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Debería ver algo similar a lo siguiente que muestra que la implementación se realizó correctamente:

        ProvisioningState     : Succeeded
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags.Count            : 0
        Tags                  :

## Paso 3: Exploración de recursos

Use estos recursos para explorar el conjunto de escalado de máquinas virtuales que acaba de crear:

- Portal de Azure: Hay disponible una cantidad limitada de información mediante el portal.
- [Explorador de recursos de Azure](https://resources.azure.com/): Esta es la mejor herramienta para explorar el estado actual del conjunto de escalado.
- Azure PowerShell: Use este comando para obtener información:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## Pasos siguientes

1. Para obtener más información, consulte [Virtual Machine Scale Sets Overview (Información general de conjuntos de escalado de máquinas virtuales)](virtual-machine-scale-sets-overview.md).

2. Plantéese configurar el escalado automático del conjunto de escalado mediante la información de [Automatic scaling and virtual machine scale sets (Escalado automático y conjuntos de escalado de máquinas virtuales)](virtual-machine-scale-sets-autoscale-overview.md).

<!---HONumber=AcomDC_0427_2016-->