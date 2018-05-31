---
title: Opciones de clúster de Windows HPC Pack en Azure | Microsoft Docs
description: Obtenga información sobre opciones con Microsoft HPC Pack para crear y administrar un clúster de informática de alto rendimiento (HPC) de Windows en la nube de Azure.
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165764"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Opciones de HPC Pack para crear y administrar un clúster para cargas de trabajo de Windows HPC en Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artículo se centra en las opciones de Azure para crear clústeres de HPC Pack para ejecutar cargas de trabajo de Windows. También hay opciones para crear clústeres de HPC Pack que ejecuten [cargas de trabajo de Linux HPC](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Clúster de HPC Pack en máquinas virtuales de Azure y en conjuntos de escalado de máquinas virtuales
### <a name="azure-resource-manager-templates"></a>Plantillas del Administrador de recursos de Azure
* (GitHub) [HPC Pack 2016 Update 1 cluster templates](https://github.com/MsHpcPack/HPCPack2016) (Plantillas de clúster de HPC Pack 2016 Update 1)
* (GitHub) [HPC Pack 2012 R2 cluster templates](https://github.com/MsHpcPack/HPCPack2012R2) (Plantillas de clúster de HPC Pack 2012 R2)
* (Guía de inicio rápido) [Create an HPC Pack 2012 R2 cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster) (Creación de un clúster de HPC Pack 2012 R2)
* (Guía de inicio rápido) [Create an HPC Pack 2012 R2 cluster with custom compute node image](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image) (Creación de un clúster de HPC Pack 2012 R2 con nodos de proceso personalizados)

### <a name="azure-vm-images"></a>Imágenes de VM de Azure
Examine las [imágenes de HPC Pack en Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) si desea crear su propio clúster de HPC Pack en Azure.


### <a name="tutorials"></a>Tutoriales
* [Tutorial: Deploy an HPC Pack 2016 cluster in Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Implementación de un clúster de HPC Pack 2016 en Azure)
* [Manage an HPC Pack 2016 cluster in Azure with Azure Active Directory (Administrar un clúster de HPC Pack 2016 en Azure con Azure Active Directory)](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>Implementación de clústeres de HPC Pack 2012 R2 en el modelo de implementación clásica
* [Creación de un clúster de HPC con el script de implementación de HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Tutorial: Introducción a un clúster de HPC Pack en Azure para ejecutar cargas de trabajo de Excel y SOA](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Administración de nodos de proceso en un clúster de HPC Pack en Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Aumento y reducción de los recursos de proceso de Azure en un clúster de HPC Pack](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>Irrumpir en Azure desde HPC Pack 2012 R2
* [Burst to Azure Worker Instances with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx) (Irrumpir en instancias de trabajo de Azure con Microsoft HPC Pack)
* [Irrumpir en Azure Batch con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)
* [Tutorial: Configuración de un clúster híbrido con HPC Pack en Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>Envío de trabajo

* [Envío de trabajos a un clúster de HPC Pack en Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)






