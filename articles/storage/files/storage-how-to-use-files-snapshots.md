---
title: "Uso de instantáneas de recursos compartidos (versión preliminar) | Microsoft Docs"
description: "Una instantánea de recurso compartido es una versión de solo lectura de un recurso compartido de Azure Files que se usa en un momento dado como método para realizar una copia de seguridad del recurso compartido."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: eb5c7d0fcb9e1106dbc0aa577ba5ecfa1bf6bee7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="work-with-share-snapshots-preview"></a>Uso de instantáneas de recursos compartidos (versión preliminar)
Las instantáneas de recursos compartidos (versión preliminar) son una versión de solo lectura de un recurso compartido de Azure Files que se usa en un momento dado. Una vez se crea la instantánea de recurso compartido, puede leerla, copiarla o eliminarla, pero no modificarla. Las instantáneas de recursos compartidos son una forma de realizar una copia de seguridad del recurso compartido en el momento en que este aparece. 

En este artículo, aprenderá a crear, administrar y eliminar instantáneas de recursos compartidos. Para más información, consulte la [introducción a las instantáneas de recursos compartidos](storage-snapshots-files.md) o las [preguntas más frecuentes acerca de las instantáneas](storage-files-faq.md).

## <a name="create-a-share-snapshot"></a>Creación de una instantánea de recurso compartido

Para crear una instantánea de recurso compartido puede usar Azure Portal, PowerShell, CLI de Azure, la API de REST o cualquier SDK de Azure Storage. En las siguientes secciones se describe cómo crear una instantánea de recurso compartido mediante el portal, la CLI y PowerShell. 

Puede tomar una instantánea del recurso compartido de un recurso compartido de archivos mientras se encuentra en uso. Sin embargo, las instantáneas de recursos compartidos solo capturan datos que ya se han escrito en un recurso compartido de archivos en el momento de la emisión del comando de la instantánea de recurso compartido. Esto podría excluir los datos almacenados en caché por aplicaciones o el sistema operativo.

### <a name="create-a-share-snapshot-by-using-the-portal"></a>Creación de una instantánea de recurso compartido mediante el portal  
Para crear una instantánea de recurso compartido de un momento específico, vaya al recurso compartido de archivos del portal y seleccione **Crear una instantánea**.

>   ![Menú Instantánea en el portal](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>Creación de una instantánea de recurso compartido mediante la CLI de Azure 2.0
Puede crear una instantánea del recurso compartido con el comando `az storage share snapshot`:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Salida de ejemplo:
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-a-share-snapshot-by-using-powershell"></a>Creación de una instantánea de recurso compartido mediante PowerShell
Puede crear una instantánea del recurso compartido con el comando `$share.Snapshot()`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>Realización de operaciones comunes de instantánea de recurso compartido

Puede enumerar las instantáneas de recursos compartidos asociadas a su recurso compartido de archivos mediante la pestaña **Versiones anteriores** de Windows, a través de REST, la biblioteca cliente, PowerShell y el portal. Una vez montado el recurso compartido de archivos, se pueden ver todas las versiones anteriores del archivo en la pestaña **Versiones anteriores** de Windows. 

En las siguientes secciones, se describe cómo usar Azure Portal, Windows y la CLI de Azure 2.0 para enumerar, examinar y restaurar instantáneas de recursos compartidos.

### <a name="share-snapshot-operations-in-the-portal"></a>Operaciones de instantánea de recursos compartidos en el portal

Puede echar un vistazo a todas sus instantáneas del recurso compartido para un recurso compartido de archivos del portal y examinar una instantánea de recurso compartido para ver su contenido.

#### <a name="view-a-share-snapshot"></a>Visualización de una instantánea de recurso compartido
En su recurso compartido de archivos, en **Instantánea**, seleccione **Ver instantáneas**.

![Comando "Ver instantáneas" del portal](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>Enumeración y examen del contenido de una instantánea de recurso compartido
Consulte la lista de instantáneas de recursos compartidos y, después, examine el contenido de cualquiera de ellas directamente seleccionando la marca de tiempo deseada.

![Instantánea seleccionada en la lista de marcas de tiempo](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

También puede hacer clic en el botón **Conectar** de su vista de instantánea de lista para obtener el comando `net use` y la ruta de acceso del directorio a una instantánea de recurso compartido determinada. Luego, puede ir directamente a dicha instantánea.


![Panel Conectar con cuadro de comandos](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>Descarga o restauración desde una instantánea de recurso compartido
En el portal, descargue o restaure un archivo desde una instantánea, para lo que debe hacer clic en los botones **Descargar** o **Restaurar**, respectivamente.

![Botones Descargar y Restaurar](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Operaciones de instantáneas de recursos compartidos en Windows
Cuando haya tomado instantáneas de los recursos compartidos de su recurso compartido de archivos, podrá ver las versiones anteriores de un recurso compartido, directorio o archivo concretos en su recurso compartido de archivos montado de Windows. Como ejemplo, así es como puede usar la característica Versiones anteriores para ver y restaurar una versión anterior de un directorio en Windows.

> [!Note]  
> Puede realizar las mismas operaciones en el nivel de recurso compartido y en el nivel de archivo. En la lista solo se muestra la versión que contiene los cambios para ese directorio o archivo. Si un directorio o archivo concretos no han cambiado entre dos instantáneas de recursos compartidos, la instantánea del recurso compartido aparece en la lista de versiones anteriores a nivel de recurso compartido, pero no en la lista de versiones anteriores del directorio o archivo.

#### <a name="mount-a-file-share"></a>Montar un recurso compartido de archivos
En primer lugar, monte el recurso compartido de archivos mediante el comando `net use`.

#### <a name="open-a-mounted-share-in-file-explorer"></a>Abrir un recurso compartido montado en el Explorador de archivos
Vaya al Explorador de archivos y busque el recurso compartido montado.

![Recurso compartido montado en el Explorador de archivos](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Enumeración de versiones anteriores
Vaya al elemento o elemento principal que hay que restaurar. Haga doble clic para ir al directorio deseado. Haga clic con el botón derecho y seleccione **Propiedades** en el menú.

![Haga clic con el botón derecho en el menú para ir a un directorio seleccionado](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Seleccione **Versiones anteriores** para ver la lista de instantáneas de recursos compartidos de este directorio. La lista puede tardar unos segundos en cargarse dependiendo de la velocidad de red y del número de instantáneas de recursos compartidos que haya en el directorio.

![Pestaña Versiones anteriores](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Puede seleccionar **Abrir** para abrir una instantánea concreta. 

![Instantánea abierta](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurar desde una versión anterior
Haga clic en **Restaurar** para copiar el contenido de todo un directorio de forma recursiva en el momento de la creación de la instantánea del recurso compartido en la ubicación original.
 ![Botón Restaurar en mensaje de advertencia](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Operaciones de instantáneas de recurso compartidos en la CLI de Azure 2.0
Puede usar la CLI de Azure 2.0 para realizar operaciones como enumerar instantáneas de recursos compartidos, examinar el contenido de estas, restaurar o descargar archivos desde las instantáneas de recurso compartidos o eliminar dichas instantáneas.

#### <a name="list-share-snapshots"></a>Enumerar instantáneas del recurso compartido

Para enumerar las instantáneas de un recurso compartido concreto, use [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) con `--include-snapshots`:

```azurecli-interactive 
az storage share list --include-snapshots
```

El comando le proporcionará una lista de instantáneas del recurso compartido, junto con todas sus propiedades asociadas. La siguiente salida es un ejemplo:

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-to-a-share-snapshot"></a>Examen de una instantánea de recurso compartido
Para examinar una instantánea de recurso compartido determinada para ver su contenido, use [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Especifique el nombre de recurso compartido y la marca de tiempo que desea examinar, como se muestra en el ejemplo siguiente:

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

En la salida, se ve que el contenido de la instantánea de recurso compartido es idéntico al del recurso compartido en el momento específico en que se creó dicha instantánea:

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-a-share-snapshot"></a>Restauración desde una instantánea de recurso compartido

Para restaurar un archivo, cópielo o descárguelo de la instantánea del recurso compartido. Use el comando `az storage file download`, como se muestra en el siguiente ejemplo:

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

En la salida, se ve que el contenido del archivo descargado y sus propiedades son idénticos al contenido y las propiedades en el momento específico en que se creó la instantánea de recurso compartido:

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

### <a name="file-share-snapshot-operations-in-azure-powershell"></a>Operaciones de la instantánea del recurso compartido de archivos en Azure PowerShell
Puede usar Azure Powershell para realizar las mismas operaciones, tales como enumerar instantáneas del recurso compartido, examinar el contenido de estas, restaurar o descargar archivos desde la instantánea del recurso compartido o eliminar instantáneas del recurso compartido.

#### <a name="list-share-snapshots"></a>Enumerar instantáneas del recurso compartido

Puede enumerar las instantáneas del recurso compartido de un recurso compartido determinado mediante `Get-AzureStorageShare`

```powershell
Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
```

#### <a name="browse-share-snapshots"></a>Examen de las instantáneas de recurso compartido
También puede ir a un recurso compartido de una instantánea específica para ver su contenido mediante `Get-AzureStorageFile` con el valor de `-Share` que apunte a la instantánea determinada.

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
Get-AzureStorageFile -Share $snapshot
```

#### <a name="restore-from-share-snapshots"></a>Restauración desde instantáneas de recurso compartido

Puede restaurar un archivo copiando o descargando un archivo desde la instantánea del recurso compartido mediante el comando `Get-AzureStorageFileContent`

```powershell
$download='C:\Temp\Download'
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $download
```

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
$directory = Get-AzureStorageFile -ShareName "ContosoShare06" -Path "ContosoWorkingFolder" | Get-AzureStorageFile
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $directory
```

## <a name="delete-a-share-snapshot"></a>Eliminación de una instantánea de recurso compartido

Para eliminar instantáneas de recursos compartidos utilice Azure Portal, PowerShell, la CLI, la API de REST o cualquier SDK de Storage. En las siguientes secciones se describe cómo eliminar instantáneas de recursos compartidos mediante Azure Portal, la CLI y PowerShell.

Para examinar las instantáneas de recursos compartidos y ver las diferencias entre dos instantáneas puede usar cualquier herramienta de comparación. Luego, puede determinar qué instantánea desea eliminar. 

No puede eliminar un recurso compartido que tenga una instantánea. Para poder eliminar el recurso compartido, antes hay que eliminar todos sus recursos compartidos.

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>Eliminación de una instantánea de recurso compartido mediante el portal  
En el portal, puede ir a la hoja de su recurso compartido de archivos y hacer clic en el botón **Eliminar** para eliminar una o varias instantáneas del recurso compartido.

>   ![Botón Eliminar](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>Eliminación de una instantánea de recurso compartido mediante la CLI de Azure 2.0
Para eliminar una instantánea de recurso compartido, utilice el comando `[az storage share delete]`. Use la marca de tiempo de la instantánea de recurso compartido para el parámetro `--snapshot '2017-10-04T23:28:35.0000000Z' ` en el ejemplo siguiente:

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Salida de ejemplo:
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>Eliminación de una instantánea de recurso compartido mediante PowerShell
Para eliminar una instantánea de recurso compartido, utilice el comando `Remove-AzureStorageShare -Share`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>pasos siguientes
* [Snapshot overview](storage-snapshots-files.md) (Introducción a las instantáneas)
* [Preguntas más frecuentes sobre la instantánea](storage-files-faq.md)
