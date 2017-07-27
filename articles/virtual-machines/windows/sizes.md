---
title: "Tamaños de las máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Enumera los tamaños diferentes disponibles para las máquinas virtuales Windows en Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/16/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 8eaf89c7f83d514b39f889b068bf1e84b9af4ca2
ms.contentlocale: es-es
ms.lasthandoff: 06/09/2017

---

# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Tamaños de las máquinas virtuales Windows en Azure

En este artículo se describen los tamaños y las opciones disponibles para las máquinas virtuales de Azure que puede usar para ejecutar las aplicaciones y cargas de trabajo de Windows. También ofrece consideraciones de implementación que hay que tener en cuenta siempre que planee usar estos recursos.  Este artículo también está disponible para [máquinas virtuales Linux](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Tipo                     | Tamaños           |    Descripción       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Uso general](sizes-general.md)          | DSv2, Dv2, DS, D, Av2, A0-7 | Uso equilibrado de la CPU en proporción de memoria. Ideal para desarrollo y pruebas, bases de datos pequeñas o medianas, y servidores web de tráfico bajo o medio. |
| [Proceso optimizado](sizes-compute.md)        | Fs, F             | Uso elevado de la CPU en proporción de memoria. Bueno para servidores web de tráfico medio, aplicaciones de red, procesos por lotes y servidores de aplicaciones.        |
| [Memoria optimizada](../virtual-machines-windows-sizes-memory.md)         | M, GS, G, DSv2, DS, Dv2, D   | Memoria alta en proporción de núcleo. Excelente para servidores de bases de datos relacionales, memorias caché de capacidad media o grande y análisis en memoria.                 |
| [Almacenamiento optimizado](../virtual-machines-windows-sizes-storage.md)        | LS                | Alto rendimiento de disco y E/S. Perfecto para bases de datos SQL y NoSQL y macrodatos.                                                         |
| [GPU](sizes-gpu.md)            | NV, NC            | Máquinas virtuales especializadas específicas para la representación de gráficos pesados y la edición de vídeo. Están disponibles con uno o varios GPU.       |
| [Proceso de alto rendimiento](sizes-hpc.md) | H, A8-11          | Nuestras máquinas virtuales de CPU más rápidas y eficaces con interfaces de red de alto rendimiento opcionales (RDMA). 

<br> 

- Para obtener información sobre los precios de los diferentes tamaños, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Para ver los límites generales de las máquinas virtuales de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../../azure-subscription-service-limits.md).
- Los costes de almacenamiento se calculan por separado según las páginas utilizadas en la cuenta de almacenamiento. Para obtener más información, consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
- Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.



## <a name="rest-api"></a>API de REST

Para obtener información sobre el uso de la API de REST para consultar los tamaños de máquina virtual, consulte lo siguiente:

- [Lista de tamaños de máquina virtual disponibles para cambio de tamaño](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing)
- [Lista de tamaños de máquina virtual disponibles para una suscripción](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)
- [Lista de tamaños de máquina virtual disponibles en un conjunto de disponibilidad](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set)

## <a name="acu"></a>ACU

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los diferentes tamaños de máquina virtual que están disponibles:
- [Uso general](sizes-general.md)
- [Proceso optimizado](sizes-compute.md)
- [Memoria optimizada](../virtual-machines-windows-sizes-memory.md)
- [Almacenamiento optimizado](../virtual-machines-windows-sizes-storage.md)
- [GPU optimizada](sizes-gpu.md)
- [Proceso de alto rendimiento](sizes-hpc.md)




