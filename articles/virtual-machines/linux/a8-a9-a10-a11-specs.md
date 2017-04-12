---
title: "Acerca de las máquinas virtuales de proceso intensivo con Linux | Microsoft Docs"
description: "Obtenga información general y algunas consideraciones sobre el uso de la serie H y los tamaños A8, A9, A10 y A11 de proceso intensivo para las máquinas virtuales con Linux"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 16cf6e2d-f401-4b22-af8c-9a337179f5f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 9a8073baea9a6ff2ecf443b516665adb10de4815
ms.lasthandoff: 04/03/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>Acerca de las máquinas virtuales de la serie H y A de procesos intensivos para Linux
Aquí se proporciona información general y algunas consideraciones sobre el uso de las series H de Azure más recientes y los antiguos tamaños A8, A9, A10 y A11, también conocidos como instancias de *proceso intensivo* . Este artículo se centra en el uso de estos tamaños con las máquinas virtuales con Linux. También puede utilizarlos para [máquinas virtuales Windows](../windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para conocer las especificaciones básicas, las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de las máquinas virtuales](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acceso a la red RDMA
Puede crear clústeres de máquinas virtuales con Linux compatibles con RDMA que ejecuten una de las siguientes distribuciones de HPC de Linux compatibles y una implementación de MPI compatible para aprovechar la red RDMA de Azure. Para conocer las opciones de implementación y los pasos de una configuración de ejemplo, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

* **Distribuciones** : debe implementar máquinas virtuales de SUSE Linux Enterprise Server (SLES) compatibles con RDMA o imágenes de HPC basadas en OpenLogic CentOS en Azure Marketplace. Las siguientes imágenes de Marketplace admiten conectividad RDMA:
  
    * SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium)
    
    * HPC 7.1 basada en CentOS y HPC 6.5 basada en CentOS  
 
        > [!NOTE]
        > Para las máquinas virtuales de la serie H, se recomienda un SLES 12 SP1 para la imagen de HPC o la imagen de HPC 7.1 basada en CentOS.
        >
        > En las imágenes de HPC basadas en CentOS, las actualizaciones del núcleo están deshabilitadas en el archivo de configuración **yum** . Esto se debe a que los controladores Linux RDMA se distribuyen en forma de paquete RPM y sus actualizaciones de estos podrían no funcionar si se actualiza el kernel.
        > 
        > 
* **MPI** : Biblioteca MPI Intel 5.x
  
    Dependiendo de la imagen de Marketplace que elija, puede ser necesaria la instalación de licencia, la instalación o la configuración independientes de Intel MPI, como sigue: 
  
  * **SLES 12 SP1 para la imagen de HPC**: los paquetes de Intel MPI se distribuyen en la máquina virtual. Instálelos ejecutando el comando siguiente:

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **Imágenes de HPC basadas en CentOS**: Intel MPI 5.1 ya está instalado.  
    
    Se necesita configuración adicional del sistema para ejecutar trabajos MPI en máquinas virtuales en clúster. Por ejemplo, en un clúster de máquinas virtuales, debe establecer la confianza entre los nodos de proceso. Para configuraciones típicas, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="considerations-for-hpc-pack-and-linux"></a>Consideraciones sobre el HPC Pack y Linux
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la solución gratuita de administración de clústeres y trabajos de HPC de Microsoft, proporciona una opción para usar las instancias de proceso intensivo con Linux. Las últimas versiones de HPC Pack admiten varias distribuciones de Linux en nodos de ejecución implementados en máquinas virtuales de Azure, administradas por un nodo principal de Windows Server. Con los nodos de proceso de Linux compatibles con RDMA que ejecutan Intel MPI, HPC Pack puede programar y ejecutar aplicaciones que tienen acceso a la red RDMA de Linux MPI. Para comenzar, consulte [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="network-topology-considerations"></a>Consideraciones sobre la topología de red
* En las máquinas virtuales con Linux compatibles con RDMA de Azure, Eth1 se reserva para el tráfico de red RDMA. No cambie ninguna configuración Eth1 ni ninguna información del archivo de configuración que haga referencia a esta red. Eth0 se reserva para el tráfico de red regular de Azure.
* En Azure, no se admite IP sobre InfiniBand (IB). Solo se admite RDMA sobre IB.



## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre la disponibilidad y los precios de los tamaños de proceso intensivo, consulte los [precios de las máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).
* Para más información sobre las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de máquinas virtuales](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para empezar a implementar y usar los tamaños de proceso intensivo con RDMA en Linux, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


