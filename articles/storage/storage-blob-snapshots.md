---
title: "Crear una instantánea de solo lectura de un blob | Microsoft Docs"
description: "Aprenda a crear una instantánea de un blob para hacer una copia de seguridad de los datos de blob en un momento determinado. Comprenda cómo se facturan las instantáneas y cómo usarlas para minimizar las cargas de capacidad."
services: storage
documentationcenter: 
author: tamram
manager: carmonm
editor: tysonn
ms.assetid: 3710705d-e127-4b01-8d0f-29853fb06d0d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 664f03a8492178daf342b659595f035b7cccec5a


---
# <a name="create-a-blob-snapshot"></a>Creación de una instantánea de un blob
## <a name="overview"></a>Información general
Una instantánea es una versión de solo lectura de un blob que se ha realizado en un momento dado. Las instantáneas son útiles para realizar copias de seguridad de blobs. Después de crear una instantánea, puede leerla, copiarla o eliminarla, pero no puede modificarla.

Una instantánea de un blob es idéntica a su blob base, salvo que el identificador URI tiene un valor **DateTime** anexado al URI del blob para indicar el momento en que se tomó la instantánea. Por ejemplo, si el identificador URI de blob en páginas es `http://storagesample.core.blob.windows.net/mydrives/myvhd`, el identificador URI de instantánea es similar a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. 

> [!NOTE]
> Todas las instantáneas comparten el identificador URI del blob base. La única distinción entre el blob base y la instantánea es el valor **DateTime** anexado.
> 
> 

Un blob puede tener cualquier número de instantáneas. Las instantáneas se conservan hasta que se eliminen explícitamente. Una instantánea no puede durar más que su blob base. Puede enumerar las instantáneas asociadas al blob base para llevar un seguimiento de las instantáneas actuales.

Cuando se crea una instantánea de un blob, las propiedades del sistema se copian en la instantánea con los mismos valores. Los metadatos del blob base también se copian en la instantánea, a menos que especifique metadatos independientes para la instantánea al crearla.

Las concesiones asociadas con el blob base no afectan la instantánea. No puede adquirir una concesión en una instantánea.

## <a name="create-a-snapshot"></a>Crear una instantánea
En el ejemplo de código siguiente se muestra cómo crear una instantánea en .NET. Este ejemplo especifica metadatos independientes para la instantánea cuando se crea.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="copy-snapshots"></a>Copiar instantáneas
Las operaciones de copia con blobs e instantáneas siguen estas reglas:

* Puede copiar una instantánea sobre su blob base. Si se mueve una instantánea a la posición del blob, puede restaurar una versión anterior de un blob. La instantánea se conserva, pero el blob base se sobrescribe con una copia que se puede escribir de la instantánea.
* Puede copiar una instantánea en un blob de destino con un nombre diferente. El blob resultante de destino es un blob que se puede escribir y no una instantánea.
* Cuando se copia un blob de origen, las instantáneas del blob de origen no se copian en el destino. Cuando un blob de destino se sobrescribe con una copia, las instantáneas asociadas al blob de destino original no se modifican.
* Cuando se crea una instantánea de un blob en bloques, la lista de bloques confirmados del blob también se copia en la instantánea. No se copiarán los bloques sin confirmar.

## <a name="specify-an-access-condition"></a>Especificar una condición de acceso
Puede especificar una condición de acceso de forma que la instantánea se cree solo si se cumple una condición. Para especificar una condición de acceso, use la propiedad **AccessCondition** . Si la condición especificada no se cumple, la instantánea no se creará y el servicio Blob devuelve el código de estado HTTPStatusCode.PreconditionFailed.

## <a name="delete-snapshots"></a>Eliminar instantáneas
No se puede eliminar un blob con instantáneas a menos que estas también se eliminen. Puede eliminar una instantánea individualmente o especificar que se eliminen todas las instantáneas cuando se elimina el blob de origen. Si intenta eliminar un blob que todavía tiene instantáneas, se producirá un error.

En el ejemplo de código siguiente se muestra cómo eliminar un blob y sus instantáneas en. NET, donde `blockBlob` es una variable de tipo **CloudBlockBlob**:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Instantáneas con Almacenamiento Premium de Azure
Las instantáneas con almacenamiento Premium, se atienen a las siguientes reglas:

* El número máximo de instantáneas por blob en páginas está limitado a 100 en una cuenta de almacenamiento premium. Si se supera ese límite, la operación de instantánea de blob devuelve el código de error 409 (**SnapshotCountExceeded**).
* Puede tomar una instantánea de un blob en páginas en una cuenta de almacenamiento premium cada 10 minutos. Si se supera esa frecuencia, la operación de instantánea de blob devuelve el código de error 409 (**SnaphotOperationRateExceeded**).
* No se puede llamar a Get Blob para leer una instantánea de un blob en páginas en una cuenta de almacenamiento premium. Si llama a Get Blob en una instantánea de una cuenta de almacenamiento premium, este devuelve un código de error 400 (**InvalidOperation**). Sin embargo, puede llamar a Get Blob Properties y Get Blob Metadata en una instantánea en una cuenta de almacenamiento premium.
* Para leer una instantánea, puede utilizar la operación Copy Blob para copiar una instantánea en otro blob en páginas de la cuenta. El blob de destino para la operación de copia no debe contener ninguna instantánea ya existente. Si el blob de destino tiene instantáneas, Copy Blob devuelve el código de error 409 (**SnapshotsPresent**).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Devolver el URI absoluto para una instantánea
Este ejemplo de código de C# crea una instantánea y escribe el identificador URI absoluto de la ubicación principal.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Descripción de cómo las instantáneas pueden incrementar los costes
Crear una instantánea, que es una copia de solo lectura de un blob, puede conllevar cargos adicionales de almacenamiento de datos en la cuenta. Cuando se diseñe la aplicación, es importante tener en cuenta cómo se pueden contraer gastos al objeto de minimizar los costes innecesarios.

### <a name="important-billing-considerations"></a>Consideraciones importantes sobre facturación
La lista siguiente incluye los puntos clave que hay que tener en cuenta a la hora de crear una instantánea.

* La cuenta de almacenamiento genera cargos para páginas o bloques únicos, bien se encuentren en el blob o en la instantánea. La cuenta no generará gastos adicionales para instantáneas asociadas a un blob hasta que actualice el blob en el que se basan. Después de actualizar el blob base, discrepa de sus instantáneas. Cuando esto sucede, se le cobra por las páginas o bloques únicos en cada blob o instantánea.
* Al reemplazar un bloque en un blob en bloques, ese bloque será considerado como un bloque único a la hora de aplicar cargos. Esto es así incluso aunque el bloque tenga el mismo identificador de bloque y los mismos datos que tiene en la instantánea. Cuando se vuelva a confirmar el bloque, divergirá de su equivalente en cualquier instantánea y se aplicarán cargos por sus datos. Lo mismo sucede con una página en un blob en páginas que se hubiera actualizado con datos idénticos.
* Si se reemplaza un blob en bloques llamando a los métodos **UploadFile**, **UploadText**, **UploadStream** o **UploadByteArray**, se reemplazan todos los bloques del blob. Si una instantánea está asociada a ese blob, todos los bloques del blob y la instantánea de base divergen ahora y se aplicarán cargos por todos los bloques en ambos blobs. Esto es así incluso si los datos del blob e instantánea de base siguen siendo idénticos.
* El servicio Blob de Azure no dispone de medios para determinar si dos bloques contienen datos idénticos. Cada bloque que se carga y confirma se trata como único, incluso si tiene los mismos datos y el mismo identificador de bloque. Dado que las cargas se incrementan con los bloques únicos, es importante considerar que cuando se actualiza un blob con una instantánea se generan bloques únicos adicionales y se aplican cargos.

> [!NOTE]
> Los procesos recomendados sugieren administrar las instantáneas con cuidado para evitar recargos. Se recomienda que administre las instantáneas de la siguiente manera:
> 
> * Elimine y vuelva a crear las instantáneas asociadas a un blob siempre que lo actualice, incluso si lo hace con datos idénticos, a menos que el diseño de la aplicación requiera que se conserven las instantáneas. Si elimina y vuelve a crear las instantáneas del blob, podrá estar seguro de que el blob y las instantáneas no van a divergir.
> * Si está realizando el mantenimiento de las instantáneas de blob, evite llamar a **UploadFile**, **UploadText**, **UploadStream** o **UploadByteArray** para actualizar el blob. Esos métodos reemplazan todos los bloques del blob, por lo que el blob base y las instantáneas divergen significativamente. En su lugar, actualice el menor número posible de bloques mediante los métodos **PutBlock** y **PutBlockList**.
> 
> 

### <a name="snapshot-billing-scenarios"></a>Escenarios de facturación de instantáneas
En las siguientes situaciones, se muestra cómo se ven incrementados los cargos para un blob en bloques y sus instantáneas.

En el escenario 1, el blob de base no se ha actualizado después de realizarse la instantánea, así que se aplicarán cargos únicamente para los bloques 1, 2 y 3.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

En el escenario 2, el blob de base está actualizado, pero no así la instantánea. Se ha actualizado el bloque 3 y, aunque contiene los mismos datos y el mismo identificador, no es igual que el bloque 3 de la instantánea. Como resultado, se aplicarán cargos a la cuenta por cuatro bloques.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

En el escenario 3, el blob de base está actualizado, pero no así la instantánea. El bloque 3 se ha reemplazado por el bloque 4 en el blob de base, pero la instantánea sigue reflejando el bloque 3. Como resultado, se aplicarán cargos a la cuenta por cuatro bloques.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

En la situación 4, el blob de base se ha actualizado totalmente y no contiene ninguno de los bloques originales. Como resultado, se aplicarán cargos a la cuenta por la totalidad de los ocho bloques únicos. Esta situación se puede dar si usa un método de actualización como **UploadFile**, **UploadText**, **UploadFromStream** o **UploadByteArray**, ya que estos métodos reemplazan todos los contenidos de un blob.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Pasos siguientes
Para obtener ejemplos adicionales mediante Almacenamiento de blobs, consulte [Ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Puede descargar una aplicación de ejemplo y ejecutarla, así como ver el código en GitHub. 




<!--HONumber=Nov16_HO3-->


