---
title: "Cómo usar una instantánea del recurso compartido de Azure Files (versión preliminar) | Microsoft Docs"
description: "Use una instantánea del recurso compartido de Azure Files. Las instantáneas del recurso compartido de Azure Files son una versión de solo lectura de un recurso compartido de Azure Files que se usa en un momento dado. Una vez se crea la instantánea del recurso compartido, puede leerla, copiarla o eliminarla, pero no modificarla. Las instantáneas del recurso compartido le ofrecen una oportunidad de realizar una copia de seguridad del recurso compartido en el momento en que este aparezca."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 6643dad5ea3ba703e26f5708cdd2e925f702847f
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-azure-file-share-snapshots-preview"></a>Trabajar con instantáneas del recurso compartido de Azure Files (versión preliminar)
Las instantáneas del recurso compartido de Azure Files (versión preliminar) son una versión de solo lectura de un recurso compartido de Azure Files que se usa en un momento dado. Una vez se crea la instantánea del recurso compartido, puede leerla, copiarla o eliminarla, pero no modificarla. Las instantáneas del recurso compartido le ofrecen una oportunidad de realizar una copia de seguridad del recurso compartido en el momento en que este aparezca. En este artículo, obtendremos información sobre cómo crear, administrar y eliminar instantáneas del recurso compartido de Azure Files. Para obtener más información sobre la instantánea del recurso compartido, consulte [share snapshot overview](storage-snapshots-files.md) (información general de la instantánea del recurso compartido) o [Preguntas más frecuentes sobre la instantánea](storage-files-faq.md)

## <a name="create-azure-files-share-snapshots"></a>Crear instantáneas del recurso compartido de Azure Files

Puede crear una instantánea del recurso compartido mediante el portal, PowerShell, la CLI, REST o cualquier Storage SDK. En las siguientes secciones se indicará cómo crear una instantánea del recurso compartido mediante el portal, la CLI y PowerShell. 

Puede tomar una instantánea del recurso compartido de un recurso compartido de archivos mientras se encuentra en uso. Sin embargo, las instantáneas del recurso compartido solo capturan datos que ya se han escrito en el recurso compartido de Azure Files en el momento de la emisión del comando de la instantánea del recurso compartido. Esto podría excluir los datos almacenados en caché por aplicaciones o el sistema operativo.

### <a name="create-share-snapshot-using-portal"></a>Crear la instantánea del recurso compartido mediante el portal  
Solo tiene que ir a su recurso compartido de archivos del portal y seleccionar el botón `Create a Snapshot` para crear una instantánea del recurso compartido a un momento dado.

>   ![./Media/Storage-Snapshots-Create/portal-Create-Snapshot.png](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-share-snapshot-using-cli-20"></a>Crear la instantánea del recurso compartido mediante la CLI 2.0
Puede crear una instantánea del recurso compartido con el comando `az storage share snapshot`:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Salida de ejemplo
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

### <a name="create-share-snapshot-using-powershell"></a>Crear la instantánea del recurso compartido mediante PowerShell
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
## <a name="list-share-snapshots-browse-share-snapshot-contents-and-restore-from-snapshots"></a>Enumerar instantáneas del recurso compartido, examinar su contenido y restaurar desde estas

Puede enumerar las instantáneas del recurso compartido asociadas a su recurso compartido de archivos mediante la integración de “Versiones anteriores” en Windows, a través de REST, la biblioteca cliente, PowerShell y el portal. Una vez montado el recurso compartido de Azure Files, puede ver todas las versiones anteriores del archivo mediante la integración de “Versiones anteriores” de SMB. Una vez montado el recurso compartido de Azure Files, puede ver todas las versiones anteriores del directorio mediante la integración de “Versiones anteriores” de SMB. En las siguientes secciones, obtendrá información sobre cómo usar Azure Portal, Windows y la CLI de Azure 2.0 para enumerar, examinar y restaurar instantáneas del recurso compartido.

### <a name="share-snapshot-operations-in-portal"></a>Operaciones de la instantánea del recurso compartido en el portal

Puede echar un vistazo a todas sus instantáneas del recurso compartido para un recurso compartido de archivos del portal y examinar la instantánea del recurso compartido para ver su contenido

#### <a name="view-share-snapshot"></a>Ver la instantánea del recurso compartido
En su recurso compartido de archivos, en Instantánea, seleccione **Ver instantáneas**

![./media/storage-snapshots-list-browse/snapshot-view-portal.png](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-share-snapshot-content"></a>Enumerar y examinar el contenido de la instantánea del recurso compartido
Consulte la lista de instantáneas del recurso compartido y, a continuación, examine su contenido directamente seleccionando la instantánea del recurso compartido en la marca de tiempo deseada.

![./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

También puede seleccionar el botón **Conectar** de su vista de instantánea de lista para obtener el comando `net use` y la ruta de acceso del directorio a una instantánea del recurso compartido determinada, que puede examinar directamente.


![./media/storage-snapshots-list-browse/snapshot-connect-portal.pngsnapshot-list-portal.png](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-share-snapshot"></a>Descargar o restaurar desde la instantánea del recurso compartido
Desde dentro del portal, descargue o restaure el archivo deseado desde una instantánea.

![./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="file-share-snapshot-operations-in-windows"></a>Operaciones de la instantánea del recurso compartido de archivos en Windows
Una vez que ha tomado instantáneas del recurso compartido de su recurso compartido de archivos, puede ver versiones anteriores de un recurso compartido, directorio o un archivo determinado en su recurso compartido de archivos de Azure montado de Windows. Como ejemplo, así es como puede usar la característica **Versiones anteriores** para ver y restaurar una versión anterior de un directorio determinado en Windows:

> [!Note]  
> Se pueden realizar las mismas operaciones en el nivel de recurso compartido y en el nivel de archivo. Solo se muestra en la lista la versión que contiene cambios para ese directorio o archivo. Si un directorio o archivo determinado no ha cambiado entre dos instantáneas del recurso compartido, la instantánea del recurso compartido solo se muestra en la lista de versiones anteriores a nivel de recurso compartido, pero no en la lista de versiones anteriores del directorio o archivo.

#### <a name="mount-file-share"></a>Montar el recurso compartido de archivos
En primer lugar, monte el recurso compartido de archivos mediante el comando de uso de red.

#### <a name="open-mounted-share-in-explorer"></a>Abrir el recurso compartido montado en el explorador
Vaya al Explorador de archivos y busque el recurso compartido montado.

![./media/storage-snapshots-list-browse/snapshot-windows-mount.png](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Enumerar versiones anteriores
 Vaya al elemento o elemento principal que es necesario restaurar. Haga doble clic para ir al directorio deseado. Haga clic con el botón derecho y seleccione “Propiedades” en el menú

![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Seleccione “**Versiones anteriores”** para ver la lista de instantáneas del recurso compartido para este directorio. El listado puede tardar unos segundos en cargarse dependiendo de la velocidad de red y el número de instantáneas del recurso compartido.

 ![./media/storage-snapshots-list-browse/snapshot-windows-list.png](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Puede seleccionar **Abrir** para examinar una instantánea determinada 

 ![./media/storage-snapshots-list-browse/snapshot-browse-windows.png](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurar desde una versión anterior
**Restaure** para copiar contenido de todo el directorio de forma recursiva en el momento de la creación de la instantánea del recurso compartido en la ubicación original.
 ![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="file-share-snapshot-operations-in-azure-cli-20"></a>Operaciones de la instantánea del recurso compartido de archivos en la CLI de Azure 2.0
Puede usar la CLI de Azure 2.0 para realizar las mismas operaciones, tales como enumerar instantáneas del recurso compartido, examinar el contenido de estas, restaurar o descargar archivos desde la instantánea del recurso compartido o eliminar instantáneas del recurso compartido.

#### <a name="list-share-snapshots"></a>Enumerar instantáneas del recurso compartido

Puede enumerar las instantáneas del recurso compartido de un recurso compartido determinado mediante [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) con `--include-snapshots`

```azurecli-interactive 
az storage share list --include-snapshots
```

#### <a name="sample-output"></a>Salida de ejemplo
El comando le proporcionará una lista de instantáneas del recurso compartido junto con todas sus propiedades asociadas.

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

#### <a name="browse-share-snapshots"></a>Examinar instantáneas del recurso compartido
También puede examinar una instantánea del recurso compartido determinada para ver su contenido mediante [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Uno debe especificar el nombre del recurso compartido `--share-name` y la marca de tiempo que deseamos examinar en `--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

#### <a name="sample-output"></a>Salida de ejemplo

Verá que el contenido de la instantánea del recurso compartido es idéntico al del recurso compartido en el momento dado en que se creó la instantánea del recurso compartido.

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
#### <a name="restore-from-share-snapshots"></a>Restaurar desde instantáneas del recurso compartido

Puede restaurar un archivo copiando o descargando un archivo desde la instantánea del recurso compartido mediante el comando `az storage file download`

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

#### <a name="sample-output"></a>Salida de ejemplo

Como puede ver, el contenido del archivo descargado y sus propiedades son idénticos al contenido y las propiedades en el momento dado en que se creó la instantánea del recurso compartido.

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

## <a name="delete-azure-files-share-snapshot"></a>Eliminar la instantánea del recurso compartido de Azure Files

Puede eliminar instantáneas del recurso compartido de archivos mediante Azure Portal, PowerShell, la CLI, la API de REST o cualquier Storage SDK. En las siguientes secciones se indicará cómo eliminar instantáneas del recurso compartido mediante Azure Portal, la CLI y PowerShell.

Puede examinar instantáneas del recurso compartido y ver las diferencias entre las dos instantáneas del recurso compartido mediante cualquier herramienta de comparación para determinar qué instantánea del recurso compartido desea eliminar. 

No puede eliminar un recurso compartido que tenga una instantánea del recurso compartido. Primero debe eliminar todas sus instantáneas del recurso compartido para poder eliminar el recurso compartido.

### <a name="delete-share-snapshot-using-portal"></a>Eliminar la instantánea del recurso compartido mediante el portal  
Puede ir a su hoja del recurso compartido de archivos y seleccionar el botón `delete` del portal para eliminar una o varias instantáneas del recurso compartido.

>   ![./media/storage-snapshots-delete/portal-snapshots-delete.png](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-using-azure-cli-20"></a>Eliminar una instantánea del recurso compartido mediante la CLI de Azure 2.0
Puede eliminar una instantánea del recurso compartido mediante el comando `[az storage share delete]` proporcionando el parámetro `--snapshot '2017-10-04T23:28:35.0000000Z' ` con la marca de tiempo de la instantánea del recurso compartido:

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Salida de ejemplo
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-using-powershell"></a>Eliminar una instantánea del recurso compartido mediante PowerShell
Puede crear una instantánea del recurso compartido con el comando `Remove-AzureStorageShare -Share`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Pasos siguientes
* [Snapshot Overview](storage-snapshots-files.md) (Información general de la instantánea)
* [Preguntas más frecuentes sobre la instantánea](storage-files-faq.md)