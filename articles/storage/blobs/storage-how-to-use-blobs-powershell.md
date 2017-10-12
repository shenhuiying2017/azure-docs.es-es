---
title: "Realización de operaciones en Azure Blob Storage con PowerShell | Microsoft Docs"
description: "Tutorial: Realización de operaciones en Azure Blob Storage con PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 565bcba848de1c518b25ff4c55a9a47aaa45bfb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Realización de operaciones en Azure Blob Storage con Azure PowerShell

El Almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos de objetos no estructurados, como texto o datos binarios, a los que puede acceder desde cualquier lugar del mundo a través de HTTP o HTTPS. Este artículo trata las operaciones básicas en Azure Blob Storage como la carga, descarga y eliminación de blobs. Aprenderá a:

> [!div class="checklist"]
> * Crear un contenedor 
> * Cargar blobs
> * Enumerar los blobs de un contenedor 
> * Descargar blobs
> * Copia de blobs
> * Eliminar blobs
> * Ver y establecer las propiedades y metadatos de un blob
> * Administrar la seguridad con firmas de acceso compartido

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Crear un contenedor

Los blobs siempre se cargan a un contenedor. Los contenedores son similares a los directorios de su equipo, ya que permiten organizar grupos de blobs en un contenedor igual que organiza los archivos en carpetas en el equipo. Una cuenta de almacenamiento puede tener cualquier número de contenedores. La única limitación es la cantidad de espacio ocupado en la cuenta de almacenamiento (hasta 500 TB). 

Al crear un contenedor, puede establecer el nivel de acceso, lo que ayuda a definir quién puede acceder a los blobs en ese contenedor. Por ejemplo, puede definirse como privado (nivel de acceso = `Off`), de forma que nadie pueda acceder sin una firma de acceso compartido o sin las claves de acceso de la cuenta de almacenamiento. Si no se especifica el nivel de acceso al crear el contenedor, el valor predeterminado es privado.

Puede que desee que las imágenes del contenedor sean accesibles públicamente. Por ejemplo, si desea almacenar imágenes para mostrarlas en su sitio web, necesitará que sean públicas. En este caso, establezca el nivel de acceso al contenedor en `blob`, y cualquier persona con una dirección URL que dirija a un blob del contenedor podrá tener acceso al blob.

Para crear el contenedor, defina el nombre del contenedor y luego créelo, estableciendo los permisos en "blob". Los nombres de contenedor deben comenzar por una letra o un número, y solo pueden contener letras, números y el carácter de guión (-). Para conocer más reglas sobre la nomenclatura de contenedores de almacenamiento, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Cree un nuevo contenedor con [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Establezca el nivel de acceso en público. El nombre del contenedor en este ejemplo es *howtoblobs*.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>Carga de blobs en un contenedor

Azure Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas.  Los archivos VHD utilizados para respaldar VM IaaS son blobs en páginas. Los blobs en anexos se utilizan para el registro, por ejemplo, cuando desea escribir en un archivo y luego sigue agregando más información. La mayoría de los archivos almacenados en Blob Storage son blobs en bloques. 

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y luego consiga una referencia al blob en bloques en ese contenedor. Una vez que tenga la referencia de blob, puede cargar datos en él mediante [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Esta operación crea el blob si todavía no existe o lo sobrescribe si ya existe.

A continuación se describe cómo cargar un blob en un contenedor. En primer lugar, establezca las variables que señalen al directorio en el equipo local donde se encuentran los archivos y establezca otra variable para el nombre del archivo que se va a cargar. Esto resulta útil cuando desea realizar la misma operación varias veces. Cargue un par de archivos para que pueda ver varias entradas al enumerar los blobs del contenedor.

Los ejemplos siguientes cargan Image001.jpg e Image002.png desde D:\\_TestImages del disco local al contenedor que acaba de crear.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

Cargue otro archivo. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

Cargue tantos archivos como desee antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Para obtener una lista de los blobs del contenedor, utilice [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) y seleccione el nombre del blob que mostrar.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Descargar blobs

Descargue los blobs en el disco local. En primer lugar, establezca una variable que señale a la carpeta local en la que desea descargar los blobs. Para cada blob que se vaya a descargar, establezca el nombre y llame a [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) para descargar el blob.

Este ejemplo copia los blobs en D:\\_TestImages\Downloads en el disco local. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Copia de blobs

Hay varios aspectos que deben considerarse al copiar blobs. El blob puede simplemente copiarse en un nuevo nombre en la misma ubicación. El blob puede copiarse en una cuenta de almacenamiento independiente. Un blob de gran tamaño (como un archivo VHD) puede copiarse en una cuenta de almacenamiento diferente. Cada una de estas operaciones se realizaría de una forma distinta.

### <a name="simple-blob-copy"></a>Copia de blob simple

Puede realizar una copia de uno de los blobs de un contenedor copiándolo en un nuevo blob. En este ejemplo se copiará en el mismo contenedor con un nombre diferente, pero también puede crear fácilmente otro contenedor y copiarlo ahí. Este ejemplo muestra cómo usar [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) para copiar el blob. Tendrá que especificar el nombre del blob de origen y el de destino, así como el nombre del contenedor.

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>Copia de un blob a una cuenta de almacenamiento diferente 

Puede que desee copiar un blob en una cuenta de almacenamiento independiente. Para ello, por ejemplo, puede copiar un archivo VHD que respalde una de sus máquinas virtuales en una cuenta de almacenamiento distinta para realizar una copia de seguridad. 

Configure una segunda cuenta de almacenamiento, recupere el contexto, configure un contenedor en esa cuenta de almacenamiento y realice la copia. Esta parte del script es casi idéntica al script anterior salvo en que usa la segunda cuenta de almacenamiento, en lugar de la primera.

```powershell
#create new storage acount, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>Copia asincrónica de blobs de gran tamaño

Si va a copiar un blob muy grande de varios GB, puede aprovechar las ventajas de la copia asincrónica, para lo que debe iniciarla y regresar posteriormente para comprobar su estado hasta que se haya completado. De esta manera, no tendrá que estar pendiente mientras se realiza la operación de copia.

Si va a realizar la copia dentro de una misma cuenta de almacenamiento, la copia se realizará muy rápidamente. Si va a copiar entre dos cuentas de almacenamiento en la misma región, la copia es bastante rápida. Sin embargo, si el origen y el destino se encuentran en regiones distintas, la copia puede tardar varias horas en completarse, dependiendo de la distancia y el tamaño del archivo. 

Este script utiliza las mismas variables definidas en el último ejemplo de copia. La diferencia estriba en que este captura el estado de la copia y lo consulta repetidamente cada 10 segundos hasta que haya terminado. Es posible que tenga que cargar un blob muy grande en su cuenta de almacenamiento para que esto tarde más de una iteración en completarse.

Use [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) para consultar el estado de la copia. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Eliminar blobs

Para quitar los blobs de una cuenta de almacenamiento, utilice [Remove-AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob). 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Lectura y escritura de las propiedades y los metadatos de un blob

Para acceder al conjunto de propiedades y métodos de una lista **IListBlobItem** recuperada, debe convertir esta última en un objeto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Si se desconoce el tipo, puede realizar una comprobación de tipo para determinar el formato al que se debe convertir. El código siguiente muestra cómo recuperar y generar las propiedades de uno de los blobs cargados anteriormente usando [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) y convirtiendo el resultado en un objeto CloudBlockBlob.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

Rellene las propiedades del sistema llamando a FetchAttributes y, a continuación, vea las propiedades. Algunas de las propiedades se pueden escribir y sus valores se pueden cambiar. Este ejemplo muestra cómo cambiar el tipo de contenido, también llamado tipo MIME.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

Cada blob tiene sus propios metadatos que puede definir. Por ejemplo, puede almacenar el nombre del usuario que ha cargado el blob o la fecha y hora en que se cargó por primera vez. Los metadatos constan de pares clave/valor. Esto puede modificarse mediante PowerShell como se indica a continuación.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>Administración de la seguridad para blobs 

De forma predeterminada, Azure Storage protege sus datos al limitar el acceso al propietario de la cuenta, quien está en posesión de las claves de acceso a la cuenta de almacenamiento. Cuando necesite compartir datos Blob en su cuenta de almacenamiento, es importante hacerlo sin poner en peligro la seguridad de las claves de acceso de la cuenta. Para ello, puede usar una dirección URL de firma de acceso compartido, que es una dirección URL al recurso que incluye parámetros de consulta y un token de seguridad que permite un nivel específico de permiso durante un período de tiempo determinado. Por ejemplo, puede que quiera otorgar acceso de lectura a un blob privado durante cinco minutos para que alguien pueda verlo. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>Establecimiento del nivel de acceso del contenedor y sus blobs en privado

En primer lugar, establezca el nivel de acceso del contenedor en `Off`, que especifica que no se proporcionará acceso sin una firma de acceso compartido o sin la clave de cuenta. Use [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl).

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>Comprobación del acceso privado

Para comprobar que no tiene acceso a los blobs de ese contenedor, cree la dirección URL a uno de los blobs sin una firma de acceso compartido y vuelva a intentar ver el blob. Con el protocolo HTTPS, la dirección URL tendrá el siguiente formato:

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

A continuación se muestra cómo crear la dirección URL del blob.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Copie la dirección URL del blob y péguela en una ventana privada del explorador; mostrará un error de autorización porque el blob es privado y no se ha incluido una firma de acceso compartido. 

### <a name="create-the-sas-uri"></a>Creación del URI de SAS

Cree un URI de SAS, es decir, el vínculo al blob que incluye los parámetros de consulta y el token de seguridad que constituyen las firmas de acceso compartido. Pegue este URI en una ventana privada del explorador. Al hacerlo, se mostrará la imagen. 

Cree primero la fecha y hora de inicio y la fecha y hora de expiración para el acceso. En este caso, se usa un período de dos minutos. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

Cree el URI de SAS con [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken). Necesita el nombre del contenedor, el nombre del blob, el contexto de la cuenta de almacenamiento, los permisos que se concederán (en este caso, lectura, escritura y eliminación) y la hora de inicio y finalización para el acceso. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

Copie el URI de SAS y colóquelo en una ventana privada del explorador. Al hacerlo, se mostrará la imagen.

Espere el tiempo suficiente para que la dirección URL expire (dos minutos en este ejemplo) y, después, pegue la dirección URL en otra ventana privada del explorador. Esta vez, obtendrá un error de autorización y no se mostrará la imagen, porque el URI de SAS ha expirado.

## <a name="clean-up-resources"></a>Limpieza de recursos

Quite todos los recursos que ha creado. Para ello, puede quitar el grupo de recursos, lo que también elimina todos los recursos incluidos en el grupo. En este caso, quita todas las cuentas de almacenamiento y el propio grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido conceptos básicos sobre la administración del almacenamiento de blobs. Por ejemplo:

> [!div class="checklist"]
> * Crear un contenedor 
> * Cargar blobs
> * Enumerar los blobs de un contenedor 
> * Descargar blobs
> * Copia de blobs
> * Eliminar blobs
> * Lectura y escritura de las propiedades y los metadatos de un blob
> * Administrar la seguridad con firmas de acceso compartido

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de almacenamiento de Microsoft Azure PowerShell
* [Cmdlets de PowerShell de almacenamiento](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de almacenamiento de Microsoft Azure
* El [Explorador de Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.