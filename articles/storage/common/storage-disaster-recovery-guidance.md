---
title: "Qué hacer si se produce una interrupción del servicio de Azure Storage | Microsoft Docs"
description: "Qué hacer si se produce una interrupción del servicio de Almacenamiento de Azure"
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/19/2017
ms.author: tamram
ms.openlocfilehash: c768bdbb8e1ce2aae3eb5b6db0e48977366c83fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Qué hacer si se produce una interrupción del servicio de Almacenamiento de Azure
En Microsoft, hacemos todo lo posible para garantizar que los servicios siempre estén disponibles. A veces, debido a factores externos que escapan de nuestro control, se producen interrupciones de servicio no planeadas en una o varias regiones. Para ayudarlo a gestionar estos raros imprevistos, ofrecemos la siguiente guía general para los servicios de Almacenamiento de Azure.

## <a name="how-to-prepare"></a>Preparación
Es fundamental que todos los clientes preparen su propio plan de recuperación ante desastres. Para recuperarse de una interrupción de los servicios de almacenamiento, normalmente hay que realizar procedimientos automatizados e implicar al personal de operaciones con el objetivo de reactivar las aplicaciones para que funcionen con normalidad. Consulte la documentación de Azure para crear su propio plan de recuperación ante desastres:

* [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure](/azure/architecture/resiliency/disaster-recovery-high-availability-azure-applications.md)
* [Guía técnica sobre resistencia en Azure](/azure/architecture/resiliency.md)
* [Servicio Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)
* [Replicación de almacenamiento de Azure](storage-redundancy.md)
* [Azure Backup](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Detección
La manera recomendada de determinar el estado del servicio de Azure es suscribirse al [panel de estado del servicio de Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Qué hacer si se produce una interrupción de los servicios de almacenamiento
Si uno o varios servicios de almacenamiento no están disponibles temporalmente en una o varias regiones, hay dos opciones para tener en cuenta. Si desea acceder de inmediato a los datos, plantéese la opción 2.

### <a name="option-1-wait-for-recovery"></a>Opción 1: esperar a que se recupere el servicio
En este caso, no se requieren acciones por su parte. Trabajaremos con rapidez para que el servicio de Azure vuelva a estar disponible. Puede supervisar el estado del servicio en el [panel de estado del servicio de Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Opción 2: copiar los datos de la región secundaria
Si eligió el [almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado) en las cuentas de almacenamiento, tendrá acceso de lectura a los datos de la región secundaria. Puede usar herramientas como [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) y la [biblioteca de movimiento de datos de Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar los datos de la región secundaria en otra cuenta de almacenamiento de una región donde no se haya producido la interrupción. Después, haga que las aplicaciones apunten a esa cuenta de almacenamiento para proporcionar acceso de lectura y escritura.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Qué esperar si se produce una conmutación por error de almacenamiento
Si eligió el [almacenamiento con redundancia geográfica (GRS)](storage-redundancy.md#geo-redundant-storage) o el [almacenamiento geográficamente redundante con acceso de lectura (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado), Almacenamiento de Azure conservará los datos en dos regiones (la principal y la secundaria). En las dos regiones, Almacenamiento de Azure mantendrá constantemente réplicas de los datos.

Cuando se produce un desastre regional que afecta a la región primaria, se tratará en primer lugar de restaurar el servicio en esa región. Según la naturaleza de los desastres y sus repercusiones, en raras ocasiones es posible que no podamos restaurar la región primaria. En ese momento, realizaremos una conmutación por error geográfica. La replicación de datos entre regiones consiste en un proceso asincrónico que puede provocar retrasos, por lo que es posible que se pierdan los cambios que todavía no se hayan replicado a la región secundaria. Puede consultar la [hora de última sincronización de la cuenta de almacenamiento](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) para obtener información sobre el estado de replicación.

Ahora hablaremos de dos aspectos relativos a la experiencia de conmutación por error geográfica de almacenamiento:

* La conmutación por error geográfica de almacenamiento solo la activa el equipo de almacenamiento de Azure. El cliente no tiene que hacer nada.
* Los puntos de conexión del servicio de almacenamiento para blobs, tablas, colas y archivos seguirán siendo los mismos después de la conmutación por error; la entrada DNS suministrada por Microsoft deberá actualizarse para cambiar de la región primaria a la secundaria.  Microsoft realizará esta actualización automáticamente como parte del proceso de conmutación por error geográfica.
* Antes de la conmutación por error geográfica y durante este proceso, no tendrá acceso de escritura a la cuenta de almacenamiento debido a las repercusiones del desastre. Sin embargo, sí que podrá seguir realizando operaciones de lectura en la base de datos secundaria si la cuenta de almacenamiento se ha configurado como RA-GRS.
* Cuando se haya completado la conmutación por error geográfica y se propaguen los cambios de DNS, se reanudará el acceso de lectura y escritura de la cuenta de almacenamiento. Esto apunta a lo que solía ser el punto de conexión secundario. 
* Tenga en cuenta que tendrá acceso de escritura si tiene GRS o RA-GRS configurado para la cuenta de almacenamiento. 
* Puede consultar la [hora de la última conmutación por error geográfica de la cuenta de almacenamiento](https://msdn.microsoft.com/library/azure/ee460802.aspx) para obtener más información.
* Después de la conmutación por error, la cuenta de almacenamiento volverá a estar completamente funcional, pero con un rendimiento reducido, ya que se hospeda realmente en una región independiente donde no se pueden realizar replicaciones geográficas. Para mitigar este riesgo, se restaurará la región primaria original y, luego, se realizará una conmutación por error geográfica para restaurar el estado original. Si la región primaria original es irrecuperable, asignamos otra región secundaria.
  Para obtener más información sobre la infraestructura de replicación geográfica de Almacenamiento de Azure, consulte el artículo del blog del equipo de almacenamiento que trata sobre las [opciones de redundancia y RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Prácticas recomendadas para proteger los datos
Para hacer una copia de seguridad de los datos de almacenamiento de forma periódico, hay algunos enfoques que recomendamos.

* Discos de máquina virtual: use el [servicio de copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) para realizar una copia de seguridad de los discos de máquina virtual que emplean las máquinas virtuales de Azure.
* Blobs en bloques: cree una [instantánea](https://msdn.microsoft.com/library/azure/hh488361.aspx) de cada blob en bloques o copie los blobs en otra cuenta de almacenamiento de otra región mediante [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) o la [biblioteca de movimiento de datos de Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Tablas: use [AzCopy](storage-use-azcopy.md) para exportar los datos de tabla en otra cuenta de almacenamiento de otra región.
* Archivos: use [AzCopy](storage-use-azcopy.md) o [Azure PowerShell](storage-powershell-guide-full.md) para copiar los archivos en otra cuenta de almacenamiento de otra región.

Para información sobre cómo crear aplicaciones que aprovechan totalmente la característica RA-GRS, consulte [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](../storage-designing-ha-apps-with-ragrs.md).

