---
title: "Tamaños de las máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Enumera los tamaños diferentes disponibles para las máquinas virtuales Linux en Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 446f4257979e6725186b32ad65cef31cdd3e7ede
ms.lasthandoff: 03/24/2017

---

# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Tamaños de las máquinas virtuales Linux en Azure
En este artículo se describen los tamaños y las opciones disponibles para las máquinas virtuales de Azure que puede usar para ejecutar las aplicaciones y cargas de trabajo de Linux. También ofrece consideraciones de implementación que hay que tener en cuenta siempre que planee usar estos recursos. Este artículo también está disponible para [máquinas virtuales Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> * Para obtener información sobre los precios de los diferentes tamaños, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
> * Para ver la disponibilidad de los tamaños de máquina virtual en las regiones de Azure, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/).
> * Para ver los límites generales de las máquinas virtuales de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).
> * Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](virtual-machines-linux-acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.
> 
> 

<br>   


| Tipo                     | Tamaños           |    Descripción       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Uso general](virtual-machines-linux-sizes-general.md)          | DSv2, Dv2, DS, D, Av2, A0-7,  | Uso equilibrado de la CPU en proporción de memoria. Ideal para desarrollo y pruebas, bases de datos pequeñas o medianas, y servidores web de tráfico bajo o medio. |
| [Proceso optimizado](virtual-machines-linux-sizes-compute.md)        | Fs, F             | Uso elevado de la CPU en proporción de memoria. Bueno para servidores web de tráfico medio, aplicaciones de red, procesos por lotes y servidores de aplicaciones.        |
| [Memoria optimizada](virtual-machines-linux-sizes-memory.md)         | GS, G, DSv2, DS   | Memoria alta en proporción de núcleo. Excelente para servidores de bases de datos relacionales, memorias caché de capacidad media o grande y análisis en memoria.                 |
| [Almacenamiento optimizado](virtual-machines-linux-sizes-storage.md)        | LS                | Alto rendimiento de disco y E/S. Perfecto para bases de datos SQL y NoSQL y macrodatos.                                                         |
| [GPU](virtual-machines-linux-sizes-gpu.md)            | NV, NC            | Máquinas virtuales especializadas específicas para la representación de gráficos pesados y la edición de vídeo. Están disponibles con uno o varios GPU.       |
| [Proceso de alto rendimiento](virtual-machines-linux-sizes-hpc.md) | H, A8-11          | Nuestras máquinas virtuales de CPU más rápidas y eficaces con interfaces de red de alto rendimiento opcionales (RDMA). 

<br>

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](virtual-machines-linux-acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.

Obtenga más información sobre los diferentes tamaños de máquina virtual que están disponibles:
- [Uso general](virtual-machines-linux-sizes-general.md)
- [Proceso optimizado](virtual-machines-linux-sizes-compute.md)
- [Memoria optimizada](virtual-machines-linux-sizes-memory.md)
- [Almacenamiento optimizado](virtual-machines-linux-sizes-storage.md)
- [GPU](virtual-machines-linux-sizes-gpu.md)
- [Proceso de alto rendimiento](virtual-machines-linux-sizes-hpc.md)




