---
title: "Crear una máquina virtual y una cuenta de almacenamiento para una aplicación escalable en Azure | Microsoft Docs"
description: "Información sobre cómo implementar una máquina virtual que se usará para ejecutar una aplicación escalable mediante Azure Blob Storage"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 0fd1cd93ca6faabcbe0007136fe427028e722733
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/23/2017
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Crear una máquina virtual y una cuenta de almacenamiento para una aplicación escalable

Este tutorial es la primera parte de una serie. En este tutorial se muestra cómo implementar una aplicación que cargue y descargue grandes cantidades de datos aleatorios con una cuenta de Azure Storage. Cuando haya terminado, tendrá una aplicación de consola que se ejecuta en una máquina virtual que carga y descarga grandes cantidades de datos en una cuenta de almacenamiento.

En la primera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Crear una cuenta de almacenamiento
> * de una máquina virtual
> * Configurar una extensión de script personalizada

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell localmente, para este tutorial se requiere la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
 
En el ejemplo se cargan 50 archivos de gran tamaño en un contenedor de blobs de una cuenta de Azure Storage. Una cuenta de almacenamiento proporciona un espacio de nombres único para almacenar y tener acceso a los objetos de datos de almacenamiento de Azure. Cree una cuenta de almacenamiento en el grupo de recursos que ha creado con el comando [New-AzureRmStorageAccount](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccount).

En el siguiente comando, sustituya su propio nombre único de manera global por la cuenta de Blob Storage donde vea el marcador de posición `<blob_storage_account>`.

```powershell-interactive
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="create-a-virtual-machine"></a>de una máquina virtual

Cree una configuración de máquina virtual. Esta configuración incluye los ajustes que se usan al implementar la máquina virtual como una imagen de máquina virtual, el tamaño y la configuración de autenticación. Cuando se realiza este paso, se le solicitará las credenciales. Los valores que especifique se configuran como el nombre de usuario y la contraseña de la máquina virtual.

Cree la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Configuración de la implementación

Para este tutorial, existen requisitos previos que deben instalarse en la máquina virtual. La extensión de script personalizada se utiliza para ejecutar un script de PowerShell que realiza las tareas siguientes:

> [!div class="checklist"]
> * Instalar .NET Core 2.0.
> * Instalar chocolatey.
> * Instalar GIT.
> * Clonar el repositorio de ejemplo.
> * Restaurar paquetes NuGet.
> * Crear 50 archivos de 1 GB con datos aleatorios.

Ejecute el cmdlet siguiente para finalizar la configuración de la máquina virtual. Este paso tarda de 5 a 15 minutos en completarse.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzureRMVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>pasos siguientes

En la primera parte de la serie, aprendió a crear una cuenta de almacenamiento, implementar una máquina virtual y configurar la máquina virtual con los requisitos previos necesarios, tales como:

> [!div class="checklist"]
> * Crear una cuenta de almacenamiento
> * de una máquina virtual
> * Configurar una extensión de script personalizada

Continúe en la segunda parte de la serie para cargar grandes cantidades de datos en una cuenta de almacenamiento mediante el paralelismo y el reintento exponencial.

> [!div class="nextstepaction"]
> [Cargar grandes cantidades de archivos grandes en paralelo en una cuenta de almacenamiento](storage-blob-scalable-app-upload-files.md)
