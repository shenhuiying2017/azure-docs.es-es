---
title: 'Introducción a Blob Storage: almacenamiento de objetos en Azure | Microsoft Docs'
description: Azure Blob Storage está diseñado para almacenar grandes cantidades de datos de objetos no estructurados, como datos de texto o binarios. Las aplicaciones pueden acceder a objetos de Blob Storage desde PowerShell o la CLI de Azure, desde código mediante bibliotecas de cliente de Azure Storage o a través de REST.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: overview
ms.date: 03/27/2018
ms.author: tamram
ms.openlocfilehash: 0fff0032ec2452413bcd1df3175634b14a64208f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-blob-storage"></a>Introducción a Blob Storage

Azure Blob Storage es la solución de almacenamiento en la nube de Microsoft para objetos de datos. Blob Storage puede almacenar grandes cantidades de datos de objetos no estructurados, como datos de texto o binarios. Se puede obtener acceso a los datos de Blob Storage desde cualquier lugar del mundo a través de HTTP o HTTPS. Puede usar el almacenamiento de blobs para exponer datos públicamente o para almacenar datos de la aplicación de manera privada.

El almacenamiento de blobs suele usarse para realizar las siguientes tareas:

* Servicio de imágenes o documentos directamente a un explorador
* Almacenamiento de archivos para acceso distribuido
* Streaming de audio y vídeo
* Almacenamiento para copia de seguridad y restauración, recuperación ante desastres y archivado
* Almacenamiento de datos para el análisis en local o en un servicio hospedado de Azure
* Almacenamiento de discos duros virtuales para su uso con Azure Virtual Machines

## <a name="blob-service-concepts"></a>Conceptos de Blob service

Blob service contiene los componentes siguientes:

![Arquitectura de blob](./media/storage-blobs-introduction/blob1.png)

* **Cuenta de almacenamiento:** todo el acceso a Azure Storage se realiza a través de una cuenta de almacenamiento. Esta cuenta de almacenamiento puede ser una **cuenta de almacenamiento de uso general (v1 o v2)** o una **cuenta de almacenamiento de blobs**. Para más información, consulte [Acerca de las cuentas de Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* **Contenedor**: un contenedor proporciona una agrupación de un conjunto de blobs. Todos los blobs deben residir en un contenedor. Además, una cuenta puede disponer de un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs. Tenga en cuenta que el nombre del contenedor debe estar en minúsculas.

* **Blob:** archivo de cualquier tipo y tamaño. Azure Storage ofrece tres tipos de blobs: blobs en bloques, [blobs en páginas](storage-blob-pageblob-overview.md) y blobs en anexos.
  
    *blobs en bloques* son ideales para almacenar archivos binarios o de texto, como documentos y archivos multimedia. *blobs en anexos* se parecen a los blobs en bloques porque se componen de bloques, pero están optimizados para las operaciones de anexión, por lo que son útiles para escenarios de registro. Un único blob en bloques puede contener un máximo de 50 000 bloques de hasta 100 MB cada uno, hasta un tamaño total de algo más de 4,75 GB (100 MB × 50 000). Un único blob en anexos puede contener un máximo de 50 000 bloques de hasta 4 MB cada uno, hasta un tamaño total de algo más de 195 GB (4 MB × 50 000).
  
    Los *blobs en páginas* pueden tener un tamaño de hasta 8 TB y son más eficaces para operaciones frecuentes de lectura y escritura. Azure Virtual Machines usa blobs en páginas como discos de sistema operativo y de datos.
  
    Para más información sobre la nomenclatura de contenedores y blobs, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

## <a name="next-steps"></a>Pasos siguientes

* [crear una cuenta de almacenamiento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Introducción a Blob Storage con .NET](storage-dotnet-how-to-use-blobs.md)
* [Azure Storage samples using .NET](../common/storage-samples-dotnet.md) (Ejemplos de Azure Storage con .NET)
* [Azure Storage samples using Java](../common/storage-samples-java.md) (Ejemplos de Azure Storage con Java)
