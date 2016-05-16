<properties
 pageTitle="Opciones de clúster de HPC Pack de Linux en la nube | Microsoft Azure"
 description="Obtenga información sobre opciones con Microsoft HPC Pack para crear y administrar un clúster de informática de alto rendimiento (HPC) de Linux en la nube de Azure."
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/29/2016"
 ms.author="danlep"/>

# Opciones para crear y administrar un clúster de informática de alto rendimiento (HPC) en Azure con Microsoft HPC Pack

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artículo se centra en las opciones para usar HPC Pack para ejecutar cargas de trabajo de Linux. También hay opciones para ejecutar [cargas de trabajo de Windows HPC con HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Ejecución de un clúster de HPC Pack en VM de Azure

### Plantillas de Azure


* (Marketplace) [HPC Pack cluster for Linux workloads (Clúster de HPC par cargas de trabajo de Linux)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Quickstart) [Create an HPC cluster with Linux compute nodes (Creación de clúster de HPC con nodos de proceso de Linux)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)


### Imágenes de VM de Azure

* [HPC Pack en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)



### Script de implementación de PowerShell

* [Creación de un clúster de HPC con el script de implementación de HPC Pack IaaS.](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Tutoriales

* [Tutorial: Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Tutorial: Ejecute NAMD con Microsoft HPC Pack en los nodos de cálculo de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Tutorial: Ejecución de OpenFOAM con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Tutorial: Ejecución de STAR-clúster MCC + con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### Administración de clústeres

* [Envío de trabajos a un clúster de HPC Pack en Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## Creación de clústeres RDMA para cargas de trabajo MPI

* [Tutorial: Ejecución de OpenFOAM con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0504_2016-->