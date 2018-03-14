---
title: "Selección de tamaños de máquina virtual para grupos de Azure Batch | Microsoft Docs"
description: "Cómo elegir uno de los tamaños de máquina virtual disponibles para los nodos de proceso en grupos de Azure Batch"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: addd1e9314a754b40cc5d49c0299f007580f512f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Selección de un tamaño de máquina virtual para nodos de proceso en un grupo de Azure Batch

Cuando seleccione un tamaño de nodo para un grupo de Azure Batch, puede elegir entre casi todas las familias y tamaños de máquinas virtuales disponibles en Azure. Azure ofrece una variedad de tamaños para máquinas virtuales Windows y Linux para diferentes cargas de trabajo. 

Hay algunas excepciones y limitaciones a la hora de elegir un tamaño de máquina virtual:
* Algunas familias o tamaños de máquina virtual no se admiten en Batch. 
* Algunos tamaños de máquina virtual están restringidos y tienen que habilitarse específicamente antes de poder asignarlos.


## <a name="supported-vm-families-and-sizes"></a>Tamaños y familias de maquina virtual compatibles

### <a name="pools-in-virtual-machine-configuration"></a>Grupos en la configuración de máquina virtual

Los grupos de Batch en la configuración de máquina virtual son compatibles con todos los tamaños de máquina virtual ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *excepto* en los siguientes casos:

| Familia  | Tamaños no compatibles  |
|---------|---------|
| Serie A básica | Basic_A0 (A0) |
| Serie A | Standard_A0 |
| Serie B | Todo |
| Serie Fsv2 <sup>*</sup> | Todo |

<sup>*</sup>Los tamaños de esta serie se encuentran dentro de los planes para la compatibilidad futura.

### <a name="pools-in-cloud-service-configuration"></a>Grupos en la configuración de Cloud Service

Los grupos de Batch en la configuración de Cloud Service son compatibles con todos los [tamaños de máquina virtual para Cloud Services](../cloud-services/cloud-services-sizes-specs.md) *excepto* en los siguientes casos:

| Familia  | Tamaños no compatibles  |
|---------|---------|
| Serie A | ExtraSmall |
| Serie Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>Familias de máquina virtual restringidas
Las siguientes familias de máquina virtual se pueden asignar en grupos de Batch, pero tiene que solicitar un aumento de la cuota específico (consulte [este artículo](batch-quota-limit.md#increase-a-quota)):
* Serie NCv2
* Serie NCv3
* Serie ND

Estos tamaños solo se pueden utilizar con los grupos en la configuración de máquina virtual.

## <a name="size-considerations"></a>Consideraciones de tamaño

* **Requisitos de aplicación**: tenga en cuenta las características y los requisitos de la aplicación que se va a ejecutar en los nodos. Aspectos tales como si la aplicación es multiproceso y cuánta memoria consume pueden ayudar a determinar el tamaño de nodo más adecuado y rentable. Para varias instancias de [cargas de trabajo MPI](batch-mpi.md) o aplicaciones CUDA, considere la posibilidad de tamaños de máquina virtual especializados [HPC](../virtual-machines/linux/sizes-hpc.md) o [habilitado GPU](../virtual-machines/linux/sizes-gpu.md), respectivamente. (Consulte [Uso de instancias compatibles con RDMA o habilitadas para GPU en grupos de Batch](batch-pool-compute-intensive-sizes.md)). 

* **Tareas por nodo**: normalmente, se selecciona un tamaño de nodo bajo el supuesto de que no se ejecutará más que una sola tarea a la vez en un nodo. No obstante, puede tener ventajas [ejecutar en paralelo](batch-parallel-node-tasks.md) varias tareas y, por tanto, varias instancias de la aplicación, en varios nodos de proceso durante la ejecución del trabajo. En este caso, es habitual elegir un tamaño de nodo de varios núcleos para acomodar el aumento de la demanda por la ejecución de tareas en paralelo.

* **Niveles de carga para diferentes tareas**: todos los nodos en un grupo tienen el mismo tamaño. Si va a ejecutar aplicaciones con requisitos del sistema o niveles de carga diferentes, es recomendable usar grupos separados. 

* **Disponibilidad por regiones**: una familia o tamaño de máquina virtual, podría no estar disponible en las regiones en las que cree las cuentas de Batch. Para comprobar que un tamaño está disponible, vea [Productos disponibles por región](https://azure.microsoft.com/regions/services/).

* **Cuotas**: la [cuota de núcleos](batch-quota-limit.md#resource-quotas) en su cuenta de Batch puede limitar el número de nodos de un tamaño específico que se puede agregar a un grupo de Batch. Para solicitar un aumento de la cuota, consulte [este artículo](batch-quota-limit.md#increase-a-quota). 

* **Configuración de grupo**: por lo general, tiene más opciones de tamaño de máquina virtual cuando crea un grupo en la configuración de máquina virtual, en comparación con la configuración de Cloud Service.

## <a name="next-steps"></a>Pasos siguientes

* Para información general más detallada acerca de Batch, consulte [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md).
* Para más información acerca del uso de tamaños de máquinas virtuales de proceso intensivo, consulte [Uso de instancias compatibles con RDMA o habilitadas para GPU en grupos de Batch](batch-pool-compute-intensive-sizes.md). 


