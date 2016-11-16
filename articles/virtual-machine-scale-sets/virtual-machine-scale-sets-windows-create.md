---
title: "Creación de un conjunto de escalado de máquinas virtuales mediante PowerShell | Microsoft Docs"
description: "Creación de un conjunto de escalado de máquinas virtuales mediante PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7bb03323-8bcc-4ee4-9a3e-144ca6d644e2
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6d70338ebf918a3f9178a4f633dd46a607d72b1c


---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Creación de un conjunto de escalado de máquinas virtuales de Windows mediante Azure PowerShell
En estos pasos se sigue un enfoque consistente en atar cabos para crear un conjunto de escalado de máquinas virtuales de Azure. Para obtener más información sobre los conjuntos de escala, consulte [Información general de conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-overview.md) .

Tardará unos 30 minutos en realizar los pasos de este artículo.

## <a name="step-1-install-azure-powershell"></a>Paso 1: Instalación de Azure PowerShell
Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que desea usar e iniciar sesión en su cuenta.

## <a name="step-2-create-resources"></a>Paso 2: Creación de recursos
Cree los recursos necesarios para su nuevo conjunto de escalado de máquinas virtuales.

### <a name="resource-group"></a>Grupos de recursos
Un conjunto de escalado de máquinas virtuales debe estar contenido en un grupo de recursos.

1. Obtenga una lista de ubicaciones disponibles donde se pueden colocar recursos:
   
        Get-AzureLocation | Sort Name | Select Name
2. Elija una ubicación que mejor le convenga, reemplace el valor de **$locName** por ese nombre de ubicación y, a continuación, cree la variable:
   
        $locName = "location name from the list, such as Central US"
3. Reemplace el valor de **$rgName** por el nombre que desee utilizar para el nuevo grupo de recursos y, a continuación, cree la variable: 
   
        $rgName = "resource group name"
4. Creación del grupo de recursos:
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    Puede ver algo parecido a este ejemplo:
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Cuenta de almacenamiento
Las máquinas virtuales utilizan las cuentas de almacenamiento para almacenar el disco del sistema operativo y los datos de diagnóstico que se emplean en las operaciones de escalado. Cuando sea posible, se recomienda tener una cuenta de almacenamiento para cada una de las máquinas virtuales creadas en un conjunto de escalas. Si procede, no planee disponer de más de 20 máquinas virtuales por cuenta de almacenamiento. En el ejemplo de este artículo se muestran 3 cuentas de almacenamiento que se crean para 3 máquinas virtuales.

1. Reemplace el valor de **$saName** por un nombre para la cuenta de almacenamiento. Compruebe que el nombre sea único. 
   
        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName
   
    Si la respuesta es **True**, significa que el nombre propuesto es único.
2. Reemplace el valor de **$rgName** por el tipo de cuenta de almacenamiento y, a continuación, cree la variable:  
   
        $saType = "storage account type"
   
    Los valores posibles son: Standard_LRS, Standard_GRS, Standard_RAGRS o Premium_LRS.
3. Creación de la cuenta:
   
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName
   
    Puede ver algo parecido a este ejemplo:
   
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
4. Repita los pasos del 1 al 4 para crear 3 cuentas de almacenamiento; por ejemplo, myst1, myst2 y myst3.

### <a name="virtual-network"></a>red virtual
Se requiere una red virtual para las máquinas virtuales del conjunto de escalado.

1. Reemplace el valor de **$subnetName** por el nombre que desee usar para la subred de la red virtual y, luego, cree la variable: 
   
        $subnetName = "subnet name"
2. Creación de la configuración de subred:
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    El prefijo de dirección puede ser diferente en la red virtual.
3. Reemplace el valor de **$netName** por el nombre que desee utilizar para la red virtual y, después, cree la variable: 
   
        $netName = "virtual network name"
4. Creación de la red virtual.
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Configuración del conjunto de escalado
Tiene todos los recursos que necesita para la configuración del conjunto de escalado, así que creémosla.  

1. Reemplace el valor de **$ipName** por el nombre que desee utilizar para la configuración de IP y, después, cree la variable: 
   
        $ipName = "IP configuration name"
2. Creación de la configuración de IP:
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. Reemplace el valor de **$vmssConfig** por el nombre que desee utilizar para la configuración del conjunto de escalado y, después, cree la variable:   
   
        $vmssConfig = "Scale set configuration name"
4. Cree la configuración para el conjunto de escalado:
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    En este ejemplo se muestra un conjunto de escalado que se crea con 3 máquinas virtuales. Para obtener más información sobre la capacidad de los conjuntos de escala, consulte [Información general de conjuntos de escala de máquinas virtuales](virtual-machine-scale-sets-overview.md) . Este paso también incluye el establecimiento del tamaño (al que se hace referencia como SkuName) de las máquinas virtuales del conjunto. Para encontrar un tamaño que satisfaga sus necesidades, consulte [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md).
5. Adición de la configuración de la interfaz de red a la configuración del conjunto de escalado:
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    Puede ver algo parecido a este ejemplo:
   
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

#### <a name="operating-system-profile"></a>Perfil de sistema operativo
1. Reemplace el valor de **$computerName** por el prefijo de nombre de equipo que quiera usar y, después, cree la variable: 
   
        $computerName = "computer name prefix"
2. Reemplace el valor de **$adminName** por el nombre de la cuenta de administrador en las máquinas virtuales y, después, cree la variable:
   
        $adminName = "administrator account name"
3. Reemplace el valor de **$adminPassword** por la contraseña de cuenta y, después, cree la variable:
   
        $adminPassword = "password for administrator accounts"
4. Creación del perfil del sistema operativo:
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Perfil de almacenamiento
1. Reemplace el valor de **$storageProfile** por el nombre que desee utilizar para el perfil de almacenamiento y, después, cree la variable:  
   
        $storageProfile = "storage profile name"
2. Creación de las variables que definen la imagen que usar:  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    Para información sobre otras imágenes que puede usar, consulte [Navegación y selección de las imágenes de máquina virtual de Azure con Windows PowerShell y la CLI de Azure](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
3. Reemplace el valor de **$vhdContainers** por una lista que contenga las rutas de acceso donde se almacenan los discos duros virtuales, como "https://mystorage.blob.core.windows.net/vhds" y, después, cree la variable:
   
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
4. Creación de un perfil de almacenamiento:
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual
Por último, puede crear el conjunto de escalado.

1. Reemplace el texto de **$vmssName** por el nombre del conjunto de escalado de máquina virtual y cree la variable:
   
        $vmssName = "scale set name"
2. Creación del conjunto de escalado:
   
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss
   
    Debería ver algo similar a este ejemplo que muestra que la implementación se realizó correctamente:
   
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

## <a name="step-3-explore-resources"></a>Paso 3: Exploración de recursos
Use estos recursos para explorar el conjunto de escalado de máquinas virtuales que creó:

* Portal de Azure: Hay disponible una cantidad limitada de información mediante el portal.
* [Azure Resource Explorer](https://resources.azure.com/): es la mejor herramienta para explorar el estado actual del conjunto de escalado.
* Azure PowerShell: use este comando para obtener información:
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>Pasos siguientes
* Administre el conjunto de escalado que acaba de crear con la información de [Administración de máquinas de un conjunto de escalado de máquinas virtuales](virtual-machine-scale-sets-windows-manage.md)
* Plantéese configurar el escalado automático del conjunto de escalas mediante la información de [Escalado automático y conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-autoscale-overview.md)
* Puede obtener más información sobre el escalado si consulta [Autoescala vertical con conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-vertical-scale-reprovision.md)




<!--HONumber=Nov16_HO2-->


