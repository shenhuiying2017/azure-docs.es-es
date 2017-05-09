---
title: Carga de un VHD de Windows para Resource Manager | Microsoft Docs
description: "Aprenda a cargar un VHD de máquina virtual de Windows desde local en Azure mediante el modelo de implementación de Resource Manager. Puede cargar un VHD desde una VM generalizada o especializada."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 192c8e5a-3411-48fe-9fc3-526e0296cf4c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 2cbd8461cb8e50765c988c64de03acf92936cc86
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---
# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Carga de un VHD de Windows desde una VM local en Azure
En este artículo se muestra cómo crear y cargar un disco duro virtual (VHD) de Windows para usarse en la creación de una VM de Azure. Puede cargar un VHD desde una VM generalizada o especializada. 

Para ver un tutorial completo sobre cómo preparar, cargar y crear una nueva máquina virtual mediante discos administrados, vea [Creación de una VM nueva a partir de un VHD generalizado cargado en Azure a través de Managed Disks](upload-generalized-managed.md) o [Carga de un VHD especializado para crear una máquina virtual en Azure](upload-specialized.md).

Para más detalles sobre discos y VHD en Azure, consulte [Acerca de los discos y los discos duros virtuales para máquinas virtuales de Azure](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prepare-the-vm"></a>Preparación de la VM
Puede cargar VHD generalizados y especializados en Azure. Cada tipo requiere la preparación de la VM antes de iniciar.

* **VHD generalizado**: Se ha quitado toda la información de cuenta personal con Sysprep de un VHD generalizado. Si tiene previsto usar VHD como una imagen desde la que crear nuevas VM, debe:
  
  * [Preparar de un VHD de Windows para cargar en Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
  * [Generalizar una máquina virtual mediante Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* **VHD especializado**: un disco duro virtual especializado mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. Si tiene previsto usar el VHD como está para crear una nueva VM, asegúrese de completar los pasos siguientes. 
  
  * [Preparar de un VHD de Windows para cargar en Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **No** generalice la VM mediante Sysprep.
  * Quite todas las herramientas de virtualización de invitado y los agentes instalados en la VM (es decir, herramientas de VMware).
  * Asegúrese de que la VM se configura para extraer su dirección IP y la configuración de DNS a través de DHCP. Esto garantiza que el servidor obtiene una dirección IP dentro de la red virtual cuando se inicia. 

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Si todavía no la ha instalado la versión 1.4 de PowerShell u otra superior, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview).

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
   
    Los valores válidos para - SkuName son los siguientes:
   
   * **Standard_LRS**: Almacenamiento con redundancia local. 
   * **Standard_ZRS**: Almacenamiento con redundancia de zona.
   * **Standard_GRS**: Almacenamiento con redundancia geográfica. 
   * **Standard_RAGRS**: Almacenamiento con redundancia geográfica con acceso de lectura. 
   * **Premium_LRS**: Almacenamiento con redundancia local premium. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Carga del VHD en la cuenta de almacenamiento
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

Dependiendo de la conexión de red y del tamaño del archivo VHD, este comando tardará algún tiempo en completarse.

## <a name="next-steps"></a>Pasos siguientes
* [Creación una VM en Azure a partir de un VHD generalizado](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Creación una VM en Azure a partir de un VHD generalizado](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) asociándola como un disco del SO cuando crea una nueva VM.


