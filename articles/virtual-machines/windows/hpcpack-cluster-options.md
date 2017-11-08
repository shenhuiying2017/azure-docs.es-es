---
title: "Opciones de clúster de Windows HPC Pack en Azure | Microsoft Docs"
description: "Obtenga información sobre opciones con Microsoft HPC Pack para crear y administrar un clúster de informática de alto rendimiento (HPC) de Windows en la nube de Azure."
services: virtual-machines-windows,cloud-services,batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: 3dd00d4fb0d334e836256e66207358c034fc005a
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Opciones de HPC Pack para crear y administrar un clúster para cargas de trabajo de Windows HPC en Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artículo se centra en las opciones para crear clústeres de HPC Pack para ejecutar cargas de trabajo de Windows. También hay opciones para crear clústeres de HPC Pack que ejecuten [cargas de trabajo de Linux HPC](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Clúster de HPC Pack en máquinas virtuales de Azure y en conjuntos de escalado de máquina virtual
### <a name="azure-templates"></a>Plantillas de Azure
* (GitHub) [HPC Pack 2016 cluster templates](https://github.com/MsHpcPack/HPCPack2016) (Plantillas de clúster de HPC Pack 2016)
* (GitHub) [HPC Pack 2012 R2 cluster templates](https://github.com/MsHpcPack/HPCPack2012R2) (Plantillas de clúster de HPC Pack 2012 R2)
* (Marketplace) [HPC Pack cluster for Windows workloads (Clúster de HPC par cargas de trabajo de Windows)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [HPC Pack cluster for Excel workloads (Clúster de HPC par cargas de trabajo de Excel)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (Inicio rápido) [Create an HPC cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Inicio rápido) [Create an HPC cluster with custom compute node image](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Imágenes de VM de Azure
* [Nodo principal de HPC Pack 2016 en Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [Nodo de proceso de HPC Pack 2016 en Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [Nodo principal de HPC Pack 2016 en Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [Nodo de proceso de HPC Pack 2016 en Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [Nodo principal de HPC Pack 2012 R2 en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Nodo de proceso de 2012 R2 en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [Nodo de proceso de HPC Pack con Excel en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>Script de implementación de PowerShell para HPC Pack 2012 R2
* [Creación de un clúster de HPC con el script de implementación de HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Tutoriales
* [Tutorial: Deploy an HPC Pack 2016 cluster in Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Implementación de un clúster de HPC Pack 2016 en Azure)
* [Tutorial: Introducción a un clúster de HPC Pack en Azure para ejecutar cargas de trabajo de Excel y SOA](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Implementación manual con el portal de Azure
* [Configuración del nodo principal de un clúster de HPC Pack en una VM de Azure](hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>Administración de clústeres
* [Administración de nodos de proceso en un clúster de HPC Pack en Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Aumento y reducción de los recursos de proceso de Azure en un clúster de HPC Pack](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Envío de trabajos a un clúster de HPC Pack en Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Job management in HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)
* [Manage an HPC Pack 2016 cluster in Azure with Azure Active Directory (Administrar un clúster de HPC Pack 2016 en Azure con Azure Active Directory)](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-worker-role-nodes"></a>Ráfaga con nodos de rol de trabajo 
* [Irrumpir en instancias de trabajo de Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Tutorial: Configuración de un clúster híbrido con HPC Pack en Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Adición de nodos "ráfaga" de Azure a un nodo principal de HPC Pack en Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-azure-batch"></a>Ráfaga con Azure Batch
* [Irrumpir en Lote de Azure con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>Clústeres de RDMA para cargas de trabajo MPI
* [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

