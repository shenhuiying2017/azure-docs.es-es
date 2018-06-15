---
title: Creación de una máquina virtual con una dirección IP pública estática (Azure PowerShell) | Microsoft Docs
description: Obtenga información sobre cómo crear una máquina virtual con una dirección IP pública estática mediante PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68656db0b76a29e7ab36fd6fa9ad4647712233ee
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525139"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>Creación de una máquina virtual con una dirección IP pública estática mediante PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI de Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (clásico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del modelo de implementación clásica.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>Inicio del script
Puede descargar el script de PowerShell completo que ha usado [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Siga los pasos siguientes para cambiar el script para que funcione en su entorno.

Cambie los valores de las variables siguientes según los valores que desee usar para la implementación. Los valores siguientes se asignan al escenario usado en este artículo:

```powershell
# Set variables resource group
$rgName                = "IaaSStory"
$location              = "West US"

# Set variables for VNet
$vnetName              = "WTestVNet"
$vnetPrefix            = "192.168.0.0/16"
$subnetName            = "FrontEnd"
$subnetPrefix          = "192.168.1.0/24"

# Set variables for storage
$stdStorageAccountName = "iaasstorystorage"

# Set variables for VM
$vmSize                = "Standard_A1"
$diskSize              = 127
$publisher             = "MicrosoftWindowsServer"
$offer                 = "WindowsServer"
$sku                   = "2012-R2-Datacenter"
$version               = "latest"
$vmName                = "WEB1"
$osDiskName            = "osdisk"
$nicName               = "NICWEB1"
$privateIPAddress      = "192.168.1.101"
$pipName               = "PIPWEB1"
$dnsName               = "iaasstoryws1"
```

## <a name="create-the-necessary-resources-for-your-vm"></a>Creación de los recursos necesarios para las máquinas virtuales
Antes de crear una máquina virtual, necesitará un grupo de recursos, red virtual, dirección IP pública y NIC que se usará con la máquina virtual.

1. Cree un nuevo grupo de recursos.

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. Cree la red virtual y subred.

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. Cree el recurso de IP pública. 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. Cree la interfaz de red (NIC) para la máquina virtual en la subred creada anteriormente, con la dirección IP pública. Observe el primer cmdlet que recupera la red virtual de Azure; esto es necesario porque se ejecutó `Set-AzureRmVirtualNetwork` para cambiar la red virtual existente.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. Cree una cuenta de almacenamiento para hospedar la unidad del sistema operativo de la máquina virtual.

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>Creación de la máquina virtual
Ahora que todos los recursos necesarios están en su lugar, puede crear una nueva máquina virtual.

1. Cree el objeto de configuración de la máquina virtual.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. Obtenga las credenciales para la cuenta de administrador local de la máquina virtual.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. Cree un objeto de configuración de máquina virtual.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Establezca la imagen de sistema operativo para la máquina virtual.

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. Configure el disco del sistema operativo.

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. Agregue la NIC a la máquina virtual.

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. Cree la máquina virtual.

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. Guarde el archivo de script.

## <a name="run-the-script"></a>Ejecute el script

Después de realizar los cambios necesarios, ejecute el script anterior. La máquina virtual se crea después de unos minutos.

## <a name="set-ip-addresses-within-the-operating-system"></a>Configuración de direcciones IP en el sistema operativo

No asigne manualmente la dirección IP pública asignada a una máquina virtual de Azure en el sistema operativo de la máquina virtual. Se recomienda no asignar estáticamente la dirección IP privada asignada a la máquina virtual de Azure en el sistema operativo de una máquina virtual, a menos que sea necesario, como al [asignar varias direcciones IP a una máquina virtual Windows](virtual-network-multiple-ip-addresses-powershell.md). Al establecer manualmente la dirección IP privada en el sistema operativo, asegúrese de que sea la misma que la asignada a la [interfaz de red](virtual-network-network-interface-addresses.md#change-ip-address-settings) de Azure; de lo contrario, perderá la conectividad a la máquina virtual. Más información sobre la configuración de la [dirección IP privada](virtual-network-network-interface-addresses.md#private).

## <a name="next-steps"></a>Pasos siguientes

Cualquier tráfico de red puede fluir hacia la máquina virtual creada en este artículo y desde ella. Puede definir reglas de seguridad entrantes y salientes en un grupo de seguridad de red que limite el tráfico que puede fluir hacia la interfaz de red, la subred o ambas y desde ellas. Para más información acerca de los grupos de seguridad de red, consulte [Seguridad de las redes](security-overview.md).