<properties
 pageTitle="Acerca de los tamaños de máquina virtual A8, A9, A10 y A11 y Windows | Microsoft Azure"
 description="Obtenga información general y consideraciones sobre el uso de las instancias de proceso intensivo A8, A9, A10 y A11 de Azure para servicios en la nube y máquinas virtuales Windows."
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="08/04/2016"
 ms.author="danlep"/>

# Sobre las instancias de proceso intensivo A8, A9, A10 y A11

Aquí se proporciona información general y algunas consideraciones sobre el uso de las instancias de Azure A8, A9, A10 y A11, también conocidas como instancias de *proceso intensivo*. Este artículo se centra en el uso de estas instancias para máquinas virtuales de Windows. Este artículo también está disponible para [máquinas virtuales de Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acceso a la red RDMA

En un único servicio en la nube, conjunto de disponibilidad o grupo de Lote de Azure, las instancias A8 y A9 pueden tener acceso a la red RDMA de Azure para ejecutar aplicaciones de la MPI de Windows que usan la interfaz Microsoft Network Direct para comunicarse entre instancias.

A continuación, se muestran requisitos previos para que las aplicaciones de MPI tengan acceso a la red RDMA en las máquinas virtuales Windows, en los servicios en la nube y en los grupos de Lote de Azure de las instancias A8 o A9. Para ver escenarios de implementación típicos, consulte [Configuración de un clúster de Windows RDMA con HPC Pack e instancias de A8 y A9 para ejecutar aplicaciones de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) y [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Lote de Azure](../batch/batch-mpi.md).


Requisito previo | Máquinas virtuales | Servicios en la nube o grupo de Lote 
---------- | ------------ | ------------- 
Sistema operativos | Windows Server 2012 R2 o Windows Server 2012 | Familia de SO invitado Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. 
MPI | MS-MPI 2012 R2 o posterior, o Intel MPI Library 5 | MS-MPI 2012 R2 o posterior, o Intel MPI Library 5 


>[AZURE.NOTE]Para las máquinas virtuales de los tamaños A8 y A9, es necesario agregar la extensión HpcVmDrivers a las máquinas virtuales para instalar los controladores de dispositivo de red de Windows necesarios para la conectividad RDMA. Según su método de implementación, la extensión HpcVmDrivers podría agregarse automáticamente a una máquina virtual de tamaño A8 o A9 o puede que tenga que agregarla manualmente. Para agregar la extensión manualmente, consulte [Administración de extensiones de máquina virtual](virtual-machines-windows-classic-manage-extensions.md).

## Consideraciones sobre HPC Pack y Linux

HPC Pack no es necesario para usar las instancias A8, A9, A10 y A11 con Windows Server, pero es una herramienta útil para ejecutar aplicaciones MPI basadas en Windows que accedan a la red de RDMA de Azure. HPC Pack 2012 R2 y las versiones posteriores incluyen un entorno de tiempo de ejecución para la implementación de la interfaz de paso de mensajes (MS-MPI) de Microsoft para Windows que puede usar la red de RDMA de Azure si se implementa en instancias A8 y A9.

Para obtener más información y listas de comprobación para usar instancias de proceso intensivo con HPC Pack en Windows Server, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## Pasos siguientes

* Para obtener más información sobre la disponibilidad y los precios de las instancias A8, A9, A10 y A11, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) y [Precios de servicios en la nube](https://azure.microsoft.com/pricing/details/cloud-services/).

* Para más información sobre las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md).

* Para empezar a implementar y usar instancias A8 y A9 con HPC Pack en Windows, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Para más información sobre el uso de instancias A8 y A9 para ejecutar aplicaciones MPI con Lote de Azure, consulte [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Lote de Azure](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0810_2016-->