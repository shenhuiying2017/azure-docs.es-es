---
title: "Inicio rápido de Azure: transferencia de objetos a y desde Azure Blob Storage mediante PowerShell | Microsoft Docs"
description: "Aprendizaje rápido para transferir objetos a y desde Azure Blob Storage mediante PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: robinsh
ms.openlocfilehash: 1a9941b21b92c70dd0a46ce2e4c75142e1786650
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-azure-powershell"></a>Transferencia de objetos a y desde Azure Blob Storage mediante Azure PowerShell

El módulo de Azure PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos de PowerShell o en scripts. En esta guía se detalla el uso de PowerShell para transferir archivos entre el disco local y Azure Blob Storage.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar este tutorial de inicio rápido se requiere la versión 3.6 o superior del módulo de Azure PowerShell. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Crear un contenedor

Los blobs siempre se cargan a un contenedor. Esto le permite organizar grupos de blobs de la misma forma que organiza archivos en carpetas en el equipo.

Establezca el nombre del contenedor y luego cree el contenedor mediante [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer), estableciendo los permisos en “blob” para permitir el acceso público de los archivos. El nombre del contenedor en este ejemplo es *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Carga de blobs al contenedor

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Los archivos VHD utilizados para respaldar VM IaaS son blobs en páginas. Los blobs en anexos se utilizan para el registro, por ejemplo, cuando desea escribir en un archivo y luego sigue agregando más información. La mayoría de los archivos almacenados en Blob Storage son blobs en bloques. 

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y luego consiga una referencia al blob en bloques en ese contenedor. Una vez que tenga la referencia de blob, puede cargar datos en él mediante [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Esta operación crea el blob si todavía no existe o lo sobrescribe si ya existe.

Los ejemplos siguientes cargan imagen001.jpg e Image002.png desde la carpeta D:\\_TestImages del disco local al contenedor que acaba de crear.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Cargue tantos archivos como desee antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Obtenga una lista de blobs del contenedor mediante [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Este ejemplo muestra solo los nombres de los blobs cargados.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Descargar blobs

Descargue los blobs en el disco local. Para cada blob descargado, establezca el nombre y la llame a [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) para descargar el blob.

Este ejemplo descarga los blobs a D:\\_TestImages\Downloads en el disco local. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Transferencia de datos con AzCopy

La utilidad [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) es otra opción para la transferencia de datos que permite ejecutar scripts de alto rendimiento para Azure Storage. Puede utilizar AzCopy para transferir datos a y desde Blob Storage, File Storage y Table Storage.

A modo de un ejemplo rápido, aquí está el comando de AzCopy para cargar un archivo denominado *myfile.txt* al contenedor *mystoragecontainer* desde dentro de una ventana de PowerShell.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Quite todos los recursos que ha creado. La manera más fácil de hacerlo consiste en eliminar el grupo de recursos. Esto también elimina todos los recursos contenidos en el grupo. En este caso, quita la cuenta de almacenamiento y el propio grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial de inicio rápido aprendió a transferir archivos entre un disco local y Azure Blob Storage. Para más información sobre cómo trabajar con Blob Storage, continúe con los procedimientos de Blob Storage.

> [!div class="nextstepaction"]
> [Procedimientos de las operaciones de Blob Storage](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referencia de cmdlets de almacenamiento de Microsoft Azure PowerShell
* [Cmdlets de PowerShell de almacenamiento](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de almacenamiento de Microsoft Azure
* El [Explorador de Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.