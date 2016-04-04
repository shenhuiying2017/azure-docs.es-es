<properties
 pageTitle="Acerca de las instancias de entre la A8 y la A11 con Windows | Microsoft Azure"
 description="Obtenga información general y consideraciones sobre el uso de las instancias de proceso intensivo A8, A9, A10 y A11 de Azure para servicios en la nube y máquinas virtuales de Windows."
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
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Acerca del uso de las instancias de proceso intensivo A8, A9, A10 y A11 con Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acceso a la red RDMA

En un único servicio en la nube o conjunto de disponibilidad, las instancias A8 y A9 pueden tener acceso a la red RDMA de Azure para ejecutar aplicaciones de la MPI de Windows que usan la interfaz Microsoft Network Direct para comunicarse entre instancias.

Consulte la siguiente tabla para obtener información acerca de los requisitos previos para que las aplicaciones de MPI tengan acceso a la red RDMA en las implementaciones de máquina virtual (IaaS) y de servicio en la nube (PaaS) de las instancias A8 o A9. Para escenarios de implementación típicos, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).


Requisito previo | Máquinas virtuales (IaaS) | Servicios en la nube (PaaS)
---------- | ------------ | -------------
Sistema operativos | Windows Server 2012 R2 o Windows Server 2012 | Familia de SO invitado Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.
MPI | MS-MPI 2012 R2 o versiones posteriores, ya sean independientes o instaladas a través de HPC Pack 2012 R2 o versiones posteriores<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 o versiones posteriores, instaladas a través de HPC Pack 2012 R2 o versiones posteriores<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]Para escenarios de IaaS, es necesario agregar la extensión HpcVmDrivers a las máquinas virtuales para instalar los controladores de dispositivo de red de Windows necesarios para la conectividad RDMA. Según su método de implementación, la extensión HpcVmDrivers podría agregarse automáticamente a una máquina virtual de tamaño A8 o A9 o es posible que tenga que agregarla manualmente. Para agregar la extensión, consulte [Administración de extensiones de máquina virtual](virtual-machines-windows-classic-manage-extensions.md).

## Consideraciones sobre HPC Pack y Linux

HPC Pack no es necesario para usar las instancias A8, A9, A10 y A11 con Windows Server, pero es una herramienta recomendada para ejecutar aplicaciones MPI basadas en Windows que accedan a la red de RDMA de Azure. HPC Pack incluye un entorno de tiempo de ejecución para la implementación de Microsoft de la interfaz de transferencia de mensajes (MS-MPI) para Windows.

Para obtener más información y listas de comprobación para usar instancias de proceso intensivo con HPC Pack en Windows Server, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

## Pasos siguientes

* Para obtener más información sobre la disponibilidad y los precios de las instancias A8, A9, A10 y A11, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/) y [Precios de servicios en la nube](https://azure.microsoft.com/pricing/details/cloud-services/).

* Para empezar a implementar y usar instancias A8 y A9 con HPC Pack en Windows, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Para obtener información sobre el uso de instancias A8 y A9 para ejecutar aplicaciones MPI con el lote de Azure, consulte [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Lote de Azure](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0323_2016-->