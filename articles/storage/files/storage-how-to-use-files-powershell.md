---
title: Administración de recursos compartidos de archivos de Azure mediante Azure PowerShell
description: Aprenda a administrar recursos compartidos de archivos de Azure mediante Azure PowerShell.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 4a7d39910fac6096ef17873a9f81c5e1d1508857
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34757146"
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>Administración de recursos compartidos de archivos de Azure mediante Azure PowerShell 
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden montar en Windows, Linux y macOS. En esta guía se describen los conceptos básicos sobre cómo trabajar con recursos compartidos de archivos de Azure mediante PowerShell. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Crear un grupo de recursos y una cuenta de almacenamiento
> * Creación de un recurso compartido de archivos de Azure 
> * Creación de directorios
> * Cargar un archivo 
> * Descarga de un archivo
> * Crear y usar una instantánea de recurso compartido

Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si desea instalar y usar PowerShell de forma local, para esta guía se requiere la versión 5.1.1 del módulo de Azure PowerShell, o cualquier versión posterior. Para averiguar qué versión del módulo de Azure PowerShell está ejecutando, ejecute `Get-Module -ListAvailable AzureRM`. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Si ya no tiene un grupo de recursos de Azure, puede crear uno nuevo con el cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la región Este de EE. UU.:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
Una cuenta de almacenamiento es un grupo compartido de almacenamiento que puede utilizar para implementar recursos compartidos de archivos de Azure u otros recursos de almacenamiento como blobs o colas. Una cuenta de almacenamiento puede contener un número ilimitado de recursos compartidos y un recurso compartido puede almacenar un número ilimitado de archivos, hasta los límites de capacidad de la cuenta de almacenamiento.

Este ejemplo crea una cuenta de almacenamiento denominada `mystorageacct<random number>` y coloca una referencia a esa cuenta de almacenamiento en la variable **$storageAcct**. Los nombres de la cuenta de almacenamiento deben ser únicos, por lo que debe usar `Get-Random` para anexar un número al nombre para que sea único. 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure
Ahora puede crear su primer recurso compartido de archivos de Azure. Puede crear un recurso compartido de archivos mediante el cmdlet [New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare). Este ejemplo crea un recurso compartido denominado `myshare`.

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

> [!Important]  
> Los nombres de recursos compartidos deben estar formados por letras minúsculas, números y guiones sencillos, pero no pueden empezar con un guion. Para obtener detalles completos sobre cómo asignar un nombre a recursos compartidos y archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Uso del contenido de un recurso compartido de archivos de Azure
Ahora que ha creado un recurso compartido de archivos de Azure, puede montarlo con SMB en [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). Como alternativa, puede trabajar con el recurso compartido de archivos de Azure con el módulo Azure PowerShell. Esto supone una mejora con respecto al montaje del recurso compartido de archivos con SMB, dado que todas las solicitudes realizadas con PowerShell se realizan con la API REST File lo que le permite crear, modificar y eliminar archivos y directorios en el recurso compartido de archivos desde:

- PowerShell Cloud Shell (que no puede montar recursos compartidos de archivos sobre SMB).
- Clientes que no pueden montar recursos compartidos de SMB, como los clientes locales que no tienen desbloqueado el puerto 445.
- Escenarios sin servidor, como en [Azure Functions](../../azure-functions/functions-overview.md). 


### <a name="create-directory"></a>Creación del directorio
Para crear un nuevo directorio denominado *myDirectory* en la raíz del recurso compartido de archivos de Azure, use el cmdlet [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory).


```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>Cargar un archivo
Para demostrar cómo cargar un archivo mediante el cmdlet [Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent), primero es necesario crear un archivo dentro de la unidad temporal de PowerShell Cloud Shell donde cargar. 

Este ejemplo pone la fecha y hora actuales en un nuevo archivo en el disco temporal y, a continuación, lo carga en el recurso compartido de archivos.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Si está ejecutando PowerShell localmente, debe sustituir `C:\Users\ContainerAdministrator\CloudDrive\` con una ruta de acceso que exista en la máquina.

Después de cargar el archivo, puede usar el cmdlet [Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile) para asegurarse de que el archivo se cargó en el recurso compartido de archivos de Azure. 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

### <a name="download-a-file"></a>Descarga de un archivo
Puede usar el cmdlet [Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent) para descargar una copia del archivo que acaba de cargar en la unidad temporal de Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Después de descargar el archivo, puede usar el cmdlet `Get-ChildItem` para ver que el archivo se ha descargado a la unidad temporal de PowerShell Cloud Shell.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

### <a name="copy-files"></a>Copiar archivos
Una tarea habitual consiste en copiar los archivos de un recurso compartido de archivos a otro o entre contenedores de almacenamiento de Azure Blob. Para demostrar esta funcionalidad, puede crear un nuevo recurso compartido y copie el archivo recién cargado en este nuevo recurso compartido con el cmdlet [Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy). 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Ahora, si enumera los archivos en el nuevo recurso compartido, debería ver el archivo copiado.

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Aunque el cmdlet `Start-AzureStorageFileCopy` es conveniente para los movimientos de archivos ad hoc entre recursos compartidos de archivos de Azure y contenedores de almacenamiento de Azure Blob, se recomienda usar AzCopy para movimientos mayores (en cuanto al número o el tamaño de los archivos que se van a mover). Obtenga más información sobre [AzCopy para Windows](../common/storage-use-azcopy.md) y [AzCopy para Linux](../common/storage-use-azcopy-linux.md). AzCopy se debe instalar localmente; ya que no está disponible en Cloud Shell. 

## <a name="create-and-modify-share-snapshots"></a>Creación y modificación de instantáneas de recurso compartido
Una tarea útil adicional que puede hacer con un recurso compartido de archivos de Azure consiste en crear instantáneas de recurso compartido. Una instantánea conserva un punto en el tiempo para un recurso compartido de archivos de Azure. Las instantáneas de recurso compartido son similares a las tecnologías de sistemas operativos que es posible que conozca, como son:
- [Servicio de instantáneas de volumen (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) para sistemas de archivos Windows como NTFS y ReFS
- Instantáneas del [Administrador de volúmenes lógicos (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantáneas de [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 

Puede crear una instantánea de un recurso compartido mediante el método `Snapshot` en el objeto de PowerShell para un recurso compartido de archivos, que se recupera con el cmdlet [Get-AzureStorageShare](/powershell/module/azure.storage/get-azurestorageshare). 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Examen de las instantáneas de recurso compartido
Puede examinar el contenido de la instantánea del recurso compartido pasando la referencia de la instantánea (`$snapshot`) al parámetro `-Share` del cmdlet `Get-AzureStorageFile`.

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Enumerar instantáneas del recurso compartido
Puede ver la lista de instantáneas que ha realizado para el recurso compartido con el siguiente comando.

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Restauración desde una instantánea de recurso compartido
Puede restaurar un archivo mediante el comando `Start-AzureStorageFileCopy` que hemos usado antes. Para los fines de esta guía de inicio rápido, primero eliminaremos nuestro archivo `SampleUpload.txt` que cargamos previamente para poder restaurarlo a partir de la instantánea.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Eliminación de una instantánea de recurso compartido
Puede eliminar una instantánea de recurso compartido mediante el cmdlet [Remove-AzureStorageShare](/powershell/module/azure.storage/remove-azurestorageshare), con la variable que contiene la referencia `$snapshot` al parámetro `-Share`.

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando haya terminado, puede usar el cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos relacionados. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Como alternativa, puede quitar recursos de uno en uno:

- Quitar los recursos compartidos de archivos de Azure que hemos creado para esta guía de inicio rápido.
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- Quitar la propia cuenta de almacenamiento (así se quitarán implícitamente los recursos compartidos de archivos de Azure que hemos creado, así como cualquier otro recurso de almacenamiento que pueda haber creado, como un contenedor de Azure Blob Storage).
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>Pasos siguientes
- [Administración de recursos compartidos de archivos con Azure Portal](storage-how-to-use-files-portal.md)
- [Administración de recursos compartidos de archivos mediante la CLI de Azure](storage-how-to-use-files-cli.md)
- [Administración de recursos compartidos de archivos mediante el Explorador de Azure Storage](storage-how-to-use-files-storage-explorer.md)
- [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
