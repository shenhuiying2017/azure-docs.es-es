---
title: "Migración de máquinas virtuales de Azure a Managed Disks | Microsoft Docs"
description: "Migre máquinas virtuales de Azure creadas con discos no administrados en cuentas de almacenamiento para usar Managed Disks."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: bacd5783a6db60dcc2aada4e8df55b2ec1e4f3aa
ms.contentlocale: es-es
ms.lasthandoff: 06/16/2017


---

# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migración de VM de Azure a Managed Disks en Azure

Azure Managed Disks simplifica la administración de almacenamiento al eliminar la necesidad de administrar cuentas de almacenamiento por separado.  También puede migrar las VM de Azure existentes a Managed Disks para aprovechar la mejor confiabilidad de las VM en un conjunto de disponibilidad. Esto garantiza que los discos de las distintas VM de un conjunto de disponibilidad estarán lo suficientemente aislados entre sí como para evitar un único punto de error. Coloca automáticamente discos de distintas VM en un conjunto de disponibilidad en unidades de escalado de almacenamiento diferentes (marcas de tiempo), lo que limita el impacto de errores únicos de unidad de escalado de almacenamiento generados debido a errores de hardware y software.
En virtud de sus necesidades, puede elegir entre dos tipos de opciones de almacenamiento:

- [Managed Disks Premium](../../storage/storage-premium-storage.md) son medios de almacenamiento basados en unidades de estado sólido (SSD) que ofrecen compatibilidad con discos de alto rendimiento y latencia baja para máquinas virtuales que ejecutan cargas de trabajo intensivas de E/S. Puede aprovechar la velocidad y el rendimiento de estos discos si migra a Managed Disks Premium.

- [Managed Disks Estándar](../../storage/storage-standard-storage.md) usan medios de almacenamiento basados en discos duros (HDD) y son más adecuados para desarrollo y pruebas y otras cargas de trabajo de acceso poco frecuente que no dan tanta importancia a la variabilidad del rendimiento.

Puede migrar a Managed Disks en los escenarios siguientes:

| Migrar...                                            | Vínculo de documentación                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Conversión de VM independientes y VM de un conjunto de disponibilidad para establecer discos administrados   | [Conversión de VM para usar discos administrados](convert-unmanaged-to-managed-disks.md) |
| Una VM única desde el modelo clásico a Resource Manager en discos administrados     | [Migración de una VM única](migrate-single-classic-to-resource-manager.md)  | 
| Todas las VM de una red virtual desde el modelo clásico a Resource Manager en discos administrados     | [Migración de recursos de IaaS desde el modelo clásico a Resource Manager](migration-classic-resource-manager-ps.md) y luego [Conversión de una VM desde discos no administrados a discos administrados](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Planeación de la conversión a Managed Disks

Esta sección puede ayudarlo a tomar la mejor decisión sobre los tipos de discos y VM.


## <a name="location"></a>Ubicación

Elija una ubicación donde Azure Managed Disks esté disponible. Si va a migrar a Managed Disks Premium, asegúrese que Premium Storage también está disponible en la región a la que planea migrar. Consulte [Servicios de Azure por región](https://azure.microsoft.com/regions/#services) para obtener información actualizada sobre las ubicaciones disponibles.

## <a name="vm-sizes"></a>Tamaños de VM

Si va a migrar a Managed Disks Premium, debe actualizar el tamaño de la VM a un tamaño compatible con Premium Storage disponible en la región donde se ubica la VM. Revise los tamaños de VM compatibles con Premium Storage. Las especificaciones de tamaño de las máquinas virtuales de Azure se muestran en [Tamaños de máquinas virtuales](sizes.md).
Repase las características de rendimiento de las máquinas virtuales que trabajan con Almacenamiento premium y elija el tamaño de máquina virtual que se mejor se ajuste a su carga de trabajo. Procure que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco.

## <a name="disk-sizes"></a>Tamaños de disco

**Managed Disks Premium**

Hay siete tipos de Managed Disks premium que se pueden usar con la máquina virtual y cada uno de ellos tiene sus límites específicos de rendimiento y E/S por segundo. Tenga presentes estos límites a la hora de elegir el tipo de disco Premium para la VM según las necesidades de capacidad, rendimiento, escalabilidad y cargas máximas de la aplicación.

| Tipo de discos Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Tamaño del disco           | 128 GB| 512 GB| 128 GB| 512 GB            | 1.024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS por disco       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Rendimiento de disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

**Discos administrados Estándar**

Hay siete tipos de Managed Disks estándar que se pueden usar con la máquina virtual. Cada uno de ellos tiene una capacidad distinta, pero los mismos límites de rendimiento y E/S por segundo. Elija el tipo de disco administrado Estándar según las necesidades de capacidad de la aplicación.

| Tipo de disco Estándar  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Tamaño del disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1.024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS por disco       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Rendimiento de disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 

## <a name="disk-caching-policy"></a>Directiva de almacenamiento en caché de disco

**Managed Disks Premium**

De forma predeterminada, la directiva de almacenamiento en caché de los discos es *Solo lectura* para todos los discos de datos Premium y *Lectura y Escritura* para el disco de sistema operativo Premium conectado a la máquina virtual. Recomendamos esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación.

## <a name="pricing"></a>Precios

Revise el [precio de Managed Disks](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Los precios de Managed Disks Premium son iguales que los de Unmanaged Disks Premium. Sin embargo, los precios de Managed Disks Estándar son distintos a los de los Unmanaged Disks Estándar.



## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Managed Disks](../../storage/storage-managed-disks-overview.md)

