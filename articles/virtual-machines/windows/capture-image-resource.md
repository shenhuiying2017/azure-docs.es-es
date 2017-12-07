---
title: "Creación de una imagen administrada en Azure | Microsoft Docs"
description: "Cree una imagen administrada de un VHD o de una VM generalizada en Azure. Se pueden utilizar imágenes para crear varias VM que usan discos administrados."
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
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: a7e94fe2cd2db398ab2afa9a6492cea144071114
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Captura de una imagen administrada de una máquina virtual generalizada en Azure

Se puede crear un recurso de imagen administrado a partir de una máquina virtual generalizada que se almacena como un disco administrado o como un disco no administrado en una cuenta de almacenamiento. A partir de ese momento, la imagen se puede utilizar para crear varias máquinas virtuales. 


## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalización de VM con Windows mediante Sysprep

Entre otras características, Sysprep elimina toda la información personal de la cuenta y prepara, entre otras cosas, la máquina para usarse como imagen. Para obtener más información sobre Sysprep, vea [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

Asegúrese de que los roles de servidor que se ejecutan en la máquina sean compatibles con Sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Si ejecuta Sysprep antes de cargar el VHD en Azure por primera vez, asegúrese de que tiene [preparada la máquina virtual](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de ejecutar Sysprep. 
> 
> 

1. Inicie sesión en la máquina virtual de Windows.
2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a **%windir%\system32\sysprep** y, después, ejecute `sysprep.exe`.
3. En **Herramienta de preparación del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la casilla **Generalizar** está activada.
4. En **Opciones de apagado**, seleccione **Apagar**.
5. Haga clic en **Aceptar**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Cuando Sysprep finaliza, apaga la máquina virtual. No reinicie la VM.


## <a name="create-a-managed-image-in-the-portal"></a>Creación de una imagen administrada en el portal 

1. Abra el [portal](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en Virtual Machines y seleccione la máquina virtual en la lista.
3. En la página de la máquina virtual, en el menú superior, haga clic en **Capturar**.
3. En **Nombre**, escriba el nombre que desea usar para la imagen.
4. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba un nombre o elija **Usar existente** y seleccione en la lista desplegable el grupo de recursos que desea utilizar.
5. Si desea eliminar la máquina virtual de origen tras haberse creado la imagen, seleccione **Eliminar automáticamente esta máquina virtual después de crear la imagen**.
6. Cuando haya terminado, haga clic en **Crear**.
16. Después de crear la imagen, esta aparecerá como un recurso **Imagen** en la lista de recursos del grupo de recursos.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Creación de una imagen de una VM mediante PowerShell

Crear una imagen directamente desde la VM garantiza que la imagen incluya todos los discos asociados a la VM, incluido el disco del SO y todos los discos de datos. En este ejemplo se muestra cómo crear una imagen administrada a partir de una máquina virtual que utiliza discos administrados.


Antes de comenzar, asegúrese de que tiene la última versión del módulo AzureRM.Compute de PowerShell. Ejecute el siguiente comando para realizar la instalación.

```azurepowershell-interactive
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](/powershell/azure/overview) (Control de versiones de Azure PowerShell).


1. Cree algunas variables.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Asegúrese de que la VM se ha desasignado.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Establezca el estado de la máquina virtual en **Generalizado**. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Obtenga la máquina virtual. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Cree la configuración de la imagen.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Cree la imagen.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Creación de una imagen a partir de un disco administrado mediante PowerShell

Si solo desea crear una imagen del disco del sistema operativo, también puede crear una imagen especificando el identificador del disco administrado como disco del sistema operativo.

    
1. Cree algunas variables. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Obtenga la máquina virtual.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name myVM -ResourceGroupName $rgName
   ```

3. Obtenga el id. del disco administrado.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Cree la configuración de la imagen.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Cree la imagen.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Creación de una imagen a partir de una instantánea mediante PowerShell

Puede crear una imagen administrada a partir de una instantánea de una VM generalizada.

    
1. Cree algunas variables. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Obtenga la instantánea.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Cree la configuración de la imagen.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Cree la imagen.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>Creación de una imagen a partir de un VHD en una cuenta de almacenamiento

Cree una imagen administrada a partir de un VHD del sistema operativo generalizado en una cuenta de almacenamiento. Es necesario el URI del VHD en la cuenta de almacenamiento, que se encuentra en formato https://*mystorageaccount*.blob.core.windows.net/*container*/*vhd_filename.vhd*. En este ejemplo, el VHD que usamos está en *mystorageaccount* en un contenedor denominado *vhdcontainer* y el nombre de archivo de VHD es *osdisk.vhd*.


1.  En primer lugar, establezca los parámetros comunes:

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Detenga/desasigne la VM.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque la VM como generalizada.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Cree la imagen con el VHD del SO generalizado.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Pasos siguientes
- Ahora puede [crear una VM a partir de la imagen administrada generalizada](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

