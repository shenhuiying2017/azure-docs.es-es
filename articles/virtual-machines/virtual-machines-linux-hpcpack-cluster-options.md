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
 ms.date="09/26/2016"
 ms.author="danlep"/>

# Opciones con HPC Pack para crear y administrar un clúster de HPC en Azure para cargas de trabajo de Linux

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artículo se centra en las opciones para usar HPC Pack para ejecutar cargas de trabajo de Linux. También hay opciones para ejecutar [cargas de trabajo de Windows HPC con HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## Ejecución de un clúster de HPC Pack en VM de Azure

### Plantillas de Azure


* (Marketplace) [HPC Pack cluster for Linux workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) (Clúster de HPC para cargas de trabajo de Linux)

* (Inicio rápido) [Create an HPC cluster with Linux compute nodes](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn) (Creación de un clúster de HPC con nodos de proceso de Linux)


### Script de implementación de PowerShell

* [Creación de un clúster de informática de alto rendimiento (HPC) en máquinas virtuales Linux con el script de implementación de HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Tutoriales

* [Tutorial: Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Tutorial: Ejecute NAMD con Microsoft HPC Pack en los nodos de cálculo de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Tutorial: Ejecución de OpenFOAM con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Tutorial: Ejecución de STAR-clúster MCC + con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### Administración de clústeres

* [Envío de trabajos a un clúster de HPC Pack en Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Job management in HPC Pack](https://technet.microsoft.com/library/jj899585.aspx) (Administración de trabajos en HPC Pack)


## Creación de clústeres RDMA para cargas de trabajo MPI

* [Tutorial: Ejecución de OpenFOAM con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0928_2016-->