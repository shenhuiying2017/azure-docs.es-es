---
title: archivo de inclusión
description: archivo de inclusión
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 203f5a766c4c8a8f1e577f6be1e18d0f9ac95403
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
Azure Blob Storage es la solución de almacenamiento de objetos de Microsoft para la nube. Blob Storage está optimizado para el almacenamiento de cantidades masivas de datos no estructurados, como texto o datos binarios.

Blob Storage resulta muy conveniente para:

* Servicio de imágenes o documentos directamente a un explorador.
* Almacenamiento de archivos para acceso distribuido.
* Streaming de audio y vídeo.
* Escribir en archivos de registro.
* Almacenamiento de datos para copia de seguridad y restauración, recuperación ante desastres y archivado.
* Almacenamiento de datos para el análisis en local o en un servicio hospedado de Azure.

Se puede acceder a los objetos de Blob Storage desde cualquier lugar del mundo a través de HTTP o HTTPS. Los usuarios o las aplicaciones cliente pueden acceder a los blobs mediante direcciones URL, la [API REST de Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), la [CLI de Azure](https://docs.microsoft.com/cli/azure/storage) o una biblioteca de cliente de Azure Storage. Las bibliotecas de cliente de almacenamiento están disponibles para varios lenguajes, como [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/) y [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Conceptos de Blob service

Blob Storage expone tres recursos: la cuenta de almacenamiento, los contenedores de la cuenta y los blobs de un contenedor. En el siguiente diagrama se muestra la relación entre estos recursos.

![Diagrama de arquitectura de Blob Storage (objeto)](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Cuenta de almacenamiento

Todo el acceso a objetos de datos en Azure Storage tiene lugar a través de una cuenta de almacenamiento. Para más información, consulte [Acerca de las cuentas de almacenamiento de Azure](../articles/storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Contenedor

Un contenedor organiza un conjunto de blobs, de forma parecida a una carpeta en un sistema de archivos. Todos los blobs residen dentro de un contenedor. Una cuenta de almacenamiento puede contener un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs. Tenga en cuenta que el nombre del contenedor debe estar en minúsculas.

### <a name="blob"></a>Blob
 
Azure Storage ofrece tres tipos de blobs: blobs en bloques, blobs en anexos y [blobs en páginas](../articles/storage/blobs/storage-blob-pageblob-overview.md) (usados para archivos VHD).

* Los blobs en bloques almacenan texto y datos binarios, hasta unos 4,7 TB. Los blobs en bloques se componen de bloques de datos que se pueden administrar de forma individual.
* Los blobs en anexos constan de bloques, como los blobs en bloques, pero están optimizados para operaciones de anexión. Los blobs en anexos resultan muy convenientes para escenarios como el registro de datos de máquinas virtuales.
* Los blobs en páginas almacenan archivos de acceso aleatorio con un tamaño de hasta 8 TB. Los blobs en páginas almacenan los archivos de disco duro virtual que respaldan las máquinas virtuales.

Todos los blobs residen dentro de un contenedor. Un contenedor es similar a una carpeta de un sistema de archivos. Puede organizar además los blobs en directorios virtuales y recorrerlos igual que haría con un sistema de archivos. 

Para conjuntos de datos de gran tamaño donde las restricciones de red impiden la carga o descarga de datos en Blob Storage a través de una conexión, puede enviar un conjunto de discos duros a Microsoft para importar o exportar datos directamente desde el centro de datos. Para más información, consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](../articles/storage/common/storage-import-export-service.md).
  
Para más información sobre la nomenclatura de contenedores y blobs, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
