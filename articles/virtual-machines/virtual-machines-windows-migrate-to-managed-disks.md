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
ms.date: 02/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: e5f2eaa55e92c9a630533a5594e1a659cc2192f1
ms.openlocfilehash: 390195c8f07430e5fac6d53a77f6bb1aba53b197


---

# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migración de VM de Azure a Managed Disks en Azure

Azure Managed Disks elimina la necesidad de administrar las [cuentas de Storage](../storage/storage-introduction.md) para las VM de Azure. Solo debe especificar el tipo [Premium](../storage/storage-premium-storage-performance.md) o [Estándar](../storage/storage-standard-storage.md) y el tamaño del disco que necesita, y Azure creará y administrará el disco por usted. 

Si actualmente usa la opción de almacenamiento estándar para los discos, migre a Managed Disks Premium para aprovechar la velocidad y el rendimiento de estos discos. [Managed Disks Premium](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage-performance) se almacenan en unidades de estado sólido (SSD), lo que ofrece compatibilidad con discos de alto rendimiento y latencia baja para máquinas virtuales que ejecutan cargas de trabajo intensivas de E/S.

Puede migrar a Managed Disks en los escenarios siguientes:

| Migrar...                                            | Vínculo de documentación                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VM de un conjunto de disponibilidad que usan discos no administrados a discos administrados   | [Conversión de VM de un conjunto de disponibilidad para usar discos administrados](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set)                                                                        |
| Discos no administrados Premium a discos administrados Premium   | [Conversión de VM de Azure existentes en discos administrados del mismo tipo de almacenamiento](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type)                                                                         |
| Discos no administrados Estándar a discos administrados Estándar | [Conversión de VM de Azure existentes en discos administrados del mismo tipo de almacenamiento](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type)                                                                         |
| Discos no administrados Estándar a discos administrados Premium  | [Migración de VM de Azure existentes mediante Unmanaged Disks Estándar a Managed Disks Premium](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks)                            |
| Una VM única desde el modelo clásico a Resource Manager en discos administrados     | [Migración de una VM única](virtual-machines-windows-migrate-single-classic-to-resource-manager.md)  | 
| Todas las VM de una red virtual desde el modelo clásico a Resource Manager en discos administrados     | [Migración de recursos de IaaS desde el modelo clásico a Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md) y luego [Conversión de una VM desde discos no administrados a discos administrados](virtual-machines-windows-convert-unmanaged-to-managed-disks.md) | 


## <a name="overview-of-managed-disks"></a>Información general de Managed Disks

Azure Managed Disks simplifica la administración de almacenamiento al eliminar la necesidad de administrar cuentas de almacenamiento por separado.  También puede migrar las VM de Azure existentes a Managed Disks para aprovechar la mejor confiabilidad de las VM en un conjunto de disponibilidad. Esto garantiza que los discos de las distintas VM de un conjunto de disponibilidad estarán lo suficientemente aislados entre sí como para evitar un único punto de error. Coloca automáticamente discos de distintas VM en un conjunto de disponibilidad en unidades de escalado de almacenamiento diferentes (marcas de tiempo), lo que limita el impacto de errores únicos de unidad de escalado de almacenamiento generados debido a errores de hardware y software. En virtud de sus necesidades, puede elegir entre dos tipos de opciones de almacenamiento: 
 
- [Managed Disks Premium](../storage/storage-premium-storage.md) son medios de almacenamiento basados en unidades de estado sólido (SSD) que ofrecen compatibilidad con discos de alto rendimiento y latencia baja para máquinas virtuales que ejecutan cargas de trabajo intensivas de E/S. Puede aprovechar la velocidad y el rendimiento de estos discos si migra a Managed Disks Premium.

- [Managed Disks Estándar](../storage/storage-standard-storage.md) usan medios de almacenamiento basados en discos duros (HDD) y son más adecuados para desarrollo y pruebas y otras cargas de trabajo de acceso poco frecuente que no dan tanta importancia a la variabilidad del rendimiento. 


## <a name="plan-for-the-conversion-to-managed-disks"></a>Planeación de la conversión a Managed Disks

Esta sección puede ayudarlo a tomar la mejor decisión sobre los tipos de discos y VM.


## <a name="location"></a>Ubicación

Elija una ubicación donde Azure Managed Disks esté disponible. Si va a migrar a Managed Disks Premium, asegúrese que Premium Storage también está disponible en la región a la que planea migrar. Consulte [Servicios de Azure por región](https://azure.microsoft.com/regions/#services) para información actualizada sobre las ubicaciones disponibles.

## <a name="vm-sizes"></a>Tamaños de VM

Si va a migrar a Managed Disks Premium, debe actualizar el tamaño de la VM a un tamaño compatible con Premium Storage disponible en la región donde se ubica la VM. Revise los tamaños de VM compatibles con Premium Storage. Las especificaciones de tamaño de las máquinas virtuales de Azure se muestran en [Tamaños de máquinas virtuales](virtual-machines-windows-sizes.md).
Repase las características de rendimiento de las máquinas virtuales que trabajan con Almacenamiento premium y elija el tamaño de máquina virtual que se mejor se ajuste a su carga de trabajo. Procure que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco.

## <a name="disk-sizes"></a>Tamaños de disco

**Managed Disks Premium**

Hay tres tipos de Managed Disks Premium que se pueden usar con la VM y cada uno de ellos tiene sus límites específicos de rendimiento y E/S por segundo. Tenga presentes estos límites a la hora de elegir el tipo de disco Premium para la VM según las necesidades de capacidad, rendimiento, escalabilidad y cargas máximas de la aplicación.

| Tipo de discos Premium  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Tamaño del disco           | 128 GB            | 512 GB            | 1.024 GB (1 TB)    |
| IOPS por disco       | 500               | 2300              | 5000              |
| Rendimiento de disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo |

**Discos administrados Estándar**

Hay cinco tipos de discos administrados Estándar que se pueden usar con la VM. Cada uno de ellos tiene una capacidad distinta, pero los mismos límites de rendimiento y E/S por segundo. Elija el tipo de disco administrado Estándar según las necesidades de capacidad de la aplicación.

| Tipo de disco Estándar  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Tamaño del disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1.024 GB (1 TB)   |
| IOPS por disco       | 500              | 500              | 500              | 500              | 500              |
| Rendimiento de disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo |

## <a name="disk-caching-policy"></a>Directiva de almacenamiento en caché de disco 

**Managed Disks Premium**

De forma predeterminada, la directiva de almacenamiento en caché de los discos es *Solo lectura* para todos los discos de datos Premium y *Lectura y Escritura* para el disco de sistema operativo Premium conectado a la máquina virtual. Recomendamos esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación.

## <a name="pricing"></a>Precios

Revise el [precio de Managed Disks](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Los precios de Managed Disks Premium son iguales que los de Unmanaged Disks Premium. Sin embargo, los precios de Managed Disks Estándar son distintos a los de los Unmanaged Disks Estándar.



## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Managed Disks](../storage/storage-managed-disks-overview.md)



<!--HONumber=Feb17_HO2-->


