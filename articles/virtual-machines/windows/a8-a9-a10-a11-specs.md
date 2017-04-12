---
title: "Acerca de las máquinas virtuales de proceso intensivo con Windows | Microsoft Docs"
description: "Obtenga información general y consideraciones sobre el uso de la serie H y las instancias de proceso intensivo A8, A9, A10 y A11 de Azure para servicios en la nube y máquinas virtuales Windows."
services: virtual-machines-windows, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: edc3cf6906bb535aec6e5203a5b306e33cd51599
ms.lasthandoff: 04/03/2017


---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-windows"></a>Acerca de las máquinas virtuales de la serie H y A de procesos intensivos para Windows
Aquí se proporciona información general y algunas consideraciones sobre el uso de las series H de Azure más recientes y las antiguas instancias A8, A9, A10 y A11, también conocidas como instancias de *proceso intensivo* . Este artículo se centra en el uso de estas instancias para máquinas virtuales de Windows. También puede utilizarlos para [máquinas virtuales Linux](../linux/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para conocer las especificaciones básicas, las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de las máquinas virtuales](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acceso a la red RDMA
Para obtener acceso a la red RDMA de Azure para el tráfico de MPI de Windows, las instancias compatibles con RDMA deben cumplir los siguientes requisitos: 

* **Sistema operativos**
  
  * **Máquinas virtuales** : Windows Server 2012 R2, Windows Server 2012.
  * **Cloud Services** : familia de SO invitado Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.

    > [!NOTE]
    > Actualmente, Windows Server 2016 no admite conectividad RDMA en Azure.
    >
    
* **MPI** : Microsoft MPI (MS-MPI) 2012 R2 o versiones posteriores, MPI Intel Library 5.x.

  Las implementaciones de MPI compatibles usan la interfaz Microsoft Network Direct para comunicarse entre las instancias. 
* **Extensión de máquina virtual HpcVmDrivers**: en las máquinas virtuales compatibles con RDMA, es necesario agregar la extensión HpcVmDrivers a las máquinas virtuales para instalar los controladores de dispositivos de red de Windows necesarios para la conectividad RDMA. (en algunas implementaciones de las instancias A8 y A9, la extensión HpcVmDrivers se agrega automáticamente). Si tiene que agregar la extensión de máquina virtual a una máquina virtual, puede usar los cmdlets de [Azure PowerShell](/powershell/azureps-cmdlets-docs). 

  
  Por ejemplo, para instalar la versión más reciente de la extensión HpcVMDrivers 1.1 en una máquina virtual compatible con RDMA existente denominada "myVM" implementada en el modelo de implementación de Resource Manager:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obtener más información, consulte el artículo de [características y extensiones de las máquinas virtuales](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). También puede trabajar con las extensiones para las máquinas virtuales implementadas en el [modelo de implementación clásica](classic/manage-extensions.md).


## <a name="considerations-for-hpc-pack-and-windows"></a>Consideraciones sobre HPC Pack y Linux
[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la solución gratuita de administración de clústeres y trabajos de HPC de Microsoft, no se necesita para usar las instancias de proceso intensivo con Windows Server. Sin embargo, es una opción para crear un clúster de proceso en Azure para ejecutar aplicaciones de MPI basadas en Windows y otras cargas de trabajo de HPC. HPC Pack 2012 R2 y las versiones posteriores incluye un entorno de tiempo de ejecución para MS-MPI que puede usar la red RDMA de Azure cuando se implementa en máquinas virtuales compatibles con RDMA.

Para más información y ver listas de comprobación para usar instancias de proceso intensivo con HPC Pack en Windows Server, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre la disponibilidad y los precios de las instancias de proceso intensivo, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) y [Precios de Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/).
* Para más información sobre las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de máquinas virtuales](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para empezar a implementar y usar instancias de proceso intensivo con HPC Pack en Windows, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Para obtener más información sobre el uso de instancias de proceso intensivo para ejecutar aplicaciones MPI con Azure Batch, consulte [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Azure Batch](../../batch/batch-mpi.md).


