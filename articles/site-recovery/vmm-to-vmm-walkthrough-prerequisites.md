---
title: "Revisión de los requisitos previos para la replicación de Hyper-V en un sitio secundario de VMM con Azure Site Recovery | Microsoft Docs"
description: "Describe los requisitos previos para la replicación de máquinas virtuales de Hyper-V en un sitio secundario de VMM con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>Paso 2: Revisión de los requisitos previos y de las limitaciones para la replicación de máquinas virtuales de Hyper-V en un sitio secundario de VMM


Una vez que haya revisado la [arquitectura del escenario](vmm-to-vmm-walkthrough-architecture.md), lea este artículo para asegurarse de que comprende los requisitos previos de la implementación al replicar máquinas virtuales locales de Hyper-V administradas en nubes de System Center Virtual Machine Manager (VMM) en un sitio secundario mediante [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites-and-limitations"></a>Requisitos previos y limitaciones

**Requisito** | **Detalles**
--- | ---
**Las tablas de Azure** | Una suscripción de [Microsoft Azure](http://azure.microsoft.com/).<br/><br/> Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery.<br/><br/> Para comprobar las regiones admitidas para Site Recovery, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
**Servidores VMM** | Se recomienda disponer de dos servidores VMM, uno en el sitio principal y otro en el secundario.<br/><br/> Se admite la replicación entre nubes en un único servidor VMM.<br/><br/> Los servidores VMM deben estar ejecutando, como mínimo, System Center 2012 SP1 con las actualizaciones más recientes.<br/><br/> Los servidores VMM necesitan acceso a Internet.
**Nubes VMM** | Cada servidor VMM debe tener una o más nubes y todas ellas deben tener definido el perfil de capacidad de Hyper-V. <br/><br/>Las nubes deben incluir uno o más grupos de hosts de VMM.<br/><br/> Si solo tiene un servidor VMM, necesita al menos dos nubes, para que actúen como principal y secundaria.
**Hyper-V** | Los servidores Hyper-V deben estar ejecutando, como mínimo, Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.<br/><br/> Un servidor de Hyper-V debe contener una o varias máquinas virtuales.<br/><br/>  Los servidores host de Hyper-V deben estar ubicados en grupos host de las nubes de VMM principal y secundaria.<br/><br/> Si ejecuta Hyper-V en un clúster con Windows Server 2012 R2, instale la [actualización 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si ejecuta Hyper-V en un clúster con Windows Server 2012, el agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Configúrelo manualmente. [Más información](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Los servidores de Hyper-V necesitan acceso a Internet.




## <a name="next-steps"></a>Pasos siguientes

Vaya al [Paso 3: Planeamiento de las redes](vmm-to-vmm-walkthrough-network.md).
