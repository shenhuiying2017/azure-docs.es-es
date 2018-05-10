---
title: Replicación de datos en Azure Storage | Microsoft Docs
description: Los datos de su cuenta de Microsoft Azure Storage se replican para garantizar la durabilidad y la alta disponibilidad. Entre las opciones de replicación se incluyen el almacenamiento con redundancia local (LRS), el almacenamiento con redundancia de zona (ZRS), el almacenamiento con redundancia geográfica (GRS) y el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 2b105cd05ace9be6ad24d092f2b12c7ad092188e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-replication"></a>Replicación de Azure Storage

Los datos de su cuenta de almacenamiento de Microsoft Azure se replican siempre para garantizar la durabilidad y la alta disponibilidad. La replicación de Azure Storage copia los datos para protegerlos de eventos previstos e imprevistos que van de errores transitorios de hardware, interrupciones del suministro eléctrico o de la red a desastres naturales masivos, entre otros. Puede optar por replicar los datos en el mismo centro de datos, en centros de datos zonales que estén en la misma región e incluso entre regiones.

La replicación garantiza que la cuenta de almacenamiento cumpla el [contrato de nivel de servicio (SLA) para Storage](https://azure.microsoft.com/support/legal/sla/storage/), incluso en caso de errores. Consulte en el SLA información acerca de las garantías de durabilidad y disponibilidad de Azure Storage.

## <a name="choosing-a-replication-option"></a>Elección de una opción de replicación

Cuando cree una cuenta de almacenamiento, puede seleccionar una de las siguientes opciones de replicación:

* [Almacenamiento con redundancia local (LRS)](storage-redundancy-lrs.md)
* [Almacenamiento con redundancia de zona (ZRS)](storage-redundancy-zrs.md)
* [Almacenamiento con redundancia geográfica (GRS)](storage-redundancy-grs.md)
* [Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).](storage-redundancy-grs.md#read-access-geo-redundant-storage)

En la tabla siguiente se ofrece una rápida información general del ámbito de durabilidad y disponibilidad que cada estrategia de replicación le proporcionará para un tipo determinado de evento (o evento de impacto similar).

| Escenario | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Falta de disponibilidad del nodo en un centro de datos |Sí |Sí |Sí |Sí
| Un centro de datos completo (de zona o no de zona) deja de estar disponible |Sin  |Sí |Sí |Sí |
| Una interrupción en toda la región |Sin  |Sin  |Sí |Sí |
| Acceso de lectura a los datos (en una región remota y con replicación geográfica) en caso de no disponibilidad en toda la región. |Sin  |Sin  |Sin  |Sí |
| Diseñado para proporcionar una durabilidad de objetos ___ a lo largo de un año determinado. |Como mínimo 99.999999999 % (once nueves)|Como mínimo 99.9999999999 % (doce nueves)|Como mínimo 99.99999999999999 % (dieciséis nueves)|Como mínimo 99.99999999999999 % (dieciséis nueves)|
| Disponible en ___ tipos de cuenta de almacenamiento |GPv1, GPv2, Blob |GPv2 |GPv1, GPv2, Blob |GPv1, GPv2, Blob

Consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/) para más información sobre las diferentes opciones de redundancia.

> [!NOTE]
> Premium Storage solo admite almacenamiento con redundancia local (LRS). Para más información sobre Premium Storage, consulte [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Cambio de estrategia de replicación
Podrá cambiar la estrategia de replicación de la cuenta de almacenamiento mediante [Azure Portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o una de las muchas [ bibliotecas de cliente de Azure](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). Si cambia el tipo de replicación de la cuenta de almacenamiento, no se producirá ningún tiempo de inactividad.

   > [!NOTE]
   > Actualmente, no se puede usar el Portal o la API para convertir la cuenta a ZRS. Sin embargo, tenemos previsto admitir la migración de LRS, GRS y RA-GRS a ZRS una vez que ZRS esté generalmente disponible. Consulte [Almacenamiento con redundancia de zona (ZRS)](storage-redundancy-zrs.md) para más información.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>¿Hay algún costo al cambiar la estrategia de replicación de mi cuenta?
Depende de la ruta de acceso de la conversión. Si se solicita la oferta de redundancia de más económica a la más cara, tenemos LRS, ZRS, GRS, y RA-GRS. Por ejemplo, si se pasa *de* LRS a cualquier otra opción, se incurrirá en cargos adicionales porque se pasará a un nivel de redundancia más sofisticado. Si se pasa *a* GRS o RA-GRS se incurrirá en un cargo por ancho de banda de salida porque los datos (en la región primaria) se están replicando en la región secundaria remota. Se trata de un cargo único en la instalación inicial. Después de copiar los datos, ya no hay ningún cargo adicional de conversión. Solo se le cobrará por replicar cualquier dato nuevo o actualizado en los datos existentes. Para obtener más información sobre los cargos de ancho de banda, consulte la [página de precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Si cambia de GRS a LRS, no hay ningún costo adicional, pero los datos replicados se eliminarán de la ubicación secundaria.

## <a name="see-also"></a>Otras referencias

- [Almacenamiento con redundancia local (LRS): redundancia de datos de bajo costo para Azure Storage](storage-redundancy-lrs.md)
- [Zone-redundant storage (ZRS): Highly available Azure Storage applications](storage-redundancy-zrs.md) (Almacenamiento con redundancia de zona (ZRS): aplicaciones de Azure Storage altamente disponibles)
- [Almacenamiento con redundancia geográfica (GRS): replicación entre regiones para Azure Storage](storage-redundancy-grs.md)
- [Objetivos de escalabilidad y rendimiento de Azure Storage](storage-scalability-targets.md)
- [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Opciones de redundancia de Microsoft Azure Storage y almacenamiento con redundancia geográfica con acceso de lectura ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Documento de SOSP: Azure Storage, un servicio de almacenamiento en nube altamente disponible con gran coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
