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
ms.date: 11/18/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: f73cbfec2ce2e41589b84997891ff0b60266c9b2
ms.openlocfilehash: 10a10e138c9c8ed8c15136bf1d6565edc57758b5


---
# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Acerca de las máquinas virtuales de la serie H y A de proceso intensivo
Aquí se proporciona información general y algunas consideraciones sobre el uso de las series H de Azure más recientes y los antiguos tamaños A8, A9, A10 y A11, también conocidos como instancias de *proceso intensivo* . Este artículo se centra en el uso de estos tamaños con las máquinas virtuales con Linux. Este artículo también está disponible para [máquinas virtuales con Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para conocer las especificaciones básicas, las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de las máquinas virtuales](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acceso a la red RDMA
Puede crear clústeres de máquinas virtuales con Linux compatibles con RDMA que ejecuten una de las siguientes distribuciones de HPC de Linux compatibles y una implementación de MPI compatible para aprovechar la red RDMA de Azure. Para conocer las opciones de implementación y los pasos de una configuración de ejemplo, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

* **Distribuciones** : debe implementar máquinas virtuales de SUSE Linux Enterprise Server (SLES) compatibles con RDMA o imágenes de HPC basadas en OpenLogic CentOS en Azure Marketplace. Solo las siguientes imágenes de Marketplace admiten los controladores Linux RDMA necesarios:
  
  * SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium)
  * SLES 12 para HPC, SLES 12 para HPC (Premium)
  * HPC 7.1 basada en CentOS
  * HPC 6.5 basada en CentOS
    
    > [!NOTE]
    > Para las máquinas virtuales de la serie H, se recomienda un SLES 12 SP1 para la imagen de HPC o la imagen de HPC 7.1 basada en CentOS.
    > 
    > En las imágenes de HPC basadas en CentOS, las actualizaciones del núcleo están deshabilitadas en el archivo de configuración **yum** . Esto se debe a que los controladores Linux RDMA se distribuyen en forma de paquete RPM y sus actualizaciones de estos podrían no funcionar si se actualiza el kernel.
    > 
    > 
* **MPI** : Biblioteca MPI Intel 5.x
  
    Dependiendo de la imagen de Marketplace que elija, puede ser necesaria la instalación de licencia, la instalación o la configuración independientes de Intel MPI, como sigue: 
  
  * **SLES 12 SP1 para la imagen de HPC** : instale los paquetes de Intel MPI distribuidos en la máquina virtual; para ello, ejecute el comando siguiente:
    
          sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  * **SLES 12 para la imagen de HPC** : debe registrarse independientemente para descargar e instalar Intel MPI. Para obtener instrucciones, consulte la [documentación de la biblioteca de Intel MPI](https://software.intel.com/en-us/intel-mpi-library/documentation).
  * **Imágenes de HPC basadas en CentOS**: Intel MPI 5.1 ya está instalado.  
    
    Se necesita configuración adicional del sistema para ejecutar trabajos MPI en máquinas virtuales en clúster. Por ejemplo, en un clúster de máquinas virtuales, debe establecer la confianza entre los nodos de proceso. Para configuraciones típicas, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="considerations-for-hpc-pack-and-linux"></a>Consideraciones sobre el HPC Pack y Linux
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la solución gratuita de administración de clústeres y trabajos de HPC de Microsoft, proporciona una opción para usar las instancias de proceso intensivo con Linux. Las últimas versiones de HPC Pack 2012 R2 admiten varias distribuciones de Linux en nodos de ejecución implementados en máquinas virtuales de Azure, administradas por un nodo principal de Windows Server. Con los nodos de proceso de Linux compatibles con RDMA que ejecutan Intel MPI, HPC Pack puede programar y ejecutar aplicaciones que tienen acceso a la red RDMA de Linux MPI. Para comenzar, consulte [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="network-topology-considerations"></a>Consideraciones sobre la topología de red
* En las máquinas virtuales con Linux compatibles con RDMA de Azure, Eth1 se reserva para el tráfico de red RDMA. No cambie ninguna configuración Eth1 ni ninguna información del archivo de configuración que haga referencia a esta red. Eth0 se reserva para el tráfico de red regular de Azure.
* En Azure, no se admite IP sobre InfiniBand (IB). Solo se admite RDMA sobre IB.

## <a name="rdma-driver-updates-for-sles-12"></a>Actualizaciones de controladores RDMA para SLES 12
Después de crear una máquina virtual basada en una imagen HPC de SLES 12, puede ser necesario actualizar los controladores RDMA en las máquinas virtuales para obtener la conectividad de red RDMA. 

> [!IMPORTANT]
> Este paso es **necesario** para las implementaciones de máquinas virtuales de HPC de SLES 12 en todas las regiones de Azure. Este paso no es necesario si implementa SLES 12 SP1 para HPC, HPC 7.1 basado en CentOS o máquina virtual de HPC 6.5 basada en CentOS. 
> 
> 

Antes de actualizar los controladores, detenga todos los procesos **zypper** o cualquier proceso que bloquee las bases de datos de repositorio de SUSE en la máquina virtual. De lo contrario, puede que los controladores no se actualicen correctamente.  

Para actualizar los controladores Linux RDMA de cada máquina virtual, ejecute uno de los siguientes conjuntos de comandos de la CLI de Azure desde el equipo cliente.

**SLES 12 para máquina virtual de HPC aprovisionada en el modelo de implementación clásico**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**SLES 12 para máquina virtual de HPC aprovisionada en el modelo de implementación de Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

> [!NOTE]
> Los controladores pueden tardar un tiempo en instalarse y el comando no devuelve resultados. Tras la actualización, la VM se reiniciará y estará lista para su uso en unos minutos.
> 
> 

### <a name="sample-script-for-driver-updates"></a>Scripts de ejemplo para las actualizaciones de controladores
Si tiene un clúster de SLES 12 para máquinas virtuales de HPC, puede crear scripts de la actualización del controlador en todos los nodos del clúster. Por ejemplo, el siguiente script actualiza los controladores en un clúster de ocho nodos.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre la disponibilidad y los precios de los tamaños de proceso intensivo, consulte los [precios de las máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).
* Para más información sobre las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para empezar a implementar y usar los tamaños de proceso intensivo con RDMA en Linux, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).




<!--HONumber=Nov16_HO4-->


