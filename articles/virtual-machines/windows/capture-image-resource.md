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
ms.date: 02/27/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: e428b755f6696bd6d4047ad77579a8e9665dfbd8
ms.contentlocale: es-es
ms.lasthandoff: 06/09/2017

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
2. Haga clic en el signo más (+) para crear un recurso nuevo.
3. En la búsqueda de filtro, escriba **Imagen**.
4. Seleccione **Imagen** en los resultados.
5. En la hoja **Imagen**, haga clic en **Crear**.
6. En **Nombre**, escriba un nombre para la imagen.
7. Si tiene más de una suscripción, seleccione la correcta de la lista desplegable **Suscripción**.
7. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba un nombre o elija **From existing** (De existentes) y seleccione en la lista desplegable el grupo de recursos que desea utilizar.
8. En **Ubicación**, elija la ubicación del grupo de recursos.
9. En **Tipo de SO**, seleccione el tipo de sistema operativo, es decir, Windows o Linux.
11. En **Blob de almacenamiento**, haga clic en **Examinar** para buscar el VHD en Azure Storage.
12. En **Tipo de cuenta**, elija Standard_LRS o Premium_LRS. Standard utiliza unidades de disco duro y Premium utiliza unidades de estado sólido. Ambas opciones emplean almacenamiento con redundancia local.
13. En **Almacenamiento en caché de disco**, seleccione la opción de caché de disco adecuada. Las opciones son **None** (Ninguno), **Read-only** (Solo lectura) y **Read\write** (Lectura y escritura).
14. Opcional: puede agregar un disco de datos existente a la imagen haciendo clic en **+ Agregar disco de datos**.  
15. Cuando haya terminado de realizar todas las selecciones, haga clic en **Crear**.
16. Después de crear la imagen, esta aparecerá como un recurso **Imagen** en la lista de recursos del grupo de recursos que ha seleccionado.



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>Creación de una imagen administrada de una VM mediante PowerShell

Crear una imagen directamente desde la VM garantiza que la imagen incluya todos los discos asociados a la VM, incluido el disco del SO y todos los discos de datos.


Antes de comenzar, asegúrese de que tiene la última versión del módulo AzureRM.Compute de PowerShell. Ejecute el siguiente comando para realizar la instalación.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](/powershell/azure/overview) (Control de versiones de Azure PowerShell).


1. Cree algunas variables.

    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Asegúrese de que la VM se ha desasignado.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Establezca el estado de la máquina virtual en **Generalizado**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Obtenga la máquina virtual. 

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Cree la configuración de la imagen.

    ```powershell
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Cree la imagen.

    ```powershell
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 



## <a name="create-a-managed-image-of-a-vhd-in-powershell"></a>Creación de una imagen administrada de un VHD en PowerShell

Cree una imagen administrada con el VHD del SO generalizado.


1.  En primer lugar, establezca los parámetros comunes:

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Detenga/desasigne la VM.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque la VM como generalizada.

    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Cree la imagen con el VHD del SO generalizado.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```


## <a name="create-a-managed-image-from-a-snapshot-using-powershell"></a>Creación de una imagen administrada a partir de una instantánea mediante PowerShell

También puede crear una imagen administrada a partir de una instantánea del VHD de una VM generalizada.

    
1. Cree algunas variables. 

    ```powershell
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Obtenga la instantánea.

   ```powershell
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Cree la configuración de la imagen.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Cree la imagen.

    ```powershell
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 
    

## <a name="next-steps"></a>Pasos siguientes
- Ahora puede [crear una VM a partir de la imagen administrada generalizada](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  


