---
title: "Movimiento de grandes cantidades de datos desde el almacenamiento en la nube y hacia él en Azure | Microsoft Docs"
description: "Información general sobre los distintos métodos de mover los datos hacia Azure Storage y desde él."
services: storage
documentationcenter: 
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: db0f09433750a3af2d70039d780a25ad64bb4df1
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2017
---
# <a name="moving-data-to-and-from-azure-storage"></a>Movimiento de datos hacia y desde Azure Storage
Si desea mover los datos locales a Azure Storage (o viceversa), hay varias maneras de hacerlo. El método que le resulte más adecuado dependerá de su escenario. Este artículo ofrece una breve descripción de diferentes escenarios y ofertas adecuadas para cada uno.

## <a name="building-applications"></a>Creación de aplicaciones
Si está creando una aplicación, desarrollando con la API de REST o con una de nuestras muchas bibliotecas de cliente es una excelente manera de mover datos hacia y desde Azure Storage.

Azure Storage proporciona bibliotecas de cliente enriquecidas para .NET, iOS, Java, Android, Plataforma universal de Windows (UWP), Xamarin, C++, Node.JS, PHP, Ruby y Python. Las bibliotecas de cliente ofrecen capacidades avanzadas, como lógica de reintentos, registro y cargas paralelas. También se puede desarrollar directamente en la API de REST, a la que se puede llamar con cualquier lenguaje que genere solicitudes HTTP/HTTPS.

Consulte [Introducción a Azure Blob Storage mediante .NET](../blobs/storage-dotnet-how-to-use-blobs.md) para más información.

Además, también ofrecemos [Data Movement Library de Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) que es una biblioteca diseñada para copia de datos de alto rendimiento en Azure y desde Azure. Consulte nuestra [documentación](https://github.com/Azure/azure-storage-net-data-movement) de Data Movement Library para más información. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Visualización o interacción rápida con los datos
Si desea una manera fácil de ver los datos de Azure Storage y también disponer de la capacidad para cargar y descargar los datos, considere la posibilidad de utilizar el Explorador de Azure Storage.

Consulte nuestra lista de [Exploradores de Azure Storage](../storage-explorers.md) para más información.

## <a name="system-administration"></a>Administración del sistema
Si requiere una utilidad de línea de comandos (por ejemplo, administradores del sistema), o se siente más cómodo con ella, estas son unas cuantas opciones para tener en cuenta:

### <a name="azcopy"></a>AzCopy
AzCopy es una utilidad de línea de comandos de Windows diseñada para la copia de datos de alto rendimiento a y desde Azure Storage. También puede copiar datos de blobs en la cuenta de almacenamiento o entre distintas cuentas de este tipo.

Consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md) para más información.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell es un módulo que proporciona cmdlets para administrar servicios en Azure. Se trata de un shell de línea de comandos basado en tareas y un lenguaje de scripts especialmente diseñados para administración del sistema.

Consulte [Usar Azure PowerShell con Azure Storage](storage-powershell-guide-full.md) para más información.

### <a name="azure-cli"></a>Azure CLI
CLI de Azure proporciona un conjunto de comandos de código abierto y multiplataforma para trabajar con los servicios de Azure. CLI de Azure está disponible en Windows, OSX y Linux.

Consulte [Uso de la CLI de Azure con Azure Storage](../storage-azure-cli.md) para más información.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Movimiento de grandes cantidades de datos con una red lenta
Uno de los mayores desafíos asociado a mover grandes cantidades de datos es el tiempo de transferencia. Si desea obtener datos desde y hacia Azure Storage sin preocuparse por los costos de la red o la escritura de código, Azure Import/Export es una solución adecuada.

Consulte [Azure Import/Export](../storage-import-export-service.md) para más información.

## <a name="backing-up-your-data"></a>Realización de una copia de seguridad de los datos
Si solo necesita hacer una copia de seguridad de los datos en Azure Storage, Azure Backup es la mejor opción. Se trata de una solución eficaz para la copia de seguridad de los datos locales y máquinas virtuales de Azure.

Consulte [Azure Backup](../../backup/backup-introduction-to-azure-backup.md) para más información.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Acceso a los datos locales y de la nube
Si necesita una solución para acceder a los datos locales y de la nube, considere el uso de la solución de almacenamiento en la nube híbrida de Azure, StorSimple. Esta solución consta de un dispositivo físico de StorSimple que almacena de manera inteligente los datos usados con más frecuencia en las unidades SSD, los datos menos usados en unidades de disco duro y los datos inactivos, de copia de seguridad o de archivado en Azure Storage.

Consulte [StorSimple](../../storsimple/storsimple-overview.md) para más información.

## <a name="recovering-your-data"></a>Recuperación de los datos
Si tiene aplicaciones y cargas de trabajo locales, necesitará una solución que permita que su negocio siga funcionando en caso de desastre. Azure Site Recovery controla la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Los datos replicados se almacenan en Azure Storage, lo que le permite eliminar la necesidad de un centro de datos secundario en el sitio.

Consulte [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) para más información.
### <a name="moving-data-faq"></a>Preguntas más frecuentes sobre movimiento de datos:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>¿Puedo migrar VHD de una región a otra sin copiar?
La única manera de copiar VHD entre regiones es copiar los datos entre las cuentas de almacenamiento en cada región. Puede utilizar AZCopy para ello. Consulte Transferencia de datos con la utilidad en línea de comandos AzCopy para obtener más información. Para grandes cantidades de datos, también puede importar/exportar de Azure. Consulte [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) para más información.
