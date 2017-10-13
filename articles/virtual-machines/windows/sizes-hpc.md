---
title: "Tamaños de las máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Enumera los tamaños diferentes disponibles para las máquinas virtuales de informática de alto rendimiento Windows en Azure."
services: virtual-machines-windows
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: a0596d134e9c26877848f93d72f35bfd2c957570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="high-performance-compute-vm-sizes"></a>Tamaños de máquina virtual de informática de alto rendimiento

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Instancias compatibles con RDMA
Un subconjunto de las instancias de proceso intensivo (H16r, H16mr, A8 y A9) incluye una interfaz de red para la conectividad de acceso directo a memoria remota (RDMA). Esta interfaz se añade a la interfaz de red estándar de Azure disponible para otros tamaños de máquina virtual. 
  
Esta interfaz permite que las instancias compatibles con RDMA se comuniquen través de una red InfiniBand, funcionando a velocidades FDR para máquinas virtuales H16r y H16mr y QDR para máquinas virtuales A8 y A9. Estas funcionalidades RDMA pueden mejorar la escalabilidad y el rendimiento de las aplicaciones de la Interfaz de paso de mensajes (MPI).

Estos son los requisitos para máquinas virtuales Windows compatibles con RDMA para acceder a la red RDMA de Azure: 

* **Sistema operativos**
  
  Windows Server 2012 R2, Windows Server 2012
  
  > [!NOTE]
  > Actualmente, Windows Server 2016 no admite conectividad RDMA en Azure.
  >

* **Conjunto de disponibilidad o servicio en la nube**: implemente las máquinas virtuales compatibles con RDMA en el mismo conjunto de disponibilidad (si utiliza el modelo de implementación de Azure Resource Manager) o en el mismo servicio en la nube (si utiliza el modelo de implementación clásica). Si utiliza Azure Batch, las máquinas virtuales compatibles con RDMA deben ser del mismo grupo.

* **MPI** : Microsoft MPI (MS-MPI) 2012 R2 o versiones posteriores, MPI Intel Library 5.x.

  Las implementaciones de MPI compatibles usan la interfaz Microsoft Network Direct para comunicarse entre las instancias. 

* **Espacio de direcciones de red RDMA** : la red RDMA en Azure reserva el espacio de direcciones 172.16.0.0/16. Para ejecutar aplicaciones MPI en instancias implementadas en una red virtual Azure, asegúrese de que el espacio de direcciones de la red virtual no se superpone a la red RDMA.

* **Extensión de máquina virtual HpcVmDrivers**: en las máquinas virtuales compatibles con RDMA, tiene que agregar la extensión HpcVmDrivers para instalar los controladores de dispositivos de red de Windows necesarios para la conectividad RDMA. (en algunas implementaciones de las instancias A8 y A9, la extensión HpcVmDrivers se agrega automáticamente). Para agregar la extensión de máquina virtual a una máquina virtual, puede usar los cmdlets de [Azure PowerShell](/powershell/azure/overview). 

  
  El comando siguiente instala la versión más reciente de la extensión HpcVMDrivers 1.1 en una máquina virtual compatible con RDMA existente denominada *myVM* implementados en el grupo de recursos denominado *myResourceGroup* en la región *Oeste de EE. UU.*:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Para obtener más información, consulte el artículo de [características y extensiones de las máquinas virtuales](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). También puede trabajar con las extensiones para las máquinas virtuales implementadas en el [modelo de implementación clásica](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>Uso de HPC Pack

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la solución gratuita de administración de trabajo y clúster de HPC de Microsoft es una opción para crear un clúster de proceso en Azure para ejecutar aplicaciones de MPI basadas en Windows y otras cargas de trabajo de HPC. HPC Pack 2012 R2 y las versiones posteriores incluye un entorno de tiempo de ejecución para MS-MPI que usa la red RDMA de Azure cuando se implementa en máquinas virtuales compatibles con RDMA.




## <a name="other-sizes"></a>Otros tamaños
- [Uso general](sizes-general.md)
- [Proceso optimizado](sizes-compute.md)
- [Memoria optimizada](../virtual-machines-windows-sizes-memory.md)
- [Almacenamiento optimizado](../virtual-machines-windows-sizes-storage.md)
- [GPU optimizada](sizes-gpu.md)

## <a name="next-steps"></a>Pasos siguientes

- Para ver listas de comprobación para usar instancias de proceso intensivo con HPC Pack en Windows Server, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Para usar instancias de proceso intensivo para ejecutar aplicaciones MPI con Azure Batch, consulte [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Azure Batch](../../batch/batch-mpi.md).

- Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.




