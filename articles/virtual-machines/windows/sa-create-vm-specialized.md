---
title: "Creación de una máquina virtual a partir de un disco especializado en Azure | Microsoft Docs"
description: "Cree una máquina virtual mediante la asociación de un disco no administrado especializado en el modelo de implementación del administrador de recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: 974d89aa96cba94fedfd1acbaf4f1d30ac8e6257
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Crear una máquina virtual a partir de un VHD especializado en una cuenta de almacenamiento

Cree una máquina virtual mediante la asociación de un disco no administrado especializado como disco del sistema operativo mediante PowerShell. Un disco especializado es una copia del VHD de una máquina virtual existente que mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. 

Tiene dos opciones:
* [Carga de un disco duro virtual](create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Copiar el VHD de una máquina virtual de Azure existente](create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>Antes de empezar
Si usa PowerShell, asegúrese de que tiene la versión más reciente del módulo de PowerShell AzureRM.Compute. Ejecute el siguiente comando para instalarla.

```powershell
Install-Module AzureRM.Compute 
```
Para más información, consulte [Azure PowerShell Versioning](/powershell/azure/overview) (Control de versiones de Azure PowerShell).


## <a name="option-1-upload-a-specialized-vhd"></a>Opción 1: Cargar un VHD especializado

Puede cargar el VHD de una máquina virtual especializada creada con una herramienta de virtualización local, como Hyper-V, o de una máquina virtual exportada desde otra nube.

### <a name="prepare-the-vm"></a>Preparación de la VM
Puede cargar un VHD especializado que se haya creado con una máquina virtual local o un VHD exportado desde otra nube. Un disco duro virtual especializado mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. Si tiene previsto usar el VHD como está para crear una nueva VM, asegúrese de completar los pasos siguientes. 
  
  * [Preparar de un VHD de Windows para cargar en Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **No** generalice la VM mediante Sysprep.
  * Quite todas las herramientas de virtualización de invitado y los agentes instalados en la VM (es decir, herramientas de VMware).
  * Asegúrese de que la VM se configura para extraer su dirección IP y la configuración de DNS a través de DHCP. Esto garantiza que el servidor obtiene una dirección IP dentro de la red virtual cuando se inicia. 


### <a name="get-the-storage-account"></a>Obtención de la cuenta de almacenamiento
Necesita una cuenta de almacenamiento de Azure para almacenar la imagen de VM cargada. Puede usar una cuenta de almacenamiento existente o crear una nueva. 

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

2. Cree una cuenta de almacenamiento denominada **mystorageaccount** en este grupo de recursos con el cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount):
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Carga del VHD en la cuenta de almacenamiento
Utilice el cmdlet [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) para cargar la imagen en un contenedor de su cuenta de almacenamiento. En este ejemplo se carga el archivo **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` para una cuenta de almacenamiento denominada **mystorageaccount** en el grupo de recursos **myResourceGroup**. El archivo se colocará en el contenedor llamado **mycontainer** y el nuevo nombre de archivo será **myUploadedVHD.vhd**.

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

En función de la conexión de red y del tamaño del archivo VHD, este comando tardará algún tiempo en completarse.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opción 2: copiar el VHD de una máquina virtual de Azure existente

Puede copiar un VHD a otra cuenta de almacenamiento para que se use al crear una máquina virtual duplicada.

### <a name="before-you-begin"></a>Antes de empezar
Asegúrese de todo esto:

* Tener información sobre las **cuentas de almacenamiento de origen y de destino**. Para la máquina virtual de origen, necesitará los nombres del contenedor y la cuenta de almacenamiento. Normalmente, el nombre del contenedor será **vhds**. También debe tener una cuenta de almacenamiento de destino. Si todavía no tiene una, puede crearla con el portal (**Más servicios** > Cuentas de almacenamiento > Agregar) o el cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). 
* Tener descargada e instalada la [herramienta AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Desasignación de la máquina virtual
Desasigne la máquina virtual para liberar espacio en el VHD que se va a copiar. 

* **Portal**: haga clic en **Máquinas virtuales** > **myVM** > Detener
* **PowerShell**: use [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) para detener (desasignar) la máquina virtual denominada **myVM** del grupo de recursos **myResourceGroup**.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

El **estado** de la máquina virtual en Azure Portal cambia de **Detenido** a **Detenido (desasignado)**.

### <a name="get-the-storage-account-urls"></a>Obtención de las URL de las cuentas de almacenamiento
Necesita las URL de las cuentas de almacenamiento de origen y de destino. Las direcciones URL se verán así: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Si ya conoce el nombre de la cuenta de almacenamiento y el contenedor, puede reemplazar simplemente la información entre corchetes para crear la dirección URL. 

Puede usar Azure Portal o Azure PowerShell para obtener la URL:

* **Portal**: haga clic en **>** para ir a **Más servicios** > **Cuentas de almacenamiento** > *cuenta de almacenamiento* > **Blobs**. El archivo VHD de origen estará probablemente en el contenedor **vhds**. Haga clic en la opción **Propiedades** del contenedor y copie el texto etiquetado como **Dirección URL**. Necesitará las direcciones URL de los contenedores de origen y de destino. 
* **PowerShell**: use [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) para obtener la información de la máquina virtual denominada **myVM** del grupo de recursos **myResourceGroup**. En los resultados, busque la sección **Perfil de almacenamiento** del **URI del VHD**. La primera parte del URI es la dirección URL al contenedor, y la última, el nombre del VHD del sistema operativo de la máquina virtual.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Obtención de las claves de acceso de almacenamiento
Busque las claves de acceso de las cuentas de almacenamiento de origen y de destino. Para más información acerca de las claves de acceso, consulte [Acerca de las cuentas de almacenamiento de Azure](../../storage/common/storage-create-storage-account.md).

* **Portal**: haga clic en **Más servicios** > **Cuentas de almacenamiento** > *cuenta de almacenamiento* > **Claves de acceso**. Copie la clave etiquetada como **clave1**.
* **PowerShell**: use [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) para obtener la clave de almacenamiento de la cuenta de almacenamiento **mystorageaccount** del grupo de recursos **myResourceGroup**. Copie la clave etiquetada como **clave1**.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Copia del VHD
Puede copiar archivos entre cuentas de almacenamiento con AzCopy. Para el contenedor de destino, si el contenedor especificado no existe, se creará automáticamente. 

Para usar AzCopy, abra una ventana de comandos en el equipo local y navegue hasta la carpeta donde está instalada dicha herramienta. Será similar a *C:\Archivos de programa (x86)\Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos los archivos dentro de un contenedor, use el modificador **/S**. Esto puede utilizarse para copiar el VHD del sistema operativo y todos los discos de datos si están en el mismo contenedor. En este ejemplo se muestra cómo copiar todos los archivos del contenedor **mysourcecontainer** de la cuenta de almacenamiento **mysourcestorageaccount** en el contenedor **mydestinationcontainer** de la cuenta de almacenamiento **mydestinationstorageaccount**. Reemplace los nombres de las cuentas de almacenamiento y los contenedores por los suyos. Reemplace `<sourceStorageAccountKey1>` y `<destinationStorageAccountKey1>` por sus propias claves.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Si solo quiere copiar un VHD específico en un contenedor con varios archivos, también puede especificar el nombre de archivo con el modificador /Pattern. En este ejemplo, solo se copiará el archivo denominado "**myFileName.vhd**".

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Cuando haya finalizado, obtendrá un mensaje parecido a este:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Solución de problemas
* Cuando use AZCopy, si ve el error El servidor no pudo autenticar la solicitud, asegúrese de que el valor del encabezado de autenticación tenga el formato correcto, incluida la firma, y, si usa la clave 2 o la clave de almacenamiento secundaria, pruebe a utilizar la clave de almacenamiento principal o 1.

## <a name="create-the-new-vm"></a>Creación de la máquina virtual 

Debe crear recursos de red y otros recursos de máquina virtual para que los use la nueva máquina virtual.

### <a name="create-the-subnet-and-vnet"></a>Creación de la subred y la red virtual

Cree la red virtual y la subred de la [red virtual](../../virtual-network/virtual-networks-overview.md).

1. Cree la subred. En este ejemplo se crea una subred llamada **mySubNet**, en el grupo de recursos **myResourceGroup** y se establece el prefijo de la dirección de subred en **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
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

### <a name="create-a-public-ip-address-and-nic"></a>Creación de una dirección IP pública y una NIC
Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

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

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creación del grupo de seguridad de red y las reglas de RDP
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

Para más información sobre puntos de conexión y reglas NSG, consulte [Apertura de puertos para una máquina virtual en Azure mediante PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="set-the-vm-name-and-size"></a>Establecimiento del nombre de VM y el tamaño

Este ejemplo establece el nombre de VM en "myVM" y el tamaño de VM en "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Incorporación de la NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Configuración del disco del SO

1. Establezca el URI para el VHD que ha cargado o copiado. En este ejemplo, el archivo de VHD denominado **myOsDisk.vhd** se mantiene en una cuenta de almacenamiento llamada **myStorageAccount** en un contenedor denominado **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Agregue el disco del sistema operativo. En este ejemplo, cuando se crea el disco del SO, el término "osDisk" se agrega al nombre de VM para crear el nombre del disco de SO. En este ejemplo también se especifica que este VHD basado en Windows debería asociarse a la VM como el disco del SO.
    
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


### <a name="complete-the-vm"></a>Completar la máquina virtual 

Cree la máquina virtual con las configuraciones que acaba de crear.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Si este comando es correcto, verá un resultado similar al siguiente:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Comprobación de que se creó la máquina virtual
Debería ver la máquina virtual recién creada en el [portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Pasos siguientes
Para iniciar sesión en la nueva máquina virtual, examine la máquina virtual en el [Portal](https://portal.azure.com), haga clic en **Conectar**y abra el archivo RDP del Escritorio remoto. Utilice las credenciales de cuenta de la máquina virtual original para iniciar sesión en la nueva máquina virtual. Para más información, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](connect-logon.md).

