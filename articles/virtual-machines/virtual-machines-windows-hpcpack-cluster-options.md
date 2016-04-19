<properties
 pageTitle="Opciones de clúster de Windows HPC Pack en la nube | Microsoft Azure"
 description="Obtenga información sobre opciones con Microsoft HPC Pack para crear y administrar un clúster de informática de alto rendimiento (HPC) de Windows en la nube de Azure."
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Opciones para crear y administrar un clúster de informática de alto rendimiento (HPC) de Windows en Azure con Microsoft HPC Pack

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Si desea ejecutar las cargas de trabajo de HPC de Linux con HPC Pack, consulte [Opciones para crear y administrar un Linux HPC cluster en Azure con Microsoft HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Ejecución de un clúster de HPC Pack en VM de Azure

### Plantillas de Azure

* (Marketplace) [HPC Pack cluster for Windows workloads (Clúster de HPC par cargas de trabajo de Windows)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [HPC Pack cluster for Excel workloads (Clúster de HPC par cargas de trabajo de Excel)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Quickstart) [Create an HPC cluster (Creación de clúster de HPC)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* (Quickstart) [Create an HPC cluster with custom compute node image (Creación de clúster de HPC con nodos de proceso personalizados)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Imágenes de VM de Azure

* [HPC Pack en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nodo de proceso de HPC Pack en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nodo de proceso de HPC Pack con Excel en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### Script de implementación de PowerShell

* [Creación de un clúster de HPC con el script de implementación de HPC Pack IaaS.](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### Tutoriales

* [Tutorial: Introducción a un clúster de HPC Pack en Azure para ejecutar cargas de trabajo de Excel y SOA](virtual-machines-windows-excel-cluster-hpcpack.md)



### Implementación manual con el portal de Azure

* [Configuración del nodo principal de un clúster de HPC Pack en una VM de Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### Administración de clústeres

* [Administración de nodos de proceso en un clúster de HPC Pack en Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)


* [Aumento y reducción de los recursos de proceso de Azure en un clúster de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Envío de trabajos a un clúster de HPC Pack en Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## Adición de nodos de rol de trabajo a un clúster de HPC Pack


* [Irrumpir en instancias de trabajo de Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Tutorial: Configuración de un clúster híbrido con HPC Pack en Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Adición de nodos "ráfaga" de Azure a un nodo principal de HPC Pack en Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)

* [Aumento y reducción de los recursos de proceso de Azure en un clúster de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

## Integración con Azure Batch 

* [Irrumpir en Lote de Azure con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## Creación de clústeres RDMA para cargas de trabajo MPI

* [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

<!---HONumber=AcomDC_0406_2016-->