---
title: Acerca de las máquinas virtuales de proceso intensivo con Windows | Microsoft Docs
description: Obtenga información general y consideraciones sobre el uso de la serie H y las instancias de proceso intensivo A8, A9, A10 y A11 de Azure para servicios en la nube y máquinas virtuales Windows.
services: virtual-machines-windows, cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: danlep

---
# Acerca de las máquinas virtuales de la serie H y A de proceso intensivo
Aquí se proporciona información general y algunas consideraciones sobre el uso de las series H de Azure más recientes y las antiguas instancias A8, A9, A10 y A11, también conocidas como instancias de *proceso intensivo*. Este artículo se centra en el uso de estas instancias para máquinas virtuales de Windows. Este artículo también está disponible para [máquinas virtuales de Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acceso a la red RDMA
Puede crear clústeres de instancias del servidor de Windows compatibles con RDMA e implementar una de las implementaciones de MPI compatibles para aprovechar la red RDMA de Azure. Esta red de baja latencia y alto rendimiento se reserva solo para el tráfico MPI.

* **Sistema operativos**
  
  * **Máquinas virtuales**: Windows Server 2012 R2, Windows Server 2012.
  * **Cloud Services**: familia de SO invitado Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.
* **MPI**: Microsoft MPI (MS-MPI) 2012 R2 o versiones posteriores, MPI Intel Library 5.x.

Las implementaciones de MPI compatibles usan la interfaz Microsoft Network Direct para comunicarse entre las instancias. Para ver las opciones de implementación y los pasos de configuración del ejemplo, consulte [Configuración de un clúster de Windows RDMA con HPC Pack e instancias de A8 y A9 para ejecutar aplicaciones de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) y [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Azure Batch](../batch/batch-mpi.md).

> [!NOTE]
> En las máquinas virtuales de proceso intensivo compatibles con RDMA, es necesario agregar la extensión HpcVmDrivers a las máquinas virtuales para instalar los controladores de dispositivos de red de Windows necesarios para la conectividad RDMA. En la mayoría de las implementaciones, la extensión HpcVmDrivers se agrega automáticamente. Si necesita agregar la extensión manualmente, consulte [Administración de extensiones de máquina virtual](virtual-machines-windows-classic-manage-extensions.md).
> 
> 

## Consideraciones sobre HPC Pack y Linux
[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la solución gratuita de administración de clústeres y trabajos de HPC de Microsoft, no se necesita para usar las instancias de proceso intensivo con Windows Server. Sin embargo, es una opción para crear un clúster de proceso en Azure para ejecutar aplicaciones de MPI basadas en Windows y otras cargas de trabajo de HPC. HPC Pack 2012 R2 y las versiones posteriores incluye un entorno de tiempo de ejecución para MS-MPI que puede usar la red RDMA de Azure cuando se implementa en máquinas virtuales compatibles con RDMA.

Para obtener más información y listas de comprobación para usar instancias de proceso intensivo con HPC Pack en Windows Server, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

## Pasos siguientes
* Para más información sobre la disponibilidad y los precios de las instancias de proceso intensivo, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) y [Precios de Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/).
* Para más información sobre las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md).
* Para empezar a implementar y usar instancias de proceso intensivo con HPC Pack en Windows, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).
* Para más información sobre el uso de instancias A8 y A9 para ejecutar aplicaciones MPI con Lote de Azure, consulte [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Lote de Azure](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0928_2016-->