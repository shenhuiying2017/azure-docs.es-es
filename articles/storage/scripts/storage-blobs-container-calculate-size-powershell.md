---
title: "Ejemplo de script de Azure PowerShell: calcular el tamaño del contenedor de blobs | Microsoft Docs"
description: "Calcule el tamaño de un contenedor en Azure Blob Storage sumando el tamaño de cada uno de sus blobs."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Cálculo del tamaño de un contenedor de Blob Storage

Este script calcula el tamaño de un contenedor en Azure Blob Storage sumando el tamaño de los blobs de dicho contenedor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>Descripción del tamaño de un contenedor de Blob Storage

El tamaño total del contenedor de Blob Storage incluye el tamaño del contenedor y el tamaño de todos los blobs del contenedor.

A continuación se describe cómo se calcula la capacidad de almacenamiento de los blobs y los contenedores de blobs. De aquí en adelante, Len(X) hace referencia al número de caracteres de la cadena.

### <a name="blob-containers"></a>Contenedores de blobs

Más abajo se muestra cómo calcular la cantidad de almacenamiento usado por contenedor de blobs:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Aquí se explica con detalle:
* En los 48 bytes de sobrecarga para cada contenedor se incluye la hora de la última modificación, los permisos, la configuración públicos y algunos metadatos del sistema.
* El nombre del contenedor se almacena como Unicode, por lo que se toma el número de caracteres y se multiplica por 2.
* Para cada elemento de metadatos almacenado del contenedor de blobs, se almacena la longitud del nombre (guardado como ASCII), así como la longitud del valor de cadena.
* En los 512 bytes por identificador firmado se incluyen el nombre del identificador firmado, la hora de inicio, la hora de expiración y los permisos.

### <a name="blobs"></a>Blobs

Más abajo se muestra cómo calcular la cantidad de almacenamiento usado por blob:

* Blob en bloques (blob base o instantánea)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* Blob en páginas (blob base o instantánea)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

Aquí se explica con detalle:

* En los 124 bytes de sobrecarga de blob, se incluyen la hora de la última modificación, el tamaño, el control de caché, el tipo de contenido, el idioma del contenido, la codificación del contenido, el MD5 del contenido, los permisos, la información de la instantánea, la concesión y algunos metadatos del sistema.
* El nombre del blob se almacena como Unicode, por lo que se toma el número de caracteres y se multiplica por 2.
* Después, para cada elemento de metadatos almacenado, se indica la longitud del nombre (guardado como ASCII), así como la longitud del valor de cadena.
* Para los blobs en bloques:
    * 8 bytes para la lista de bloques.
    * El número de bloques multiplicado por el tamaño del identificador de bloque en bytes.
    * Además, el tamaño de los datos de todos los bloques confirmados y sin confirmar. Tenga en cuenta que, cuando se usan instantáneas, este tamaño solo incluye los datos únicos de este blob base o de instantánea. Si los bloques sin confirmar no se han usado al cabo de una semana, se recopilarán y, a partir de ese momento, ya no contarán para la facturación.
* Para los blobs en páginas:
    * El número de intervalos de páginas no consecutivas con datos multiplicado por 12 bytes. Este es el número de intervalos de páginas únicas que se ven al llamar a la API GetPageRanges.
    * Además, el tamaño de los datos en bytes de todas las páginas almacenadas. Tenga en cuenta que, cuando se usan instantáneas, este tamaño solo incluye las páginas únicas del blob base o de instantánea que se cuenta.

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la facturación de Azure Storage, vea [Understanding Windows Azure Storage Billing](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/) (Descripción de la facturación de Microsoft Azure Storage).

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Puede encontrar ejemplos de script adicionales de PowerShell de almacenamiento en los [ejemplos de PowerShell para Azure Storage](../blobs/storage-samples-blobs-powershell.md).
