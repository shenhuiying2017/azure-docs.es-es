---
title: "Revisión de los requisitos previos para la replicación de Hyper-V en Azure (con System Center VMM) mediante Azure Site Recovery | Microsoft Docs"
description: "Describe los requisitos previos para configurar la replicación, la conmutación por error y la recuperación en Azure de máquinas virtuales de Hyper-V locales en nubes de VMM con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>Paso 2: Revisión de los requisitos previos para la replicación en Azure de Hyper-V (con VMM)

Después de revisar la [arquitectura del escenario](vmm-to-azure-walkthrough-architecture.md), lea este artículo para asegurarse de que entiende los requisitos previos de implementación. 

## <a name="prerequisites-and-limitations"></a>Requisitos previos y limitaciones

**Requisito** | **Detalles**
--- | ---
**Cuenta de Azure** | Necesita una [cuenta de Microsoft Azure](http://azure.microsoft.com/).
**Almacenamiento de Azure** | Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados.<br/><br/> La cuenta de almacenamiento debe estar en la misma región que el almacén de Azure Recovery Services.<br/><br/>Puede usar [almacenamiento con redundancia geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) o almacenamiento con redundancia local. Se recomienda el almacenamiento con redundancia geográfica. Con el almacenamiento con redundancia geográfica, los datos resisten en caso de una interrupción regional o si no se puede recuperar la región primaria.<br/><br/> Puede usar una cuenta de almacenamiento de Azure estándar o puede usar Azure [Premium Storage](../storage/common/storage-premium-storage.md). Premium Storage puede hospedar cargas de trabajo de E/S intensas y, normalmente, se usa para las máquinas virtuales que necesitan un alto rendimiento constante de E/S y latencia baja. Si utiliza Premium Storage para los datos replicados, también necesitará una cuenta de almacenamiento estándar. Una cuenta de almacenamiento estándar almacena los registros de replicación que capturan los cambios continuos en los datos locales.
**Red de Azure** | Se necesita una [red de Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md) a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error. La red de Azure debe estar en la misma región que el almacén de Recovery Services.
**Servidores de VMM locales** | Necesita uno o más servidores de VMM con System Center 2012 R2 o versiones posteriores.<br/><br/> Todos los servidores de VMM deben tener una o varias nubes privadas. Cada nube necesita uno o más grupos host.<br/><br/> El servidor de VMM necesita acceso a Internet.
**Hyper-V local** | Los servidores host de Hyper-V deben ejecutar como mínimo Windows Server 2012 R2 con el rol de Hyper-V habilitado, o Microsoft Hyper-V Server 2012 R2. Deben instalarse las actualizaciones más recientes.<br/><br/> El host de Hyper-V debe estar situado en un grupo host de VMM (situado en una nube de VMM).<br/><br/> Un host debe tener una o varias máquinas virtuales que desee replicar.<br/><br/> Los hosts de Hyper-V deben estar conectados a Internet para replicación en Azure, directamente o mediante un servidor proxy. Los servidores de Hyper-V deben tener instaladas las correcciones que se describen en el artículo [2961977](https://support.microsoft.com/kb/2961977).
**Máquinas virtuales de Hyper-V locales** | Las máquinas virtuales que desee replicar deben ejecutar un [sistema operativo compatible](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) y cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). El nombre de la máquina virtual puede modificarse una vez habilitada la replicación. 




## <a name="next-steps"></a>Pasos siguientes

Vaya al [paso 3: planeamiento de la capacidad](vmm-to-azure-walkthrough-capacity.md)
