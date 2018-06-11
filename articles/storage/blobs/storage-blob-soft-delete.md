---
title: Eliminación temporalmente de blobs de Azure Storage (versión preliminar) | Microsoft Docs
description: Azure Storage ofrece la posibilidad de eliminar temporalmente (versión preliminar) objetos de blob, con el fin de que se puedan recuperar más fácilmente los datos cuando una aplicación u otro usuario de la cuenta de almacenamiento los hayan modificado o eliminado por error.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 05/31/2018
ms.author: mihauss
ms.openlocfilehash: 93b60f8957a6ae225dbc5beb33a7de817ffc5bc2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34701690"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Eliminación temporal de blobs de Azure Storage

## <a name="overview"></a>Información general

Azure Storage ofrece la posibilidad de eliminar temporalmente objetos de blob, con el fin de que pueda recuperar más fácilmente los datos cuando una aplicación u otro usuario de la cuenta de almacenamiento los hayan modificado o eliminado por error.

## <a name="how-does-it-work"></a>¿Cómo funciona?

Cuando está activada, la eliminación temporal para guardar y recuperar los datos cuando se eliminan los blobs o las instantáneas de blob. Esta protección se extiende a los datos de blob que se borran como resultado de una sobrescritura.

Cuando se eliminan los datos, pasan a un estado de eliminación temporal, en lugar de borrarse de forma permanente. Cuando la eliminación temporal está activada y se sobrescriben los datos, se genera una instantánea de eliminación temporal para guardar el estado de los datos sobrescritos. Los objetos que se han eliminado temporalmente no se ven, salvo que se enumeren de forma explícita.
Se puede configurar el tiempo durante el que los datos eliminados se pueden recuperar antes de que expiren permanentemente.

La eliminación temporal es compatible con las versiones anteriores; no es preciso realizar ningún cambio en las aplicaciones para aprovechar las ventajas de los mecanismos de protección que se obtienen con esta característica. Sin embargo, la [recuperación de datos](#recovery) incorpora la nueva **Undelete Blob** API.

### <a name="configuration-settings"></a>Valores de configuración

Cuando se crea una cuenta, la eliminación temporal está desactivada de forma predeterminada. La eliminación temporal también está desactivada de forma predeterminada en las cuentas de almacenamiento existentes. Esta característica se puede activar o desactivar en cualquier momento de la vida de una cuenta de almacenamiento.

Aunque la función esté desactivada será posible acceder y recuperar los datos eliminados temporalmente, suponiendo que los datos eliminados temporalmente se guardaran cuando la característica estaba activada. Si se activa la eliminación temporal, también hay que configurar el periodo de retención.

El período de retención indica la cantidad de tiempo durante el que los datos eliminados temporalmente se almacenan y están disponibles para su recuperación. En el caso de los blobs y las instantáneas de blob que se eliminan explícitamente, el reloj del período de retención se pone en marcha cuando se eliminan los datos. Sin embargo, en el caso de las instantáneas eliminadas temporalmente que genera la característica de eliminación temporal cuando se sobrescriben los datos, se pone en marcha cuando se genera la instantánea. Actualmente, los datos que se eliminan temporalmente se pueden conservar un periodo que oscila entre 1 y 365 días.

El período de retención de la eliminación temporal se puede cambiar en cualquier momento. El periodo de retención actualizado solo se aplicará a los datos recién eliminados. Los datos eliminados con anterioridad expirarán en función del periodo de retención que se configuró en el momento en que se eliminaron. Si intenta eliminar un objeto eliminado temporalmente, su hora de expiración no resultará afectará.

### <a name="saving-deleted-data"></a>Guardado de los datos eliminados

La eliminación temporal conserva los datos en muchos casos en los que los blobs o las instantáneas de blob se eliminan o se sobrescriben.

Si se usan **Put Blob**, **Put Block**, **Put Block List** o **Copy Blob** cuando se sobrescribe un blob, se genera automáticamente una instantánea del estado del blob antes de la operación de escritura. Esta es una instantánea de eliminación temporal; es invisible, a menos que se enumeren explícitamente los objetos que se han eliminado temporalmente. Para aprender a enumerar objetos que se han eliminado temporalmente, consulte la sección [Recuperación](#recovery).

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Los datos eliminados temporalmente son de color gris, mientras que los datos activos están son de color azul. Los últimos datos escritos aparecen debajo de los datos más antiguos. Cuando B0 se sobrescribe con B1, se genera una instantánea de eliminación temporal de B0. Cuando B1 se sobrescribe con B2, se genera una instantánea de eliminación temporal de B1.*

> [!NOTE]
> La eliminación temporal solo ofrece protección contra escritura en las operaciones de copia cuando está activada en la cuenta del blob de destino.

> [!NOTE]
> La eliminación temporal no permite la protección contra escritura de blobs en el nivel de archivo. Si un blob archivado se sobrescribe con blob nuevo en cualquier nivel, el primero expira de forma permanente.

Si se llama a **Delete Blob** en una instantánea, esta se marca como eliminada temporalmente. No se una nueva instantánea.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Los datos eliminados temporalmente son de color gris, mientras que los datos activos están son de color azul. Los últimos datos escritos aparecen debajo de los datos más antiguos. Si se llama a **Snapshot Blob**, B0 pasa a ser una instantánea y B1 es el estado activo del blob. Cuando se elimina la instantánea B0, se marca como eliminada temporalmente.*

Cuando se llama a **Delete Blob** en un blob base (cualquier blob que sea en sí mismo una instantánea), dicho blob se marca como eliminado temporalmente. Por coherencia con el comportamiento anterior, si se llama a **Delete Blob** en un blob que tiene instantáneas activas, se produce un error. Sin embargo, si se llama a **Delete Blob** en un blob con instantáneas eliminadas temporalmente, no se produce un error. Si la eliminación temporal está activada, se pueden eliminar un blob y todas sus instantáneas en una sola operación. Al hacerlo, tanto el blob base como las instantáneas de marcan como eliminados temporalmente.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Los datos eliminados temporalmente son de color gris, mientras que los datos activos están son de color azul. Los últimos datos escritos aparecen debajo de los datos más antiguos. En este caso, se realiza una llamada a **Delete Blob** para eliminar B2 y todas las instantáneas asociadas. El blob activo, B2, y todas las instantáneas asociadas se marcan como eliminados temporalmente.*

> [!NOTE]
> Cuando se sobrescribe un blob eliminado temporalmente, se genera automáticamente una instantánea de eliminación temporal del estado del blob antes de la operación de escritura. El nuevo blob hereda el nivel del blob sobrescrito.

La eliminación temporal no guarda los datos si se eliminan el contenedor o la cuenta, ni tampoco cuando se sobrescriben los metadatos y las propiedades del blob. Para proteger una cuenta de almacenamiento contra cualquier eliminación por error, se puede configurar un bloqueo mediante Azure Resource Manager. Para más información, consulte el artículo de Azure Resource Manager [Bloqueo de recursos para impedir cambios inesperados](../../azure-resource-manager/resource-group-lock-resources.md).

En la tabla siguiente se detalla el comportamiento esperado cuando se activa la eliminación temporal:

| Operación de API REST                                                                                                | Tipo de recurso                 | DESCRIPCIÓN                                                                                                 | Cambio en el comportamiento                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------|-------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Eliminar](/rest/api/storagerp/StorageAccounts/Delete)              | Cuenta                       | Elimina la cuenta de almacenamiento, incluidos todos los contenedores y blobs que contiene.                           | Sin cambios. Los contenedores y blobs de la cuenta eliminada no se pueden recuperar.                                                                                                                                                                                                                                                                                          |
| [Delete Container](/rest/api/storageservices/fileservices/delete-container)       | Contenedor                     | Elimina el contenedor, incluidos todos los blobs que contiene.                                                | Sin cambios. Los blobs del contenedor eliminado no se pueden recuperar.                                                                                                                                                                                                                                                                                                       |
| [Put Blob](/rest/api/storageservices/fileservices/put-blob)                       | Blobs en bloques, de anexión y de página | Crea un nuevo blob o reemplaza uno en un contenedor                                          | Si se utiliza para reemplazar un blob existente, se genera automáticamente una instantánea del estado del blob antes de la llamada. Esto también se aplica a un blob que se ha eliminado antes temporalmente si, y solo si, se reemplaza por un blob del mismo tipo (en bloques, de anexión o de página). Si se reemplaza por un blob de cualquier otro tipo, todos los datos eliminados temporalmente existentes expirarán de forma permanente. |
| [Delete Blob](/rest/api/storageservices/fileservices/delete-blob)                 | Blobs en bloques, de anexión y de página | Marca un blob o una instantánea de blob para su eliminación. El blob o la instantánea se eliminan posteriormente, durante la recolección de elementos no utilizados | Si se usa para eliminar una instantánea de blob, esta se marca como eliminada temporalmente. Si se usa para eliminar un blob, este se marca como eliminado temporalmente.                                                                                                                                                                                                                           |
| [Copy Blob](/rest/api/storageservices/fileservices/copy-blob)                     | Blobs en bloques, de anexión y de página | Copia un blob de origen en un blob de destino de la misma cuenta de almacenamiento o de otra.       | Si se utiliza para reemplazar un blob existente, se genera automáticamente una instantánea del estado del blob antes de la llamada. Esto también se aplica a un blob que se ha eliminado antes temporalmente si, y solo si, se reemplaza por un blob del mismo tipo (en bloques, de anexión o de página). Si se reemplaza por un blob de cualquier otro tipo, todos los datos eliminados temporalmente existentes expirarán de forma permanente. |
| [Put Block](/rest/api/storageservices/put-block)                                  | Blobs en bloques                   | Crea un nuevo bloque que se confirmará como parte de un blob en bloques.                                                | Si se utiliza para confirmar un bloque en un blob que esté activo, no habrá cambio alguno. Si utiliza para confirmar un bloque en un blob que se ha eliminado temporalmente, se crea un nuevo blob y se genera automáticamente una instantánea para capturar el estado del blob eliminado temporalmente.                                                                                                                      |
| [Put Block List](/rest/api/storageservices/fileservices/put-block-list)           | Blobs en bloques                   | Confirma un blob mediante la especificación del conjunto de identificadores de bloque que componen el blob en bloques.                             | Si se utiliza para reemplazar un blob existente, se genera automáticamente una instantánea del estado del blob antes de la llamada. Esto también se aplica a un blob que se eliminó temporalmente si, y solo si, es un blob en bloques. Si se reemplaza por un blob de cualquier otro tipo, todos los datos eliminados temporalmente existentes expirarán de forma permanente.                                                |
| [Put Page](/rest/api/storageservices/fileservices/put-page)                       | Blobs en páginas                    | Escribe un rango de páginas en un blob en páginas.                                                                     | Sin cambios. Los datos del blob en páginas que se sobrescriben o se borrar con esta operación no se guardan ni se pueden recuperar.                                                                                                                                                                                                                                                   |
| [Append Block](/rest/api/storageservices/fileservices/append-block)               | Blobs de anexión                  | Escribe un bloque de datos al final de un blob de anexión.                                                         | Sin cambios.                                                                                                                                                                                                                                                                                                                                                           |
| [Set Blob Properties](/rest/api/storageservices/fileservices/set-blob-properties) | Blobs en bloques, de anexión y de página | Establece los valores de las propiedades del sistema definidas para un blob.                                                       | Sin cambios. Las propiedades del blob sobrescribe no se pueden recuperar.                                                                                                                                                                                                                                                                                                          |
| [Set Blob Metadata](/rest/api/storageservices/fileservices/set-blob-metadata)     | Blobs en bloques, de anexión y de página | Establece los metadatos definidos por el usuario para el blob especificado como uno o varios pares de nombre y valor.                          | Sin cambios. Los metadatos del blob sobrescrito no se pueden recuperar.                                                                                                                                                                                                                                                                                                             |

Es importante tener en cuenta que si se llama a "Put Page" para sobrescribir o borrar los rangos de un blob en páginas no se generarán instantáneas automáticamente. Los discos de máquina virtual tienen el respaldo de los blobs en páginas y usan **Put Page** para escribir los datos.

### <a name="recovery"></a>Recuperación

Para facilitar la recuperación de datos eliminados, hemos introducido la nueva API "Undelete Blob". Si se llama a la API Undelete en un blob base eliminado temporalmente, se restauran tanto el blob como todas las instantáneas eliminadas temporalmente y se activan. Si se llama a la API Undelete en un blob base activo, todas las instantáneas eliminadas temporalmente se restauran como activas. Cuando las instantáneas se restauran como activas, parecen generadas por el usuario; no sobrescriben el blob base.

Para restaurar un blob en una instantánea eliminada temporalmente puede llamar a **Undelete Blob** en el blob base. Luego puede copiar la instantánea sobre el blob activo. También puede copiar la instantánea en un blob nuevo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Los datos eliminados temporalmente son de color gris, mientras que los datos activos están son de color azul. Los últimos datos escritos aparecen debajo de los datos más antiguos. En este caso, **Undelete Blob** se llama en el blob B, con lo que se restaura el blob base, B1, y todas las instantáneas asociadas, aquí simplemente B0, como activas. En el segundo paso, se copia B0 sobre el blob base. Esta operación de copia genera una instantánea de eliminación automática de B1.*

Para ver los blobs y las instantáneas de blob eliminados temporalmente, puede elegir incluir datos eliminados en **List Blobs**. Puede elegir ver solo los blobs base eliminados temporalmente o incluir también las instantáneas de blob eliminadas temporalmente. En todos los datos eliminados temporalmente se puede ver la hora en que se eliminaron, así como el número de días que faltan para que expiren de forma permanente.

### <a name="example"></a>Ejemplo

A continuación se ve la salida de la consola de un script de .NET que carga, sobrescribe, crea instantáneas, elimina y restaura un blob denominado "HelloWorld" cuando la eliminación temporal está activada:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

En la sección [Pasos siguientes](#next-steps) puede encontrar un puntero a la aplicación que generó esta salida.

## <a name="pricing-and-billing"></a>Precios y facturación

Todos los datos eliminados temporalmente se factura con la misma tasa que los datos activos. Sin embargo, los datos que se eliminen permanentemente después del periodo de retención configurado no se cobrarán. Para ver un análisis más profundo de las instantáneas y cómo generan cargos, consulte [Creación de una instantánea de un blob](storage-blob-snapshots.md).

No se le cobrarán las transacciones relacionadas con la generación automática de instantáneas. Las transacciones de **Undelete Blob** se le cobrarán con la tarifa de "Operaciones de escritura".

Para más información acerca de los precios de Azure Blob Storage en general, consulte la [página de precios de Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Cuando activa inicialmente la eliminación temporal, se recomienda usar un período de retención pequeño para comprender mejor cómo afecta la característica a la facturación.

## <a name="quickstart"></a>Guía de inicio rápido

### <a name="azure-portal"></a>Azure Portal
Para habilitar la eliminación temporal, vaya a la opción **Eliminación temporal** de **Blob Service**. Luego, haga clic en **Habilitado** y escriba el número de días que desea conservar los datos que se han eliminado temporalmente.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Para ver los blobs eliminados temporalmente, active la casilla **Mostrar blobs eliminados**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Para ver instantáneas eliminadas temporalmente de un blob dado, seleccione el blob y haga clic en **Ver instantáneas**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Asegúrese de que la casilla **Mostrar instantáneas eliminadas** está activada.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Al hacer clic en un blob o en una instantánea eliminados temporalmente, observe las nuevas propiedades del blob. Indican cuándo se eliminó el objeto y el número de días que quedan hasta que el blob o la instantánea de blob expire de forma permanente. Si el objeto que se ha eliminado temporalmente no es una instantánea, también tendrá la opción de recuperarlo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Recuerde que al recuperar un blob también se recuperan todas las instantáneas asociadas. Para recuperar las instantáneas eliminadas temporalmente de un blob activo, haga clic en el blob y seleccione **Undelete all snapshots** (Recuperar todas las instantáneas).

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Después de recuperar las instantáneas de un blob, puede hacer clic en **Promover** para copiar una instantánea en el blob raíz, con lo que se restaura el blob en la instantánea.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell
Para habilitar la eliminación temporal, actualice las propiedades del servicio del cliente del blob. En el ejemplo siguiente se habilita la eliminación temporal en un subconjunto de cuentas de una suscripción:

```powershell
Set-AzureRmContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzureRMStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzureStorageDeleteRetentionPolicy -RetentionDays 7
```

Para recuperar los blobs que se eliminaron accidentalmente, llame a Undelete en ellos. Recuerde que al llamar a **Undelete Blob**, tanto en los blobs activos como en los eliminados temporalmente, restaurará todas las instantáneas asociadas eliminadas temporalmente como activas. En el ejemplo siguiente se llama a Undelete en todos los blobs activos y eliminados temporalmente de un contenedor:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzureStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
### <a name="azure-cli"></a>Azure CLI 
Para habilitar la eliminación temporal, actualice las propiedades del servicio del cliente del blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Para comprobar si la opción eliminación temporal está activada, use el siguiente comando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Biblioteca de cliente de Python

Para habilitar la eliminación temporal, actualice las propiedades del servicio del cliente del blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Biblioteca de cliente de .Net

Para habilitar la eliminación temporal, actualice las propiedades del servicio del cliente del blob:

```csharp
// Get the blob client’s service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client’s service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Para recuperar los blobs que se eliminaron accidentalmente, llame a Undelete en ellos.
Recuerde que al llamar a **Undelete Blob**, tanto en los blobs activos como en los eliminados temporalmente, restaurará todas las instantáneas asociadas eliminadas temporalmente como activas. En el ejemplo siguiente se llama a Undelete en todos los blobs activos y eliminados temporalmente de un contenedor:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para recuperar hasta una versión específica del blob, primero llame a Undelete en un blob y, después, copie la instantánea deseada sobre el blob. En el ejemplo siguiente se recupera un blob en bloques a su última instantánea generada:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

## <a name="should-i-use-soft-delete"></a>¿Debo usar la eliminación temporal?

Si existe la posibilidad de que una aplicación u otro usuario de la cuenta de almacenamiento modifiquen o borren sus datos, es aconsejable activar la eliminación temporal.
La eliminación temporal forma parte de una estrategia de protección de datos y puede ayudarle a evitar la pérdida accidental de datos.

## <a name="faq"></a>Preguntas más frecuentes

**¿Para qué tipos de almacenamiento se puede usar la eliminación temporal?**

Actualmente, la eliminación temporal solo está disponible para el almacenamiento de blobs (objeto).

**¿Está disponible la eliminación temporal para todos los tipos de cuentas de almacenamiento?**

Sí, la eliminación temporal no solo está disponible para las cuentas de almacenamiento de blobs, sino también para los blobs de las cuentas de uso general (tanto GPV1 como GPv2). Esto se aplica tanto a las cuentas estándar como a las premium. La eliminación temporal no está disponible para los discos administrados.

**¿Está disponible la eliminación temporal para todas las capas de almacenamiento?**

Sí, la eliminación temporal está disponible para todas las capas de almacenamiento, es decir, la de acceso frecuente, la de acceso esporádico y la de archivo. Sin embargo, la eliminación temporal no permite la protección contra la sobrescritura en los blobs de la capa de archivo.

**¿Se puede usar la API de Set Blob Tier para almacenar los blobs por niveles con instantáneas eliminadas temporalmente?**

Sí. Las instantáneas eliminadas temporalmente permanecerán en el nivel original, pero el blob base se moverá al nivel nuevo. 

**Las cuentas de almacenamiento Premium tienen un límite de instantáneas de blob de 100. ¿Cuentan las instantáneas eliminadas temporalmente para este límite?**

No, no cuentan.

**¿Se puede activar la eliminación temporal en cuentas de almacenamiento existentes?**

Sí, la eliminación temporal se puede configurar tanto para las cuentas de almacenamiento nuevas como las existentes.

**Si elimino toda una cuenta o un contenedor con la eliminación temporal activada, ¿se guardarán todos los blobs asociados?**

No, si elimina toda una cuenta o un contenedor, todos los blobs asociados se eliminarán permanentemente. Para aprender a proteger una cuenta de almacenamiento contra eliminaciones accidentales, consulte el artículo de Azure Resource Manager [Bloqueo de recursos para impedir cambios inesperados](../../azure-resource-manager/resource-group-lock-resources.md).

**¿Puedo ver las métricas de capacidad de los datos eliminados?**

Los datos eliminados temporalmente se incluyen como parte de la capacidad total de la cuenta de almacenamiento. Para más información acerca del seguimiento y la supervisión de la capacidad de almacenamiento, consulte el artículo [Storage Analytics](../common/storage-analytics.md).

**¿Se puede acceder a los datos eliminados temporalmente aunque se desactive la eliminación temporal?**

Sí, aunque la eliminación temporal esté desactivada podrá acceder a los datos eliminados temporalmente que no hayan expirado y recuperarlos.

**¿Puedo leer y copiar instantáneas eliminadas temporalmente de mi blob?**

Sí, pero antes debe llamar a Undelete en el blob.

**¿Está disponible la eliminación temporal para todos los tipos de blob?**

Sí, la eliminación temporal está disponible para los blobs en bloques, blobs de anexación y blobs en páginas.

**¿Está la eliminación temporal disponible para los discos de máquina virtual?**

La eliminación temporal está disponible tanto para los discos no administrados Estándar como para los Premium. La eliminación temporal sólo le ayudará a recuperar los datos eliminados por **Delete Blob**, **Put Blob**, **Put Block List**, **Put Block** y **Copy Blob**. Los datos que se sobrescriben con una llamada a **Put Page** no se pueden recuperar.

**¿Es preciso cambiar las aplicaciones existentes para usar la eliminación temporal?**

Se puede sacar provecho de la eliminación temporal independientemente de la versión de API que se use. Sin embargo, para enumerar y recuperar blobs e instantáneas de blob eliminados temporalmente, será preciso usar la versión del 29-07-2017 de [API REST de Storage Services](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services), o cualquier versión superior. En general, siempre se recomienda utilizar la versión más reciente, independientemente de que se use esta característica.

## <a name="next-steps"></a>Pasos siguientes

* [Código de ejemplo de .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)

* [Blob Service REST API](/rest/api/storageservices/fileservices/blob-service-rest-api) (API de REST de Blob service)

* [Replicación de Azure Storage](../common/storage-redundancy.md)

* [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

* [Qué hacer si se produce una interrupción del servicio Azure Storage](../common/storage-disaster-recovery-guidance.md)
