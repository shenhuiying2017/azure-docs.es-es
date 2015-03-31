<properties 
	pageTitle="Crear una instantánea de un blob" 
	description="Guía para crear instantáneas de blob de almacenamiento de Azure" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Crear una instantánea de un blob

Puede crear una instantánea de un blob. Una instantánea es una versión de solo lectura de un blob que se ha realizado en un momento dado. Una vez se crea la instantánea, puede leerla, copiarla o eliminarla, pero no modificarla. Las instantáneas le ofrecen una oportunidad de realizar una copia de seguridad de un blob en el momento en que éste aparezca.

Las instantáneas de un blob tienen el mismo nombre que el blob de base del cual se ha realizado la instantánea, con un valor DateTime anexado para indicar el momento en el que se realizó la instantánea. Por ejemplo, si el URI de blob en páginas es http://storagesample.core.blob.windows.net/mydrives/myvhd, el URI de instantánea será similar a http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z. Este valor se puede utilizar para hacer referencia a la instantánea para otras operaciones. Las instantáneas de un blob comparten el URI y se distinguen solo por el valor DateTime. En el código de biblioteca de cliente, la propiedad Snapshot del blob devuelve un valor DateTime que identifica de forma única la instantánea en relación con su blob base. Puede usar este valor para realizar otras operaciones en la instantánea.

Un blob puede tener cualquier número de instantáneas. Las instantáneas se conservan hasta que se eliminan explícitamente. Una instantánea no puede durar más tiempo que su blob de origen. Puede enumerar las instantáneas asociadas al blob para llevar un seguimiento de las instantáneas actuales.

## Heredar propiedades

Cuando se crea una instantánea de un blob, las propiedades del sistema se copian en la instantánea con los mismos valores. Esta lista muestra las propiedades del sistema que se han copiado para la biblioteca cliente de almacenamiento en .NET:

- ContentType 

- ContentEncoding 

- ContentLanguage

- Length

- CacheControl

- ContentMd5 


En la instantánea no se copian las concesiones asociadas con el blob base. Las instantáneas no se pueden conceder.

## Copiar instantáneas 

Las operaciones de copia con blobs e instantáneas siguen estas reglas:

- Puede copiar una instantánea sobre su blob base. Si se mueve una instantánea a la posición del blob, puede restaurar una versión anterior de un blob. La instantánea se conserva, pero el origen se sobrescribe con una copia que se puede leer y escribir.

- Puede copiar una instantánea en un blob de destino con un nombre diferente. El blob resultante de destino es un blob que se puede escribir y no una instantánea.

- Cuando se copia un blob de origen, las instantáneas del blob de origen no se copian en el destino. Cuando un blob de destino se sobrescribe con una copia, las instantáneas asociadas al blob de destino bajo su nombre no se modifican. 

- Cuando se crea una instantánea de un blob en bloques, la lista de bloques confirmados del blob también se copia en la instantánea. No se copiarán los bloques sin confirmar.

## Especificar una condición de acceso 

Puede especificar una condición de acceso de forma que la instantánea se cree solo si se cumple una condición. Para especificar una condición de acceso, use la propiedad AccessCondition. Si la condición especificada no se cumple, la instantánea no se creará y el servicio Blob devuelve el código de estado HTTPStatusCode.PreconditionFailed.

## Eliminar instantáneas 

No se puede eliminar un blob con instantáneas a menos que las instantáneas también se eliminen. Puede eliminar las instantáneas individualmente o bien, indicar al servicio de almacenamiento que elimine todas las instantáneas cuando elimine el blob de origen. Si intenta eliminar un blob que todavía tiene instantáneas, la llamada devolverá un error.

## Instantáneas con almacenamiento Premium
Las instantáneas con almacenamiento Premium, se atienen a las siguientes reglas:

- El número de instantáneas por blob en páginas está limitado a 100 en cuentas de almacenamiento Premium. Si se supera ese límite, la operación de instantánea de blob devuelve el código de error 409 (SnapshotCountExceeded).

- Cada diez minutos, se podrá tomar una sola instantánea de un blob en páginas en una cuenta de almacenamiento Premium. Si se supera ese límite, la operación de instantánea de blob devuelve el código de error 409 (SnaphotOperationRateExceeded).

- No se puede leer la instantánea de un blob en páginas en una cuenta de almacenamiento Premium a través de Get Blob. Si llama a Get Blob en una instantánea de una cuenta de almacenamiento Premium, éste devolverá el código de error 400 (operación no válida). Sin embargo, puede llamar a Get Blob Properties y Get Blob Metadata en una instantánea.

- Para leer una instantánea, puede utilizar la operación Copy Blob para copiar una instantánea en otro blob en páginas de la cuenta. El blob de destino para la operación de copia no debe contener ninguna instantánea ya existente. Si el blob de destino tiene instantáneas, Copy Blob devuelve el código de error 409 (SnapshotsPresent).

## Construir el URI absoluto para una instantánea 

Este ejemplo de código generará el URI absoluto de una instantánea a partir de su objeto de blob de base.

C#

	var snapshot = blob.CreateSnapshot();
	var uri = Microsoft.WindowsAzure.StorageClient.Protocol.BlobRequest.Get
    (snapshot.Uri, 
    0, 
    snapshot.SnapshotTime.Value, 
    null).Address.AbsoluteUri;

<!--HONumber=47-->
