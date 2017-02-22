---
title: "Migración desde AWS y otras plataformas a Managed Disks en Azure | Microsoft Docs"
description: "Cree VM en Azure con VHD cargados desde otras nubes, como AWS u otras plataformas de virtualización, y aproveche las ventajas de Azure Managed Disks."
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
ms.sourcegitcommit: ccde5cf9a45c79cfd441633722960007aad46088
ms.openlocfilehash: fe00166c443d8db678ec46cfd8b66c327176ddcf


---

# <a name="migrate-from-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migración desde AWS y otras plataformas a Managed Disks en Azure

Puede cargar archivos de VHD desde AWS o soluciones de virtualización locales en Azure para crear VM que aprovechen las ventajas de Managed Disks. Azure Managed Disks elimina la necesidad de administrar las cuentas de Storage para las VM IaaS de Azure. Solo debe especificar el tipo (Premium o Estándar) y el tamaño del disco que necesita, y Azure creará y administrará el disco automáticamente. 

Puede cargar VHD generalizados o especializados. 
**VHD generalizado**: Se ha quitado toda la información de cuenta personal con Sysprep de un VHD generalizado. 
**VHD especializado**: un disco duro virtual especializado mantiene las cuentas de usuario, las aplicaciones y otros datos de estado de la máquina virtual original. 

> [!IMPORTANT]
> Antes de cargar los VHD en Azure, debe consultar [Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Escenario                                                                                                                         | Documentación                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Es posible que tenga instancias EC2 de AWS existentes que quisiera migrar a Azure Managed Disks                                     | [Migración de Amazon Web Services (AWS) a Azure Managed Disks](virtual-machines-windows-aws-to-azure.md)                           |
| Tiene una VM de otra plataforma de virtualización que quisiera usar como imagen para crear varias VM de Azure. | [Carga de un VHD generalizado en Azure y creación de una VM nueva con Managed Disks](virtual-machines-windows-upload-generalized-managed.md) |
| Cuenta con una VM personalizada de forma exclusiva que quisiera recrear en Azure.                                                      | [Carga de un VHD especializado en Azure y creación de una VM nueva con Managed Disks](virtual-machines-windows-upload-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Información general de Managed Disks

Azure Managed Disks simplifica la administración de VM al eliminar la necesidad de administrar cuentas de almacenamiento. Managed Disks también aprovecha las ventajas de la mejor confiabilidad de las VM en un conjunto de disponibilidad. Esto garantiza que los discos de las distintas VM de un conjunto de disponibilidad estarán lo suficientemente aislados entre sí como para evitar un único punto de error. Coloca automáticamente discos de distintas VM en un conjunto de disponibilidad en unidades de escalado de almacenamiento diferentes (marcas de tiempo), lo que limita el impacto de errores únicos de unidad de escalado de almacenamiento generados debido a errores de hardware y software. En virtud de sus necesidades, puede elegir entre dos tipos de opciones de almacenamiento: 
 
- [Managed Disks Premium](../storage/storage-premium-storage.md) son medios de almacenamiento basados en unidades de estado sólido (SSD) que ofrecen compatibilidad con discos de alto rendimiento y latencia baja para máquinas virtuales que ejecutan cargas de trabajo intensivas de E/S. Puede aprovechar la velocidad y el rendimiento de estos discos si migra a Managed Disks Premium.  

- [Managed Disks Estándar](../storage/storage-standard-storage.md) usan medios de almacenamiento basados en discos duros (HDD) y son más adecuados para desarrollo y pruebas y otras cargas de trabajo de acceso poco frecuente que no dan tanta importancia a la variabilidad del rendimiento.  

## <a name="plan-for-the-migration-to-managed-disks"></a>Planeación de la migración a Managed Disks

Esta sección puede ayudarlo a tomar la mejor decisión sobre los tipos de discos y VM.


### <a name="location"></a>Ubicación

Elija una ubicación donde Azure Managed Disks esté disponible. Si va a migrar a Managed Disks Premium, asegúrese que Premium Storage también está disponible en la región a la que planea migrar. Consulte [Servicios de Azure por región](https://azure.microsoft.com/regions/#services) para obtener información actualizada sobre las ubicaciones disponibles.

### <a name="vm-sizes"></a>Tamaños de VM

Si va a migrar a Managed Disks Premium, debe actualizar el tamaño de la VM a un tamaño compatible con Premium Storage disponible en la región donde se ubica la VM. Revise los tamaños de VM compatibles con Premium Storage. Las especificaciones de tamaño de las máquinas virtuales de Azure se muestran en [Tamaños de máquinas virtuales](virtual-machines-windows-sizes.md).
Repase las características de rendimiento de las máquinas virtuales que trabajan con Almacenamiento premium y elija el tamaño de máquina virtual que se mejor se ajuste a su carga de trabajo. Procure que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco.

### <a name="disk-sizes"></a>Tamaños de disco

**Managed Disks Premium**

Hay tres tipos de Managed Disks Premium que se pueden usar con la VM y cada uno de ellos tiene sus límites específicos de rendimiento y E/S por segundo. Considere estos límites a la hora de elegir el tipo de disco Premium para la VM según las necesidades de capacidad, rendimiento, escalabilidad y cargas máximas de la aplicación.

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

### <a name="disk-caching-policy"></a>Directiva de almacenamiento en caché de disco 

**Managed Disks Premium**

De forma predeterminada, la directiva de almacenamiento en caché de los discos es *Solo lectura* para todos los discos de datos Premium y *Lectura y Escritura* para el disco de sistema operativo Premium conectado a la máquina virtual. Recomendamos esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación.

### <a name="pricing"></a>Precios

Revise el [precio de Managed Disks](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Los precios de Managed Disks Premium son iguales que los de Unmanaged Disks Premium. Sin embargo, los precios de Managed Disks Estándar son distintos a los de los Unmanaged Disks Estándar.


## <a name="next-steps"></a>Pasos siguientes

- Antes de cargar los VHD en Azure, debe consultar [Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)



<!--HONumber=Feb17_HO3-->


