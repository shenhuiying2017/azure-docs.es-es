---
title: "Migración con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo migrar máquinas virtuales locales y de Azure con el servicio Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: edf6ffe1cd55884f1c18498213df290cb19bb246
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="about-migration"></a>Información sobre la migración

Lea este artículo para obtener información general rápida sobre cómo el servicio [Azure Site Recovery](site-recovery-overview.md) facilita la migración de las máquinas. 

A continuación se indica qué puede migrar con Site Recovery:

- **Migrar desde un entorno local a Azure**: migre las máquinas virtuales Hyper-V locales, las máquinas virtuales de VMware y los servidores físicos a Azure. Después de la migración, las cargas de trabajo que se ejecutan en las máquinas locales se ejecutarán en las máquinas virtuales de Azure. 
- **Migrar dentro de Azure**: migre las máquinas virtuales de Azure entre regiones de Azure. 
- **Migrar AWS**: migre instancias de Windows AWS a máquinas virtuales IaaS de Azure. 


## <a name="what-do-we-mean-by-migration"></a>¿Qué entendemos por migración?

Además de utilizar Site Recovery para la recuperación ante desastres de máquinas virtuales locales y de Azure, puede usar el servicio Site Recovery para migrarlas. ¿Cuál es la diferencia?

- Para la recuperación ante desastres, debe replicar las máquinas con regularidad en Azure. Cuando se produce una interrupción, se conmutan por error las máquinas desde el sitio principal al sitio de Azure secundario y se accede a ellas desde ahí. Cuando el sitio principal vuelva a estar disponible, puede realizar una conmutación por recuperación desde Azure.
- Para la migración, replique las máquinas locales en Azure o las máquinas virtuales de Azure en una región secundaria. Después, conmute por error la máquina virtual del sitio principal al secundario y complete el proceso de migración. No hay implicada ninguna conmutación por recuperación.  


## <a name="migration-scenarios"></a>Escenarios de migración

**Escenario** | **Detalles**
--- | ---
**Migración desde un entorno local a Azure** | Puede migrar las máquinas virtuales de VMware locales, las máquinas virtuales Hyper-V y los servidores físicos a Azure. Para ello, debe completar casi los mismos pasos que seguiría para una recuperación ante desastres completa. Lo único que no debe hacer es una conmutación por recuperación de las máquinas de Azure al sitio local.
**Migración entre regiones de Azure** | Puede migrar máquinas virtuales de Azure de una región de Azure a otra. Una vez completada la migración, ahora puede configurar la recuperación ante desastres de las máquinas virtuales de Azure en la región secundaria a la que ha migrado.
**Migración de AWS a Azure** | Puede migrar instancias de AWS a máquinas virtuales de Azure. Site Recovery trata las instancias de AWS como servidores físicos en lo que respecta a la migración. 

## <a name="next-steps"></a>Pasos siguientes

- [Migración de máquinas locales a Azure](migrate-tutorial-on-premises-azure.md)
- [Migración de máquinas virtuales de una región de Azure a otra](azure-to-azure-tutorial-migrate.md)
- [Migración de AWS a Azure](migrate-tutorial-aws-azure.md)
