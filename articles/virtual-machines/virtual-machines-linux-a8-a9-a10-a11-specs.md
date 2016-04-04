<properties
 pageTitle="Acerca de las instancias A8 - A11 y Linux | Microsoft Azure"
 description="Obtenga información general y algunas consideraciones sobre el uso de los tamaños de proceso intensivo A8, A9, A10 y A11 para las máquinas virtuales de Linux."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Acerca del uso de las instancias de proceso intensivo A8, A9, A10 y A11 con Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acceso a la red RDMA

En un único servicio en la nube o en un conjunto de disponibilidad, las instancias A8 y A9 pueden acceder a la red RDMA en Azure para ejecutar aplicaciones de la MPI de Linux. En este momento, Azure Linux RDMA solo se admite con [Intel MPI Library 5](https://software.intel.com/es-ES/intel-mpi-library/).

>[AZURE.NOTE] Actualmente los controladores Azure Linux RDMA no están disponibles para instalarse a través de extensiones de controladores. Solamente están disponibles mediante la imagen SLES 12 habilitada para RDMA desde Azure Marketplace.

En la tabla siguiente se resumen los requisitos previos para las aplicaciones de la MPI de Linux para acceder a la red RDMA en clústeres de nodos de ejecución (IaaS). Consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md) para obtener opciones de implementación y pasos de configuración.

Requisito previo | Máquinas virtuales (IaaS)
------------ | -------------
Sistema operativos | Imagen de SLES 12 HPC de Azure Marketplace
MPI | Intel MPI Library 5

## Consideraciones sobre el HPC Pack y Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) es la solución gratuita de administración de trabajos y clústeres HPC de Microsoft para Windows. Las últimas versiones de HPC Pack 2012 R2 admiten varias distribuciones de Linux en nodos de ejecución implementados en máquinas virtuales de Azure, administradas por un nodo principal de Windows Server. Los nodos de cálculo de Linux implementados en las máquinas virtuales A8 o A9 y que ejecutan una implementación de MPI compatible pueden ejecutar aplicaciones de MPI que tienen acceso a la red RDMA. Para comenzar, consulte [Get started with Linux compute nodes in an HPC Pack cluster in Azure](virtual-machines-linux-classic-hpcpack-cluster.md) (Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure).

## Pasos siguientes

* Para obtener más información sobre la disponibilidad y los precios de las instancias A8, A9, A10 y A11, consulte [Precios de Máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/).

* Para empezar a implementar y usar las instancias A8 y A9 con RDMA en Linux, consulte [Set up a Linux RDMA cluster to run MPI applications](virtual-machines-linux-classic-rdma-cluster.md) (Configuración de un clúster RDMA de Linux para ejecutar aplicaciones MPI).

<!---HONumber=AcomDC_0323_2016-->