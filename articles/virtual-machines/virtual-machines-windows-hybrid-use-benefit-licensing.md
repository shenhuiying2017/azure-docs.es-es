<properties
   pageTitle="Ventaja de uso híbrido de Azure para Windows Server | Microsoft Azure"
   description="Descubra cómo maximizar las ventajas de Software Assurance de Windows Server para incorporar licencias locales a Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="05/03/2016"
   ms.author="georgem"/>

# Ventaja de uso híbrido de Azure para Windows Server

Para los clientes que usan Windows Server con Software Assurance, puede llevar sus licencias de Windows Server locales a Azure y ejecutar máquinas virtuales de Windows Server en Azure a un coste reducido. La ventaja de uso híbrido de Azure permite ejecutar máquinas virtuales de Windows Server en Azure y pagar solo la tarifa de proceso básica. Para obtener más información, consulte la [página sobre la licencia de la ventaja de uso híbrido de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). En este artículo se explica cómo implementar máquinas virtuales de Windows Server en Azure para aprovechar esta ventaja con respecto a la licencia.

> [AZURE.NOTE] No puede utilizar imágenes de Azure Marketplace para implementar máquinas virtuales de Windows Server usando la ventaja de uso híbrido de Azure. Debe implementar las máquinas virtuales con plantillas de Resource Manager o PowerShell para registrar correctamente las máquinas virtuales como aptas para el descuento de la tarifa de proceso básica.

## Requisitos previos
Se deben cumplir un par de requisitos previos para poder utilizar la ventaja de uso híbrido de Azure para máquinas virtuales de Windows Server en Azure:

- Tener instalado el módulo de Azure PowerShell.
- Haber cargado el VHD de Windows Server en Almacenamiento de Azure.

### Azure PowerShell
Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que desea usar e iniciar sesión en su cuenta de Azure. Aunque vaya a implementar las máquinas virtuales con plantillas de Resource Manager, necesitará tener instalado Azure PowerShell para poder cargar el VHD de Windows Server (consulte el siguiente paso).

### Carga de un VHD de Windows Server

Para implementar una máquina virtual de Windows Server en Azure, primero debe crear un VHD que contenga la compilación de Windows Server base. Este VHD debe haberse preparado adecuadamente mediante Sysprep antes de cargarlo en Azure. También puede [obtener más información sobre los requisitos de VHD y el proceso de Sysprep](./virtual-machines-windows-upload-image.md). Una vez que haya preparado el VHD, cárguelo en su cuenta de Almacenamiento de Azure mediante el cmdlet `Add-AzureRmVhd` de la siguiente forma:

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

También puede obtener más detalles sobre la [carga del VHD en un proceso de Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] Este artículo se centra en la implementación de máquinas virtuales de Windows Server, pero también puede implementar máquinas virtuales de cliente de Windows de la misma manera. En los ejemplos siguientes, debe reemplazar `Server` por `Client` como corresponda.

## Implementación de una máquina virtual a través de PowerShell (introducción rápida)
Al implementar la máquina virtual de Windows Server mediante PowerShell, dispone de un parámetro adicional para `-LicenseType`. Una vez que el VHD esté cargado en Azure, cree una nueva máquina virtual con `New-AzureRmVM` y especifique el tipo de licencia de la siguiente forma:

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm
    -LicenseType Windows_Server
```

También puede [leer un tutorial más detallado sobre la implementación de una máquina virtual en Azure a través de PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) más adelante o consultar una guía más descriptiva sobre los diferentes pasos necesarios para [crear una máquina virtual Windows mediante Resource Manager y PowerShell](./virtual-machines-windows-ps-create.md).

## Implementación de una máquina virtual a través de Resource Manager
En las plantillas de Resource Manager, se puede especificar un parámetro adicional para `licenseType`. Obtenga más información sobre la [Creación de plantillas de Azure Resource Manager](../resource-group-authoring-templates.md). Una vez que haya cargado el VHD en Azure, edite la plantilla de Resource Manager para incluir el tipo de licencia como parte del proveedor de procesos e impleméntela de la forma habitual:

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## Comprobación de que la máquina virtual está utilizando la ventaja de licencia
Una vez que haya implementado la máquina virtual mediante el método de implementación de Resource Manager o PowerShell, compruebe el tipo de licencia con `Get-AzureRmVM` de esta forma:
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

Verá un resultado similar al siguiente:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Esto contrasta con la siguiente máquina virtual implementada sin la licencia de ventaja de uso híbrido de Azure, como una implementada directamente desde la galería de Azure:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## Tutorial detallado de PowerShell

Los siguientes pasos detallados de PowerShell muestran una implementación completa de una máquina virtual. Puede obtener más contexto sobre los cmdlets reales y los distintos componentes que se crean en [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](./virtual-machines-windows-ps-create.md). Con estos pasos, creará el grupo de recursos, la cuenta de almacenamiento y las redes virtuales, definirá la máquina virtual y, por último, la creará.
 
En primer lugar, obtenga credenciales de forma segura, establezca una ubicación y defina un nombre para el grupo de recursos:

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Cree una dirección IP pública:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Defina la subred, la NIC y la red virtual:

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Asigne un nombre a la máquina virtual y cree una configuración de máquina virtual:

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Defina el sistema operativo:

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Agregue la NIC a la máquina virtual:

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Defina la cuenta de almacenamiento que se va a usar:

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Cargue el VHD, preparado adecuadamente, y conéctelo a la máquina virtual que se vaya a usar:

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Por último, cree la máquina virtual y defina el tipo de licencia para utilizar la ventaja de uso híbrido de Azure:

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## Pasos siguientes

Obtenga más información sobre la [licencia de la ventaja de uso híbrido de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Obtenga más información sobre el [uso de plantillas de Resource Manager](../resource-group-overview.md).

<!---HONumber=AcomDC_0601_2016-->