---
title: Objetivos de escalabilidad y rendimiento de Azure Storage | Microsoft Docs
description: "Obtenga información sobre los objetivos de escalabilidad y rendimiento para Azure Storage, incluida la capacidad, la velocidad de solicitudes y el ancho de banda entrante y saliente para las cuentas Premium y Standard de Storage. Comprenda los objetivos de rendimiento para las particiones en cada uno de los servicios de Azure Storage."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/24/2017
ms.author: tamram
ms.openlocfilehash: f62f2020d40e473886cb679cdfe1c164b95f7114
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Objetivos de escalabilidad y rendimiento de Azure Storage
## <a name="overview"></a>Información general
Este artículo trata cuestiones de rendimiento y escalabilidad de Azure Storage. Para ver un resumen de otros límites de Azure, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../../azure-subscription-service-limits.md).

> [!NOTE]
> Todas las cuentas de almacenamiento se ejecutan en la nueva topología de red plana y admiten los objetivos de escalabilidad y rendimiento descritos en este artículo, independientemente de cuándo se crearon. Para obtener más información acerca de la arquitectura de red plana de Azure Storage y de la escalabilidad, vea [Microsoft Azure Storage: un servicio de almacenamiento en la nube altamente disponible](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Los objetivos de escalabilidad y rendimiento que mencionamos aquí son objetivos exigentes, pero se pueden lograr. En todos los casos, la velocidad de solicitudes y el ancho de banda obtenido por la cuenta de almacenamiento depende del tamaño de los objetos almacenados, de los patrones de acceso utilizados y del tipo de carga de trabajo que realiza la aplicación. Asegúrese de probar el servicio para determinar si el rendimiento se ajusta a sus requisitos. Si es posible, evite picos en la tasa de tráfico y asegúrese de que este se distribuya equitativamente entre las particiones.
> 
> Cuando la aplicación alcanza el límite de lo que puede administrar una partición para la carga de trabajo, Azure Storage comienza a responder con el código de error 503 (servidor ocupado) o el código de error 500 (tiempo de espera de operación). Si se producen estos errores, la aplicación debe utilizar una directiva de retroceso exponencial para los reintentos. El retroceso exponencial permite que disminuya la carga de la partición y evita los picos de tráfico en esa partición.
> 
> 

Si las necesidades de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, puede compilar la aplicación de forma que use varias cuentas de almacenamiento. A continuación, puede dividir los objetos de datos en esas cuentas de almacenamiento. Para obtener información sobre los precios por volumen, consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="scalability-targets-for-a-storage-account"></a>Objetivos de escalabilidad para una cuenta de almacenamiento
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Objetivos de escalabilidad de Azure Blob Storage
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Objetivos de escalabilidad de Azure Files
Para obtener más información sobre los objetivos de escalabilidad y rendimiento para Azure Files y Azure File Sync, consulte [Azure Files scalability and performance targets](../files/storage-files-scale-targets.md) (Objetivos de escalabilidad y rendimiento de Azure Files).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Objetivos de escalabilidad de Azure File Sync
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Objetivos de escalabilidad de Azure Queue Storage
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Objetivos de escalabilidad de Azure Table Storage
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Otras referencias
* [Detalles de precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/)
* [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../../azure-subscription-service-limits.md)
* [Replicación de Azure Storage](../storage-redundancy.md)
* [Lista de comprobación de rendimiento y escalabilidad de Microsoft Azure Storage](../storage-performance-checklist.md)
* [Microsoft Azure Storage: un servicio de almacenamiento en nube altamente disponible con gran coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

