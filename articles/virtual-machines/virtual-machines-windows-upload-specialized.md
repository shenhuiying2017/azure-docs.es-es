---
title: "Carga de un VHD especializado en Azure para usarlo en la creación de una VM | Microsoft Docs"
description: "Carga de un VHD especializado en Azure para usarlo en la creación de una VM."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: fc8cb82e952a05b161f00ef9ebfbd4852d3987d4


---

# <a name="upload-a-specialized-vhd-to-azure-to-use-for-creating-a-new-vm"></a>Carga de un VHD especializado en Azure para usarlo en la creación de una VM

Un disco duro virtual especializado mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. Puede cargar un VHD especializado en Azure y usarlo para crear una VM que use Managed Disks o una cuenta de almacenamiento no administrada. Se recomienda que use [Managed Disks](../storage/storage-managed-disks-overview.md) para aprovechar la administración simplificada y las características adicionales que ofrece Managed Disks.


> [!IMPORTANT]
> Antes de cargar los VHD en Azure, debe consultar [Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


* Para información sobre los precios de los distintos tamaños de VM, consulte [Precios de VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
* Para información sobre los precios de almacenamiento, consulte los [precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/blobs/). 
* Para ver la disponibilidad de los tamaños de máquina virtual en las regiones de Azure, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/).
* Para ver los límites generales de las máquinas virtuales de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

## <a name="before-you-begin"></a>Antes de empezar
Si usa PowerShell, asegúrese de que tiene la última versión del módulo AzureRM.Compute de PowerShell. Ejecute el siguiente comando para realizar la instalación.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning) (Control de versiones de Azure PowerShell).


## <a name="prepare-the-vm"></a>Preparación de la VM
 
Si tiene previsto usar el VHD especializado como está para crear una nueva VM, asegúrese de completar los pasos siguientes. 
  * Si va a usar Managed Disks, revise [Plan for the migration to Managed Disks](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) (Planeación de la migración a Managed Disks).
  * [Preparar de un VHD de Windows para cargar en Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **No** generalice la VM mediante Sysprep.
  * Quite todas las herramientas de virtualización de invitado y los agentes instalados en la VM (es decir, herramientas de VMware).
  * Asegúrese de que la VM se configura para extraer su dirección IP y la configuración de DNS a través de DHCP. Esto garantiza que el servidor obtiene una dirección IP dentro de la red virtual cuando se inicia. 
  * Apague la VM antes de continuar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Si todavía no la ha instalado la versión 1.4 de PowerShell u otra superior, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs).

1. Abra Azure PowerShell e inicie sesión en la cuenta de Azure. Se abrirá una ventana emergente para que escriba sus credenciales de cuenta de Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Obtenga los identificadores de suscripción para las suscripciones disponibles.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Establezca la suscripción correcta con el identificador de suscripción. Reemplace `<subscriptionID>` con el identificador de la suscripción correcta.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obtención de la cuenta de almacenamiento
Necesita una cuenta de almacenamiento de Azure para almacenar la imagen de VM cargada. Puede usar una cuenta de almacenamiento existente o crear una nueva. 

Si va a usar el VHD para crear un disco administrado para una VM, la ubicación de la cuenta de almacenamiento debe ser la misma ubicación donde va a crear la VM.

Para mostrar las cuentas de almacenamiento disponibles, escriba:

```powershell
Get-AzureRmStorageAccount
```

Si desea utilizar una cuenta de almacenamiento existente, puede continuar con la sección [Carga de la imagen de VM en la cuenta de almacenamiento](#upload-the-vm-vhd-to-your-storage-account).

Si necesita crear una nueva cuenta de almacenamiento, siga estos pasos:

1. Necesita el nombre del grupo de recursos donde se debe crear la cuenta de almacenamiento. Para averiguar todos los grupos de recursos que están en la suscripción, escriba:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Para crear un grupo de recursos denominado **myResourceGroup** en la región **Oeste de EE. UU.**, escriba:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Cree una cuenta de almacenamiento denominada **mystorageaccount** en este grupo de recursos con el cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx):
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Los valores válidos para - SkuName son los siguientes:
   
   * **Standard_LRS**: Almacenamiento con redundancia local. 
   * **Standard_ZRS**: Almacenamiento con redundancia de zona.
   * **Standard_GRS**: Almacenamiento con redundancia geográfica. 
   * **Standard_RAGRS**: Almacenamiento con redundancia geográfica con acceso de lectura. 
   * **Premium_LRS**: Almacenamiento con redundancia local premium. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Carga del VHD en la cuenta de almacenamiento

Use el cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) para cargar el VHD en un contenedor de su cuenta de almacenamiento. En este ejemplo se carga el archivo **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` para una cuenta de almacenamiento denominada **mystorageaccount** en el grupo de recursos **myResourceGroup**. El archivo se colocará en el contenedor llamado **mycontainer** y el nuevo nombre de archivo será **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Si se realiza correctamente, obtendrá una respuesta similar a la siguiente:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependiendo de la conexión de red y del tamaño del archivo VHD, este comando tardará algún tiempo en completarse.

Guarde la ruta de acceso del **URI de destino** para usarla más adelante si va a crear un disco administrado o una nueva VM usando el VHD cargado.

### <a name="other-options-for-uploading-a-vhd"></a>Otras opciones para cargar un disco duro virtual

También puede cargar un VHD en la cuenta de almacenamiento mediante uno de los siguientes medios:

-   [API de tipo Copy Blob de Almacenamiento Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Blobs de carga de Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)

-   [Referencia de la API de REST de servicios de importación y exportación de almacenamiento](https://msdn.microsoft.com/library/dn529096.aspx)

    Se recomienda utilizar el servicio Import/Export si se calcula que el tiempo de carga va a ser superior a siete días. Puede usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para calcular el tiempo a partir del tamaño de los datos y la unidad de transferencia. 

    Import/Export se puede usar para realizar copias en una cuenta de almacenamiento estándar. Para copiar desde el almacenamiento estándar a una cuenta de Almacenamiento premium, use una herramienta como AzCopy.

    
## <a name="create-the-subnet-and-vnet"></a>Creación de la subred y la red virtual

Cree la red virtual y la subred de la [red virtual](../virtual-network/virtual-networks-overview.md).

1. Cree la subred. En este ejemplo se crea una subred llamada **mySubNet**, en el grupo de recursos **myResourceGroup** y se establece el prefijo de la dirección de subred en **10.0.0.0/24**.
   
    ```powershell
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Cree la red virtual. En este ejemplo se establece el nombre de la red virtual en **myVnetName**, la ubicación en **Oeste de EE. UU.** y el prefijo de dirección de la red virtual en **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>Creación de una dirección IP pública y una NIC
Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

1. Cree la dirección IP pública. En este ejemplo, el nombre de la dirección IP pública se establece en **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Cree la NIC. En este ejemplo, el nombre de la NIC se establece en **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creación del grupo de seguridad de red y las reglas de RDP
Para poder iniciar sesión en la máquina virtual mediante RDP, debe tener una regla de seguridad que permita el acceso RDP en el puerto 3389. Como el disco duro virtual de la nueva máquina virtual se creó a partir de una máquina virtual especializada existente, después de crear la máquina virtual puede usar una cuenta existente de la máquina virtual de origen que tenga permiso para iniciar sesión mediante RDP.

En este ejemplo se establece el nombre de NSG en **myNsg** y el nombre de la regla de RDP en **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para más información sobre puntos de conexión y reglas NSG, consulte [Apertura de puertos para una máquina virtual en Azure mediante PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="set-the-vm-name-and-size"></a>Establecimiento del nombre de VM y el tamaño

Este ejemplo establece el nombre de VM en "myVM" y el tamaño de VM en "Standard_A2".

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>Incorporación de la NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>Configuración del disco del SO

El SO especializado podría ser un VHD que ha [cargado en Azure](virtual-machines-windows-upload-image.md) o una [copia del VHD de una VM de Azure existente](virtual-machines-windows-vhd-copy.md). 

Puede elegir una de las dos opciones:
- **Opción 1**: crear un disco administrado especializado a partir de un VHD en una cuenta de almacenamiento existente que se usará como el disco del SO.

o 

- **Opción 2**: usar un VHD especializado almacenado en su propia cuenta de almacenamiento (un disco no administrado). 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>Opción 1: crear un disco administrado desde un disco especializado no administrado

1. Cree un disco administrado a partir del VHD especializado de su cuenta de almacenamiento. En este ejemplo se usa **myOSDisk1** como nombre del disco, se coloca el disco en el almacenamiento **StandardLRS** y se usa **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** como el URI para el VHD de origen.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri $urlOfUploadedImageVhd -ResourceGroupName $rgName
    ```

2. Agregue el disco del SO a la configuración. Este ejemplo establece el tamaño del disco en **128 GB** y adjunta el disco administrado como un disco del SO **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -ManagedDiskStorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

Opcional: adjunte discos administrados adicionales como discos de datos. Esta opción da por hecho que ha creado los discos de datos administrados mediante la [creación de discos de datos administrados](virtual-machines-windows-create-managed-disk-ps.md). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Opción 2: conectar un VHD que se encuentra en una cuenta de almacenamiento existente

1. Establezca el URI para el VHD que desea usar. En este ejemplo, el archivo de VHD denominado **myOsDisk.vhd** se mantiene en una cuenta de almacenamiento llamada **myStorageAccount** en un contenedor denominado **myContainer**.

    ```powershell
    $osDiskUri = $urlOfUploadedImageVhd
    ```
2. Agregue el disco del SO mediante la dirección URL del VHD del SO copiado. En este ejemplo, cuando se crea el disco del SO, el término "osDisk" se agrega al nombre de VM para crear el nombre del disco de SO. En este ejemplo también se especifica que este VHD basado en Windows debería asociarse a la VM como el disco del SO.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: si tiene discos de datos que necesitan estar conectados a la VM, agregue los discos de datos mediante el uso de las direcciones URL de VHD de datos y el número de unidad lógica (LUN) correspondiente.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Cuando se use una cuenta de almacenamiento, las direcciones URL de los discos de datos y del sistema operativo tienen un formato similar al siguiente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Para encontrarla en el portal, diríjase al contenedor de almacenamiento de destino, haga clic en el VHD de datos o del sistema operativo que se ha copiado y copie el contenido de la dirección URL.


## <a name="create-the-vm"></a>Creación de la máquina virtual

Cree la máquina virtual con las configuraciones que acaba de crear.

```powershell
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Si este comando es correcto, verá un resultado similar al siguiente:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>Comprobación de que se creó la máquina virtual
Debería ver la máquina virtual recién creada en el [portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Pasos siguientes
Para iniciar sesión en la nueva máquina virtual, examine la máquina virtual en el [Portal](https://portal.azure.com), haga clic en **Conectar**y abra el archivo RDP del Escritorio remoto. Utilice las credenciales de cuenta de la máquina virtual original para iniciar sesión en la nueva máquina virtual. Para más información, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



<!--HONumber=Feb17_HO2-->


