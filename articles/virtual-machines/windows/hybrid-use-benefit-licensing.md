---
title: "Ventaja de uso híbrido de Azure para Windows Server | Microsoft Docs"
description: "Descubra cómo maximizar las ventajas de Software Assurance de Windows para incorporar licencias locales a Azure"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/26/2017
ms.author: xujing
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: a986ddf22f059dc55bb9bff5c6eaf27324b716cd
ms.contentlocale: es-es
ms.lasthandoff: 08/31/2017

---
# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Ventaja de uso híbrido de Azure para Windows Server
Para los clientes con Software Assurance, la ventaja de uso híbrida de Azure le permite usar las licencias de Windows Server y cliente de Windows locales y ejecutar máquinas virtuales de Windows en Azure a bajo costo. La ventaja de uso híbrido de Azure para Windows Server incluye Windows Server 2008R2, Windows Server 2012, Windows Server 2012R2 y Windows Server 2016. Para obtener más información, consulte la [página sobre la licencia de la ventaja de uso híbrido de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!NOTE]
> En este artículo, se explica cómo implementar la ventaja de las licencias con las imágenes de Windows Server. También puede realizar este procedimiento con [imágenes de Windows 10 Desktop](#windows-desktop-multitenant-hosting-deployment).
>

## <a name="ways-to-use-azure-hybrid-use-benefit"></a>Formas de usar la ventaja de uso híbrido de Azure
Existen diversas formas de implementar máquinas virtuales Windows con la ventaja de uso híbrido de Azure:

1. Puede implementar máquinas virtuales a partir de [imágenes específicas de Marketplace].
2. Puede [cargar una máquina virtual personalizada](#upload-a-windows-vhd) e [implementar mediante una plantilla de Resource Manager](#deploy-a-vm-via-resource-manager) o [Azure PowerShell](#detailed-powershell-deployment-walkthrough).

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>Implementación de una máquina virtual mediante Azure Marketplace
Las siguientes imágenes están disponibles en Marketplace preconfiguradas con la ventaja de uso híbrido de Azure: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 y Windows Server 2008 SP1. Estas imágenes pueden implementarse directamente desde Azure Portal, plantillas de Resource Manager o Azure PowerShell.

Puede implementar estas imágenes directamente desde Azure Portal. Para su uso en plantillas de Resource Manager y con Azure PowerShell, consulte la lista de imágenes de la siguiente manera:

Para Windows Server:
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
- 2016-Datacenter versión 2016.127.20170406 o superior

- 2012-R2-Datacenter versión 4.127.20170406 o superior

- 2012-Datacenter versión 3.127.20170406 o superior

- 2008-R2-SP1 versión 2.127.20170406 o superior

Para cliente de Windows:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "Windows-HUB"
```

## <a name="upload-a-windows-server-vhd"></a>Carga de un VHD de Windows Server
Para implementar una máquina virtual de Windows Server en Azure, primero debe crear un VHD que contenga la compilación de Windows base. Este VHD debe haberse preparado adecuadamente mediante Sysprep antes de cargarlo en Azure. También puede obtener más información tanto [sobre los requisitos de los discos duros virtuales y el proceso Sysprep](upload-generalized-managed.md) como sobre la [compatibilidad de Sysprep con los roles de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Cree una copia de seguridad de la VM antes de ejecutar Sysprep. 

Asegúrese de que tiene [la versión de Azure PowerShell más reciente instalada y configurada](/powershell/azure/overview). Cuando haya preparado el disco duro virtual, cárguelo en su cuenta de Almacenamiento de Azure mediante el cmdlet `Add-AzureRmVhd` de la siguiente forma:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server y Dynamics también pueden utilizar una concesión de licencias de Software Assurance. Aun así, es preciso preparar la imagen de Windows Server, para lo que hay que instalar los componentes de la aplicación y proporcionar las claves de licencia según corresponda y, a luego, cargar la imagen del disco en Azure. Revise la documentación adecuada para ejecutar Sysprep con su aplicación, como [Consideraciones acerca de la instalación de SQL Server con SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) o [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) [Creación de una imagen de referencia de SharePoint Server 2016 (Sysprep)].
>
>

También puede obtener más detalles sobre la [carga del disco duro virtual en un proceso de Azure](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account).


## <a name="deploy-a-vm-via-resource-manager-template"></a>Implementación de una máquina virtual a través de una plantilla de Resource Manager
En las plantillas de Resource Manager, se puede especificar un parámetro adicional para `licenseType` . En [Creación de plantillas de Azure Resource Manager](../../resource-group-authoring-templates.md), puede encontrar más información al respecto. Una vez que haya cargado el VHD en Azure, edite la plantilla de Resource Manager para incluir el tipo de licencia como parte del proveedor de procesos e impleméntela de la forma habitual:

Para Windows Server:
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

Para que el cliente de Windows solo con la imagen de Azure Marketplace:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Implementación de una máquina virtual a través del inicio rápido de PowerShell
Cuando se implementa una máquina virtual de Windows Server mediante PowerShell, se dispone de un parámetro adicional para `-LicenseType`. Cuando el disco duro virtual esté cargado en Azure, cree una máquina virtual mediante `New-AzureRmVM` y especifique el tipo de concesión de licencias de la siguiente forma:

Para Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Para que el cliente de Windows solo con la imagen de Azure Marketplace:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

A continuación, puede [leer un tutorial más detallado sobre la implementación de una máquina virtual en Azure a través de PowerShell](hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough), o bien consultar una guía más descriptiva sobre los diferentes pasos que deben darse para [crear una máquina virtual Windows mediante Resource Manager y PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Comprobación de que la máquina virtual está utilizando la ventaja de licencia
Cuando haya implementado la máquina virtual mediante el método de implementación de Resource Manager o PowerShell, compruebe el tipo de licencia con `Get-AzureRmVM` , como se indica a continuación:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

La salida es similar a la del ejemplo siguiente para Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Esta salida contrasta con la siguiente máquina virtual implementada sin la licencia de ventaja de uso híbrido de Azure, como una implementada directamente desde la galería de Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-deployment-walkthrough"></a>Tutorial de implementación de PowerShell detallado
Los siguientes pasos detallados de PowerShell muestran una implementación completa de una máquina virtual. Puede obtener más contexto sobre los cmdlets reales y los distintos componentes que se crean en [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Con estos pasos, crea el grupo de recursos, la cuenta de almacenamiento y las redes virtuales, definirá la máquina virtual y, por último, la creará.

En primer lugar, obtenga credenciales de forma segura, establezca una ubicación y defina un nombre para el grupo de recursos:

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

Cree una dirección IP pública:

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

Defina la subred, la NIC y la red virtual:

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Asigne un nombre a la máquina virtual y cree una configuración de máquina virtual:

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Defina el sistema operativo:

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Agregue la NIC a la máquina virtual:

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Defina la cuenta de almacenamiento que se va a usar:

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

Cargue el VHD, preparado adecuadamente, y conéctelo a la máquina virtual que se vaya a usar:

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Por último, cree la máquina virtual y defina el tipo de licencia para utilizar la ventaja de uso híbrido de Azure:

Para Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

## <a name="deploy-a-virtual-machine-scale-set-via-resource-manager-template"></a>Implemente un conjunto de escalado de máquinas virtuales con una plantilla de Resource Manager
En las plantillas de Resource Manager del conjunto de escalado de máquinas virtuales, se puede especificar un parámetro adicional para `licenseType`. En [Creación de plantillas de Azure Resource Manager](../../resource-group-authoring-templates.md), puede encontrar más información al respecto. Edite la plantilla de Resource Manager para incluir la propiedad licenseType como parte de virtualMachineProfile del conjunto de escalado e implemente la plantilla como normal (vea el ejemplo siguiente en el que se utiliza la imagen de Windows Server 2016):


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```

> [!NOTE]
> Próximamente estará disponible la compatibilidad para implementar un conjunto de escalado de máquinas virtuales con ventajas AHUB a través de PowerShell y otras herramientas SDK.
>

## <a name="next-steps"></a>Pasos siguientes
Más información sobre las [ventajas del uso híbrido de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Más información sobre el [uso de plantillas de Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Más información sobre [Azure Hybrid Use Benefit and Azure Site Recovery make migrating applications to Azure even more cost-effective (La ventaja de uso híbrido de Azure y Azure Site Recovery rentabilizan aún más la migración de aplicaciones a Azure)](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/).

