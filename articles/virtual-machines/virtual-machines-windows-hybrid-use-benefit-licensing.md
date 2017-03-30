---
title: "Ventaja de uso híbrido de Azure para Windows Server y cliente de Windows| Microsoft Docs"
description: "Descubra cómo maximizar las ventajas de Software Assurance de Windows para incorporar licencias locales a Azure"
services: virtual-machines-windows
documentationcenter: 
author: george-moore
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 3/10/2017
ms.author: georgem
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: dfa3cf27eebd04507c101fc9421f13dfef39c39f
ms.lasthandoff: 03/21/2017


---
# <a name="azure-hybrid-use-benefit-for-windows-server-and-windows-client"></a>Ventaja de uso híbrido de Azure para Windows Server y cliente de Windows
Para los clientes con Software Assurance, la ventaja de uso híbrida de Azure le permite usar las licencias de Windows Server y cliente de Windows locales y ejecutar máquinas virtuales de Windows en Azure a bajo costo. La ventaja de uso híbrido de Azure para Windows Server incluye Windows Server 2008R2, Windows Server 2012, Windows Server 2012R2 y Windows Server 2016. Ventaja de uso híbrido de Azure para cliente de Windows incluye Windows 10. Para obtener más información, consulte la [página sobre la licencia de la ventaja de uso híbrido de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

>[!IMPORTANT]
>Las ventajas de uso híbrido de Azure para cliente de Windows se encuentran actualmente en la versión preliminar. Solo los clientes de Enterprise con Windows 10 Enterprise E3/E5 por usuario o VDA de Windows por usuario (licencias de suscripción de usuario o licencias de suscripción de usuario de complemento) ("licencias aplicables") son válidos.
>
>

## <a name="ways-to-use-azure-hybrid-use-benefit"></a>Formas de usar la ventaja de uso híbrido de Azure
Existen diversas formas de implementar máquinas virtuales Windows con la ventaja de uso híbrido de Azure:

1. Si tiene una suscripción a un Contrato Enterprise, puede [implementar máquinas virtuales desde imágenes de Marketplace específicas](#deploy-a-vm-using-the-azure-marketplace) que estén preconfiguradas con la ventaja de uso híbrido de Azure.
2. Sin un Contrato Enterprise, puede [cargar una máquina virtual personalizada](#upload-a-windows-vhd) e [implementar mediante una plantilla de Resource Manager](#deploy-a-vm-via-resource-manager) o [Azure PowerShell](#detailed-powershell-deployment-walkthrough).

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>Implementación de una máquina virtual mediante Azure Marketplace
Para los clientes con [suscripciones a Contrato Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx), las imágenes están disponibles en Marketplace preconfiguradas con la ventaja de uso híbrido de Azure. Estas imágenes pueden implementarse directamente desde Azure Portal, plantillas de Resource Manager o Azure PowerShell, por ejemplo. Las imágenes de Marketplace se indican mediante el nombre `[HUB]` de la siguiente manera:

![Imágenes de la ventaja de uso híbrido de Azure en Azure Marketplace](./media/virtual-machines-windows-hybrid-use-benefit/ahub-images-portal.png)

Puede implementar estas imágenes directamente desde Azure Portal. Para su uso en plantillas de Resource Manager y con Azure PowerShell, consulte la lista de imágenes de la siguiente manera:

Para Windows Server:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "WindowsServer-HUB"
```

Para cliente de Windows:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "Windows-HUB"
```

Si no tiene una suscripción a un Contrato Enterprise, siga leyendo para obtener instrucciones sobre cómo cargar una máquina virtual personalizada e implementarla con la ventaja de uso híbrido de Azure.


## <a name="upload-a-windows-vhd"></a>Carga de un VHD de Windows
Para implementar una máquina virtual de Windows en Azure, primero debe crear un VHD que contenga la compilación de Windows base. Este VHD debe haberse preparado adecuadamente mediante Sysprep antes de cargarlo en Azure. También puede obtener más información tanto [sobre los requisitos de los discos duros virtuales y el proceso Sysprep](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) como sobre la [compatibilidad de Sysprep con los roles de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Cree una copia de seguridad de la VM antes de ejecutar Sysprep. 

Asegúrese de que tiene [la versión de Azure PowerShell más reciente instalada y configurada](/powershell/azureps-cmdlets-docs). Cuando haya preparado el disco duro virtual, cárguelo en su cuenta de Almacenamiento de Azure mediante el cmdlet `Add-AzureRmVhd` de la siguiente forma:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server y Dynamics también pueden utilizar una concesión de licencias de Software Assurance. Aun así, es preciso preparar la imagen de Windows Server, para lo que hay que instalar los componentes de la aplicación y proporcionar las claves de licencia según corresponda y, a luego, cargar la imagen del disco en Azure. Revise la documentación adecuada para ejecutar Sysprep con su aplicación, como [Consideraciones acerca de la instalación de SQL Server con SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) o [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) [Creación de una imagen de referencia de SharePoint Server 2016 (Sysprep)].
>
>

También puede obtener más detalles sobre la [carga del disco duro virtual en un proceso de Azure](virtual-machines-windows-upload-image.md#upload-the-vhd-to-your-storage-account).


## <a name="deploy-an-uploaded-vm-via-resource-manager"></a>Implementación de una máquina virtual cargada a través de Resource Manager
En las plantillas de Resource Manager, se puede especificar un parámetro adicional para `licenseType` . En [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md), puede encontrar más información al respecto. Una vez que haya cargado el VHD en Azure, edite la plantilla de Resource Manager para incluir el tipo de licencia como parte del proveedor de procesos e impleméntela de la forma habitual:

Para Windows Server:
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

Para cliente de Windows:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-an-uploaded-vm-via-powershell-quickstart"></a>Implementación de una máquina virtual cargada a través del inicio rápido de PowerShell
Cuando se implementa una máquina virtual de Windows Server mediante PowerShell, se dispone de un parámetro adicional para `-LicenseType`. Cuando el disco duro virtual esté cargado en Azure, cree una máquina virtual mediante `New-AzureRmVM` y especifique el tipo de concesión de licencias de la siguiente forma:

Para Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Para cliente de Windows:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

A continuación, puede [leer un tutorial más detallado sobre la implementación de una máquina virtual en Azure a través de PowerShell](virtual-machines-windows-hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough), o bien consultar una guía más descriptiva sobre los diferentes pasos que deben darse para [crear una máquina virtual Windows mediante Resource Manager y PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


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
Los siguientes pasos detallados de PowerShell muestran una implementación completa de una máquina virtual. Puede obtener más contexto sobre los cmdlets reales y los distintos componentes que se crean en [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Con estos pasos, crea el grupo de recursos, la cuenta de almacenamiento y las redes virtuales, definirá la máquina virtual y, por último, la creará.

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

Para cliente de Windows:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Client"
```

## <a name="next-steps"></a>Pasos siguientes
Más información sobre las [ventajas del uso híbrido de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Más información sobre el [uso de plantillas de Resource Manager](../azure-resource-manager/resource-group-overview.md).

