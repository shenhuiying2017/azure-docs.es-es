---
title: "Característica única de blobs en páginas de Azure | Microsoft Docs"
description: "Información general acerca de blobs en páginas de Azure, ventajas y casos de uso con scripts de ejemplo."
services: storage
documentationcenter: 
author: anasouma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: wielriac
ms.openlocfilehash: 019b793f6d2b4cb70514d867b78c9501240baeda
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="unique-features-of-azure-page-blobs"></a>Características únicas de blobs en páginas de Azure

Azure Storage ofrece tres tipos de Blob Storage: blobs en bloques, blobs en anexos y blobs en páginas. Blobs en bloques está compuesto por bloques y son ideales para almacenar archivos de texto o binarios, así como para cargar archivos grandes de forma eficaz. Blobs en anexos también está compuesto por bloques, pero están optimizados para la anexión de operaciones, lo que resulta ideal para escenarios de registro. Blobs en páginas está compuesto por páginas de 512 bytes de hasta 8 TB en total y son más eficaces para operaciones aleatorias y frecuentes de lectura/escritura. Blobs en páginas constituye la base de los discos de IaaS de Azure. Este artículo se centra en explicar las características y ventajas de blobs de páginas.

## <a name="overview"></a>Información general
Los blobs en páginas son una colección de páginas de 512 bytes, que proporcionan la capacidad de leer y escribir intervalos arbitrarios de bytes. Por lo tanto, los blobs en páginas son ideales para almacenar estructuras de datos esparcidos y basadas en índices, como discos de datos y de sistema operativo para máquinas virtuales y bases de datos. Por ejemplo, Azure SQL DB usa blobs en páginas como almacenamiento persistente subyacente para sus bases de datos. Además, blobs en páginas suele usarse para archivos con actualizaciones basadas en intervalos.  

Las características clave de blobs en páginas de Azure son la interfaz REST, la durabilidad para el almacenamiento subyacente y las funcionalidades de migración sin problemas a Azure. En la sección siguiente, analizaremos detalladamente cada modelo. Además, los blobs en páginas de Azure se admiten actualmente en dos tipos de almacenamiento: Premium Storage y Standard Storage. La opción Premium Storage está diseñada específicamente para cargas de trabajo que requieren alto rendimiento coherente y baja latencia, lo que hace que los blobs en páginas premium sean perfectos para bases de datos de almacenamiento de datos de alto rendimiento.  La opción Standard Storage es más rentable para cargas de trabajo que se ejecutan sin tener en cuenta la latencia.

## <a name="sample-use-cases"></a>Casos de uso de ejemplo

Hablemos sobre un par de casos de uso de los blobs en páginas, comenzando con los discos de IaaS de Azure. Los blobs en páginas de Azure son la columna vertebral de la plataforma virtual de discos de IaaS de Azure. Tanto los discos de datos como el sistema operativo de Azure se implementan como discos virtuales donde los datos se conservan de forma duradera en la plataforma de Azure Storage y, después, se entregan a las máquinas virtuales para obtener el máximo rendimiento. Los discos de Azure se conservan en [formato VHD](https://technet.microsoft.com/library/dd979539.aspx) Hyper-V y se almacenan como un [Blob en páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) en Azure Storage. Además de usar discos virtuales para máquinas virtuales de IaaS de Azure, Blobs en páginas también permite escenarios de PaaS y DBaaS como servicio de Azure SQL DB, que actualmente usa blobs en páginas para almacenar datos SQL, lo que permite operaciones de lectura y escritura aleatorias y rápidas para la base de datos. Otro ejemplo sería si dispone de un servicio PaaS para acceder a elementos multimedia compartidos para aplicaciones colaborativas de edición de vídeo, los blobs en páginas habilitan un acceso rápido a ubicaciones aleatorias en los elementos multimedia. También permite una edición y combinación rápida y eficaz de dichos elementos multimedia por parte de varios usuarios. 

Servicios de Microsoft de primera entidad, como Azure Site Recovery o Azure Backup, así como muchos terceros desarrolladores de aplicaciones han implementado las innovaciones líderes en el sector mediante el uso de la interfaz de REST de blobs en páginas. A continuación se incluyen algunos de los escenarios únicos implementados en Azure: 
* Administración de instantáneas incrementales orientadas a la aplicación: las aplicaciones pueden aprovechar las API de REST y las instantáneas de blobs en páginas para guardar los puntos de control de las aplicaciones sin incurrir en costosas duplicaciones de datos. Esto es posible porque se admiten las instantáneas locales para blobs en páginas, que no requieren copiar todo el conjunto de datos. Estas API de instantáneas públicas también permiten acceder y copiar las diferencias entre instantáneas.
* Migración en vivo de la aplicación y de los datos locales a la nube: copie los datos locales y use las API de REST para escribir directamente en blobs en páginas de Azure mientras la máquina virtual local continúa ejecutándose. Una vez alcanzado el objetivo, puede conmutar por error rápidamente a la máquina virtual de Azure con esos datos. Esto permite la migración de las máquinas virtuales y de los discos virtuales de un ambiente local a la nube con un tiempo de inactividad mínimo, ya que la migración de datos se realiza en segundo plano mientras se sigue usando la máquina virtual y el tiempo de inactividad necesario para la conmutación por error es reducido (unos minutos).
* El acceso compartido [basado en SAS](../common/storage-dotnet-shared-access-signature-part-1.md) permite escenarios como varios lectores y un único escritor, y admite el control de simultaneidad.

## <a name="page-blob-features"></a>Características de blobs en páginas

### <a name="rest-api"></a>API DE REST
Consulte el documento siguiente para empezar a [desarrollar con Blobs en páginas](storage-dotnet-how-to-use-blobs.md). Por ejemplo, veamos cómo obtener acceso a Blobs en páginas mediante el uso de la biblioteca del cliente de almacenamiento para. NET. 

En el siguiente diagrama se describen las relaciones globales entre la cuenta, los contenedores y los blobs en páginas.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-certain-size"></a>Crear un blob en páginas vacío y de un tamaño determinado
Para crear un blob en páginas, primero debe crear un objeto CloudBlobClient, con el URI base para acceder al almacenamiento de blobs para la cuenta de almacenamiento (pbaccount en la figura 1) junto con el objeto de StorageCredentialsAccountAndKey tal y como se muestra a continuación.  El ejemplo muestra cómo crear una referencia a un objeto CloudBlobContainer y, después, cómo crear el contenedor (testvhds) si aún no existe.  A continuación, con el objeto CloudBlobContainer, podemos crear una referencia a un objeto CloudPageBlob especificando el nombre de blob en página (os4.vhd) al que se quiere acceder. Luego, para crear el blob en página, llamamos a [CloudPageBlob.Create](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) pasando el tamaño máximo para el blob que queremos crear.  blobSize debe ser un múltiplo de 512 bytes.

```csharp
using Microsoft.WindowsAzure.StorageClient;
long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Cambiar el tamaño de un Blob en páginas
Para cambiar el tamaño de un Blob en páginas después de crearlo, utilice [Resize](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) API. El tamaño solicitado debe ser un múltiplo de 512 bytes.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Escritura de páginas en un Blob en páginas
Para escribir páginas, use el método [CloudPageBlob.WritePages](/library/microsoft.windowsazure.storageclient.cloudpageblob.writepages.aspx).  Esto le permitirá escribir un conjunto secuencial de páginas de hasta 4 MB. El desplazamiento que se está escribiendo debe comenzar en un límite de 512 bytes (startingOffset % 512 == 0), y terminar en un límite de 1 byte.  El código siguiente muestra un ejemplo de una llamada a WritePages para un objeto de blob al que se está accediendo:
```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Tan pronto como una solicitud de escritura para un conjunto secuencial de páginas se realiza correctamente en Blob service y se replica para su durabilidad y resistencia, la operación de escritura se confirma y se envía una notificación al cliente.  

En el siguiente diagrama se muestran dos operaciones de escritura independientes:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Una operación de escritura, que comienza en el desplazamiento 0 de 1024 bytes de longitud. 
2.  Una operación de escritura, que comienza en el desplazamiento 4096 de 1024 bytes de longitud. 

#### <a name="reading-pages-from-a-page-blob"></a>Leer páginas de un blob en páginas
Para leer las páginas, use el método [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) para leer un intervalo de bytes desde el blob en páginas. Esto permite descargar el blob completo o un intervalo de bytes a partir de cualquier posición de desplazamiento en el blob. Al leer, el desplazamiento no tiene que partir de un múltiplo de 512. Al leer bytes de una página NUL, el servicio devuelve cero bytes.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
En la siguiente ilustración se muestra una operación de lectura con BlobOffSet de 256 y rangeSize de 4352. Los datos devueltos se resaltan en color naranja. Se devuelven ceros para páginas NUL

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Si tiene un blob apenas lleno, es posible que quiera cargar solo las regiones de página válidas para evitar pagar la salida de cero bytes y reducir la latencia de descarga.  Para determinar qué páginas están respaldadas por datos, use [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). A continuación, puede enumerar los intervalos devueltos y descargar los datos de cada intervalo. 
```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the rangeSize
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // TODO: use the buffer for the page range just read
} 
```

#### <a name="leasing-a-page-blob"></a>Concesión de un blob en páginas
La operación de concesión de blob establece y administra un bloqueo en un blob para las operaciones de escritura y lectura. Esta operación es muy útil en escenarios donde se tiene acceso desde varios clientes a un blob en páginas para asegurarse de que un único cliente puede escribir en el blob a la vez. Discos de Azure, por ejemplo, aprovecha este mecanismo de concesión para asegurar que solo una máquina virtual está administrando el disco. La duración del bloqueo puede ser de 15 a 60 segundos, o puede ser infinita. Consulte la documentación [aquí](/rest/api/storageservices/lease-blob) para obtener más detalles.

> Utilice el siguiente vínculo para obtener [ejemplos de código](/resources/samples/?service=storage&term=blob&sort=0) para muchos otros escenarios de aplicación. 

Además de API de REST enriquecidas, Blob en páginas también proporciona acceso compartido, durabilidad y seguridad mejorada. Trataremos esos beneficios con más detalle en los párrafos siguientes. 

### <a name="concurrent-access"></a>Acceso simultáneo
La API de REST de blob en páginas y su mecanismo de concesión permite que las aplicaciones accedan al blob en páginas desde varios clientes. Por ejemplo, supongamos que necesita crear un servicio en la nube distribuido que comparte los objetos de almacenamiento con varios usuarios. Podría tratarse de una aplicación web que sirve una extensa colección de imágenes a varios usuarios. Una opción para esta implementación es usar una máquina virtual con discos conectados. Las desventajas de esto son (i) la restricción de que un disco solo puede adjuntarse a una única máquina virtual, y esto limita la escalabilidad, la flexibilidad y aumenta los riesgos. Si hay un problema con la máquina virtual o el servicio que se ejecuta en la máquina virtual, no se podrá acceder a la imagen, debido a la concesión, hasta que esta expire o se interrumpa. Otra desventaja es (ii) el coste adicional de tener una máquina virtual de IaaS. 

Una alternativa es usar Blobs en páginas directamente a través de la API de REST de Azure Storage. Esta opción elimina la necesidad de costosas máquinas virtuales de IaaS, ofrece total flexibilidad de acceso directo desde varios clientes, simplifica la administración de servicios mediante la eliminación de la necesidad de adjuntar o desasociar discos, y elimina el riesgo de problemas en la máquina virtual. Además, proporciona el mismo nivel de rendimiento para las operaciones de lectura y escritura aleatorias que un disco.

### <a name="durability-and-high-availability"></a>Durabilidad y alta disponibilidad
Las opciones Standard Storage y Premium Storage ofrecen almacenamiento duradero donde los datos del blob en páginas siempre se replican para asegurar su durabilidad y alta disponibilidad. Para más información acerca de la redundancia de Azure Storage, consulte esta [documentación](../common/storage-redundancy.md). Azure ha ofrecido de manera constante durabilidad de nivel empresarial para discos IaaS y blobs en páginas, con una [tasa de error anualizada del 0 %](https://en.wikipedia.org/wiki/Annualized_failure_rate). Es decir, Azure nunca perdió datos de un blob en páginas de ningún cliente. 

### <a name="seamless-migration-to-azure"></a>Migración fácil a Azure
Para los clientes y desarrolladores interesados en la implementación de su propia solución de copia de seguridad personalizada, Azure también ofrece instantáneas incrementales que solo contienen las diferencias. Esta característica evita el costo de la copia inicial completa, lo que reduce considerablemente el costo de copia de seguridad. Junto con la capacidad eficaz de lectura y copia de datos diferenciales, esta es otra eficaz funcionalidad que permite aún más innovaciones por parte de los desarrolladores, lo que conduce a una experiencia inmejorable de copia de seguridad y recuperación ante desastres en Azure. Puede configurar su propia copia de seguridad y recuperación ante desastres para máquinas virtuales en Azure mediante el uso de [Blob Snapshot](/rest/api/storageservices/snapshot-blob) API junto con [Get Page Ranges](/rest/api/storageservices/get-page-ranges) API y [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob) API, que puede usar para copiar fácilmente datos incrementales para recuperación ante desastres. 

Además, muchas empresas tienen cargas de trabajo críticas que se ejecutan en centros de datos locales. Para migrar la carga de trabajo a la nube, una de las principales preocupaciones sería el tiempo de inactividad necesario para copiar los datos y el riesgo de imprevistos tras el cambio. En muchos casos, el tiempo de inactividad puede ser un factor negativo para la migración a la nube. Con la API de REST de blobs en página, Azure resuelve este problema habilitando la migración a la nube con una interrupción mínima para las cargas de trabajo críticas. 

Para obtener ejemplos acerca de cómo realizar una instantánea y cómo restaurar un blob en páginas desde una instantánea, consulte el artículo acerca de la [configuración de un proceso de copia de seguridad con instantáneas incrementales](../../virtual-machines/windows/incremental-snapshots.md).


## <a name="summary"></a>Resumen
Azure se esfuerza en ofrecer la mejor experiencia para nuestros clientes. Creamos la plataforma de almacenamiento con durabilidad de datos de nivel empresarial, para que nuestros clientes pueden confiar datos críticos a Azure. Azure ofrece una experiencia de desarrollo y soporte técnico de API única para blobs en páginas, que ninguna otra plataforma en la nube pública puede proporcionar. Esto ha dado lugar a numerosas innovaciones de primeras entidades y terceros, como migraciones sin problemas a la nube, experiencias superiores de copia de seguridad y recuperación ante desastres, compatibilidad con PaaS y DBaaS, soluciones de almacenamiento distribuido y otras innovaciones para discos y máquinas virtuales de IaaS. En general, Azure destaca entre todas las plataformas de nube pública gracias a estas capacidades únicas, y los clientes de Azure disfrutan de estas ventajas que ninguna otra plataforma en la nube proporciona.
